# 🐛 Bug: Multiple issues in `getFindResultsByGlobalRegExp()` causing incorrect results

## 🔴 Severity: High
**Impact:** Incorrect search results, skipped matches, potential crashes

## 📝 Description

The `getFindResultsByGlobalRegExp()` function in `lib/getFindResultsByGlobalRegExp.ts` has multiple bugs that cause incorrect behavior:

1. **`regex.lastIndex` not reset** - Causes incorrect results when function called multiple times
2. **`offset` parameter misleading** - Doesn't actually offset the search
3. **Deprecated `.substr()` usage** - Future compatibility issue
4. **Potential group extraction failure** - Silent failures when indexOf returns -1

## 🐛 Bug #1: `regex.lastIndex` Not Reset (CRITICAL)

### **Root Cause**

```typescript
// lib/getFindResultsByGlobalRegExp.ts, line 16
while ((regexResult = regex.exec(text)) !== null) {
  // ... processes results
}
// ❌ regex.lastIndex is NOT reset after loop!
```

When a global regex is used with `.exec()`, it maintains `lastIndex` state. If the same regex object is reused, subsequent calls fail.

### **Steps to Reproduce**

```javascript
import getFindResultsByGlobalRegExp from '~/lib/getFindResultsByGlobalRegExp';

const sharedRegex = /test/g;
const text = "test test test";

// First call - works correctly
const result1 = getFindResultsByGlobalRegExp(text, sharedRegex);
console.log(result1.length); // 3 ✅

// Second call with SAME regex object - FAILS!
const result2 = getFindResultsByGlobalRegExp(text, sharedRegex);
console.log(result2.length); // 0 ❌ (should be 3!)

// Why? regex.lastIndex is still at the end from first call
console.log(sharedRegex.lastIndex); // 15 (end of string)
```

### **Where this happens**

Looking at `lib/getPartsOfJson.ts`:

```typescript
// Lines 36, 73, 111, 130, 149 - Multiple calls with potentially shared regexes
const objectMatch = getFindResultsByGlobalRegExp(text, OBJECT_REGEX);
const arrayMatch = getFindResultsByGlobalRegExp(text, ARRAY_REGEX);
// ... etc
```

If these regexes are defined at module level (common pattern), they'll have stale `lastIndex`.

### **Expected Behavior**
Function should reset `regex.lastIndex = 0` before use:

```typescript
export default function getFindResultsByGlobalRegExp(
  text: string,
  regex: RegExp,
  offset = 0,
): RegExpResult[] {
  regex.lastIndex = 0; // ✅ Reset before use

  let results: RegExpResult[] = [];
  let regexResult;
  // ... rest of function
}
```

---

## 🐛 Bug #2: `offset` Parameter Is Misleading

### **Root Cause**

```typescript
// Line 6: Parameter defined
offset = 0

// Line 47: Only used in final result index
index:
  regexResult.index +
  (regexResult.groups?.pretarget?.length || 0) +
  offset,
```

### **Problem**

The parameter name `offset` suggests it offsets **where the search starts**, but it actually just adds to the final index value.

**What users expect:**
```javascript
getFindResultsByGlobalRegExp("hello world", /world/g, 6)
// Expected: Search starts at index 6, finds "world" at relative index 0
```

**What actually happens:**
```javascript
getFindResultsByGlobalRegExp("hello world", /world/g, 6)
// Actual: Searches entire string, then adds 6 to result index
// Returns index: 6 + 6 = 12 (incorrect!)
```

### **Proposed Fix**

Either:
1. **Rename parameter** to `indexOffset` or `baseOffset` for clarity
2. **Implement true offset** by slicing the text:

```typescript
export default function getFindResultsByGlobalRegExp(
  text: string,
  regex: RegExp,
  offset = 0,
): RegExpResult[] {
  regex.lastIndex = 0;

  // Implement true offset
  const searchText = offset > 0 ? text.slice(offset) : text;
  let results: RegExpResult[] = [];
  let regexResult;

  while ((regexResult = regex.exec(searchText)) !== null) {
    // Add offset to indices
    const newResult: RegExpResult = {
      match: regexResult.groups?.target || fullMatchText,
      index: regexResult.index + offset, // ✅ Correct offset
      groups,
    };
    results = [...results, newResult];
  }
  return results;
}
```

---

## 🐛 Bug #3: Deprecated `.substr()` Usage

### **Location**
```typescript
// Line 29
const inMatchGroupOffset = text.substr(groupOffset).indexOf(match);
```

### **Issue**
`.substr()` is deprecated (MDN marked as obsolete). Should use `.slice()` or `.substring()`.

### **Fix**
```typescript
const inMatchGroupOffset = text.slice(groupOffset).indexOf(match);
```

---

## 🐛 Bug #4: Potential Group Extraction Failure

### **Root Cause**

```typescript
// Lines 26-41
while (typeof regexResult[groupIndex] === 'string') {
  const match: string = regexResult[groupIndex];
  const inMatchGroupOffset = text.substr(groupOffset).indexOf(match);

  if (inMatchGroupOffset >= 0) { // ⚠️ What if it's -1?
    groupOffset += inMatchGroupOffset;
    // ...
  }
  groupIndex++;
}
```

### **Problem**

If `.indexOf()` returns `-1` (match not found), the code:
- Skips the group (correct)
- But `groupOffset` becomes stale
- Next iterations use wrong offset
- Silent failure - no error thrown

### **Proposed Fix**

