## Hello! 👋

This excercise will ultimately be a part of the talk I'm preparing for Practical Cartography Day at NACIS 2024 in Tacoma.

Here's the Abstract: 

> Ever wondered how you can create custom web maps for your portfolio, passion project, or job WITHOUT setting up a tile server, web server, or cloud services account? Hoping to do it without too much coding or cost? Got some maps you made and just want to display with a little bit of interactivity? This talk will walk you quickly through what you need to do to create and show interactive web maps serverlessly with PMtiles, Protomaps JS, and MapLibre.

This work is informed by ongoing efforts to rehab old portfolio pieces of mine (see [this repo](https://github.com/mizmay/RentAffordability) for example). I'm undertaking it because I think converting GIS projects to portfolio pieces *as web maps* should be easier!

Follow these steps to help test my theory:
1. Fork or clone this repo to your Github account
2. Get the example working via Github Pages from your own account
3. Get the example working locally via your personal computer
4. Swap out the data with your own PMTiles archive (and interactivity layer)
5. Edit the reference map layers via the stylesheet
6. File a PR to share your map back to the projects page [ WIP ]
   
### 1. Fork or clone this repo to your Github account

Forking this project will copy this project over to your Github account, but if you can't or don't want to, cloning it is fine too. This just means you will be starting with a local copy on your personal computer.

**Fork** by pressing the **Fork** button near the top of this page and following the prompts.

**Clone** by pressing the **<> Code button**. For our purposes this just means download your own copy. If you are unfamiliar with Github, the simpest way to do this is to "Download Zip", however if you opt for this, you will need to get a version of this repo into your own Github account to move on to step 2, or you can skip to step 3 for now. 

### 2. Get the example working via Github Pages from your own account

Once you have a copy of this repo in your own account, open it on the web and click **Settings**.

Under the **Code and Automation** panel, click **Pages**

Under **Branch**, select "main", then press **Save**

Click on **Visit Site** near the top. You should now be able to see it working via a URL like "https://<yourgithubhandle>.github.io/WebMapStarterKit/".

[Click here](https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site#creating-your-site) for more detailed instructions on Github Pages.

### 3. Get the example working locally via your personal computer

Clone your copy of this repo to your local hard drive if you haven't already. 

Open up `index.html` and change the title. Commit and push to see the change via Github Pages.

For developing locally, I use [Visual Studio Code](https://code.visualstudio.com/) with [Live Preview](https://marketplace.visualstudio.com/items?itemName=ms-vscode.live-server) installed, but you can use any text editor for this, and preview your changes by setting up a local server (assuming you have [Node](https://runjs.app/blog/how-to-start-a-node-server) or [Python](https://realpython.com/python-http-server/) installed).

### 4. Swap out the data with your own PMTiles archive (and interactivity layer)

When you prepare your data in your favorite GIS environment, include attributes for anything you want to style variably. 

In the example case, I styled the counties as red or blue based on whether the Republican or Democrat won> I used hex values; the last two digits define opacity based on relative population density, so the logic goes something like this:

```
CASE
 WHEN "party" = 'DEMOCRAT' THEN
    CASE
        WHEN "votes_sqkm" <= 10 THEN '#0015BC10'
        WHEN "votes_sqkm" > 10 AND "votes_sqkm" <= 115 THEN '#0015BC20'
        WHEN "votes_sqkm" > 115 AND "votes_sqkm" <= 600 THEN '#0015BC60'
        WHEN "votes_sqkm" > 600 AND "votes_sqkm" <= 3000 THEN '#0015BC80'
        WHEN "votes_sqkm" > 3000 THEN '#0015BC'
    END
WHEN "party" = 'REPUBLICAN' THEN
    CASE
        WHEN "votes_sqkm" <= 10 THEN '#DE010010'
        WHEN "votes_sqkm" > 10 AND "votes_sqkm" <= 115 THEN '#DE010020'
        WHEN "votes_sqkm" > 115 AND "votes_sqkm" <= 600 THEN '#DE010060'
        WHEN "votes_sqkm" > 600 AND "votes_sqkm" <= 3000 THEN '#DE010080'
        WHEN "votes_sqkm" > 3000 THEN '#DE0100'
    END
END
```

I also created and exported GeoJSONs in QGIS for outlining and labeling the "swing states", aka states that were won by > 3% in 2020.

I created the PMTiles archive using <a href="https://github.com/felt/tippecanoe">Tippecanoe</a> at the command line on OSX, if you are on a Windows machine you may need to search around for solutions to make this work.

Once installed, I created individual PMTiles archives for each of the GeoJSONs I exported from QGIS. I built tiles to cover tile zoom levels from 0 to 7, more than you really need to zoom in on my map but still far less than street level, which saves my archives from getting too big. Then I merged them all together.

```
WebMapStarterKit % tippecanoe -v
tippecanoe v2.53.0

WebMapStarterKit % tippecanoe -z7 --projection=EPSG:4326 -o county_election_results_2020_coterminoususa.pmtiles -l counties county_election_results_2020_coterminoususa.geojson
3108 features, 687694 bytes of geometry and attributes, 126012 bytes of string pool, 0 bytes of vertices, 0 bytes of nodes
  99.9%  7/33/48

WebMapStarterKit % tippecanoe -z7 --projection=EPSG:4326 -o states_swing_2020_coterminoususa.pmtiles -l states states_swing_2020_coterminoususa.geojson               
49 features, 114477 bytes of geometry and attributes, 547 bytes of string pool, 0 bytes of vertices, 0 bytes of nodes
  99.9%  7/23/47

WebMapStarterKit % tippecanoe -z7 --projection=EPSG:4326 -o swing_state_labels.pmtiles -l swing_state_labels swing_state_labels.geojson
7 features, 117 bytes of geometry and attributes, 83 bytes of string pool, 0 bytes of vertices, 0 bytes of nodes
  95.6%  7/33/46  

WebMapStarterKit % tile-join -o MyData.pmtiles *.pmtiles
```


### 5. Edit the reference map layers via the stylesheet

### 6. File a PR to share your map back to the projects page
