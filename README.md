# YaraSharp
C# wrapper around the Yara pattern matching library.

## Usage
```C#
//  Declare external variables (could be null)
Dictionary<string, object> Externals = new Dictionary<string, object>()
{
    { "filename", string.Empty },
    { "filepath", string.Empty },
    { "extension", string.Empty }
};

//  Context is where yara is initialized
//  From yr_initialize() to yr_finalize()
using (YaraSharp.CContext YSContext = new YaraSharp.CContext())
{

  //  Compiling rules from files
  using (YaraSharp.CCompiler YSCompiler = new YaraSharp.CCompiler(Externals))
  {
  
    //  Errors occured during rule compilation: ignored_file : List<reasons>
    Dictionary<string, List<string>> Errors = new Dictionary<string, List<string>>();
    using (YaraSharp.CRules YSRules = YSInstance.CompileFromFiles(YSCompiler, RuleFilenames, Externals, out Errors))
    {
    
        //  Some file to test yara rules
        string Filename = "<some_file>";

        //  Get matches
        List<YaraSharp.CMatches> Matches = YSInstance.ScanFile(Filename, YSRules, 
              new Dictionary<string, object>()
              {
                  { "filename", System.IO.Path.GetFileName(Filename) },
                  { "filepath", System.IO.Path.GetFullPath(Filename) },
                  { "extension", System.IO.Path.GetExtension(Filename) }
              }, 
              0);

        //  Iterate over matches
        foreach (YaraSharp.CMatches Match in Matches)
        {
            //...
        }
    }
    //  Log errors
  }
}
```
For async scanning use **must** call dispose methods:
```C#
YaraSharp.CContext YSContext = new YaraSharp.CContext();
YaraSharp.CRules YSRules = YSInstance.CompileFromFiles(YSCompiler, RuleFilenames, Externals, out Errors);

//  Async here

YSRules.Dispose();
YSContext.Dispose();
```
## Reference
[Libyara C API documentation](http://yara.readthedocs.io/en/v3.7.0/capi.html) for a general overview on how to use libyara. 

## Features and limitations

* Metadata supported
* Externals supported
* Async scanning supported
* Modules not supported

## Note
Soultion contains 2 projects:
- yara-master - where you *can* update yara sources for a new version
- YaraSharp - where you *can* modify sources in order to add / repair wrapper features