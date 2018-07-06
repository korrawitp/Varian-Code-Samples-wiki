## How do I access planned control points and leaf positions?

Below is a simple Eclipse v11 script code snippet that starts with the active patient and iterates through the course, plans, beams, control points and leaf positions.  One could also start this at the active plan by starting from 'context.PlanSetup', or the list of loaded plans by ‘context.PlansInScope’.

```csharp
    public void Execute(ScriptContext context /**, System.Windows.Window window**/)
    {
      if (context.Patient == null)
        MessageBox.Show("Please load a patient!");

      int numCourse, numPlans, numBeams, numControlPts, numLeaves;
      numCourse = numPlans = numBeams = numControlPts = numLeaves = 0;

      // Iterate through all patient courses...
      foreach (var course in context.Patient.Courses)
      {
        numCourse++;
        // ... and plans
        foreach (var plan in course.PlanSetups)
        {
          numPlans++;
          // ... and beams
          foreach (var beam in plan.Beams)
          {
            numBeams++;
            foreach (var controlPoint in beam.ControlPoints)
            {
              numControlPts++;
              // Get the 2 dimensional array of leaf positions. Each bank (carriage)
              // of leaves has an array of leaf positions. There are 2 banks 
              // of opposing leaves.
              float[,](,) lp = controlPoint.LeafPositions;
              for (int bankIndex = 0; bankIndex <= lp.GetUpperBound(0); bankIndex++)
              {
                for (int leafIndex = 0; leafIndex <= lp.GetUpperBound(1); leafIndex++)
                {
                  numLeaves++;
                  float leafPosition = lp[bankIndex, leafIndex](bankIndex,-leafIndex);
                  // TODO: do something with the leaf position
                }
              }
            }
          }
        }
      }
      MessageBox.Show(
        string.Format("Patient {0} has {1} courses, {2} plans, {3} beams," +
                      "{4} control points, and {5} leaf positions.",
           context.Patient.Name.ToString(), numCourse, numPlans, numBeams, 
            numControlPts, numLeaves),
         "VarianDeveloper"); 
    }
```
