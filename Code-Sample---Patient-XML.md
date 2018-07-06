## How do I get the full XML description of a patient in the Eclipse Scripting API?

Below is a Eclipse v11 script code sample that extracts the full ESAPI XML description for the active patient and saves it to the windows temp directory.  The generated file could be large (a few MBs) and may take a minute or two to create.

```csharp
public void Execute(ScriptContext context)
{
  XmlWriterSettings settings = new XmlWriterSettings();
  settings.Indent = true;
  settings.IndentChars = ("\t");
  System.IO.MemoryStream mStream = new System.IO.MemoryStream();
  using (XmlWriter writer = XmlWriter.Create(mStream, settings))
  {
    writer.WriteStartDocument(true);
    writer.WriteStartElement("Patient");
    context.Patient.WriteXml(writer);       // add the Patient XML to the writer stream
    writer.WriteEndElement(); // </Patient>
    writer.WriteEndDocument();

    // done writing to the memory stream
    writer.Flush();
    mStream.Flush();

    // create the XML file.
    string temp = System.Environment.GetEnvironmentVariable("TEMP");
    string sXMLPath = string.Format("{0}\\{1}({2})-patient.xml", temp, context.Patient.LastName, context.Patient.Id);
    using (System.IO.FileStream file = new System.IO.FileStream(sXMLPath, System.IO.FileMode.Create, System.IO.FileAccess.Write))
    {
      // Have to rewind the MemoryStream in order to read its contents. 
      mStream.Position = 0;
      mStream.CopyTo(file);
      file.Flush();
      file.Close();
    }
    // 'Start' generated XML file to launch browser window
    System.Diagnostics.Process.Start(sXMLPath);
    // Sleep for a few seconds to let internet browser window to start
    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(3));
  }
}
```
