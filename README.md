# diffler
A fast property comparer of instances using compiled expression trees on the fly.

If you need to collect the different property values of instances of the same kind,
you may find this tool efficient and useful, because:
- it is not using reflection
- it executes the comparision upon the precompiled expression tree for the given type, thus making it much faster than using reflection
- supports comparing complex types (by default equality comparer)
- supports comparing arrays
- supports ignoring properties from comparision

#### Solution
A comparer algorithm is compiled and cached for every type, which are generated by using [expression trees](https://msdn.microsoft.com/en-us/library/bb397951.aspx). The result of the comparision is contained in a `Dictionary<string,object>` instance, which holds the different property name and value pairs. The C# source codes are contained in a Visual Studio 2015 solution, the tests are using Moq and MSPEC.

#### Usage

##### Comparing two instances of T
```CSharp
using rblt.Tools;

/// t1 and t2 are instances of T
T t1 = ...
T t2 = ...

/// getting the different properties and their values:
var delta = Diff.Them(t1, t2);

/// which is the same as using the extension method:
var delta = t1.Versus(t2);
```

##### Ignoring properties
You can ignore arbitrary property from comparision by marking them with the `DiffIgnore` property attribute. See the example below.

#### Example

```CSharp
using rblt.Tools;

public class TestClass
{
    public string PropString { get; set; }
    
    [DiffIgnore]
    public object PropObject { get; set; }
    
    public int[] PropArray { get; set; }
}

public static void Main(string[] args)
{
    var t1 = new TestClass()
    {
        PropString = "string1",
        PropObject = new object(),
        PropArray = new int[] { 1, 2, 3 }
    };
    
    var t2 = new TestClass()
    {
        PropString = "string2",
        PropObject = new object(),
        PropArray = new int[] { 1, 2, 3 }
    };
    
    IDictionary<string, object> delta = t1.Versus(t2);
    
    /*
    
      The resulting dictionary contains the
      different propertyname/value pairs of "t2".
      
      delta = {
        { "PropString", "string2" }
      };
      
    */
}
```

#### Remarks
The comparision is not recursive, so child properties of complex properties are not targeted for comparision.

#### Todos
The comparision could be made recursive for child properties.
