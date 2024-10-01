# SyncMedia

Editors: Marisa DeMeglio (DAISY), Lars Wallin (Colibrio)

## Relationship to Other Specifications

SyncMedia is an evolution of [EPUB3 Media Overlays](https://www.w3.org/publishing/epub32/epub-mediaoverlays.html) and, like Media Overlays, is built on [[SMIL3]]. Compared to Media Overlays, SyncMedia incorporates additional SMIL concepts, and also includes custom features. 

A more detailed comparison of SyncMedia to both SMIL3 and EPUB3 Media Overlays can be found in the [SyncMedia Explainer](explainer.html#relationship-to-smil3-and-epub-media-overlays).

## SyncMedia

SyncMedia is an XML format for synchronized media presentations. It uses a subset of [[SMIL3]] and also defines its own custom features. SyncMedia files use the filename extension `.sync`. 

The default namespace for SyncMedia is that of SMIL: `http://www.w3.org/ns/SMIL`. 

SyncMedia custom features use the SyncMedia namespace, which is `https://w3.github.io/sync-media-pub`.

::: {.note}
This is a placeholder namespace URL; see [issue 36](https://github.com/w3c/sync-media-pub/issues/36)
:::

This section defines SyncMedia's elements and attributes, and gives examples. 

### Definitions  

<dfn id="dfn-media-object" data-dfn-type="dfn">Media Object</dfn>
: A media resource in the [=Sync Media Document=]

<dfn id="dfn-media-parameters" data-dfn-type="dfn">Media Parameters</dfn>
:   Named parameters to communicate options to the [=Media Object Renderer=]

<dfn id="dfn-media-object-renderer" data-dfn-type="dfn">Media Object Renderer</dfn>
:   A component used by the [=Sync Media Player=] to render [=Media Objects=]. Different media types may necessitate different renderers.

<dfn id="dfn-parallel-time-container" data-dfn-type="dfn">Parallel Time Container</dfn>
:   A [=Time Container=] in which children are rendered in parallel

<dfn id="dfn-role" data-dfn-type="dfn">Role</dfn>
:   Gives the structural semantics for the item

<dfn id="dfn-sequential-time-container" data-dfn-type="dfn">Sequential Time Container</dfn>
:   A [=Time Container=] in which children are rendered in sequence

<dfn id="dfn-sync-media-document" data-dfn-type="dfn">Sync Media Document</dfn>
:   The document containing the SyncMedia presentation.

<dfn id="dfn-sync-media-player" data-dfn-type="dfn">Sync Media Player</dfn>
:   A user agent that knows how to process and playback [=Sync Media Documents=]

<dfn id="dfn-time-container">Time Container</dfn>
:   The container that dictates the playback order for its children

<dfn id="dfn-track">Track</dfn>
:   An organizational concept that defines a purposeful virtual rendering space for media objects, not tied to a visual layout, with default properties


### Document Structure

Each SyncMedia document MUST have a `smil` element as its root. 

A SyncMedia document contains two parts: a `head` and a `body`. The head contains metainformation and track information. The temporal presentation of media objects is laid out in the body. Time containers can be used to render media in parallel or to arrange sequences. 

A SyncMedia document MUST have a `body`. It MAY have a `head`.

| Element | Description |
| --------|-------------|
| `smil`{#smil} | Root element |
| `head`{#head} | Information not related to temporal behavior |
| `body`{#body} | Main [=sequential time container=] for the presentation. |

### Time Containers

Media objects are arranged in time containers, which determine whether they are rendered together (in parallel) or one after the other (in sequence). Time containers MAY be nested in other time containers (but MUST NOT be nested in media objects).

| Element | Description |
|---------|-------------|
| `seq`{#seq} | A [=sequential time container=] for media and/or time containers. |
| `par`{#par} | A [=parallel time container=] for media and/or time containers. | 


{% example "Using time containers to associate text references with audio clips, to create a synchronized text and audio presentation"%}
<smil xmlns="http://www.w3.org/ns/SMIL">
    <body>
        <par>
            <audio src="chapter01.mp3" clipBegin="30" clipEnd="40"/>
            <text src="chapter01.html#heading_01"/>
        </par>
        <par>
            <audio src="chapter01.mp3" clipBegin="40" clipEnd="50"/>
            <text src="chapter01.html#para_01"/>
        </par>
        <par>
            <audio src="chapter01.mp3" clipBegin="50" clipEnd="60"/>
            <text src="chapter01.html#para_02"/>
        </par>
    </body>
</smil>
{% endexample %}


<section id="structural-semantics">
<h4>Structural semantics</h4>
<p>Structural semantics MAY be added to time containers via the <a href="#sync:role"><code>sync:role</code></a> attribute. Values MUST come from <a href="https://www.w3.org/TR/wai-aria/#document_structure_roles">WAI-ARIA Document Structure</a> or <a href="https://www.w3.org/TR/dpub-aria-1.0/">DPUB-ARIA</a>.</p>

<section class="informative">
<h5>Benefits of structural semantics</h5>
<p>There are benefits to applying structural semantics to time containers in SyncMedia. User agents that understand semantic role values MAY customize the user experience, for example by enabling the skipping of types of secondary content that interferes with the flow of narration (such as page number announcements, often included to provide a point of reference between print and digital editions); or escaping complex structures, such as tables or charts.
</p>
</section>
</section>

#### Attributes

| Attribute | Description |
|-----------|-------------|
| `sync:role`{#sync:role} | One or more semantic role(s) | 

 

::: .TODO
__TODO__
[Issue 12](https://github.com/w3c/sync-media-pub/issues/12)
:::

{% example "Using role to mark a page number" %}
<smil xmlns="http://www.w3.org/ns/SMIL" sync:xmlns="https://w3.github.io/sync-media-pub">
    <body>
        <par>
            <audio src="chapter01.mp3" clipBegin="50" clipEnd="60"/>
            <text src="chapter01.html#para_02"/>
        </par>
        <par sync:role="doc-pagebreak">
            <audio src="chapter01.mp3" clipBegin="60" clipEnd="62"/>
            <text src="chapter01.html#pg_04"/>
        </par>
        <par>
            <audio src="chapter01.mp3" clipBegin="62" clipEnd="70"/>
            <text src="chapter01.html#para_03"/>
        </par>
    </body>
</smil>
{% endexample %}

### Media Objects

Media resources are included in SyncMedia via media objects. The actual media resource is an external file, or quite commonly, a segment of a file, such as an audio or video clip, or part of an HTML document.

The table below describes the media objects in SyncMedia. `Ref` can be used to represent any media, but authors often prefer to use media type-specific synonyms.

| Element | Description |
|---------|-------------|
| `audio`{#audio} | References audio media.| 
| `image`{#image} | References image media.|
| `ref`{#ref} | Generic media reference | 
| `text`{#text} | References content in an external text-based document.|
| `video`{#video} | References video media.|

#### Attributes

Attributes on media objects are used to 
* express the location of the media source, including segment
* assign a media object to a [=track=]
* indicate that a media object repeats

| Attribute | Description |
|-----------|-------------|
| `clipBegin`{#clipBegin}| Start of a timed media clip, as in SMIL3's <a data-cite="SMIl3/smil30.html#smil-extended-media-object-adef-clipBegin">clipBegin</a> | 
| `clipEnd`{#clipEnd} | End of a timed media clip, as in SMIL3's <a data-cite="SMIl3/smil30.html#smil-extended-media-object-adef-clipEnd">clipEnd</a> |
| `panZoom`{#panZoom} | Rectangular portion of media object, as in SMIL3's <a data-cite="SMIL3/smil30.html#smil-extended-media-object-adef-panZoom">panZoom</a>|
| `repeatCount`{#repeatCount} | Specifies the number of iterations of a timed media object. Values are a number, or "indefinite", as in SMIL3's <a data-cite="SMIL3/smil-timing.html#adef-repeatCount">repeatCount</a> |
| `src`{#src} | URL of media file, optionally including a media fragment [[media-frags]] | 
| `sync:track`{#sync:trackref} | ID of a `sync:track` element.|


::: {.note}
EPUB Media Overlays <a href="https://www.w3.org/publishing/epub/epub-mediaoverlays.html#app-clock-examples">clock values</a> are considered valid clip begin and end values, because the [SMIL MediaClipping Module](https://www.w3.org/TR/SMIL/smil-extended-media-object.html#smilMediaNS-MediaClipping) states that if no metric specifier is given, Normal Play Time (`npt`) is assumed (not `smpte`).
:::

If both an `src` with a media fragment and `clipBegin`/`clipEnd` attributes are present, clipping MUST be applied to the resource with respect to the media fragment offset(s), as defined in [All Media Fragment Clients](https://www.w3.org/TR/media-frags/#media-fragment-clients). 

::: {.note}
It is RECOMMENDED to use a media fragment on `src` to refer to a large chunk of media; and to use `clipBegin` and `clipEnd` for defining fine-grained clips. This is to separate the requirement on the client of retrieving the resource, perhaps done using a URI request to a server, from locating a segment of the resource, done with [Media Fragments clip start/end points](https://www.w3.org/TR/media-frags/#valid-uri-temporal). Otherwise, if a client is fetching every phrase individually, it would then have to implement complex caching to smooth out playback so as to remove glitching between clips.
:::

#### Embedded media

Embedded media, such as a video in an HTML document, MAY be referenced by the URL of its embedding document plus a selector.

Therefore, [=media object renderer=]s SHOULD support opening an HTML document and dereferencing content based on a selector.

{% example "Referring to video media contained within an HTML document" %}
<par>
    <text src="doc.html#para1"/>
    <video src="doc.html#video1" clipBegin="0" clipEnd="10"/>
</par>
{% endexample %}



#### Parameters

SyncMedia uses SMIL3's <a data-cite="SMIL3/smil30.html#smil-extended-media-object-edef-param">param</a> to send parameters to [=media object renderer=]s.

| Element | Description |
|---------|-------------|
| `param`{#param} | Media object rendering parameter. |

The attributes for `param` are:

| Attribute | Description |
|-----------|-------------|
| `name`{#name} | Parameter name |
| `value`{#value} | Parameter value |

The following parameter `name` values are defined:

| Name | Allowed value(s) | Description | For media object(s) | 
|------| ------|------------|-------------|
| `cssClass` | One or more strings | Indicates class name(s) to apply | Media that can be styled with CSS | 
| `clipPath` | As defined by the [SVG path data attribute](https://www.w3.org/TR/SVG11/paths.html#DAttribute) | The shape that will be used to apply a clip mask to the media | Visual media |
| `pan` | Between -1 (full left) and 1 (full right) | Indicates the left/right pan | Audible media |
| `playbackRate` | 1.0 (normal rate), less, or more | Indicates the playback rate. Values SHOULD align with HTML's {%raw%}{{HTMLMediaElement/playbackRate}}{%endraw%}. | Timed media |
| `volume` | Between 0 and 1 | Indicates the volume | Audible media |

::: {.note}
`clipPath` specifies a clipping path using an SVG path definition. The clipping is applied to the visible region of the Media Object on which it is defined. When combined with `panZoom`, the `clipPath` SHOULD be applied inside the rect defined by the `panZoom` attribute.
:::

{% example "Using param to add synchronized highlighting to HTML element" %}
<smil xmlns="http://www.w3.org/ns/SMIL">
    <body>
        <par>
            <audio src="chapter01.mp3" clipBegin="30" clipEnd="40"/>
            <text src="chapter01.html#heading_01">
                <param name="cssClass" value="highlight"/>
            </text>
        </par>
        <par>
            <audio src="chapter01.mp3" clipBegin="40" clipEnd="50"/>
            <text src="chapter01.html#para_01">
                <param name="cssClass" value="highlight"/>
            </text>
        </par>
        <par>
            <audio src="chapter01.mp3" clipBegin="50" clipEnd="60"/>
            <text src="chapter01.html#para_02">
                <param name="cssClass" value="highlight"/>
            </text>
        </par>
    </body>
</smil>
{% endexample %}


### Tracks

SyncMedia presentations organize media objects of the same types into virtual spaces called "tracks". Tracks MUST be placed in the SyncMedia document `head`. Tracks have several useful features:

1. A track MAY provide default [params](#param) that then get applied to any media object on that track. 
2. A track MAY be set as the default for a given media object type (e.g. all the `audio` media objects can be automatically assigned to a track).
3. A track MAY have a default source for all its media objects to use, in combination with any fragment specifier on the media object itself.

All of these features reduce verbosity as otherwise these properties would have to be explicitly stated on each media object. 

| Element | Description |
|---------|-------------|
| `sync:track`{#sync:track} | A virtual space to which [=Media Objects=] are assigned. A user agent MAY offer interface controls on a per-track basis (e.g. adjust volume on the narration track). A `sync:track` MAY have [=media parameters=], which act as defaults for [=Media Objects=] on that track.  |

#### Attributes

| Attribute | Description |
|-----------|-------------|
| `sync:label`{#sync:label} | The track's label |
| `sync:defaultSrc`{#sync:defaultSrc} | URL of the default file that media objects on this track will use.|
| `sync:defaultFor`{#sync:defaultFor} | Media objects of the type specified (one of: `audio`, `image`, `video`, `text`, `ref`) are automatically assigned to this track. |
| `sync:trackType`{#sync:trackType} | Indicates which presentation feature is embodied by this track. |

::: .TODO
__TODO__:
[Issue 31](https://github.com/w3c/sync-media-pub/issues/31)
:::


{% example "A track for an HTML document with default values and a cssClass param" %}
<smil xmlns="http://www.w3.org/ns/SMIL" sync:xmlns="https://w3.github.io/sync-media-pub">
    <head>
        <sync:track sync:label="Page" sync:defaultFor="text" 
            sync:defaultSrc="chapter01.html" sync:trackType="contentDocument">
            <param name="cssClass" value="highlight"/>
        </sync:track>
    </head>
    <body>
        <par>
            <audio src="chapter01.mp3" clipBegin="30" clipEnd="40"/>
            <text src="#heading_01"/>
        </par>
        <par>
            <audio src="chapter01.mp3" clipBegin="40" clipEnd="50"/>
            <text src="#para_01"/>
        </par>
        <par>
            <audio src="chapter01.mp3" clipEnd="50" clipEnd="60"/>
            <text src="#para_02"/>
        </par>
    </body>
</smil>
{% endexample %}

{% example "Two audio tracks: one for narration (the default track for audio media objects), and one for background music."%}
<smil xmlns="http://www.w3.org/ns/SMIL" sync:xmlns="https://w3.github.io/sync-media-pub">
    <head>
        <sync:track id="background-music" sync:trackType="backgroundAudio">
            <param name="volume" value="0.5"/>
        </sync:track>
        <sync:track sync:label="Narration" sync:defaultFor="audio" sync:trackType="audioNarration"/>
        <sync:track sync:label="Page" sync:defaultFor="text" sync:trackType="contentDocument">
            <param name="cssClass" value="highlight"/>
        </sync:track>
    </head>
    <body>
        <par>
            <audio sync:track="background-music" src="bkmusic.mp3" repeat="indefinite"/>
            <seq>
                <par>
                    <audio src="chapter01.mp3" clipBegin="30" clipEnd="40"/>
                    <text src="chapter01.html#heading_01"/>
                </par>
                <par>
                    <audio src="chapter01.mp3" clipBegin="40" clipEnd="50"/>
                    <text src="chapter01.html#para_01"/>
                </par>
                <par>
                    <audio src="chapter01.mp3" clipEnd="50" clipEnd="60"/>
                    <text src="chapter01.html#para_02"/>
                </par>
            </seq>
        </par>
    </body>
</smil>
{% endexample %}

The reason for including a narration `sync:track`, even though it supplies no default values, is because it would enable a user agent to have separate controls for narration audio vs background music audio. {.note}


### Metadata
SyncMedia has a generic mechanism for incorporating metadata but does not define any specific metadata. Metadata MUST go in the SyncMedia document `head`.

| Element | Description |
| -----| ----------- |
| `metadata`{#metadata} | Extension point that allows the inclusion of metadata from any metainformation structuring language |


## Playback

### Processing

#### Applying track values to media objects

[=Tracks=] MAY provide defaults for [=media objects=]. This section gives the rules for how to apply these values.

| Track attribute | Impact on media object |
|-----------------|------------------------|
| `sync:defaultSrc` | Provides the `src` for the media object. If the media object has an `src` which is only a selector, then the selector is appended to the track's `sync:defaultSrc`. Any other value for a media object `src` overrides the track's `sync:defaultSrc`. |

In addition, any [=media parameters=] defined for a track are inherited by any media objects on that track. The exception is when the media objects themselves provide a parameter of the same `name`, in which case, the media object's parameter `value` overrides the track's parameter `value`.

### Rendering

After the SyncMedia document has been processed, it is ready to be rendered.

| Element | Rendering behavior | 
|--------|--------------------|
| `body` | Render like `seq`  |
| `seq` | Render each child in order, each starting after the previous completes. Done when the last child is finished.|
| `par` | Render each child at the same time. Done when all the children are finished.|
| `audio` | Play the referenced portion of audio media and apply `params`. Done when the referenced portion has finished. |
| `image` | Load the image file or segment and apply `params`. Not timed, so considered done immediately. |
| `ref` | Infer the media type and, if supported, render the file or segment, and apply `params`. If timed, done when the segment is finished; if untimed, done immediately. |
| `text` | Display the HTML document, ensure the referenced element is visible, and apply `params`. Not timed, so considered done immediately. |
| `video` | Play the video file or segment and apply `params`. Done when the segment is finished. |


Note about media with `repeatCount` and when it's considered done {.note}


### User Interaction

::: .TODO
__TODO__: how much to cover here?
:::

* Moving through the presentation meaningfully, e.g. previous/next sentence or para
* Exposing controls for multitrack presentations
* Note about global adjustments and track types (indicated by `trackType`) that they might not make sense for, e.g. speeding up a presentation but not speeding up the background music.

## XML

### Additional attributes

In addition to the attributes already covered, this section adds the following standard XML attributes:

| Attribute | Description |
|-----------|-------------|
| `xml:base`{#xml:base} | Document base URL, as defined in [[XMLBASE]] |
| `xml:id`{#xml:id} | Unique identifier for an element, as defined in [[XML-ID]]|
| `xml:lang`{#xml:lang} | Language identifier, as defined in [[XML]] |

### Content model

This is the XML content model for SyncMedia. Required elements and attributes are indicated.

<table summary="XML content model for SyncMedia">
    <thead><tr><th>Element</th><th>Attributes</th><th>Content</th></tr></thead>
    <tbody>
        <tr>
            <td><a href="#smil">`smil`</a> <span class="deemph">(required)</span></td>
            <td>
                <ul>
                    <li><a href="#xml:base">`xml:base`</a></li>
                    <li><a href="#xml:id">`xml:id`</a></li>
                    <li><a href="#xml:lang">`xml:lang`</a></li>
                </ul>
            </td>
            <td>
                In this order:
                <ul>
                    <li><a href="#head">`head`</a> (0 or 1)</li>
                    <li><a href="#body">`body`</a> (exactly 1)</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td><a href="#head">`head`</a></td>
            <td>
                <ul>
                    <li><a href="#xml:id">`xml:id`</a></li>
                    <li><a href="#xml:lang">`xml:lang`</a></li>
                </ul>
            </td>
            <td>
                In any order:
                <ul>
                    <li><a href="#metadata">`metadata`</a> (0 or more)</li>
                    <li><a href="#sync:track">`sync:track`</a> (0 or more)</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td><a href="#metadata">`metadata`</a></td>
            <td>
                <ul>
                    <li><a href="#xml:id">`xml:id`</a></li>
                    <li><a href="#xml:lang">`xml:lang`</a></li>
                </ul>
            </td>
            <td>0 or more elements from any namespace</td>
        </tr>
        <tr>
            <td><a href="#sync:track">`sync:track`</a></td>
            <td>
                <ul>
                    <li><a href="#sync:label">`sync:label`</a> <span class="deemph">(required)</span></li>
                    <li><a href="#sync:defaultSrc">`sync:defaultSrc`</a></li>
                    <li><a href="#sync:defaultFor">`sync:defaultFor`</a></li>
                    <li><a href="#sync:trackType">`sync:trackType`</a></li>
                    <li><a href="#xml:id">`xml:id`</a></li>
                    <li><a href="#xml:lang">`xml:lang`</a></li>
                </ul>
            </td>
            <td>
                <ul>
                    <li><a href="#param">`param`</a> (0 or more) </li>
                </ul>
            </td>
        </tr>
        <tr>
            <td><a href="#param">`param`</a></td>
            <td>
                <ul>
                    <li><a href="#name">`name`</a> <span class="deemph">(required)</span></li>
                    <li><a href="#value">`value`</a> <span class="deemph">(required)</span></li>
                    <li><a href="#xml:id">`xml:id`</a></li>
                    <li><a href="#xml:lang">`xml:lang`</a></li>
                </ul>
            </td>
            <td>Empty</td>
        </tr>
        <tr>
            <td><a href="#body">`body`</a></td>
            <td>
                <ul>
                    <li><a href="#sync:role">`sync:role`</a></li>
                    <li><a href="#xml:id">`xml:id`</a></li>
                    <li><a href="#xml:lang">`xml:lang`</a></li>
                </ul>
            </td>
            <td>
                In any order:
                <ul>
                    <li><a href="#audio">`audio`</a> (0 or more)</li>
                    <li><a href="#image">`image`</a> (0 or more)</li>
                    <li><a href="#par">`par`</a> (0 or more)</li>
                    <li><a href="#ref">`ref`</a> (0 or more)</li>
                    <li><a href="#seq">`seq`</a> (0 or more)</li>
                    <li><a href="#text">`text`</a> (0 or more)</li>
                    <li><a href="#video">`video`</a> (0 or more)</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td><a href="#seq">`seq`</a></td>
            <td>
                <ul>
                    <li><a href="#sync:role">`sync:role`</a></li>
                    <li><a href="#xml:id">`xml:id`</a></li>
                    <li><a href="#xml:lang">`xml:lang`</a></li>
                </ul>
            </td>
            <td>
                In any order:
                <ul>
                    <li><a href="#audio">`audio`</a> (0 or more)</li>
                    <li><a href="#image">`image`</a> (0 or more)</li>
                    <li><a href="#par">`par`</a> (0 or more)</li>
                    <li><a href="#ref">`ref`</a> (0 or more)</li>
                    <li><a href="#seq">`seq`</a> (0 or more)</li>
                    <li><a href="#text">`text`</a> (0 or more)</li>
                    <li><a href="#video">`video`</a> (0 or more)</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td><a href="#par">`par`</a></td>
            <td>
                <ul>
                    <li><a href="#sync:role">`sync:role`</a></li>
                    <li><a href="#xml:id">`xml:id`</a></li>
                    <li><a href="#xml:lang">`xml:lang`</a></li>
                </ul>
            </td>
            <td>
                In any order:
                <ul>
                    <li><a href="#audio">`audio`</a> (0 or more)</li>
                    <li><a href="#image">`image`</a> (0 or more)</li>
                    <li><a href="#par">`par`</a> (0 or more)</li>
                    <li><a href="#ref">`ref`</a> (0 or more)</li>
                    <li><a href="#seq">`seq`</a> (0 or more)</li>
                    <li><a href="#text">`text`</a> (0 or more)</li>
                    <li><a href="#video">`video`</a> (0 or more)</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td><a href="#audio">`audio`</a></td>
            <td>
                <ul>
                    <li><a href="#clipBegin">`clipBegin`</a></li>
                    <li><a href="#clipEnd">`clipEnd`</a></li>
                    <li><a href="#repeatCount">`repeatCount`</a></li>
                    <li><a href="#src">`src`</a> <span class="deemph">(required)</span></li>
                    <li><a href="#sync:trackref">`sync:track`</a></li>
                    <li><a href="#xml:id">`xml:id`</a></li>
                    <li><a href="#xml:lang">`xml:lang`</a></li>
                </ul>
            </td>
            <td>
                <ul>
                    <li><a href="#param">`param`</a> (0 or more)</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td><a href="#image">`image`</a></td>
            <td>
                <ul>
                    <li><a href="#src">`src`</a> <span class="deemph">(required)</span></li>
                    <li><a href="#sync:trackref">`sync:track`</a></li>
                    <li><a href="#panZoom">`panZoom`</a></li>
                    <li><a href="#xml:id">`xml:id`</a></li>
                    <li><a href="#xml:lang">`xml:lang`</a></li>
                </ul>
            </td>
            <td>
                <ul>
                    <li><a href="#param">`param`</a> (0 or more)</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td><a href="#ref">`ref`</a></td>
            <td>
                <ul>
                    <li><a href="#clipBegin">`clipBegin`</a></li>
                    <li><a href="#clipEnd">`clipEnd`</a></li>
                    <li><a href="#panZoom">`panZoom`</a></li>
                    <li><a href="#repeatCount">`repeatCount`</a></li>
                    <li><a href="#src">`src`</a> <span class="deemph">(required)</span></li>
                    <li><a href="#sync:trackref">`sync:track`</a></li>
                    <li><a href="#xml:id">`xml:id`</a></li>
                    <li><a href="#xml:lang">`xml:lang`</a></li>
                </ul>
            </td>
            <td>
                <ul>
                    <li><a href="#param">`param`</a> (0 or more)</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td><a href="#text">`text`</a></td>
            <td>
                <ul>
                    <li><a href="#src">`src`</a> <span class="deemph">(required)</span></li>
                    <li><a href="#sync:trackref">`sync:track`</a></li>
                    <li><a href="#xml:id">`xml:id`</a></li>
                    <li><a href="#xml:lang">`xml:lang`</a></li>
                </ul>
            </td>
            <td>
                <ul>
                    <li><a href="#param">`param`</a> (0 or more)</li>
                </ul>
            </td>
        </tr>
        <tr>
            <td><a href="#video">`video`</a></td>
            <td>
                <ul>
                    <li><a href="#clipBegin">`clipBegin`</a></li>
                    <li><a href="#clipEnd">`clipEnd`</a></li>
                    <li><a href="#panZoom">`panZoom`</a></li>
                    <li><a href="#repeatCount">`repeatCount`</a></li>
                    <li><a href="#src">`src`</a> <span class="deemph">(required)</span></li>
                    <li><a href="#sync:trackref">`sync:track`</a></li>
                    <li><a href="#xml:id">`xml:id`</a></li>
                    <li><a href="#xml:lang">`xml:lang`</a></li>
                </ul>
            </td>
            <td>
                <ul>
                    <li><a href="#param">`param`</a> (0 or more)</li>
                </ul>
            </td>
        </tr>
    </tbody>
</table>

## Acknowledgements {.appendix}

At the time of publication, the members of the [Synchronized Multimedia for Publications Community Group](https://www.w3.org/community/sync-media-pub/) were:

Avneesh Singh (DAISY Consortium), Ben Dugas (Rakuten, Inc.), Chris Needham (British Broadcasting Corporation), Daniel Weck (DAISY Consortium), Didier Gehrer, Farrah Little (BC Libraries Cooperative), George Kerscher (DAISY Consortium), Ivan Herman (W3C), James Donaldson, Lars Wallin (Colibrio), Livio Mondini, Lynn McCormack (CAST, Inc), Marisa DeMeglio (DAISY Consortium, chair), Markku Hakkinen (Educational Testing Service), Matt Garrish (DAISY Consortium), Michiel Westerbeek (Tella), Nigel Megitt (British Broadcasting Corporation), Romain Deltour (DAISY Consortium), Wendy Reid (Rakuten, Inc.), Zheng Xu (Rakuten, Inc.)
