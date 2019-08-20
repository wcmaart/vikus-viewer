# Williams College Museum of Art Collection Explorer (VIKUS Viewer)

WCMA wants a way for faculty and staff to browse the 13300+ items in the collection, to find specific items and discover new ones that might be useful in their curricula. 

For this Collection Explorer, we're extending [VIKUS Viewer](https://github.com/cpietsch/vikus-viewer), an open-source tool that uses [D3.js](https://d3js.org/), [PIXI.js](https://pixijs.com/), and a little bit of [Vue](https://vuejs.org/) to render sortable, filterable views of very large data collections.

## Setup

This is a fork of the original VIKUS Viewer repository (see the original README below). 

The folder structure here should be
```
css
data
font
img
index.html
js
README.md
thumbs
```

Because the data is so large, you may not see a `data` or a `thumbs` here. We've hidden them in `.gitignore`, and put `data` in another repo:  
<https://github.com/wcmaart/vikus-viewer-data>

Please either
1. pull down `data` separately (manually download it from github) and put it inside this folder, or
2. _(✨preferred)_ set up a `vikus-viewer-data` repo _outside_ this `vikus-viewer` repo (in the same parent folder), and set up a symbolic link / alias at `vikus-viewer/data` to `vikus-viewer-data/data`, e.g. from the parent folder:  
`ln -s ./vikus-viewer-data/data ./vikus-viewer/data`

ℹ️ In the future, we'll look into setting this up as a git submodule so that it can be independently pulled down.

The `thumbs` folder can be huge (~1-6GB), so we do the same for that: put it in a parent or sibling directory and symbolic link it inside this repository.  

ℹ️ In the future, we may want to put this data into GitLFS? _Where does it live currently?_

## Previewing builds

The latest working (🤞) preview build will be in the `preview` branch:  
<https://github.com/wcmaart/vikus-viewer/tree/preview>

To run it, switch into the directory and run a localhost server. From the root of this repo, you can run [MAMP](https://www.mamp.info/en/) (php) or [sirv-cli](https://github.com/lukeed/sirv/tree/master/packages/sirv-cli) (node), or simply use the basic PHP development server: 
```bash
php -S localhost:4444
```

## Progress and status

_We will delete this section, or replace it with a new to-do list when complete._

1. ~~get the branch working and document in the README.md how to set up (1 hr)~~
2. reconcile the `tms_id` and get the color info in the `kibana.txt` (split into HSV) (2 hr)
3. set it up so that chad can determine sort criteria in a config file (1 hr)
4. reconcile the big json and the csv to see how they correspond, how they work (1 hr)
5. stacklayout for grid view with proper filtering (1.5 hr)
6. see if we can cluster items / have multiples of same item (2 hrs)
   - _…look into d3 bubble chart (or circle-packing) code, then see if we can have multiple instances of the same artwork on the canvas_
7. if not, then filter by second set of tags (3 hrs, 2 parts)
   - do filtering (1.5 hr) 
   - add another row or tab at top (1.5 hr)

📚
----

![VIKUS Viewer](https://vikusviewer.fh-potsdam.de/assets/teaser.png)

# VIKUS Viewer

[VIKUS Viewer](https://vikusviewer.fh-potsdam.de/) is a web-based visualization system that arranges thousands of cultural artifacts on a dynamic canvas and supports the exploration of thematic and temporal patterns of large collections, while providing rapid access to high-resolution imagery.

## Documentation

This repo contains the HTML, CSS and JS of the VIKUS Viewer software. To get started you will have to clone this repo and run a webserver. We recommend nginx for production, but any web server will work too. There is no server-side logic necessary. To minimize the loading time your should make use of GZIP compression on JS and CSV files. Also enable HTTP/2, since Multiplexing will heavily help with loading all those image assets.

### Metadata

To use the VIKUS Viewer for a custom image collection, you need to prepare metadata files that describe the collection and objects, and configure the visualization. To get started, you first need to create a ```/data``` folder which will contain all metadata and image files. Have a look at the metadata generated for the [Van Gogh collection](https://github.com/cpietsch/vikus-viewer-data/tree/master/vangogh) (Van Gogh Museum) as a reference for the following descriptions.

#### [config.json](https://github.com/cpietsch/vikus-viewer-data/blob/master/vangogh/config.json)

This is the configuration file that defines the project name, data URLs, columns, styles, and what is shown in the detail sidebar of your collection. The URLs don't have to be absolute, but it can be handy if your assets are not hosted on the same server.

#### [data.csv](https://github.com/cpietsch/vikus-viewer-data/blob/master/vangogh/data.csv)

The data.csv holds all the metadata information for each object in the collection. The following fields are mandatory: `id
`, `keywords`, `year`.
- `id` is is linked to the name of the corresponding image. (id: 123 -> 123.jpg)
- `keywords` comma seperated list of keywords for the tags on the top
- `year` can be a number or a string, will be sorted ascending
- `_fields` these are custom metadata fields (note the prefixed underscore)

All of the columns are beeing sticked together to enable the freetext search.

#### [timeline.csv](https://github.com/cpietsch/vikus-viewer-data/blob/master/vangogh/timeline.csv)

The timeline.csv holds the information for the timeline displayed underneath the years.
- `year` can be a number or a string, is linked to the `year` field in data.csv
- `title` the headline of the blurb
- `text` first detail text when zoomed in a little bit
- `extra` additional text when zoomed to the maximum

#### [info.md](https://github.com/cpietsch/vikus-viewer-data/blob/master/vangogh/info.md)

This is the information displayed on the left side when opening the visualization. You can put in any kind of [Markdown](https://marked.js.org/).


### Images

Apart from the metadata, you need to preprocess the image files, i.e., to generate sprites and textures for the different zoom levels. Please see the  [vikus-viewer-script](https://github.com/cpietsch/vikus-viewer-script) for the details. After running the script you can place the resulting folders into ```/data``` or any other location. Make sure that the texture URLs in the config.json point to these folders.

### t-SNE

As an alternative to the temporal view, you can create a t-SNE layout based on image similarity. The script in [vikus-viewer-script](https://github.com/cpietsch/vikus-viewer-script) creates a tsne.csv which can be put next to the data.csv in the `/data` folder. Image similarity is calculated via the [imagenet activation logit](https://beta.observablehq.com/@cpietsch/imagenet-activation-logit) and then run throught t-SNE. An additional spacing step ensures no overlaying images in the distribution.

## Credits

VIKUS Viewer was designed and developed by Christopher Pietsch. 
The VIKUS Viewer software is based on the visualization code behind [Past Visions](https://github.com/cpietsch/fw4), a collaborative effort by Katrin Glinka, Christopher Pietsch, and Marian Dörk carried out at the University of Applied Sciences Potsdam in the context of the Urban Complexity Lab during the research project VIKUS (2014-2017). Related Paper: [Past Visions and Reconciling Views]( http://www.digitalhumanities.org/dhq/vol/11/2/000290/000290.html). 
The T-SNE view has been implemented for the [Sphaera project](https://sphaera.mpiwg-berlin.mpg.de/) with funding from [Chronoi-REM](https://www.berliner-antike-kolleg.org/rem)

### Libraries
- [pixi.js](https://github.com/pixijs/pixi.js)
- [d3.js](https://github.com/d3/d3)

## License

You may use VIKUS Viewer under the terms of the MIT License. See http://en.wikipedia.org/wiki/MIT_License for more information.


Copyright (C) 2018 Christopher Pietsch, and contributors

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
 OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
 THE SOFTWARE.
