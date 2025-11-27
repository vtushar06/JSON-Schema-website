# 🐛 Bug: Infinite recursion in `getScopesOfParsedJsonSchema()` with circular references

## 🔴 Severity: Critical
**Impact:** Application crash, potential DoS

## 📝 Description

The `getScopesOfParsedJsonSchema()` function in `lib/getScopesOfParsedJsonSchema.ts` does not detect circular references in JSON schemas, causing infinite recursion and stack overflow crashes.

## 🔍 Root Cause

The function recursively traverses schema properties without tracking visited objects:

```typescript
// lib/getScopesOfParsedJsonSchema.ts, lines 26-29
...getScopesOfParsedJsonSchema(
  parsedJsonSchema.properties?.[property],
  `${jsonPath}['properties']['${property}']`,
),
```

**Missing protections:**
- ❌ No visited set to track processed objects
- ❌ No maximum recursion depth limit
- ❌ No circular reference detection
- ❌ No object identity check

## 🚨 Steps to Reproduce

```javascript
// Create a schema with circular reference
const circularSchema = {
  type: 'object',
  properties: {
    self: null
  }
};

// Create circular reference
circularSchema.properties.self = circularSchema;

// This will cause stack overflow
import getScopesOfParsedJsonSchema from '~/lib/getScopesOfParsedJsonSchema';
getScopesOfParsedJsonSchema(circularSchema); // 💥 RangeError: Maximum call stack size exceeded
```

## 📸 Expected vs Actual Behavior

**Expected:**
- Function detects circular reference
- Returns scopes found so far OR
- Throws informative error: "Circular reference detected at path X"

**Actual:**
- Infinite recursion
- Stack overflow crash
- No error message
- Browser/Node.js crash

## 🎯 Real-World Impact

### **Where this is used:**
`components/JsonEditor.tsx:387` - processes user-provided JSON schemas

### **Attack scenario:**
1. User opens JSON Schema editor
2. Pastes schema with circular `$ref` (common after dereferencing)
3. App crashes immediately
4. Poor user experience / potential DoS

### **Common circular schemas:**
```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "properties": {
    "self": { "$ref": "#" }
  }
}
```

After dereferencing, this becomes circular.

## ✅ Proposed Solution

### **Option 1: Use WeakSet for visited tracking** ⭐ (Recommended)

```typescript
export default function getScopesOfParsedJsonSchema(
  parsedJsonSchema: any,
  jsonPath = '$',
  visited: WeakSet<object> = new WeakSet()
): JsonSchemaPathWithScope[] {
  if (typeof parsedJsonSchema !== 'object' || parsedJsonSchema === null)
    return [];

  // Detect circular reference
  if (visited.has(parsedJsonSchema)) {
    console.warn(`Circular reference detected at ${jsonPath}`);
    return [];
  }

  // Mark as visited
  visited.add(parsedJsonSchema);

  // ... rest of function
  // Pass 'visited' to all recursive calls
}
```

### **Option 2: Add maximum depth limit**

```typescript
export default function getScopesOfParsedJsonSchema(
  parsedJsonSchema: any,
  jsonPath = '$',
  maxDepth = 100,
  currentDepth = 0
): JsonSchemaPathWithScope[] {
  if (currentDepth > maxDepth) {
    console.warn(`Maximum depth ${maxDepth} exceeded at ${jsonPath}`);
    return [];
  }

  // ... recursive calls with currentDepth + 1
}
```

### **Option 3: Combine both** (Best)

Use WeakSet for circular detection AND max depth as fallback safety.

## 🧪 Test Cases

```typescript
describe('getScopesOfParsedJsonSchema', () => {
  it('should handle circular references without crashing', () => {
    const circular = { type: 'object', properties: {} };
    circular.properties.self = circular;

    expect(() => {
      getScopesOfParsedJsonSchema(circular);
    }).not.toThrow();
  });

  it('should handle deeply nested schemas', () => {
    const deep = { type: 'object', properties: {} };
    let current = deep;
    for (let i = 0; i < 200; i++) {
      current.properties = { nested: { type: 'object', properties: {} } };
      current = current.properties.nested;
    }

    expect(() => {
      getScopesOfParsedJsonSchema(deep);
    }).not.toThrow();
  });

  it('should handle self-referencing $ref patterns', () => {
    const schema = {
      type: 'object',
      properties: {
        tree: {
          type: 'object',
          properties: {}
        }
      }
    };
    schema.properties.tree.properties.left = schema.properties.tree;
    schema.properties.tree.properties.right = schema.properties.tree;

    const result = getScopesOfParsedJsonSchema(schema);
    expect(result).toBeDefined();
  });
});
```

## 📋 Acceptance Criteria

- [ ] Function detects circular references using WeakSet
- [ ] Function has maximum depth limit (default: 100)
- [ ] No stack overflow on circular schemas
- [ ] Logs warning when circular reference detected
- [ ] All existing tests pass
- [ ] New tests for circular references added
- [ ] Performance impact is minimal (<5% slower)

## 🔗 Related Files

- `lib/getScopesOfParsedJsonSchema.ts` (main fix)
- `components/JsonEditor.tsx:387` (usage)
- Tests to be added in `lib/__tests__/getScopesOfParsedJsonSchema.test.ts`

## 💻 Environment

- **Affected versions:** All versions
- **Browser:** All (Chrome, Firefox, Safari)
- **Node.js:** All versions

## 🏷️ Labels

`🐛 Bug`, `Status: Triage`, `Priority: High`, `Security`

## ⏱️ Estimated Fix Time

4-6 hours (implementation + tests)

---

**Are you working on this?**
- [ ] Yes
- [ ] No
