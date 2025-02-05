# Farming Simulator XML Tools

This contains some quick python tools to make some aspects of modding a little easier

To install just download the python script you want (or all of them).  They have no dependencies.

If for some reason drag-and-drop to the python script doesn't work, make sure you have python files set to open with the python interpreter, if that doesn't work, repair your installation / re-install

---

## compareTranslations.py

This will compare two (or more) translation files to each other, and tell you if there are translated entities that do not appear in all files.

__Usage:__

```shell
$ python .\compareTranslations.py --folder ./testFiles/translations

$ python .\compareTranslations.py .\testFiles\translations\translation_en.xml .\testFiles\translations\translation_de.xml
```

### Translation file format
By default, compareTranslations.py expects a file of the format:

```xml
<l10n>
  <translationContributors>JTSage</translationContributors>

  <texts>
    <text name="title_simpleInspector" text="Simple Inspector" />
  </texts>
</l10n>
```

If you have a file that uses the other format, you can use the `--giants_ekv` command line argument

```xml
<l10n><elements>
  <e k="some_tag" v="Some Translated Text" tag="0"/>
</elements></l10n>
```

### Sample Output

```text
Files Found: 2
WARNING: 'translation_en.xml' contains a duplicate entry for 'input_SimpleInspector_reload_config'
Mismatch(es) Found:

  Text Name : input_SimpleInspector_reload_config
   Found In      : ['translation_en.xml']
   Not Found In  : ['translation_de.xml']

There are mismatched translations
```

---

## comparei3dMappings.py

This will compare the `<i3dMappings>` section in your vehicle / implement / placable XML files and tell you what is different.  Useful when using the same I3D file for multiple store items.

__Usage:__

```shell
$ python .\comparei3dMappings.py .\testFiles\i3dMap\mrplow_light_5.xml .\testFiles\i3dMap\mrplow_heavy_5.xml
```

Requires at least 2 files.

### Sample Output

```text
Files Found: 4

Mismatch(es) Found (missing):

  Text Name : mrplow_component5
   Found In      : ['mrplow_heavy_5.xml', 'mrplow_light_5.xml']
   Not Found In  : ['mrplow_light_3.xml', 'mrplow_heavy_3.xml']

Mismatch(es) Found (different):

  ID Name : mrplow_vis
   Mappings : [{'mrplow_heavy_5.xml': '0>0'}, {'mrplow_light_3.xml': '0>0'}, {'mrplow_light_5.xml': '0>0'}, {'mrplow_heavy_3.xml': '0>40'}]

There are mismatched I3D Mappings
```

---

## i3dMapper.py

This is just an i3d mapper, built to be fast in python

__Usage:__

```shell
$ python .\i3dMapper.py .\testFiles\i3d\mrplow.i3d
```

__Options:__

* `--no_pretty_print` Do not line up the "node" entries on output.  Pretty print is on by default.

### Sample Output

```xml
<i3dMappings>
  <i3dMapping id="mrplow_component1"          node="0>" />
  <i3dMapping id="mrplow_vis"                 node="0>0" />
  <i3dMapping id="ai"                         node="0>0|0" />
</i3dMappings>
```

---

## checkLiterals.py

Check string literals in vehicles / placable XML files to see if you missed any of the obvious $l10n translations

__Usage:__

```shell
$ python .\checkLiterals.py .\testFiles\i3dMap\mrplow_light_5.xml .\testFiles\i3dMap\mrplow_heavy_5.xml
```

### Sample Output

```text
Files Found: 2
Possible missed translation in tag: designConfiguration, current value is: Amazone
Possible missed translation in tag: designConfiguration, current value is: CaseIH
...
File: mrplow_heavy_5.xml HAS detected missing translations

done.
```

---

## i3dChecker.py

Check an i3d file for some common mistakes.  Looks at real lights, collisons, linked lights, and shadow maps.

__Usage:__

```shell
$ python .\i3dChecker.py .\testFiles\i3d\mrplow.i3d
```

__Options:__

* __--no-shadow-check__  Disable checking visible shapes for shadow maps
* __--no-light-check__   Disable checking linked lights
* __--no-light-info__    Disable output of light info
* __--no-col-info__      Disable checking collision info


### Sample Output - Shadow Maps

```text
hasShadowMap | castsShadowMap on renderable shapes:
  Node Name: cw_crumble_hanger
   Casts Shadow Map: no | Receives Shadow Map: no
  Node Name: decalAmazone_02
   Casts Shadow Map: no | Receives Shadow Map: yes
```

### Sample Output - Old Linked Lights

```text
Links to old lights in i3d:
  Linked Light with fileId '5' is 'rearLightOvalWhite_01', should be 'rearLight26White'
```

### Sample Output - RealLights

```text
RealLights Information:
  Node Name: frontLightLow
   Type: spot, Color: #D8D8FF, Shadows: no, Range: 20 m, Cone Angle: 80 °, Drop Off: 3 m
```

### Sample Output - Unknown collisionMask

```text
Unknown, uncommon, or depreciated collisionMasks:
  collisionMask for 'torion1914_main_component1' (1075851266)/(0x40203002) is unknown, and should be checked
```

### Sample Output - Unusual collisionMask

```text
Unknown, uncommon, or depreciated collisionMasks:
  collisionMask for 'mixerWagonHUDTrigger' (3170304)/(0x306000) is unusual, but does exist in some giants files
```

### Sample Output - Depreciated collisionMask bit

```text
Unknown, uncommon, or depreciated collisionMasks:
  collisionMask for 'torion1914_main_component1' (6303746)/(0x603002) uses depreciated bits and needs corrected
```
