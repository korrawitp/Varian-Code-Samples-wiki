## How do I iterate through contours of a structure?

Below is a simple Eclipse v11 script code snippet that starts with the active structure set and iterates through all structures, and all contours of each structure.  This script is looking for CT slices that have multiple contours present per slice.

```csharp
public void Execute(ScriptContext context /**, System.Windows.Window window**/)
{
    // make sure context.StructureSet && context.StructureSet.Image are not null
    if (context.StructureSet == null || context.StructureSet.Image == null)
    {
        MessageBox.Show("This script requires a structure set and 3D image be loaded!");
        return;
    }
    StructureSet ss = context.StructureSet;
    int slicesInImage = ss.Image.ZSize;
    int counter = 0;

    // Loop through all contours on each image plane for each structure.  
    // An image plane may correspond to a physical slice, or may be interpolated 
    // between slices, depending on slice thickness and spacing of the image.
    foreach (Structure structure in ss.Structures)
    {
        for (int z = 0; z < slicesInImage; z++)
        {
            VVector[]()()[]()() contoursOnPlane = structure.GetContoursOnImagePlane(z);
            if (contoursOnPlane.GetLength(0) > 1) counter++;
        }
    }
    MessageBox.Show("This script found "+counter.ToString()+
        " image planes that contain multiple contours for a structure.",
        "VarianDeveloper");
}
```
