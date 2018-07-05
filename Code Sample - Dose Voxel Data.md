## How do I access the dose voxel grid for a certain structure?

Below is Eclipse v11 script code that implements a mean dose calculation method for a given structure.

{code:c#}
    public DoseValue CalculateMeanDose(PlanSetup plan, Structure structure)
    {
      Dose dose = plan.Dose;
      if (dose == null)
        return new DoseValue(Double.NaN, DoseValue.DoseUnit.Unknown);

      plan.DoseValuePresentation = DoseValuePresentation.Absolute;

      double sum = 0.0;
      int count = 0;

      double xres = 2.5;
      double yres = 2.5;
      double zres = 2.5;

      int xcount = (int)((dose.XRes * dose.XSize) / xres);
      System.Collections.BitArray segmentStride = new System.Collections.BitArray(xcount);
      double[]() doseArray = new double[xcount](xcount);
      DoseValue.DoseUnit doseUnit = dose.DoseMax3D.Unit;

      for (double z = 0; z < dose.ZSize * dose.ZRes; z += zres)
      {
        for (double y = 0; y < dose.YSize * dose.YRes; y += yres)
        {
          // sum of dose values inside of structure to the power of 'a' etc. if a = 1 then its the mean
          VVector start = dose.Origin +
                          dose.YDirection * y +
                          dose.ZDirection * z;
          VVector end = start + dose.XDirection * dose.XRes * dose.XSize;

          SegmentProfile segmentProfile = structure.GetSegmentProfile(start, end, segmentStride);
          DoseProfile doseProfile = null;

          for (int i = 0; i < segmentProfile.Count; i++)
          {
            if (segmentStride[i](i))
            {
              if (doseProfile == null)
              {
                doseProfile = dose.GetDoseProfile(start, end, doseArray);
              }

              double doseValue = doseProfile[i](i).Value;
              if (!Double.IsNaN(doseValue))
              {
                sum += doseProfile[i](i).Value;
                count++;
              }
            }
          }
          doseProfile = null;
        }
      }
      double mean = sum / ((double)count);
      return new DoseValue(mean, doseUnit);
    }
{code:c#}