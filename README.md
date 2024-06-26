# Alex's UE5 Style Guide

This is a fork of Michael Allar's excellent [UE4/5 Style Guide](https://github.com/Allar/ue5-style-guide). Since Mr. Allar's activity has been focused on a VR project for the past two years, I decided I would make this fork to clean up, update, and personalize the repo for myself. While you're here, I hope you find value in my fork. :)

## Linter Documentation and Discussion

Information regarding Mr. Allar's Linter can be found on his [ReadTheDocs](https://ue4-style-guide.readthedocs.io/en/latest/) page. His company, Gamemakin LLC, also has a public Discord server (<http://discord.gamemak.in>) with a `#linter` channel.

## Table of Contents

0. [Terminology](#terminology)
    * [Identifier](#terminology--identifier)
    * [Cases](#terminology--cases)
    * [Channel Packing](#terminology--channel-packing)
1. [Identifiers](#identifiers)
    * [Forbidden Characters](#identifiers--forbidden-characters)
2. [Asset Naming Conventions](#asset-naming)
    * [Base Asset Name](#asset-naming--base)
    * [Asset Name Modifiers](#asset-naming--modifiers)
3. [Content Directory Structure](#content-structure)
    * [Folder Names](#content-structure--folder-names)
    * [Use a Top Level Folder for Project Specific Assets](#content-structure--top-level)
    * [Use the Developers Folder for Local Testing](#content-structure--developers-folder)
    * [All Map Files Belong in a Folder Called Maps](#content-structure--maps-folder)
    * [Use a `Core` Folder for Critical Blueprints and Other Assets](#content-structure--core-folder)
    * [Do Not Create Folders Called `Assets`, `Meshes`, Etc.](#content-structure--redundant-folders)
    * [Very Large Asset Sets Get Their Own Folder Layout](#content-structure--large-sets)
    * [`MaterialLibrary`](#content-structure--material-library)
    * [No Empty Folders](#content-structure--no-empty-folders)
4. [C++](#cpp)
    * [Follow the Epic C++ Coding Standard](#cpp--follow-coding-standard)
5. [Blueprints](#blueprints)
    * [Fix Warnings and Errors](#blueprints--fix-warnings-and-errors)
    * [Follow Variable Naming Best Practices](#blueprints--follow-variable-naming)
    * [Mark Configurable Variables as Editable](#blueprints--mark-configurable-as-editable)
    * [Give Editable Variables Tooltips](#blueprints--editable-variables-tooltips)
    * [Use Slider And Value Ranges for Editable Variables When Appropriate](#blueprints--editable-ranges)
    * [Use Variable Categories When Necessary](#blueprints--variable-categories)
    * [Variable Access Level](#blueprints--access-level)
    * [Advanced Display](#blueprints--advanced-display)
    * [Transient Variables](#blueprints--transient-variables)
    * [Config Variables](#blueprints--config-variables)
    * [Function Naming](#blueprints--function-naming)
    * [All Functions Must Have Return Nodes](#blueprints--functions-use-return)
    * [No Function Should Have More Than 50 Nodes](#blueprints--function-node-limit)
    * [All Public Functions Should Have A Description](#blueprints--public-functions-description)
    * [All Custom Static Plugin `BlueprintCallable` Functions Must Be Categorized By Plugin Name](#blueprints--plugin-category)
    * [No Spaghetti](#blueprints--no-spaghetti)
    * [Align Nodes By Primary Wires](#blueprints--align-wires)
    * [Graphs Should Be Reasonably Commented](#blueprints--graphs-commented)
    * [Handle Casting Errors Where Appropriate](#blueprints--handle-cast-errors)
    * [Remove Dangling / Loose / Dead Nodes](#blueprints--remove-dangling-nodes)
6. [Static Meshes](#static-meshes)
    * [Static Mesh UVs](#static-meshes--uvs)
    * [LODs Should Be Set Up Correctly](#static-meshes--lods)
    * [Modular Socketless Assets Should Snap to the Grid Cleanly](#static-meshes--modular-snapping)
    * [All Meshes Must Have Collision](#static-meshes--collision)
    * [All Meshes Should Be Scaled Correctly](#static-meshes--scaled)
7. [Niagara](#niagara)
    * [No Spaces, Ever](#niagara--no-spaces)
8. [Levels / Maps](#levels)
    * [No Errors or Warnings](#levels--no-errors-or-warnings)
    * [Lighting Should Be Built](#levels--lighting-built)
    * [No Player Visible Z Fighting](#levels--no-visible-z-fighting)
    * [Marketplace Specific Rules](#levels--marketplace-rules)
9. [Textures](#textures)
    * [Dimensions Are Powers of 2](#textures--dimensions)
    * [Texture Density Should Be Uniform](#textures--uniform-density)
    * [Textures Should Be No Bigger Than 8192](#textures--maximum-size)
    * [Textures Should Be Grouped Correctly](#textures--groups)

## 0. Terminology <a name="terminology"></a>

### Identifier <a name="terminology--identifier"></a>

An Identifier is anything that resembles or serves as a "name". For example, the name of an asset, the name of a material layer, a blueprint property, a variable, a folder name, or a data table row name, etc...

### Cases <a name="terminology--cases"></a>

There are a few different ways you can `CaseWordsWhenNaming`. Here are some common casing types:

> #### PascalCase <a name="terminology--pascalcase"></a>
>
> Capitalize every word and remove all spaces, e.g. `DesertEagle`, `StyleGuide`, `ASeriesOfWords`.
>
> #### camelCase <a name="terminology--camelcase"></a>
>
> The first letter is always lowercase but every following word starts with uppercase, e.g. `desertEagle`, `styleGuide`, `aSeriesOfWords`.
>
> #### Snake_case <a name="terminology--snake_case"></a>
>
> Words can arbitrarily start upper or lowercase but words are separated by an underscore, e.g. `desert_Eagle`, `Style_Guide`, `a_Series_of_Words`.

### Channel Packing <a name="terminology--channel-packing"></a>

It is common practice to pack multiple layers of texture data into one texture. For example, Emissive, Roughness, and Ambient Occlusion could be packed together as the Red, Green, and Blue channels of a texture respectively.

Packing 4 channels of data into a texture (RGBA) is not recommended except for an Alpha/Opacity mask in the Diffuse/Albedo's alpha channel as a texture with an alpha channel incurs more overhead than one without.

## 1. Identifiers <a name="identifiers"></a>

### Forbidden Characters <a name="identifiers--forbidden-characters"></a>

In any `Identifier` of any kind, **never** use the following unless absolutely forced to:

* White space of any kind
* Backward slashes `\`
* Symbols i.e. `#!@$%`
* Any Unicode character

Any `Identifier` should strive to only have the following characters when possible (the RegEx `[A-Za-z0-9_]+`)

* ABCDEFGHIJKLMNOPQRSTUVWXYZ
* abcdefghijklmnopqrstuvwxyz
* 1234567890
* _ (sparingly)

This ensures the greatest compatibility for data using identifiers across all platforms and tools, and by extension prevents downtime due to potentially bad character handling for identifiers in code you don't control.

## 2. Asset Naming Conventions <a name="asset-naming"></a>

Naming conventions should be treated as law. A project that conforms to a naming convention is able to have its assets managed, searched, parsed, and maintained with incredible ease.

Most assets are generally prefixed with an acronym of the asset type followed by an underscore.

### Base Asset Name - `Prefix_BaseAssetName_Variant_Suffix` <a name="asset-naming--base"></a>

All assets should have a *Base Asset Name*. A Base Asset Name represents a logical grouping of related assets. Any asset that is part of this logical group should follow the standard of  `Prefix_BaseAssetName_Variant_Suffix`.

Keeping the pattern `Prefix_BaseAssetName_Variant_Suffix` in mind and using common sense is generally enough to produce good asset names. Here are some detailed rules regarding each element.

`Prefix` and `Suffix` are to be determined by the asset type through the following [Asset Name Modifier](#asset-naming--modifiers) tables.

`BaseAssetName` should be determined by a short and easily recognizable name related to the context of this group of assets. For example, if you had a character named Bob, all of Bob's assets would have the `BaseAssetName` of `Bob`.

For unique and specific variations of assets, the `Variant` is either a short and easily recognizable name that represents a logical grouping of assets that are a subset of an asset's base name. For example, if Bob had multiple skins these skins should still use `Bob` as the `BaseAssetName`, but include a recognizable `Variant`. An 'Evil' skin would be referred to as `Bob_Evil` and a 'Retro' skin would be referred to as `Bob_Retro`.

For unique but generic variations of assets, the `Variant` is a two-digit number starting at `01`. For example, if you have an environment artist generating nondescript rocks, they would be named `Rock_01`, `Rock_02`, `Rock_03`, etc. Except for rare exceptions, you should never require a three-digit variant number. If you have more than 100 assets, you should consider organizing them with different base names or using multiple variant names.

Depending on how your asset variants are made, you can chain together variant names. For example, if you are creating flooring assets for an Arch Viz project you should use the base name `Flooring` with chained variants such as `Flooring_Marble_01`, `Flooring_Maple_01`, `Flooring_Tile_Squares_01`.

#### Examples <a name="asset-naming--base--examples"></a>

##### Bob

| Asset Type              | Asset Name                                                 |
| ----------------------- | ---------------------------------------------------------- |
| Skeletal Mesh           | SK_Bob                                                     |
| Material                | M_Bob                                                      |
| Texture (Diffuse/Albedo)| T_Bob_D                                                    |
| Texture (Normal)        | T_Bob_N                                                    |
| Texture (Evil Diffuse)  | T_Bob_Evil_D                                               |

##### Rocks

| Asset Type              | Asset Name                                                 |
| ----------------------- | ---------------------------------------------------------- |
| Static Mesh (01)        | S_Rock_01                                                  |
| Static Mesh (02)        | S_Rock_02                                                  |
| Static Mesh (03)        | S_Rock_03                                                  |
| Material                | M_Rock                                                     |
| Material Instance (Snow)| MI_Rock_Snow                                               |

### Asset Name Modifiers <a name="asset-naming--modifiers"></a>

When naming an asset, use these tables to determine the prefix and suffix to use with an asset's [Base Asset Name](#asset-naming--base).

#### Most Common <a name="asset-naming--modifiers--common"></a>

| Asset Type              | Prefix     | Suffix     | Notes                            |
| ----------------------- | ---------- | ---------- | -------------------------------- |
| Level / Map             |            |            | [Should be in a folder called Maps.](#content-structure--maps-folder) |
| Level (Persistent)      |            | _P         |                                  |
| Level (Audio)           |            | _Audio     |                                  |
| Level (Lighting)        |            | _Lighting  |                                  |
| Level (Geometry)        |            | _Geo       |                                  |
| Level (Gameplay)        |            | _Gameplay  |                                  |
| Blueprint               | BP_        |            |                                  |
| Material                | M_         |            |                                  |
| Static Mesh             | S_         |            | Many use SM_. We use S_.         |
| Skeletal Mesh           | SK_        |            |                                  |
| Texture                 | T_         | _?         | See [Textures](#asset-naming--modifiers--textures)    |
| Particle System         | PS_        |            |                                  |
| Widget Blueprint        | WBP_       |            |                                  |

#### Animations <a name="asset-naming--modifiers--animations"></a>

| Asset Type              | Prefix     | Suffix     | Notes                            |
| ----------------------- | ---------- | ---------- | -------------------------------- |
| Aim Offset              | AO_        |            |                                  |
| Aim Offset 1D           | AO_        |            |                                  |
| Animation Blueprint     | ABP_       |            |                                  |
| Animation Composite     | AC_        |            |                                  |
| Animation Montage       | AM_        |            |                                  |
| Animation Sequence      | A_         |            |                                  |
| Blend Space             | BS_        |            |                                  |
| Blend Space 1D          | BS_        |            |                                  |
| Level Sequence          | LS_        |            |                                  |
| Morph Target            | MT_        |            |                                  |
| Paper Flipbook          | PFB_       |            |                                  |
| Rig                     | Rig_       |            |                                  |
| Control Rig             | CR_        |            |                                  |
| Skeletal Mesh           | SK_        |            |                                  |
| Skeleton                | SKEL_      |            |                                  |

### Artificial Intelligence <a name="asset-naming--modifiers--artificial-intelligence"></a>

| Asset Type              | Prefix     | Suffix     | Notes                            |
| ----------------------- | ---------- | ---------- | -------------------------------- |
| AI Controller           | AIC_       |            |                                  |
| Behavior Tree           | BT_        |            |                                  |
| Blackboard              | BB_        |            |                                  |
| Decorator               | BTDecorator_ |          |                                  |
| Service                 | BTService_ |            |                                  |
| Task                    | BTTask_    |            |                                  |
| Environment Query       | EQS_       |            |                                  |
| EnvQueryContext         | EQS_       | Context    |                                  |

### Blueprints <a name="asset-naming--modifiers--blueprints"></a>

| Asset Type              | Prefix     | Suffix     | Notes                            |
| ----------------------- | ---------- | ---------- | -------------------------------- |
| Blueprint               | BP_        |            |                                  |
| Blueprint Component     | BP_        | Component  | I.e. BP_InventoryComponent       |
| Blueprint Function Library | BPFL_   |            |                                  |
| Blueprint Interface     | BPI_       |            |                                  |
| Blueprint Macro Library | BPML_      |            | Do not use macro libraries if possible. |
| Enumeration             | E          |            | No underscore.                   |
| Structure               | F or S     |            | No underscore.                   |
| Tutorial Blueprint      | TBP_       |            |                                  |
| Widget Blueprint        | WBP_       |            |                                  |

### Materials <a name="asset-naming--modifiers--materials"></a>

| Asset Type                    | Prefix     | Suffix     | Notes                            |
| ----------------------------- | ---------- | ---------- | -------------------------------- |
| Material                      | M_         |            |                                  |
| Material (Post Process)       | PP_        |            |                                  |
| Material Function             | MF_        |            |                                  |
| Material Instance             | MI_        |            |                                  |
| Material Parameter Collection | MPC_       |            |                                  |
| Subsurface Profile            | SP_        |            |                                  |
| Physical Materials            | PM_        |            |                                  |
| Decal                         | M_, MI_    | _Decal     |                                  |

### Textures <a name="asset-naming--modifiers--textures"></a>

| Asset Type              | Prefix     | Suffix     | Notes                            |
| ----------------------- | ---------- | ---------- | -------------------------------- |
| Texture                 | T_         |            |                                  |
| Texture (Diffuse/Albedo/Base Color)| T_ | _D      |                                  |
| Texture (Normal)        | T_         | _N         |                                  |
| Texture (Roughness)     | T_         | _R         |                                  |
| Texture (Alpha/Opacity) | T_         | _A         |                                  |
| Texture (Ambient Occlusion) | T_     | _O         |                                  |
| Texture (Bump)          | T_         | _B         |                                  |
| Texture (Emissive)      | T_         | _E         |                                  |
| Texture (Mask)          | T_         | _M         |                                  |
| Texture (Specular)      | T_         | _S         |                                  |
| Texture (Metallic)      | T_         | _M         |                                  |
| Texture (Packed)        | T_         | _*         | See notes below about [packing](#asset-naming--modifiers--channel-packing). |
| Texture Cube            | TC_        |            |                                  |
| Media Texture           | MT_        |            |                                  |
| Render Target           | RT_        |            |                                  |
| Cube Render Target      | RTC_       |            |                                  |
| Texture Light Profile   | TLP        |            |                                  |

#### Channel Packing* Suffix <a name="asset-naming--modifiers--channel-packing"></a>

Simply stack the suffix letters from the above table together in the order of the packed textures.

For example, if Emissive, Roughness, and Ambient Occlusion were channel packed together, then the suffix would be `_ERO`.

> It is generally acceptable to include an Alpha/Opacity layer in your Diffuse/Albedo's alpha channel and as this is common practice, adding `A` to the `_D` suffix is optional.

*See [Channel Packing](#terminology--channel-packing)

### Miscellaneous <a name="asset-naming--modifiers--misc"></a>

| Asset Type                 | Prefix     | Suffix     | Notes                            |
| -------------------------- | ---------- | ---------- | -------------------------------- |
| Animated Vector Field      | VFA_       |            |                                  |
| Camera Anim                | CA_        |            |                                  |
| Color Curve                | Curve_     | _Color     |                                  |
| Curve Table                | Curve_     | _Table     |                                  |
| Data Asset                 | *_         |            | Prefix should be based on class. |
| Data Table                 | DT_        |            |                                  |
| Float Curve                | Curve_     | _Float     |                                  |
| Foliage Type               | FT_        |            |                                  |
| Force Feedback Effect      | FFE_       |            |                                  |
| Landscape Grass Type       | LG_        |            |                                  |
| Landscape Layer            | LL_        |            |                                  |
| Matinee Data               | Matinee_   |            |                                  |
| Media Player               | MP_        |            |                                  |
| File Media Source          | FMS_       |            |                                  |
| Object Library             | OL_        |            |                                  |
| Redirector                 |            |            | These should be fixed up ASAP.   |
| Sprite Sheet               | SS_        |            |                                  |
| Static Vector Field        | VF_        |            |                                  |
| Substance Graph Instance   | SGI_       |            |                                  |
| Substance Instance Factory | SIF_       |            |                                  |
| Touch Interface Setup      | TI_        |            |                                  |
| Vector Curve               | Curve_     | _Vector    |                                  |

### Paper 2D <a name="asset-naming--modifiers--paper-2d"></a>

| Asset Type              | Prefix     | Suffix     | Notes                            |
| ----------------------- | ---------- | ---------- | -------------------------------- |
| Paper Flipbook          | PFB_       |            |                                  |
| Sprite                  | SPR_       |            |                                  |
| Sprite Atlas Group      | SPRG_      |            |                                  |
| Tile Map                | TM_        |            |                                  |
| Tile Set                | TS_        |            |                                  |

### Physics <a name="asset-naming--modifiers--physics"></a>

| Asset Type              | Prefix     | Suffix     | Notes                            |
| ----------------------- | ---------- | ---------- | -------------------------------- |
| Physical Material       | PM_        |            |                                  |
| Physics Asset           | PHYS_      |            |                                  |
| Destructible Mesh       | DM_        |            |                                  |

### Sounds <a name="asset-naming--modifiers--sounds"></a>

| Asset Type              | Prefix     | Suffix     | Notes                            |
| ----------------------- | ---------- | ---------- | -------------------------------- |
| Dialogue Voice          | DV_        |            |                                  |
| Dialogue Wave           | DW_        |            |                                  |
| Media Sound Wave        | MSW_       |            |                                  |
| Reverb Effect           | Reverb_    |            |                                  |
| Sound Attenuation       | ATT_       |            |                                  |
| Sound Class             |            |            | No prefix/suffix. Should be put in a folder called SoundClasses |
| Sound Concurrency       |            | _SC        | Should be named after a SoundClass |
| Sound Cue               | A_         | _Cue       |                                  |
| Sound Mix               | Mix_       |            |                                  |
| Sound Wave              | A_         |            |                                  |

### User Interface <a name="asset-naming--modifiers--user-interface"></a>

| Asset Type              | Prefix     | Suffix     | Notes                            |
| ----------------------- | ---------- | ---------- | -------------------------------- |
| Font                    | Font_      |            |                                  |
| Slate Brush             | Brush_     |            |                                  |
| Slate Widget Style      | Style_     |            |                                  |
| Widget Blueprint        | WBP_       |            |                                  |

### Effects <a name="asset-naming--modifiers--effects"></a>

| Asset Type              | Prefix     | Suffix     | Notes                            |
| ----------------------- | ---------- | ---------- | -------------------------------- |
| Particle System         | PS_        |            |                                  |
| Material (Post Process) | PP_        |            |                                  |

**[⬆ Back to Top](#table-of-contents)**

## 3. Content Directory Structure <a name="content-structure"></a>

Equally important as asset names, the directory structure style of a project should be considered law. Asset naming conventions and content directory structure go hand in hand, and a violation of either causes unneeded chaos.

There are multiple ways to lay out the content of a UE5 project. In this style, we will be using a structure that relies more on the filtering and search abilities of the Content Browser for those working with assets to find assets of a specific type instead of another common structure that groups asset types with folders.

> If you are using the prefix [naming convention](#asset-naming) above, using folders to contain assets of similar types such as `Meshes`, `Textures`, and `Materials` is a redundant practice as asset types are already both sorted by prefix as well as able to be filtered in the content browser.

<details>
    <summary>Example Project Content Structure</summary>
    <pre>
|-- Content
    |-- <a href="#content-structure--top-level">GenericShooter</a>
        |-- Art
        |   |-- Industrial
        |   |   |-- Ambient
        |   |   |-- Machinery
        |   |   |-- Pipes
        |   |-- Nature
        |   |   |-- Ambient
        |   |   |-- Foliage
        |   |   |-- Rocks
        |   |   |-- Trees
        |   |-- Office
        |-- Characters
        |   |-- Bob
        |   |-- Common
        |   |   |-- <a href="#content-structure--large-sets">Animations</a>
        |   |   |-- Audio
        |   |-- Jack
        |   |-- Steve
        |   |-- <a href="#content-structure--folder-names--no-unicode">Zoe</a>
        |-- <a href="#content-structure--core-folder">Core</a>
        |   |-- Characters
        |   |-- Engine
        |   |-- <a href="#content-structure--folder-names--no-spaces">GameModes</a>
        |   |-- Interactables
        |   |-- Pickups
        |   |-- Weapons
        |-- Effects
        |   |-- Electrical
        |   |-- Fire
        |   |-- Weather
        |-- <a href="#content-structure--maps-folder">Maps</a>
        |   |-- Campaign1
        |   |-- Campaign2
        |-- <a href="#content-structure--material-library">MaterialLibrary</a>
        |   |-- Debug
        |   |-- Metal
        |   |-- Paint
        |   |-- Utility
        |   |-- Weathering
        |-- Placeables
        |   |-- Pickups
        |-- Weapons
            |-- Common
            |-- Pistols
            |   |-- DesertEagle
            |   |-- RocketPistol
            |-- Rifles
    </pre>
</details>

The reasons for this structure are listed in the following sub-sections.

### Folder Names <a name="content-structure--folder-names"><a>

These are common rules for naming any folder in the content structure.

#### Always Use PascalCase* <a name="content-structure--folder-names--pascalcase"></a>

PascalCase refers to starting a name with a capital letter and then instead of using spaces, every following word also starts with a capital letter. For example, `DesertEagle`, `RocketPistol`, and `ASeriesOfWords`.

*See [PascalCase](#terminology--pascalcase).

#### Never Use Spaces <a name="content-structure--folder-names--no-spaces"></a>

This is a re-enforcement of the most important part of [Always Use PascalCase](#content-structure--folder-names--pascalcase): never use spaces. Spaces can cause various engineering tools and batch processes to fail. Ideally, your project's root also contains no spaces and is located somewhere such as `D:\Project` instead of `C:\Users\My Name\My Documents\Unreal Projects`.

#### Never Use Unicode Characters And Other Symbols <a name="content-structure--folder-names--no-unicode"></a>

If one of your game characters is named 'Zoë', its folder name should be `Zoe`. Unicode characters can be worse than [Spaces](#content-structure--folder-names--no-spaces) for engineering tools and some parts of UE5 don't support Unicode characters in paths either.

Related to this, if your project has [unexplained issues](https://answers.unrealengine.com/questions/101207/undefined.html) and your computer's user name has a Unicode character (i.e. your name is `Zoë`), any project located in your `My Documents` folder will suffer from this issue. Often simply moving your project to something like `D:\Project` will fix these mysterious issues.

Using other characters outside `a-z`, `A-Z`, and `0-9` such as `@`, `-`, `_`, `,`, `*`, and `#` can also lead to unexpected and hard-to-track issues on other platforms, source control, and weaker engineering tools.

### Use a Top Level Folder for Project Specific Assets <a name="content-structure--top-level"><a>

All of a project's assets should exist in a folder named after the project. For example, if your project is named 'Generic Shooter', *all* of it's content should exist in `Content/GenericShooter`.

> The `Developers` folder is not for assets that your project relies on and therefore is not project specific. See [Developer Folders](#content-structure--developers-folder) for details about this.

<details>
    <summary>Why?</summary>

#### Reason 1. No Global Assets

Often in code style guides it is written that you should not pollute the global namespace and this follows the same principle. When assets are allowed to exist outside of a project folder, it often becomes much harder to enforce a strict structure layout because assets not in a folder encourage the bad behavior of not having to organize assets.

Every asset should have a purpose, otherwise it does not belong in a project. If an asset is an experimental test and shouldn't be used by the project it should be put in a [`Developer`](#content-structure--developers-folder) folder.

#### Reason 2. Reduce Migration Conflicts

When working on multiple projects it is common for a team to copy assets from one project to another if they have made something useful for both. When this occurs, the easiest way to perform the copy is to use the Content Browser's Migrate functionality as it will copy over not just the selected asset but all of its dependencies.

These dependencies are what can easily get you into trouble. If two project's assets do not have a top level folder and they happen to have similarly named or already previously migrated assets, a new migration can accidentally wipe any changes to the existing assets.

This is also the primary reason why Epic's Marketplace staff enforces the same policy for submitted assets.

After a migration, safe merging of assets can be done using the 'Replace References' tool in the content browser with the added clarity that assets not belonging to a project's top level folder are clearly pending a merge. Once assets are merged and fully migrated, there shouldn't be another top level folder in your Content tree. This method is *100%* guaranteed to make any migrations that occur completely safe.

<details>
    <summary>Master Material Example</summary>

For example, say you created a master material in one project that you would like to use in another project so you migrated that asset over. If this asset is not in a top level folder, it may have a name like `Content/MaterialLibrary/M_Master`. If the target project doesn't have a master material already, this should work without issue.

As work on one or both projects progresses, their respective master materials may change to be tailored for their specific projects due to the course of normal development.

The issue comes when, for example, an artist for one project created a nice generic modular set of static meshes and someone wants to include that set of static meshes in the second project. If the artist who created the assets used material instances based on `Content/MaterialLibrary/M_Master` as they're instructed to, when a migration is performed there is a great chance of conflict for the previously migrated `Content/MaterialLibrary/M_Master` asset.

This issue can be hard to predict and hard to account for. The person migrating the static meshes may not be the same person who is familiar with the development of both project's master materials, and they may not be even aware that the static meshes in question rely on material instances which then rely on the master material. The Migrate tool requires the entire chain of dependencies to work however, so it will be forced to grab `Content/MaterialLibrary/M_Master` when it copies these assets to the other project and it will overwrite the existing asset.

It is at this point where if the master materials for both projects are incompatible in *any way*, you risk breaking possibly the entire material library for a project as well as any other dependencies that may have already been migrated, simply because assets were not stored in a top level folder. The simple migration of static meshes now becomes a very ugly task.
</details>

#### Reason 3. Samples, Templates, and Marketplace Content Are Risk-Free

An extension to reducing migration conflicts, if a team member decides to add sample content, template files, or assets they bought from the marketplace, it is guaranteed, as long your project's top-level folder is uniquely named, that these new assets will not interfere with your project.

You can not trust marketplace content to fully conform to the [top level folder rule](#content-structure--top-level). There exist many assets that have the majority of their content in a top level folder but also have possibly modified Epic sample content as well as level files polluting the global `Content` folder.

When adhering to the [top level folder rule](#content-structure--top-level), the worst marketplace conflict you can have is if two marketplace assets both have the same Epic sample content. If all your assets are in a project specific folder, including sample content you may have moved into your folder, your project will never break.

#### Reason 4. DLC, Sub-Projects, and Patches Are Easily Maintained

If your project plans to release DLC or has multiple sub-projects associated with it that may either be migrated out or simply not cooked in a build, assets relating to these projects should have their own separate top level content folder. This makes cooking DLC separate from main project content far easier. Sub-projects can also be migrated in and out with minimal effort. If you need to change the material of an asset or add some very specific asset override behavior in a patch, you can easily put these changes in a patch folder and work safely without the chance of breaking the core project.
</details>

### Use the Developers Folder for Local Testing <a name="content-structure--developers-folder"></a>

Assets in the experimental stage should stay in the Developers folder.

<details>
    <summary>Why?</summary>

During a project's development, it is very common for team members to have a sort of 'sandbox' where they can experiment freely without risking the core project. Because this work may be ongoing, these team members may wish to put their assets on a project's source control server. Not all teams choose to use the Developer folders, but the ones that do use them often run into a common problem with assets submitted to source control.

It is very easy for a team member to accidentally use assets that are not ready for use, which will cause issues once those assets are removed. For example, an artist may be iterating on a modular set of static meshes and still working on getting their sizing and grid snapping correct. If a world builder sees these assets in the main project folder, they might use them all over a level not knowing they could be subject to incredible change and/or removal. This causes massive amounts of re-working for everyone on the team to resolve.

If these modular assets were placed in a Developer folder, the world builder should never have had a reason to use them and the whole issue would never happen. The Content Browser has specific View Options that will hide Developer folders (they are hidden by default) making it impossible to accidentally use Developer assets under normal use.

Once the assets are ready for use, an artist simply has to move the assets into the project specific folder and fix up redirectors. This is essentially 'promoting' the assets from experimental to production.
</details>

### All Map Files Belong in a Folder Called Maps <a name="content-structure--maps-folder"></a>

Map files are incredibly special and it is common for every project to have its own map naming system, especially if they work with sub-levels or streaming levels. No matter what system of map organization is in place for the specific project, all levels should belong in `/Content/Project/Maps`.

<details>
    <summary>Why?</summary>

Being able to tell someone to open a specific map without having to explain where it is is a great time saver and general 'quality of life' improvement. It is common for levels to be within sub-folders of `Maps`, such as `Maps/Campaign1/` or `Maps/Arenas`, but the most important thing here is that they all exist within `/Content/Project/Maps`.

This also simplifies the job of cooking for engineers. Wrangling levels for a build process can be extremely frustrating if they have to dig through arbitrary folders for them. If a team's maps are all in one place, it is much harder to accidentally not cook a map in a build. It also simplifies lighting build scripts as well as QA processes.
</details>

### Use a `Core` Folder for Critical Blueprints and Other Assets <a name="content-structure--core-folder"></a>

Use `/Content/Project/Core` folder for assets that are absolutely fundamental to a project's workings. For example, base `GameMode`, `Character`, `PlayerController`, `GameState`, `PlayerState`, and related Blueprints should live here.

<details>
    <summary>Why?</summary>

This creates a very clear "don't touch these" message for other team members. Non-engineers should have very little reason to enter the `Core` folder. When following a good code structure style, designers should be making their gameplay tweaks in child classes that expose functionality. World builders should be using prefab Blueprints in designated folders instead of potentially abusing base classes.
</details>

<details>
    <summary>Example</summary>

If your project requires pickups that can be placed in a level, there should exist a base Pickup class in `Core/Pickups` that defines the base behavior for a pickup. Specific pickups such as Health or Ammo should exist in a folder such as `/Content/Project/Placeables/Pickups/`. Game designers can define and tweak pickups in this folder however they please, but they should not touch `Core/Pickups` as they may unintentionally break pickups project-wide.
</details>

### Do Not Create Redundant Folders Called `Assets`, `Meshes`, `Textures`, `Materials`, and So On <a name="content-structure--redundant-folders"></a>

The [top level rule](#content-structure--top-level) and [asset naming conventions](#asset-naming) make these folders redundant.

<details>
    <summary>Why?</summary>

All asset names are named with their asset type in mind. These folders offer only redundant information and the use of these folders can easily be replaced with the robust and easy-to-use filtering system the Content Browser provides.

Want to view only static mesh in `Environment/Rocks/`? Simply turn on the Static Mesh filter. If all assets are named correctly, they will also be sorted in alphabetical order regardless of prefixes. Want to view both static meshes and skeletal meshes? Simply turn on both filters. This eliminates the need to potentially have to `Control-Click` select two folders in the Content Browser's tree view.

> This also extends the full path name of an asset for very little benefit. The `S_` prefix for a static mesh is only two characters, whereas `Meshes/` is seven characters.

Not doing this also prevents the inevitability of someone putting a static mesh or a texture in a `Materials` folder.
</details>

### Very Large Asset Sets Get Their Own Folder Layout <a name="content-structure--large-sets"></a>

This can be seen as a pseudo-exception to [redundant folders](#content-structure--redundant-folders).

Some asset types can have a huge volume of related files where each asset has a unique purpose. The two most common are Animation and Audio assets. If you find yourself having 15+ of these assets that belong together, they should be together.

For example, animations that are shared across multiple characters should lay in `Characters/Common/Animations` and may have sub-folders such as `Locomotion` or `Cinematic`.

> This does not apply to assets like textures and materials. It is common for a `Rocks` folder to have a lot of textures if there are a lot of rocks, but these textures are generally only related to a few specific rocks and should be named appropriately. Even if these textures are part of a [Material Library](#content-structure--material-library).

### `MaterialLibrary` <a name="content-structure--material-library"></a>

If your project makes use of master materials, layered materials, or any form of reusable materials or textures that do not belong to any subset of assets, these assets should be located in `Content/Project/MaterialLibrary`.

This way all 'global' materials have a place to live and are easily located.

> This also makes it incredibly easy to enforce a 'use material instances only' policy within a project. If all artists and assets should be using material instances, then the only regular material assets that should exist are within this folder. You can easily verify this by searching for base materials in any folder that isn't the `MaterialLibrary`.

The `MaterialLibrary` doesn't have to consist of purely materials. Shared utility textures, material functions, and other things of this nature should be stored here as well within folders that designate their intended purpose. For example, generic noise textures should be located in `MaterialLibrary/Utility`.

Any testing or debug materials should be within `MaterialLibrary/Debug`. This allows debug materials to be easily stripped from a project before shipping and makes it incredibly apparent if production assets are using them if reference errors are shown.

### No Empty Folders <a name="content-structure--no-empty-folders"></a>

There simply shouldn't be any empty folders. They clutter the content browser.

<details>
    <summary>Having Trouble With One?</summary>

If you find that the content browser has an empty folder you can't delete, you should perform the following:

1. Be sure you're using source control.
1. Immediately run Fix Up Redirectors on your project.
1. Navigate to the folder on your disk and delete the assets inside.
1. Close the editor.
1. Make sure your source control state is in sync (i.e. if using Perforce, run a Reconcile Offline Work on your content directory)
1. Open the editor. Confirm everything still works as expected. If it doesn't, revert, figure out what went wrong, and try again.
1. Ensure the folder is now gone.
1. Submit changes to source control.

</details>

**[⬆ Back to Top](#table-of-contents)**

## 4. C++ <a name="cpp"></a>

### Follow the Epic C++ Coding Standard <a name="cpp--follow-coding-standard"></a>

The [Epic C++ Coding Standard](https://dev.epicgames.com/documentation/en-us/unreal-engine/epic-cplusplus-coding-standard-for-unreal-engine) is Epic's style guide for C++ in Unreal Engine projects. Most of the general ideas within it can be applied to blueprint as well.

Make sure you're well versed in Epic's C++ style if you plan to make engine contributions.

## 5. Blueprints <a name="blueprints"></a>

This section will focus on Blueprint classes and their internals. When possible, style rules conform to [Epic's Coding Standard](https://dev.epicgames.com/documentation/en-us/unreal-engine/epic-cplusplus-coding-standard-for-unreal-engine).

Remember: Blueprinting badly bears blunders, beware! (Phrase by [KorkuVeren](http://github.com/KorkuVeren))

### Fix Warnings and Errors <a name="blueprints--fix-warnings-and-errors"></a>

All blueprints should compile with zero warnings and zero errors. You should fix blueprint warnings and errors immediately as they can quickly cascade into very scary unexpected behavior.

Do *not* submit broken blueprints to source control. If you must store them on source control, shelve them instead.

Broken blueprints can cause problems that manifest in other ways, such as broken references, unexpected behavior, cooking failures, and frequent unneeded recompilation. A broken blueprint has the power to break your entire game.

### Follow Variable Naming Best Practices <a name="blueprints--follow-variable-naming"></a>

#### Nouns <a name="blueprints--variables--naming--nouns"></a>

All non-boolean variable names must be clear, unambiguous, and descriptive nouns.

#### PascalCase <a name="blueprints--variables--naming--pascalcase"></a>

All non-boolean variables should be in the form of [PascalCase](#terminology--pascalcase).

##### Examples <a name="blueprints--variables--naming--pascalcase--example"></a>

* `Score`
* `Kills`
* `TargetPlayer`
* `Range`
* `CrosshairColor`
* `AbilityID`

#### Boolean `b` Prefix <a name="blueprints--variables--naming--boolean-prefix"></a>

All booleans should be named in PascalCase but prefixed with a lowercase `b`.

Example: Use `bDead` and `bEvil`, **not** `Dead` and `Evil`.

UE5 Blueprint editors know not to include the `b` in user-friendly displays of the variable.

#### Boolean Names <a name="blueprints--variables--naming--booleans"></a>

##### General And Independent State Information <a name="blueprints--variables--naming--booleans--general-state"></a>

All booleans should be named as descriptive adjectives when possible if representing general information. Do not include words that phrase the variable as a question, such as `Is`. This is reserved for functions.

Example: Use `bDead` and `bHostile` **not** `bIsDead` and `bIsHostile`.

Try to not use verbs such as `bRunning`. Verbs tend to lead to complex states.

##### Complex States <a name="blueprints--variables--naming--booleans--complex-state"></a>

Do not to use booleans to represent complex and/or dependent states. This makes state adding and removing complex and no longer easily readable. Use an enumeration instead.

Example: When defining a weapon, do **not** use `bReloading` and `bEquipping` if a weapon can't be both reloading and equipping. Define an enumeration named `EWeaponState` and use a variable with this type named `WeaponState` instead. This makes it far easier to add new states to weapons.

Example: Do **not** use `bRunning` if you also need `bWalking` or `bSprinting`. This should be defined as an enumeration with clearly defined state names.

#### Considered Context <a name="blueprints--variables--naming--context"></a>

All variable names must not be redundant with their context as all variable references in Blueprint will always have context.

##### Examples <a name="blueprints--variables--naming--context--examples"></a>

Consider a Blueprint called `BP_PlayerCharacter`.

Bad examples:

* `PlayerScore`
* `PlayerKills`
* `MyTargetPlayer`
* `MyCharacterName`
* `CharacterSkills`
* `ChosenCharacterSkin`

All of these variables are named redundantly. It is implied that the variable is representative of the `BP_PlayerCharacter` it belongs to because it is `BP_PlayerCharacter` that is defining these variables.

Good examples:

* `Score`
* `Kills`
* `TargetPlayer`
* `Name`
* `Skills`
* `Skin`

#### Do *Not* Include Atomic Type Names <a name="blueprints--variables--naming--atomic"></a>

Atomic or primitive variables are variables that represent data in their simplest form, such as booleans, integers, floats, and enumerations.

Strings and vectors are considered atomic in terms of style when working with Blueprints, however they are technically not atomic.

> While vectors consist of three floats, vectors are often able to be manipulated as a whole, same with rotators.

Also:

> Do *not* consider Text variables as atomic, they are secretly hiding localization functionality. The atomic type of a string of characters is `String`, not `Text`.

Atomic variables should not have their type name in their name.

Example: Use `Score`, `Kills`, and `Description` **not** `ScoreFloat`, `FloatKills`, `DescriptionString`.

The only exception to this rule is when a variable represents 'a number of' something to be counted *and* when using a name without a variable type is not easy to read.

Example: A fence generator needs to generate X number of posts. Store X in `NumPosts` or `PostsCount` instead of `Posts` as `Posts` may potentially read as an Array of a variable type named `Post`.

#### Do Include Non-Atomic Type Names <a name="blueprints--variables--naming--complex"></a>

Non-atomic or complex variables are variables that represent data as a collection of atomic variables. Structs, Classes, Interfaces, and primitives with hidden behavior such as `Text` and `Name` all qualify under this rule.

> While an Array of an atomic variable type is a list of variables, Arrays do not change the 'atomicness' of a variable type.

These variables should include their type name while still considering their context.

If a class owns an instance of a complex variable, i.e. if a `BP_PlayerCharacter` owns a `BP_Hat`, it should be stored as the variable type as without any name modifications.

Example: Use `Hat`, `Flag`, and `Ability` **not** `MyHat`, `MyFlag`, and `PlayerAbility`.

If a class does not own the value a complex variable represents, you should use a noun along with the variable type.

Example: If a `BP_Turret` has the ability to target a `BP_PlayerCharacter`, it should store its target as `TargetPlayer` as when in the context of `BP_Turret` it should be clear that it is a reference to another complex variable type that it does not own.

#### Arrays <a name="blueprints--variables--naming--arrays"></a>

Arrays follow the same naming rules as above, but should be named as a plural noun.

Example: Use `Targets`, `Hats`, and `EnemyPlayers`, **not** `TargetList`, `HatArray`, `EnemyPlayerArray`.

### Mark Configurable Variables as Editable <a name="blueprints--mark-configurable-as-editable"></a>

All variables that are safe to change the value of in order to configure behavior of a blueprint should be marked as `Editable`.

Conversely, all variables that are not safe to change or should not be exposed to designers should *not* be marked as editable, unless for engineering reasons the variable must be marked as `Expose On Spawn`.

Do not arbitrarily mark variables as `Editable`.

### Give Editable Variables Tooltips <a name="blueprints--editable-variables-tooltips"></a>

All `Editable` variables, including those marked editable just so they can be marked as `Expose On Spawn`, should have a description in their `Tooltip` fields that explains how changing this value affects the behavior of the blueprint.

### Use Slider And Value Ranges for Editable Variables When Appropriate <a name="blueprints--editable-ranges"></a>

All `Editable` variables should make use of slider and value ranges if there is ever a value that a variable should *not* be set to.

Example: A blueprint that generates fence posts might have an editable variable named `PostsCount` and a value of -1 would not make any sense. Use the range fields to mark 0 as a minimum.

If an editable variable is used in a Construction Script, it should have a reasonable Slider Range defined so that someone can not accidentally assign it a large value that could crash the editor.

A Value Range only needs to be defined if the bounds of a value are known. While a Slider Range prevents accidental large number inputs, an undefined Value Range allows a user to specify a value outside the Slider Range that may be considered 'dangerous' but still valid.

### Use Variable Categories When Necessary <a name="blueprints--variable-categories"></a>

If a class has only a small number of variables, categories are not required.

If a class has a moderate amount of variables (5-10), all `Editable` variables should have a non-default category assigned. A common category is `Config`.

If a class has a large amount of variables, all `Editable` variables should be categorized into sub-categories using the category `Config` as the base category. Non-editable variables should be categorized into descriptive categories describing their usage.

> You can define sub-categories by using the pipe character `|`, i.e. `Config | Animations`.

Example: A weapon class set of variables might be organized as:

    |-- Config
    |    |-- Animations
    |    |-- Effects
    |    |-- Audio
    |    |-- Recoil
    |    |-- Timings
    |-- Animations
    |-- State
    |-- Visuals

### Variable Access Level <a name="blueprints--access-level"></a>

In C++, variables have a concept of access level. Public means any code outside the class can access the variable. Protected means only the class and any child classes can access this variable internally. Private means only this class and no child classes can access this variable.

Blueprints do not have a defined concept of protected access currently.

Treat `Editable` variables as public variables. Treat non-editable variables as protected variables.

#### Private Variables <a name="blueprints--variables--access--private"></a>

Unless it is known that a variable should only be accessed within the class it is defined and never a child class, do not mark variables as private. Until variables are able to be marked `protected`, reserve private for when you absolutely know you want to restrict child class usage.

### Advanced Display <a name="blueprints--advanced-display"></a>

If a variable should be editable but often untouched, mark it as `Advanced Display`. This makes the variable hidden unless the advanced display arrow is clicked.

To find the `Advanced Display` option, it is listed as an advanced displayed variable in the variable details list.

### Transient Variables <a name="blueprints--transient-variables"></a>

Transient variables are variables that do not need to have their value saved and loaded and have an initial value of zero or null. This is useful for references to other objects and actors who's value isn't known until run-time. This prevents the editor from ever saving a reference to it, and speeds up saving and loading of the blueprint class.

Because of this, all transient variables should always be initialized as zero or null. To do otherwise would result in hard to debug errors.

### Config Variables <a name="blueprints--config-variables"></a>

Do not use the `Config Variable` flag. This makes it harder for designers to control blueprint behavior. Config variables should only be used in C++ for rarely changed variables. Think of them as `Advanced Advanced Display` variables.

### Function Naming <a name="blueprints--function-naming"></a>

The naming of functions, events, and event dispatchers is critically important. Based on the name alone, certain assumptions can be made about functions. For example:

* Is it a pure function?
* Is it fetching state information?
* Is it a handler?
* Is it an RPC?
* What is its purpose?

These questions and more can all be answered when functions are named appropriately.

#### All Functions Should Be Verbs <a name="blueprints--functions--naming--verbs"></a>

All functions and events perform some form of action, whether its getting info, calculating data, or causing something to explode. Therefore, all functions should all start with verbs. They should be worded in the present tense whenever possible. They should also have some context as to what they are doing.

`OnRep` functions, event handlers, and event dispatchers are an exception to this rule.

Good examples:

* `Fire` - Good example if in a Character / Weapon class, as it has context. Bad if in a Barrel / Grass / any ambiguous class.
* `Jump` - Good example if in a Character class, otherwise, needs context.
* `Explode`
* `ReceiveMessage`
* `SortPlayerArray`
* `GetArmOffset`
* `GetCoordinates`
* `UpdateTransforms`
* `EnableBigHeadMode`
* `IsEnemy` - ["Is" is a verb.](http://writingexplained.org/is-is-a-verb)

Bad examples:

* `Dead` - Is Dead? Will deaden?
* `Rock`
* `ProcessData` - Ambiguous, these words mean nothing.
* `PlayerState` - Nouns are ambiguous.
* `Color` - Verb with no context, or ambiguous noun.

#### Property RepNotify Functions Always `OnRep_Variable` <a name="blueprints--functions--naming--onrep"></a>

All functions for replicated with notification variables should have the form `OnRep_Variable`. This is forced by the Blueprint editor. If you are writing a C++ `OnRep` function however, it should also follow this convention when exposing it to Blueprints.

#### Info Functions Returning Bool Should Ask Questions <a name="blueprints--functions--naming--bool-question"></a>

When writing a function that does not change the state of or modify any object and is purely for getting information, state, or computing a yes/no value, it should ask a question. This should also follow [the verb rule](#blueprints--functions--naming--verbs).

This is extremely important as if a question is not asked, it may be assumed that the function performs an action and is returning whether that action succeeded.

Good examples:

* `IsDead`
* `IsOnFire`
* `IsAlive`
* `IsSpeaking`
* `IsHavingAnExistentialCrisis`
* `IsVisible`
* `HasWeapon` - ["Has" is a verb.](https://www.yourdictionary.com/articles/helping-verbs)
* `WasCharging` - ["Was" is past-tense of "be".](https://www.yourdictionary.com/articles/helping-verbs) Use "was" when referring to 'previous frame' or 'previous state'.
* `CanReload` - ["Can" is a verb.](https://www.yourdictionary.com/articles/helping-verbs)

Bad examples:

* `Fire` - Is on fire? Will fire? Do fire?
* `OnFire` - Can be confused with event dispatcher for firing.
* `Dead` - Is dead? Will deaden?
* `Visibility` - Is visible? Set visibility? A description of flying conditions?

#### Event Handlers and Dispatchers Should Start With `On` <a name="blueprints--functions--naming--events"></a>

Any function that handles an event or dispatches an event should start with `On` and continue to follow [the verb rule](#blueprints--functions--naming--verbs). The verb may move to the end however if past-tense reads better.

[Collocations](http://dictionary.cambridge.org/us/grammar/british-grammar/about-words-clauses-and-sentences/collocation) of the word `On` are exempt from following the verb rule.

`Handle` is not allowed. It is 'Unreal' to use `On` instead of `Handle`, while other frameworks may prefer to use `Handle` instead of `On`.

Good examples:

* `OnDeath` - Common collocation in games
* `OnPickup`
* `OnReceiveMessage`
* `OnMessageRecieved`
* `OnTargetChanged`
* `OnClick`
* `OnLeave`

Bad examples:

* `OnData`
* `OnTarget`
* `HandleMessage`
* `HandleDeath`

#### Remote Procedure Calls Should Be Prefixed With Target <a name="blueprints--functions--naming--remote-procedure-call-target"></a>

Any time an RPC is created, it should be prefixed with either `Server`, `Client`, or `Multicast`. No exceptions.

After the prefix, follow all other rules regarding function naming.

Good examples:

* `ServerFireWeapon`
* `ClientNotifyDeath`
* `MulticastSpawnTracerEffect`

Bad examples:

* `FireWeapon` - Does not indicate its an RPC of some kind.
* `ServerClientBroadcast` - Confusing.
* `AllNotifyDeath` - Use `Multicast`, never `All`.
* `ClientWeapon` - No verb, ambiguous.

### All Functions Must Have Return Nodes <a name="blueprints--functions-use-return"></a>

All functions must have return nodes, no exceptions.

Return nodes explicitly note that a function has finished its execution. In a world where blueprints can be filled with `Sequence`, `ForLoopWithBreak`, and backwards reroute nodes, explicit execution flow is important for readability, maintenance, and easier debugging.

The Blueprint compiler is able to follow the flow of execution and will warn you if there is a branch of your code with an unhandled return or bad flow if you use return nodes.

In situations like where a programmer may add a pin to a Sequence node or add logic after a for loop completes but the loop iteration might return early, this can often result in an accidental error in code flow. The warnings the Blueprint compiler will alert everyone of these issues immediately.

### No Function Should Have More Than 50 Nodes <a name="blueprints--function-node-limit"></a>

Simply, no function should have more than 50 nodes. Any function this big should be broken down into smaller functions for readability and ease of maintenance.

The following nodes are not counted as they are deemed to not increase function complexity:

* Comment
* Route
* Cast
* Getting a Variable
* Breaking a Struct
* Function Entry
* Self

### All Public Functions Should Have A Description <a name="blueprints--public-functions-description"></a>

This rule applies more to public facing or marketplace blueprints, so that others can more easily navigate and consume your blueprint API.

Simply, any function that has an access specificer of Public should have its description filled out.

### All Custom Static Plugin `BlueprintCallable` Functions Must Be Categorized By Plugin Name <a name="blueprints--plugin-category"></a>

If your project includes a plugin that defines `static` `BlueprintCallable` functions, they should have their category set to the plugin's name or a subset category of the plugin's name.

For example, `Zed Camera Interface` or `Zed Camera Interface | Image Capturing`.

### No Spaghetti <a name="blueprints--no-spaghetti"></a>

Wires should have clear beginnings and ends. You should never have to mentally untangle wires to make sense of a graph. Many of the following sections are dedicated to reducing spaghetti.

### Align Nodes By Primary Wires <a name="blueprints--align-wires"></a>

If you ever have to decide between straightening a linear white exec line or straightening data lines of some kind, always straighten the white exec line.

Always align wires, not nodes. You can't always control the size and pin location on a node, but you can always control the location of a node and thus control the wires. Straight wires provide clear linear flow. Wiggly wires wear wits wickedly. You can straighten wires by using the Straighten Connections command with BP nodes selected. Hotkey: Q

Good example: The tops of the nodes are staggered to keep a perfectly straight white exec line.
![Aligned By Wires](https://github.com/Allar/ue5-style-guide/blob/main/images/bp-graphs-align-wires-good.png?raw=true "Aligned By Wires")

Bad Example: The tops of the nodes are aligned creating a wiggly white exec line.
![Bad](https://github.com/Allar/ue5-style-guide/blob/main/images/bp-graphs-align-wires-bad.png?raw=true "Wiggly")

Acceptable Example: Certain nodes might not cooperate no matter how you use the alignment tools. In this situation, try to minimize the wiggle by bringing the node in closer.
![Acceptable](https://github.com/Allar/ue5-style-guide/blob/main/images/bp-graphs-align-wires-acceptable.png?raw=true "Acceptable")

### Graphs Should Be Reasonably Commented <a name="blueprints--graphs-commented"></a>

Blocks of nodes should be wrapped in comments that describe their higher-level behavior. While every function should be well named so that each individual node is easily readable and understandable, groups of nodes contributing to a purpose should have their purpose described in a comment block. If a function does not have many blocks of nodes and its clear that the nodes are serving a direct purpose in the function's goal, then they do not need to be commented as the function name and  description should suffice.

### Handle Casting Errors Where Appropriate <a name="blueprints--handle-cast-errors"></a>

If a function or event assumes that a cast always succeeds, it should appropriately report a failure in logic if the cast fails. This lets others know why something that is 'supposed to work' doesn't. A function should also attempt a graceful recovery after a failed cast if it's known that the reference being casted could ever fail to be casted.

This does not mean every cast node should have its failure handled. In many cases, especially events regarding things like collisions, it is expected that execution flow terminates on a failed cast quietly.

### Remove Dangling / Loose / Dead Nodes <a name="blueprints--remove-dangling-nodes"></a>

All nodes in all blueprint graphs must have a purpose. You should not leave dangling blueprint nodes around that have no purpose or are not executed.

**[⬆ Back to Top](#table-of-contents)**

## 6. Static Meshes <a name="static-meshes"></a>

This section will focus on Static Mesh assets and their internals.

### Static Mesh UVs <a name="static-meshes--uvs"></a>

If Linter is reporting bad UVs and you can't seem to track it down, open the resulting `.log` file in your project's `Saved/Logs` folder for exact details as to why it's failing. I am hoping to include these messages in the Lint report in the future.

#### All Meshes Must Have UVs <a name="static-meshes--uvs--no-missing"></a>

Pretty simple. All meshes, regardless of how they are to be used, should not be missing UVs.

#### All Meshes Must Not Have Overlapping UVs for Lightmaps <a name="static-meshes--uvs--no-overlapping"></a>

Pretty simple. All meshes, regardless of how they are to be used, should have valid non-overlapping UVs.

### LODs Should Be Set Up Correctly <a name="static-meshes--lods"></a>

This is a subjective check on a per-project basis, but as a general rule any mesh that can be seen at varying distances should have proper LODs.

### Modular Socketless Assets Should Snap to the Grid Cleanly <a name="static-meshes--modular-snapping"></a>

This is a subjective check on a per-asset basis, but any modular socketless assets should snap together cleanly based on the project's grid settings.

It is up to the project whether to snap based on a power of 2 grid or on a base 10 grid. However, if you are authoring modular socketless assets for the marketplace, Epic requires that they snap cleanly when the grid is set to 10 units or bigger.

### All Meshes Must Have Collision <a name="static-meshes--collision"></a>

Regardless of whether an asset is going to be used for collision in a level, all meshes should have proper collision defined. This helps the engine with things such as bounds calculations, occlusion, and lighting. Collision should also be well-formed to the asset.

### All Meshes Should Be Scaled Correctly <a name="static-meshes--scaled"></a>

This is a subjective check on a per-project basis, however all assets should be scaled correctly to their project. Level designers or blueprint authors should not have to tweak the scale of meshes to get them to confirm in the editor. Scaling meshes in the engine should be treated as a scale override, not a scale correction.

**[⬆ Back to Top](#table-of-contents)**

## 7. Niagara <a name="niagara"></a>

This section will focus on Niagara assets and their internals.

### No Spaces, Ever <a name="niagara--no-spaces"></a>

As mentioned in [Identifiers Forbidden Characters](#identifiers--forbidden-characters), spaces and all white space characters are forbidden in identifiers. This is especially true for Niagara systems as it makes working with things significantly harder if not impossible when working with HLSL or other means of scripting within Niagara and trying to reference an identifier.

(Original Contribution by [@dunenkoff](https://github.com/Allar/ue5-style-guide/issues/58))

**[⬆ Back to Top](#table-of-contents)**

## 8. Levels / Maps <a name="levels"></a>

This section will focus on Level assets and their internals.

### No Errors or Warnings <a name="levels--no-errors-or-warnings"></a>

All levels should load with zero errors or warnings. If a level loads with any errors or warnings, they should be fixed immediately to prevent cascading issues.

You can run a map check on an open level in the editor by using the console command "map check".

Please note: Linter is even more strict on this than the editor is currently, and will catch load errors that the editor will resolve on its own.

### Lighting Should Be Built <a name="levels--lighting-built"></a>

It is normal during development for levels to occasionally not have lighting built. However, lighting should always be built when doing a test/internal/shipping build or any build that is to be distributed.

### No Player Visible Z Fighting <a name="levels--no-visible-z-fighting"></a>

Levels should not have any [z-fighting](https://en.wikipedia.org/wiki/Z-fighting) in all areas visible to the player.

### Marketplace Specific Rules <a name="levels--marketplace-rules"></a>

If a project is to be sold on the UE5 Marketplace, it must follow these rules.

#### Overview Level <a name="levels--marketplace-rules--overview"></a>

If your project contains assets that should be visualized or demoed, you must have a map within your project that contains the name "Overview".

This overview map, if it is visualizing assets, should be set up according to [Epic's guidelines](http://help.epicgames.com/customer/en/portal/articles/2592186-marketplace-submission-guidelines-preparing-your-assets#Required%20Levels%20and%20Maps).

For example, `InteractionComponent_Overview`.

#### Demo Level <a name="levels--marketplace-rules--demo"></a>

If your project contains assets that should be demoed or come with some sort of tutorial, you must have a map within your project that contains the name "Demo". This level should also contain documentation in some form that illustrates how to use your project. See Epic's Content Examples project for good examples of how to do this.

If your project is a gameplay mechanic or other form of system as opposed to an art pack, this can be the same as your "Overview" map.

For example, `InteractionComponent_Overview_Demo`, `ExplosionKit_Demo`.

**[⬆ Back to Top](#table-of-contents)**

## 9. Textures <a name="textures"></a>

This section will focus on Texture assets and their internals.

### Dimensions Are Powers of 2 <a name="textures--dimensions"></a>

All textures, except for UI textures, must have dimensions in multiples of powers of 2. Textures do not have to be square.

For example, `128x512`, `1024x1024`, `2048x1024`, `1024x2048`, `1x512`.

### Texture Density Should Be Uniform <a name="textures--uniform-density"></a>

All textures should be of a size appropriate for their standard use case. Appropriate texture density varies from project to project, but all textures within that project should have a consistent density.

For example, if a project's texture density is 8 pixels per 1 unit, a texture that is meant to be applied to a 100x100 unit cube should be 1024x1024, as that is the closest power of 2 that matches the project's texture density.

### Textures Should Be No Bigger Than 8192 <a name="textures--maximum-size"></a>

No texture should have a dimension that exceeds 8192 in size unless you have a very explicit reason to do so. Often, using a texture this big is simply just a waste of resources.

### Textures Should Be Grouped Correctly <a name="textures--groups"></a>

Every texture has a Texture Group property used for LODing, and this should be set correctly based on its use. For example, all UI textures should belong in the UI texture group.

**[⬆ Back to Top](#table-of-contents)**

## Major Contributors

* [Michael Allar](http://allarsblog.com): [GitHub](https://github.com/Allar)
* [CosmoMyzrailGorynych](https://github.com/CosmoMyzrailGorynych)
* [billymcguffin](https://github.com/billymcguffin)
* [akenatsu](https://github.com/akenatsu)

**[⬆ Back to Top](#table-of-contents)**

## Amendments

We encourage you to fork this guide and change the rules to fit your team's style guide. Below, you may list some amendments to the style guide. This allows you to periodically update your style guide without having to deal with merge conflicts.
