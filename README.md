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

## Skinning

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

- FBX: AnimStacks + AnimLayer + AnimCurveNode (AnimLayer rarely used / usually only one)  
- Blender: NLA strips + actions  
- glTF: animations + channels  
- USD: clipSets + timeSamples (without clip sets, only one animation is supported)  
- Unity: AnimationClip + AnimationCurve (Animators can blend between multiple AnimationClips)  
- Unreal: AnimSequence + AnimCurves  
- three.js: AnimationClip + KeyframeTrack (Mixers can blend between multiple AnimationClips)  

### Interpolation Types
- Unity: per keyframe
- glTF: per 
- Blender: 
