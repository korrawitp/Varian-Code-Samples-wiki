## How do I get the full XML description of a plan in the Eclipse Scripting API?

Below is a Eclipse v11 script code sample that extracts the full ESAPI XML description for the active plan and saves it to the windows temp directory.  The generated file could be large (a few MBs) and may take a minute or two to create.

This sample has a code extension that generates control point XML and puts it beneath a tag in the plan called "<BeamAndControlPoints>".

{code:c#}
public void Execute(ScriptContext context /**, System.Windows.Window window**/)
{
  XmlWriterSettings settings = new XmlWriterSettings();
  settings.Indent = true;
  settings.IndentChars = ("\t");
  System.IO.MemoryStream mStream = new System.IO.MemoryStream();
  using (XmlWriter writer = XmlWriter.Create(mStream, settings))
  {
    writer.WriteStartDocument(true);
    writer.WriteStartElement("PlanSetup");
    WritePlanXML(context.PlanSetup, 
      CtrlPtSelector.IncludeControlPoints, 
      writer);
    writer.WriteEndElement(); // </PlanSetup>
    writer.WriteEndDocument();

    // done writing
    writer.Flush();
    mStream.Flush();

    // write the XML file.
    string temp = System.Environment.GetEnvironmentVariable("TEMP");
    string sXMLPath = string.Format("{0}\\{1}({2})-plan-{3}.xml", temp, 
      context.Patient.LastName, context.Patient.Id, context.PlanSetup.Id);
    using (System.IO.FileStream file = new System.IO.FileStream
      (sXMLPath, System.IO.FileMode.Create, System.IO.FileAccess.Write))
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
protected enum CtrlPtSelector { NoControlPoints = 0, IncludeControlPoints };
protected void WritePlanXML(PlanSetup plan, CtrlPtSelector writeCtrlPts, XmlWriter writer)
{
  plan.WriteXml(writer);
  if (writeCtrlPts == CtrlPtSelector.IncludeControlPoints)
  {
    WriteBeamAndControlPoints(plan, writer);
  }
}
protected void WriteBeamAndControlPoints(PlanSetup plan, XmlWriter writer)
{
  writer.WriteStartElement("BeamAndControlPoints");
  foreach (var beam in plan.Beams)
  {
    writer.WriteStartElement("Beam");
    beam.WriteXml(writer);
    // ... and controlpoints
    writer.WriteStartElement("ControlPoints");
    beam.ControlPoints.WriteXml(writer);
    foreach (var controlPoint in beam.ControlPoints)
    {
      writer.WriteStartElement("ControlPoint");
      controlPoint.WriteXml(writer);
      float[,](,) lp = controlPoint.LeafPositions;
      if (lp.Length > 1)
      {
        writer.WriteStartElement("LeafPositions");
        for (int i = 0; i <= lp.GetUpperBound(0); i++)
        {
          writer.WriteStartElement("Bank");
          writer.WriteAttributeString("number", i.ToString());
          for (int j = 0; j <= lp.GetUpperBound(1); j++)
          {
            float f = lp[i, j](i,-j);
            writer.WriteString(f.ToString() + " ");
          }
          writer.WriteEndElement();//</Bank>
        }
        writer.WriteEndElement();//</LeafPositions>
      }
      writer.WriteEndElement();//</ControlPoint>
    }
    writer.WriteEndElement();//</ControlPoints>
    writer.WriteEndElement(); // </Beam>
  }
  writer.WriteEndElement(); // </BeamAndControlPoints>
}
{code:c#}