```typescript
while (typeof regexResult[groupIndex] === 'string') {
  const match: string = regexResult[groupIndex];
  const inMatchGroupOffset = text.slice(groupOffset).indexOf(match);

  if (inMatchGroupOffset >= 0) {
    groupOffset += inMatchGroupOffset;
    const group: RegExpGroupResult = {
      name,
      match,
      index: groupOffset,
    };
    groupOffset += match.length;
    groups = [...groups, group];
  } else {
    // ✅ Log warning about missing group
    console.warn(`Group ${groupIndex} not found in text at offset ${groupOffset}`);
  }
  groupIndex++;
}
```

---

## 🧪 Test Cases

```typescript
describe('getFindResultsByGlobalRegExp', () => {
  describe('Bug #1: regex.lastIndex not reset', () => {
    it('should work correctly when called multiple times with same regex', () => {
      const regex = /test/g;
      const text = "test test test";

      const result1 = getFindResultsByGlobalRegExp(text, regex);
      expect(result1).toHaveLength(3);

      const result2 = getFindResultsByGlobalRegExp(text, regex);
      expect(result2).toHaveLength(3); // Should still be 3!
    });
  });

  describe('Bug #2: offset parameter', () => {
    it('should correctly offset search start position', () => {
      const text = "hello world hello";
      const regex = /hello/g;

      const result = getFindResultsByGlobalRegExp(text, regex, 6);
      expect(result).toHaveLength(1); // Should find only second "hello"
      expect(result[0].index).toBe(12);
    });
  });

  describe('Bug #3: deprecated substr', () => {
    it('should use slice instead of substr', () => {
      const code = fs.readFileSync('lib/getFindResultsByGlobalRegExp.ts', 'utf-8');
      expect(code).not.toContain('.substr(');
      expect(code).toContain('.slice(');
    });
  });

  describe('Bug #4: group extraction', () => {
    it('should handle missing groups gracefully', () => {
      const text = "hello world";
      const regex = /(?<greeting>hello) (?<name>\w+)/g;

      expect(() => {
        getFindResultsByGlobalRegExp(text, regex);
      }).not.toThrow();
    });
  });
});
```

---

## ✅ Proposed Complete Fix

```typescript
export default function getFindResultsByGlobalRegExp(
  text: string,
  regex: RegExp,
  offset = 0,
): RegExpResult[] {
  // Validation
  if (Array.isArray(text))
    throw new Error('array used for regular expression!');
  if (!regex.global)
    throw new Error(
      `regex for getFindResultsByGlobalRegExp() has no global flag ${regex.toString()}`,
    );

  // ✅ FIX #1: Reset lastIndex
  regex.lastIndex = 0;

  // ✅ FIX #2: Implement true offset
  const searchText = offset > 0 ? text.slice(offset) : text;

  let results: RegExpResult[] = [];
  let regexResult;

  while ((regexResult = regex.exec(searchText)) !== null) {
    const fullMatchText = regexResult[0];
    let groupIndex = 1;
    let groupOffset: number = regexResult.index + offset; // Adjust for offset
    let groups: RegExpGroupResult[] = [];

    const groupsReverseMap: Record<string, string> = Object.entries(
      regexResult.groups || {},
    ).reduce((acc, [groupName, match]) => {
      return { ...acc, [match]: groupName };
    }, {});

    while (typeof regexResult[groupIndex] === 'string') {
      const match: string = regexResult[groupIndex];
      const name = groupsReverseMap[match] || null;

      // ✅ FIX #3: Use .slice() instead of .substr()
      const inMatchGroupOffset = text.slice(groupOffset).indexOf(match);

      if (inMatchGroupOffset >= 0) {
        groupOffset += inMatchGroupOffset;
        const group: RegExpGroupResult = {
          name,
          match,
          index: groupOffset,
        };
        groupOffset += match.length;
        groups = [...groups, group];
      } else {
        // ✅ FIX #4: Log warning for missing group
        console.warn(
          `Regex group ${groupIndex} (${match}) not found at expected offset ${groupOffset}`
        );
      }
      groupIndex++;
    }

    const newResult: RegExpResult = {
      match: regexResult.groups?.target || fullMatchText,
      index:
        regexResult.index +
        (regexResult.groups?.pretarget?.length || 0) +
        offset,
      groups,
    };
    results = [...results, newResult];
  }

  return results;
}
```

---

## 📋 Acceptance Criteria

- [ ] `regex.lastIndex` reset to 0 before use
- [ ] `offset` parameter works correctly (true offset, not just index addition)
- [ ] All `.substr()` replaced with `.slice()`
- [ ] Group extraction failures logged with warnings
- [ ] All existing tests pass
- [ ] New tests for all 4 bugs added
- [ ] No performance regression

## 🔗 Related Files

- `lib/getFindResultsByGlobalRegExp.ts` (main fix)
- `lib/getPartsOfJson.ts` (uses this function 5+ times)
- `components/StyledMarkdown.tsx:19` (uses this function)
- Tests to be added in `lib/__tests__/getFindResultsByGlobalRegExp.test.ts`

## 💻 Environment

- **Affected versions:** All versions
- **Browser:** All
- **Node.js:** All

## 🏷️ Labels

`🐛 Bug`, `Status: Triage`, `Priority: High`, `Code Quality`

## ⏱️ Estimated Fix Time

6-8 hours (implementation + tests for all 4 bugs)

---

**Are you working on this?**
- [ ] Yes
- [ ] No
