# SWE262P - Milestone 5

This milestone enhances the `org.json.XML` library with **asynchronous support** for XML-to-JSON conversion. It introduces a new method, `toJSONObjectAsync(...)`, that allows client code to **parse large XML documents in a non-blocking way** by providing success and failure callbacks.

**Author:**  
Yifei Wang ([yifew59@uci.edu](mailto:yifew59@uci.edu))  
Cherine Cho ([cherinec@uci.edu](mailto:cherinec@uci.edu))

---

## How to Run

Milestone 5 builds upon the repository used in previous milestones.

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

## Implementation (starting from line 1340 in XML.java)

````
public static void toJSONObjectAsync(Reader reader, Function<String, String> keyTransformer, Consumer<JSONObject> onSuccess, Consumer<Exception> onFailure)
````

This method performs asynchronous parsing of XML into a JSONObject, allowing the caller to define:

- A Function<String, String> to transform XML tag names.
- An onSuccess callback that receives the parsed result.
- An onFailure callback that handles parsing errors.

---

## Test Cases (starting from line 1724 in XMLTest.java)

Unit tests for the `XML.toJSONObjectAsync` functionality are implemented in `XMLTest.java`. This method enables non-blocking XML-to-JSON parsing, with custom key transformations and callback-based control flow.

### **Test 1: testAsyncBookListXml**

- Parses a nested `<library>` structure containing multiple `<book>` entries. Each book has `<title>` and `<author>` fields.

  Verifies:
  
  - Key transformation using key -> key.toLowerCase()
  - Correct handling of arrays (multiple `<book>` elements)
  - Proper invocation of the success callback
  
  ```
  <library>
    <book>
      <title>Effective Java</title>
      <author>Joshua Bloch</author>
    </book>
    <book>
      <title>Clean Code</title>
      <author>Robert C. Martin</author>
    </book>
  </library>
  
  → {
    "library": {
      "book": [
        { "title": { "content": "Effective Java" }, "author": { "content": "Joshua Bloch" } },
        { "title": { "content": "Clean Code" }, "author": { "content": "Robert C. Martin" } }
      ]
    }
  }
  ```
  
  
