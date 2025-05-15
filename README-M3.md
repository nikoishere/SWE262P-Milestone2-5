# SWE262P - Milestone 3

This milestone enhances the original `org.json.XML` library with a new overloaded version of the `toJSONObject()` method. This new method allows **key transformation during XML parsing**, giving client code the ability to inject a transformation function that dynamically alters XML tag and attribute names into customized JSON keys during the parsing process itself.

**Author:**

Yifei Wang ([yifew59@uci.edu](mailto:yifew59@uci.edu))

Cherine Cho ([cherinec@uci.edu](mailto:cherinec@uci.edu))

---

## **How to Run**

This milestone builds upon the release tagged [20250107](https://github.com/stleary/JSON-java/releases/tag/20250107) of the [stleary/JSON-java](https://github.com/stleary/JSON-java) repository.

Make sure your environment includes:

- **Java 8 or higher**
- **Maven 3.9+**

You can compile and test the project using:

```bash
mvn clean
mvn compile
mvn test
```

You should see something similar to:

```
Tests run: 676, Failures: 0, Errors: 0, Skipped: 2
```

To only run tests related to Milestone 3, use:

```bash
mvn test -Dtest="org.json.junit.XMLTest"
```

---

## Implementation (starting from line 1203 in XML.java)

This milestone introduces the following new static method in the `org.json.XML` class:

```java
public static JSONObject toJSONObject(Reader reader, Function<String, String> keyTransformer)
```

### **Purpose**

This method allows dynamic key transformation by applying a user-defined function to **every tag name** and **attribute name** during the XML parsing process. This enables use cases such as:

- Prefixing all keys (e.g., `"name"` → `"swe262_name"`)
- Reversing key names (e.g., `"foo"` → `"oof"`)
- Uppercasing keys
- Any arbitrary transformation logic defined by the client

### **How it Works**

- A functional interface `Function<String, String>` is passed in by the client.
- This transformer is applied **during parsing**:
  - On every element tag name
  - On every attribute name
- The transformed key is then used in the resulting `JSONObject` structure.

This avoids the need for a second pass over the JSON object after parsing, unlike the approach in Milestone 1.

---

## Performance Commentary

Compared to performing key transformation **after** the XML is converted to a `JSONObject` (as done in Milestone 1), doing this **inside** the library during parsing has notable advantages:

- **Memory efficiency**: We avoid building and then copying/translating the entire `JSONObject` tree.
- **CPU savings**: String transformations are performed once during streaming parse, rather than in a separate traversal.
- **Scalability**: For large XML files, in-place transformation reduces overhead significantly.

However, this tight coupling also means:
- Transformation logic cannot be changed once parsing begins.
- More responsibility is pushed into the library layer, requiring careful validation and testing.

---

## Test Cases (starting from line 1522 in XMLTest.java)

The test cases for Milestone 3 are located in `src/test/java/org/json/junit/XMLTest.java` and verify key transformation behavior for various scenarios.

### **Test 1: testKeyPrefixTransformation**

Tests prefixing all XML tags/attributes with `"swe262_"`.

```xml
<foo><bar>value</bar></foo>
→ {"swe262_foo": {"swe262_bar": {"content": "value"}}}
```

---

### **Test 2: testKeyReverseTransformation**

Tests reversing all keys.

```xml
<abc><xyz>1</xyz></abc>
→ {"cba": {"zyx": {"content": 1}}}
```

---

### **Test 3: testSimpleKeyPrefixTransform**

Checks nested tags with simple prefix transformation.

```xml
<person><name>Tom</name><age>25</age></person>
→ {"swe262_person": {"swe262_name": "Tom", "swe262_age": 25}}
```

---

### **Test 4: testIdentityTransform**

Ensures behavior matches original `toJSONObject(reader)` when using identity function.

---

### **Test 5: testNestedTransform**

Tests transformation of deeply nested XML with both uppercasing and prefixing.

```xml
<catalog><book><title>Clean Code</title></book></catalog>
→ {"X_CATALOG": {"X_BOOK": {"X_TITLE": "Clean Code"}}}
```

---

## Summary

This milestone provides a flexible, extensible, and efficient way to apply key-level transformations to XML data during JSON conversion. It leverages Java's functional programming features for customization while optimizing performance by working inline during parsing.