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

### **Test 2: testAsyncParsingSuccess**

- Verifies that the `toJSONObjectAsync` method correctly invokes the success callback when provided with valid XML input.

  **Input XML:**
  ```xml
  <book>
    <title>Async Programming</title>
  </book>
  ```

  **Expected Output:**
  ```json
  {
    "book": {
      "title": {
        "content": "Async Programming"
      }
    }
  }
  ```

  **Key Points:**
  - Ensures the success callback is triggered within the timeout period.
  - Confirms the parsed `JSONObject` matches the expected structure.

---

### **Test 3: testAsyncParsingFailure**

- Verifies that the `toJSONObjectAsync` method correctly invokes the error callback when provided with invalid XML input.

  **Input XML:**
  ```xml
  <book><title>Oops</book>
  ```

  **Expected Behavior:**
  - The error callback is triggered with an exception indicating the XML parsing failure.

  **Key Points:**
  - Ensures the error callback is invoked within the timeout period.
  - Confirms the exception is properly caught and handled.

---

### **Test 4: testAsyncLargeXMLSuccess**

- Tests the ability of the `toJSONObjectAsync` method to handle large XML inputs efficiently and correctly invoke the success callback.

  **Input XML:**
  ```xml
  <library>
    <book><title>Title 0</title></book>
    <book><title>Title 1</title></book>
    ...
    <book><title>Title 499</title></book>
  </library>
  ```

  **Expected Output:**
  ```json
  {
    "library": {
      "book": [
        { "title": { "content": "Title 0" } },
        { "title": { "content": "Title 1" } },
        ...
        { "title": { "content": "Title 499" } }
      ]
    }
  }
  ```

  **Key Points:**
  - Confirms the success callback is triggered within the timeout period.
  - Validates the parsed `JSONObject` contains all 500 `<book>` entries.
  - Demonstrates the method's ability to handle large-scale XML parsing without errors.

---

These additions provide a comprehensive explanation of the test cases you implemented for Milestone 5. You can copy and paste this content into the **Test Cases** section of your `README-M5.md` file.### **Test 2: testAsyncParsingSuccess**

- Verifies that the `toJSONObjectAsync` method correctly invokes the success callback when provided with valid XML input.

  **Input XML:**
  ```xml
  <book>
    <title>Async Programming</title>
  </book>
  ```

  **Expected Output:**
  ```json
  {
    "book": {
      "title": {
        "content": "Async Programming"
      }
    }
  }
  ```

  **Key Points:**
  - Ensures the success callback is triggered within the timeout period.
  - Confirms the parsed `JSONObject` matches the expected structure.

---

### **Test 3: testAsyncParsingFailure**

- Verifies that the `toJSONObjectAsync` method correctly invokes the error callback when provided with invalid XML input.

  **Input XML:**
  ```xml
  <book><title>Oops</book>
  ```

  **Expected Behavior:**
  - The error callback is triggered with an exception indicating the XML parsing failure.

  **Key Points:**
  - Ensures the error callback is invoked within the timeout period.
  - Confirms the exception is properly caught and handled.

---

### **Test 4: testAsyncLargeXMLSuccess**

- Tests the ability of the `toJSONObjectAsync` method to handle large XML inputs efficiently and correctly invoke the success callback.

  **Input XML:**
  ```xml
  <library>
    <book><title>Title 0</title></book>
    <book><title>Title 1</title></book>
    ...
    <book><title>Title 499</title></book>
  </library>
  ```

  **Expected Output:**
  ```json
  {
    "library": {
      "book": [
        { "title": { "content": "Title 0" } },
        { "title": { "content": "Title 1" } },
        ...
        { "title": { "content": "Title 499" } }
      ]
    }
  }
  ```

  **Key Points:**
  - Confirms the success callback is triggered within the timeout period.
  - Validates the parsed `JSONObject` contains all 500 `<book>` entries.
  - Demonstrates the method's ability to handle large-scale XML parsing without errors.

---

These additions provide a comprehensive explanation of the test cases you implemented for Milestone 5. You can copy and paste this content into the **Test Cases** section of your `README-M5.md` file.
  
  
