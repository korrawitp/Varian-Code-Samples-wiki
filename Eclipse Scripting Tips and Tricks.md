**How do I extract DVH data?**
See [Code Sample - Extract DVH Data](Extract-DVH-Data).

**How do I iterate through the image voxel data?**
See [Code Sample - Iterate through 3D Image](Code-Sample---Iterate-through-3D-Image).

**How do I access the dose voxel data for a certain structure?**
See [Code Sample - Dose Voxel Data](Code-Sample---Dose-Voxel-Data).

**How do I iterate through contours of a structure?**
See [Code Sample - Iterate Contours](Code-Snippet---Iterate-Contours).

**How do I access MLC leaf positions in control points?**
See [Code Sample - Access Leaf Positions](Code-Snippet---Access-Leaf-Positions).

**How can I easily access the application context everywhere in my app?**
See [Code Sample - Using a Singleton Pattern With ESAPI](Using-a-Singleton-Pattern-With-ESAPI)

**How do I make asynchronous calls using async and await with the API?**
See [Code Sample - Getting Asynchronous with the Eclipse Scripting API](Getting-Asynchonous-with-the-Eclipse-Scripting-API).

**How do I get the version of Eclipse?**
{code:c#}
string sVersion = System.Reflection.Assembly.GetAssembly
  (typeof(VMS.TPS.Common.Model.API.Application)).GetName().Version.ToString();
{code:c#}

**How do I get XML data for scripting objects?**
See [Code Sample - Generate Full Patient XML](Code-Sample---Patient-XML).
See [Code Sample - Generate Plan XML](Code-Sample---Plan-XML).

.