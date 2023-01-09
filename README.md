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

### Spec  
- [Spec and Grammar for USDA](https://github.com/PixarAnimationStudios/USD/pull/2126)  
- [USD Crate format Documentation](https://github.com/PixarAnimationStudios/USD/pull/2086)    

### Links  
- https://github.com/matiascodesal/awesome-usd  
- https://remedy-entertainment.github.io/USDBook/  

## Textures

### UV Coordinate System

Starts top left or bottom left  

### UV Range 0..1 or more

UV is a per-vertex attribute.  
It is usually used for texturing - defining which parts of a texture end up on which part of the model.  
There's a distinction between non-overlapping UVs (for example for lightmaps or when painting on a model) and overlapping UVs (multiple parts of the model get the same section of the texture applied, for example with trim sheets).  

Vertices can store zero, one, or more sets of UV coordinates. Commonly one or two are supported, with some formats supporting 8 or more. A typical case is storing UVs for regular texturing in the first channel, and having a second set of UVs in the second channel for lightmaps or other cases.  

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
Some viewers by default look _at_ the forward-facing side of a model, some viewers look _along_ the forward direction. 
For example, while coordinate systems match between glTF and USD, the default view direction is flipped.  

## Skinning

### Bones and skins

Many models require soft parts and deformations. To achieve this, a technique commonly used is "rigging" (creating bone objects with a hierarchical relationship, also called skeleton) and "skinning" (defining how bones affect the model).  

Bones usually have a bind pose (the orientation they have in relation to vertices) and vertices store bone weights (which bones influence them and how much). Typically, for a deformable model (such as a human hand) each vertex is affected by multiple bones, while for a rigid model (such as a robot) each vertex may only be affected by a single bone, while some wires may be affected by multiple.  

### Bone Orientation

Some softwares are opinionated about which axis a bone must be aligned on (which axis is forward, which axis is up). This often leads to difficulties when exchanging already rigged data between softwares, especially when constraints (kinematic chains) are used.  

### Multiple skins per file

Most file formats allow storing multiple skinned meshes in one file. Support for im- and exporting such data varies and can cause problems. 

### Implementation Details

Usually 1, 2, 4, 8 or more bones can influence a single vertex (has a performance impact). Sometimes this is configurable (for example in Unity).  
Since skinned meshes are usually driven by bones, their own transformation doesn't / shouldn't matter. Despite this, transforming the skinned mesh itself can lead to culling errors or lighting problems. 
Some formats enforce bones to all be hierarchical children of the skinned mesh, others allow arbitrary hierarchies. 

## Blend Shapes

In some cases, deformations are too complex to represent them with bones. One example of such complex deformations are animated faces.  
To represent them, additional data can be stored per vertex that defines additional vertex positions. These are called blend shapes, shape keys, morph targets or similar.  

Blend shapes can usually store additional copies of existing per-vertex data, such as positions, normals, or tangents. Some formats also allow storing color data or UV data as blend shapes. 
Usually, data is stored additively — that means only the difference in data to the base mesh is stored. Often, only a few vertices differ between shapes (for example, a small wrinkle in a face), so only saving the difference reduces storage. This is called _sparse_ data in some formats.  

Some formats (e.g. glTF) define that viewers must recalculate normal and tangent data when missing, which means often only vertices need to be stored.  

Blend shapes can and often are combined with bone animations. Typical cases include humanoid meshes with rigged heads (neck/skull/yaw/eyes) and shapes for facial expression. 

### Common systems 

FACS
Apple
Google

### Target count limitations

Viewers support a varying amount of blend shapes. Some only support 4 or 8, which is usually not sufficient for complex facial animation. Many viewers support a large number (hundreds) of blend shapes.  

### Implementation Details

Blend shapes are efficient to calculate on the GPU but rather expensive on the CPU, so some operations are slow. This includes 
culling / bounding box calculations, or finding intersections with meshes with blend shapes. 
Usually, blend shapes are defined and used with normalized weights 0..1, but technically weights can be any number (both positive and negative).  
Viewers that support only a limited amount of blend shapes have varying strategies for which targets get used. Some just take the first N shapes, others sort and take the first N shapes applying to each vertex.  

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

# Formats

## FBX 

Blender adds an empty node called "Armature" on export.  
This is problematic when a FBX file has been created e.g. in Maya (exports without such a root node).  
Blender has options what to do with the root (Null, Root, LimbNode) but neither of those skips the object.  

Unreal removes "Armature" root nodes by default to counter Blender behaviour.  
Unity does not remove "Armature" root nodes and doesn't have an option for it.   

Blender Docs: https://docs.blender.org/manual/en/3.3/addons/import_export/scene_fbx.html
> FBX bones seems to be -X aligned, Blender’s are Y aligned    

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
