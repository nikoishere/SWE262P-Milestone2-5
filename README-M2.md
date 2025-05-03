# SWE262P-Milestone 2

This milestone enhances the original `org.json.XML` library with two new overloads of the `toJSONObject()` method. These methods allow users to **extract** or **replace** sub-objects from an XML stream using a specified JSONPointer path—without parsing the entire XML document unnecessarily.

**Author:**

Yifei Wang ([yifew59@uci.edu](mailto:yifew59@uci.edu))

Cherine Cho ([cherinec@uci.edu](mailto:cherinec@uci.edu))

------



## **How to Run**

Since the official [stleary/JSON-java](https://github.com/stleary/JSON-java) repository is frequently updated, for this milestone, the reference version is the release tagged [20250107](https://github.com/stleary/JSON-java/releases/tag/20250107).



We strongly recommend using **Java 8 or higher** and **Maven 3.9+** to build and run the project. Make sure the following commands run successfully from the root of your project:

```
mvn clean
mvn compile
mvn test
```

With this environment and the specified checkpoint, the project should compile and pass all baseline tests successfully:

```
Tests run: 676, Failures: 0, Errors: 0, Skipped: 2
```

If you only want to run a specific test or Milestone 2, run this command:

```
mvn test -Dtest="org.json.junit.XMLTest"
```

------



## Implementation (starting from line 1042 in XML.java)

This milestone introduces two new overloaded static methods in the org.json.XML class, implemented starting from **line 1042** in `src/main/java/org/json/XML.java`. 



### **Method 1: Extracting a Sub-object by Path**

```
public static JSONObject toJSONObject(Reader reader, JSONPointer path)
```



**Purpose**

This method is designed to efficiently extract a specific sub-object from an XML stream using a JSONPointer, without needing to parse the entire XML document. 



**How it works**

The input JSONPointer is first split into its individual tag components to represent the hierarchical path. The method then uses a XMLTokener to traverse the XML content token by token. As it encounters each tag, it checks whether it matches the expected structure of the path. When the target depth is reached, matching tags and their contents are collected into a list of XML fragments. The parser stops immediately once the entire sub-object has been captured. Finally, the collected XML fragment is reassembled and converted into a JSONObject using the existing XML.toJSONObject(String) method.



### **Method 2: Replacing a Sub-object by Path**

```
public static JSONObject toJSONObject(Reader reader, JSONPointer path, JSONObject replacement)
```



**Purpose**

This method allows replacing a specific sub-object in an XML document with a user-provided JSONObject, using a given JSONPointer path.



**How it works**

Similar to the extraction method, this function parses the XML stream token by token and collects the opening tags that lead to the target path. Once the target sub-object is found, it is replaced by converting the provided JSONObject into its XML form. The new XML fragment is then inserted in place of the original one. Afterward, any necessary closing tags are appended in reverse order to properly close the surrounding XML structure. The resulting XML string is finally converted back into a JSONObject using the existing XML.toJSONObject(String) method.



### **In-Place Operations**

In this milestone, both new methods are designed to handle XML more efficiently by working in-place during parsing.



For the extraction method, instead of converting the entire XML into a JSONObject and then navigating with a JSONPointer like we did in Milestone 1, we now scan the XML stream as it’s being read and stop as soon as we find the target sub-object. This avoids a lot of unnecessary work and makes the process faster, especially for large XML files.



The replacement method follows a similar idea. While parsing the XML, we skip over the original sub-object at the given path and directly insert the new content (converted from a JSONObject). This way, we don’t need to build and modify a full JSONObject after parsing. We handle it while reading the XML, which is more efficient.

------



## Test Cases (starting from line 1431 in XMLTest.java)

The test cases for Milestone 2 begin at **line 1431** in `src/test/java/org/json/junit/XMLTest.java.`.



### **Test 1: testExtractJSONObject**

**Purpose**

This test checks `XML.toJSONObject(Reader reader, JSONPointer path)` and verifies the correct extraction of a <book> element from a sample XML string. The JSONPointer navigates to /catalog/book, and the method is expected to return a JSONObject that accurately reflects the structure and content of the <book> element.



### **Test 2: testReplaceJSONObject**

**Purpose**

This test checks `XML.toJSONObject(Reader reader, JSONPointer path, JSONObject replacement)` and whether the replacement logic correctly replaces the entire <book> element with a placeholder JSONObject containing "NA" values for all fields.



### **Test 3: testReplaceJSONObjectWithInvalidPath**

**Purpose**

This test checks `XML.toJSONObject(Reader reader, JSONPointer path, JSONObject replacement)` and handles the case where the given path does not exist in the XML document. In this case, no replacement should occur, and the original structure should remain unchanged.