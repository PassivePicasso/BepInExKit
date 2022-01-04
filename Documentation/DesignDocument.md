# BepInEx Kit Design Doc

### Introduction

BepInEx Kit is an extension for ThunderKit that simplifies the process of creating BepInEx Plugins for Unity Games

### Features
* Provides a Template Manifest as well as Pipelines and PathReferences for automating BepInEx Plugin deployment and testing.
* Provides Wizard for installing BepInEx into the project
* Provides Wizard for creating a new BepInEx Mod Project
* ManifestDatum to automate managing Mod Version
***
### BepInEx Wizard 
1. Verify that a Game has been configured with ThunderKit
	* Prompt user to Configure Game if they have not done so
2. Checks if Game has an established Thunderstore Community and checks if there is a BepInEx package on that store
	* If found, Prompt user to see if they want to use the store version or if they would like to use a new configuration. 
3. Checks for BepInEx in project, prompt to overwrite if found
4. Runs checks on game
	* Detect stripped assemblies
	* Detect IL2CPP
5. Present BepInEx version options for selection
6. Download selected BepInEx version
7. Unpack zip into Packages/BepInExPack/BepInExPack folder
8. Reconfigure unpacked BepInEx
	1. Add unstripped assemblies from current Editor version if stripped assemblies detected
		* This can possibly cause problems, we may need to determine alternative methods of handling this.
	2. Update doorstop_config.ini with unstripped assembly flag
	3. Create Files in Packages/BepInExPack
		* README.md
		* manifest.json
		* package.json
		* icon.png
		* (ThunderKit Manifest) BepInEx.asset
			* Copy of Default-BepInEx
			* Populate appropriate fields to build out package for publishing
***
### New Mod Wizard 
1. Checks for BepInEx in project, Launches BepInEx Wizard if not found
2. Allows entry of a name for a mod
3. Creates in order
	1. Folder: Assets/\<ModName>/
	2. Folder: Assets/\<ModName>/Source 
	3. AssemblyDefinition: Assets/\<ModName>/Source/\<ModName>.asmdef
	4. Mod Template Class: Assets/\<ModName>/Source/\<ModName>.cs
	5. Markdown: Assets/\<ModName>/README.md
	6. Icon:  Assets/\<ModName>/Icon.PNG  (blank white 256x256 png?)
	7. Manifest: Assets/\<ModName>/\<ModName>.asset
		* Set Manifest.identity.name = \<ModName>
		* Add StageAssemblies ManfiestDatum and populate with
			* Assets/\<ModName>/Source/\<ModName>.asmdef
		* Populate Manifest with created files
		* Add dependency to bepinex 
***
### BepInEx Version Mapper Datum
* This ManifestDatum provides a method to centrally manage the Mod version.  
	* When this Datum's value is changed, it will trigger the following actions
		* Automatically update ManifestIdentity.version
		* Generate an ModVersion.cs file populated with the following snippet substituting \<ModName> and \<ModVersion> with appropriate values
```
namespace <ModName> { 
	public static class ModVersion {
		public const string Version = "<ModVersion>";
	}
}
```
***
### Limitations
* Does not currently support IL2CPP as IL2CPP features have not been built out in ThunderKit