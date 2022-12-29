# 3d-formats

## USD

### Omniverse

Multiple UV Sets: no  
Texture Transform: no  
ClipSets (multiple animations): no  
Transparency: partial  

### QuickLook

Multiple UV Sets: yes  
Texture Transform: broken / inverted  
ClipSets (multiple animations): no  
Transparency: specific USD version  

### usdview

Multiple UV Sets: yes  
Texture Transform: yes  
Transparency: not fully supported    

## Textures

### UV Coordinate System

Starts top left or bottom left  

### UV Range 0..1 or more

UV is a per-vertex attribute.  
It is usually used for texturing - defining which parts of a texture end up on which part of the model.  
There's a distinction between non-overlapping UVs (for example for lightmaps or when painting on a model) and overlapping UVs (multiple parts of the model get the same section of the texture applied, for example with trim sheets).  

UV ranges outside 0..1 are sometimes used:
- to distinguish between materials (also called UDIMs) - e.g. range 0..1 is material A, range 1..2 is material B
- for tiling textures - the texture repeats over the surface of the model
- for texture arrays - 0..1 is index 0, 1..2 is index 1, can be used both in X and Y

### Relationship to texture wrap modes 

Wrap Modes specify what happens with a texture when accessing data outside its normalized 0..1 range. 
Typically, formats support at least Clamp and Repeat. Sometimes, there's also Mirror. Often wrap modes can be specified separately for U and V directions.  

Wrap modes are especially important to get right for textures with mimaps where opposite edges look different. A good example is a grass texture — typically it will be transparent at the top and opaque at the bottom, and repeating in X. So it would have wrap modes U: Repeat and V: Clamp. 

## Lights and Shadows

### Realtime approximations vs. Path tracing

### Lights

Default orientation   
Light values / real-world or not  
Falloff  
Inner and outer radius  
Punctual light sources: Spot, Directional, Point  
Image-Based Lighting  
Ambient Lights  
Area, Tube, Spherical  
IES profiles  

### Shadows

Realtime (usually shadow maps)  
Baked (precalculated)  
Hard vs. Soft shadows  
Very soft shadows  
Shadow cascades  

## Cameras

Default orientation  
Default look direction - may be different between viewers, not just file formats  

## Skinning

### Bone Orientation

### Number of skin weights per bone

### Number of Blend Shape weights taken into account

### Multiple skins per file

### FBX 

Blender adds an empty node called "Armature" on export.  
This is problematic when a FBX file has been created e.g. in Maya (exports without such a root node).  
Blender has options what to do with the root (Null, Root, LimbNode) but neither of those skips the object.  

Unreal removes "Armature" root nodes by default to counter Blender behaviour.  
Unity does not remove "Armature" root nodes and doesn't have an option for it.   

Blender Docs: https://docs.blender.org/manual/en/3.3/addons/import_export/scene_fbx.html
> FBX bones seems to be -X aligned, Blender’s are Y aligned  

### Skinned glTF Import in Blender

Need to set bone orientation manually, not use the default  

## Animations

### Terminology

- FBX: AnimStacks + AnimLayer + AnimCurveNode (AnimLayer rarely used / usually only one)  
- Blender: NLA strips + actions + F-Curves   
- glTF: animations + channels  
- USD: [valueClips/clipSets](https://graphics.pixar.com/usd/release/api/_usd__page__value_clips.html) + timeSamples on attributes (without clip sets, only one animation is supported)  
- Unity: AnimationClip + AnimationCurve (Animators can blend between multiple AnimationClips)  
- Unreal: AnimSequence + AnimCurves  
- three.js: AnimationClip + KeyframeTrack (Mixers can blend between multiple AnimationClips)  

### Interpolation Types  

- Unity: per keyframe – constant, linear, freeform (configured via in/out tangents)  
- glTF: per channel – constant, linear, cubic  
- three.js: per AnimationTrack – Discrete (constant), Linear, Smooth (Cubic), can create custom interpolators    
- Blender: per keyframe – constant, linear, bézier (configured via in/out tangents)  
- USD: per attribute – held (constant) or linear  

### Sequencing of animations

Arranging animations on a timeline  
Blending/Mixing of animations 
Animation layers 

## Hard Workflows

- import skinned + animated glTF file into Blender, make changes, export again
  - need to be careful with bone orientations, Blender likes flipping into its own format but doesn't allow flipping back
  - working in Blender is a bit annoying as bones look "wrong"

- import skinned + animated FBX file into Blender, make changes, export again
  - hierarchy will change if original FBX wasn't created in Blender
  - impossible to use in external tools since that extra hierarchy node breaks existing animation clips in other softwares (e.g. Unity)
  - Unreal [seems to have fixed this](https://krisredbeard.wordpress.com/tutorials/tutorial-prevent-blender-fbx-exporter-adding-extra-root-bone/) on an import level (by skipping explicitly the "Armature" node if it exists)
  - people have went as far as [making modifications to the default exporter in custom ones](https://github.com/A-Ribeiro/CustomBlenderFBXExporter) to allow skipping this (not tested)

- bake lightmaps in Blender that take Filmic into account and export to other softwares (bake the look transform in)  
  - image editor has an option to bake LUT into textures  
