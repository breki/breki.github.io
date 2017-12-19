# Azurite Mapmaking Knowledgebase

## Features
### Showing villages
By default, villages are shown at zoom level 12 (1 : 144,000) and above. To configure at what zoom level to show villages, add `village-min-zoom` custom property:
```xml 
<props>
  <village-min-zoom>13</village-min-zoom>
</props>
```
### Showing hamlets
By default, hamlets are shown at zoom level 13 (1 : 72,000) and above. To configure at what zoom level to show villages, add `hamlet-min-zoom` custom property:
```xml 
<props>
  <hamlet-min-zoom>13</hamlet-min-zoom>
</props>
```
## Sea Topology
### Troubleshooting
There are several mechanisms for troubleshooting problems with sea topology:
1. using `coastline` map theme to just work on coastline.
1. using the debug mode when rendering, which renders the tiles grid, tile IDs and tile types.
1. `SeaTileGenerator.GenerateNonEmptyTile()` method contains (commented) catch block that catches exceptions that happen when there is an error in clipping coastline polylines for a given tile. The code produces GeoJSON output for that tile which can be analyzed in a [GeoJSON viewer](http://geojson.io).
1. `SeaTopologyResolver.ResolveLevel()` method catches exceptions and renders the topology into a `seatopo.png` bitmap file.
1. `SeaTopologyGenerator.GenerateSeaTopology()` method contains an empty catch block that just rethrows the exception. If the `throw;` is commented, the sea topology is still rendered (albeit in an incomplete way). 

## Toponyms
### Using default toponym names instead of international ones
By default, international toponym names are used (if they exist). To make a map using the default (local) names, add `use-default-names-only` custom property:
```xml 
<props>
  <use-default-names-only>True</use-default-names-only>
</props>
```

### Transliterations

#### Non-English characters to English
Sometimes, a map covering the English-speaking country (probably erroneously) shows a placename with non-English character (like San José in California). To convert these characters to English alphabet, use `nonenglish-to-english` custom property:
```xml 
<props>
  <nonenglish-to-english>True</nonenglish-to-english>
</props>
```

#### Cyrillic to Latin
By default, Cyrillic script is not transliterated into Latin. To enable this transliteration, add `cyrillic_to_latin` custom property with a value specifying the Cyrillics culture ID:
```xml 
<props>
  <cyrillic-to-latin>sr</cyrillic-to-latin>
</props>
```
Currently, transliteration into two Cyrillic cultures are supported:
1. `bel` : Belarusian (using [BGN/PCGN romanization of Belarusian](https://en.wikipedia.org/wiki/BGN/PCGN_romanization_of_Belarusian))
2. `sr`: Serbian

## Text

### Showing Cyrillic labels
The default font CantoraOne used on gmap style for neighborhoods and suburbs does not support Cyrillic characters, so maps that show Cyrillic labels should switch to using a different font (like Cuprum) by setting the `suburb-neighborhood-font` custom property:
```xml 
<props>
  <suburb-neighborhood-font>Cuprum</suburb-neighborhood-font>
</props>
```


### Labels are too far from symbols
![](/azurite/img/labels-too-far-from-symbols.png?raw=true)

This can happen when **superfast** level of detail is used, since in that mode the placement algorithm chooses 
the initial label placement as the final one.

### Illustrator reports "Certain alternate glyphs are not available in this version of Illustrator"
This happens if some characters in the SVG text cannot be rendered by the font used. I had this problem with Roman numerals (entered as their special Unicode characters), for which I had to implement `RomanNumeralsTransliterator`.

Too see what characters are used in the document, run `TtfFileReadingTests.DocumentCharacterAnalysis` test case.