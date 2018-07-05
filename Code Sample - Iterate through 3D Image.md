## How do I iterate through the raw voxel data of a 3D image?

Below is a simple Eclipse v11/v13 script code snippet that starts with the active 3D image dataset and iterates through the raw voxel data.

{code:c#}
public void Execute(ScriptContext context)
{
  Image image = context.Image;
  if (image == null)
  {
    MessageBox.Show("Please load a 3D image.", "VarianDeveloper");
    return;
  }

  int[,](,) voxelPlane = new int[image.XSize, image.YSize](image.XSize,-image.YSize);

  for (int z = 0; z < image.ZSize; z++)
  {
    image.GetVoxels(z, voxelPlane);
    for (int y = 0; y < image.YSize; y++)
    {
      for (int x = 0; x < image.XSize; x++)
      {
        int imageValue = voxelPlane[x, y](x,-y);
      }
    }
  }
}
{code:c#}