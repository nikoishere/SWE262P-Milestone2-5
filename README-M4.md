# SWE262P - Milestone 4

This milestone extends the `org.json.XML` library with **streaming support** for JSON structures derived from XML. It introduces a new method `toStream()` that enables client code to **traverse and process all nodes of a `JSONObject`** in a functional style using Java Streams.

**Author:**  
Yifei Wang ([yifew59@uci.edu](mailto:yifew59@uci.edu))  
Cherine Cho ([cherinec@uci.edu](mailto:cherinec@uci.edu))

---

## 🚀 How to Run

Milestone 4 builds upon the repository used in previous milestones.

Make sure your environment includes:

- **Java 8 or higher**
- **Maven 3.9+**

To compile and test the project:

```bash
mvn clean
mvn compile
mvn test
```

To run only tests related to `XMLTest`:

```bash
mvn test -Dtest="org.json.junit.XMLTest"
```

---

## 📌 New Methods in `XML.java`

### ✅ 1. `public static Stream<JSONNode> toStream(JSONObject jsonObject)`

This method converts a `JSONObject` into a `Stream<JSONNode>`, where each `JSONNode` represents an element from the tree.

### ✅ 2. `private static Stream<JSONNode> streamFromObject(Object current, String currentPath)`

This recursive helper method traverses a `JSONObject` or `JSONArray` and collects all nodes with their corresponding JSONPointer-like paths.

---

### 🧱 New Class: `JSONNode`

```java
public static class JSONNode {
    private final String path;
    private final Object value;

    public JSONNode(String path, Object value) {
        this.path = path;
        this.value = value;
    }

    public String getPath() { return path; }
    public Object getValue() { return value; }

    @Override
    public String toString() {
        return "JSONNode{path='" + path + "', value=" + value + "}";
    }
}
```

Each `JSONNode` holds:

- `path`: a string representation of the location within the JSON object (e.g., `/catalog/book[0]/title`)
- `value`: the value found at that location, which can be a primitive, `JSONObject`, or `JSONArray`

---

## 💡 Purpose

This functionality enables client code to:

- Traverse deeply nested JSON structures in a flat stream.
- Use Java 8 Stream API to `map`, `filter`, or `collect` elements from the XML-converted `JSONObject`.
- Process and transform data **lazily** and **efficiently**, without needing to recursively write tree traversals.

---

## 🧪 Test Cases

Test cases are added in `XMLTest.java` and ensure the stream includes correct paths and values.  


---

## 📈 Performance Commentary

### ✅ Benefits of `toStream()`:

- **Lazy processing**: Stream operations avoid eagerly building full intermediate data structures.
- **Scalable**: Allows efficient handling of large JSON trees without deep recursion logic in client code.
- **Composable**: Integrates smoothly with Java's functional constructs (`filter`, `map`, `collect`, etc.).
- **Clean code**: Reduces boilerplate by removing the need for manual traversal logic.

### ⚠️ Considerations:

- Still traverses the entire JSON tree at least once internally.
- Currently builds an `ArrayList` before streaming — future optimizations could improve memory usage by generating stream elements directly.

---
