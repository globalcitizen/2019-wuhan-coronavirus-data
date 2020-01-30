# 2019 Wuhan Coronavirus data (2019-nCoV)

This public repository archives data over time from various public sources on the web.

Data is presented as timestamped CSV files, for maximum compatibility.

It is hoped that this data will be useful to those producing visualizations or analyses.

Code is included.

## Sample animation

Shown here in GIF format. There is a better (smaller/higher resolution) webm format also generated.

![image](data-sources/dxy/data/latest-animation.gif)

## Sample visualization

![image](data-sources/dxy/data/20200130-182204-dxy-2019ncov-data.svg)

![image](data-sources/bno/data/20200130-055100-bno-2019ncov-data.svg)

Generates static SVGs. 

Source images were [this one](https://upload.wikimedia.org/wikipedia/commons/f/fe/China_blank_province_map.svg) and [this one](https://commons.wikimedia.org/wiki/File:BlankMap-World.svg).

## Generating

### China

For a China map, the following command sequence will grab data from DXY and render it.

```
./build china
```

You now have timestamped JSON, CSV and SVG files in the `data-sources/dxy/data/` subdirectory.

### World

For a world map, the process is similar.

```
./build world
```

You now have timestamped CSV and SVG files in `data-sources/bno/data`.

## Software Dependencies

Probably an incomplete list:
 * bash
 * perl
 * php
 * imagemagick
 * ffmpeg
 * wget

## Sources used

### [BNO](https://bnonews.com/index.php/2020/01/the-latest-coronavirus-cases/)

Includes detail on foreign sources, individual provincial update URLs. Updated once per day or so.
 * Data is timestamped in US Eastern Standard Time (ET) timezone
 * [Direct link to latest data](https://raw.githubusercontent.com/globalcitizen/2019-wuhan-coronavirus-data/master/data-sources/bno/data/20200130-055100-bno-2019ncov-data.csv)

### [DXY](https://3g.dxy.cn/newh5/view/pneumonia)

High level information without specific source URLs. However, this is updated frequently and appears to be the best available data.

 * Data is timestamped in Beijing (CST) timezone
 * [Direct link to latest data](https://raw.githubusercontent.com/globalcitizen/2019-wuhan-coronavirus-data/master/data-sources/dxy/data/20200130-182204-dxy-2019ncov-data.csv)

## TODO

 * Add historical data from personal archives and [Alihealth](https://alihealth.taobao.com/medicalhealth/influenzamap?chInfo=spring2020-stay-in) (thanks to @aboutmydreams / #3)
 * Add key to global view
 * Convert disparate data sources in to singular SQLite database
 * Add cities (needs an improved visualization system)
 * More visualization options:
   * [amcharts](https://www.amcharts.com/demos/map-with-curved-lines/?theme=dark) (icky licensing but looks cool)
   * [d3.js](https://d3js.org/) and [d3-china-map](https://github.com/clemsos/d3-china-map) (good option but possibly overkill)
   * [jvectormap](https://jvectormap.com/) and [jvectormap china](https://jvectormap.com/maps/countries/china/) (good option but no cities)
   * [kartograph](http://kartograph.org/) (good option)
 * Add other sources, eg.
   * [National Health Commission daily reports](http://www.nhc.gov.cn/xcs/yqtb/list_gzbd.shtml)
   * [Tencent live monitoring page](https://news.qq.com//zt2020/page/feiyan.htm)
   * [English Wikipedia](https://en.wikipedia.org/wiki/2019%E2%80%9320_Wuhan_coronavirus_outbreak) (although it generally lags DXY on updates, the summary table is good for international data citations)
   * [Fuuuuuuu's map](https://maphub.net/Fuuuuuuu/map) which seems to have many international cases missed by BNO
 * Potential for different source SVGs, eg. [naturalearthdata](https://www.naturalearthdata.com/downloads/)

## Links of note

 * *[Potential for global spread of a novel coronavirus from China ](https://academic.oup.com/jtm/advance-article/doi/10.1093/jtm/taaa011/5716260)* (2020-01-27)
   * Ranks Burma, Cambodia, India, Indonesia, Philippines as relatively vulnerable.
 * *[Real-time nowcast and forecast on the extent of the Wuhan CoV outbreak, domestic and international spread](https://www.med.hku.hk/f/news/3549/7418/Wuhan-coronavirus-outbreak_AN-UPDATE_20200127.pdf)* (2020-01-27)
   * __Hong Kong University professors estimate 43,590 infections as of 2020-01-25__. (ie. ~20x 'confirmed cases')
   * __2019-nCoV may be about to become a global epidemic__
   * __Self-sustaining human-to-human spread is already present in all major Chinese cities__
 * *[Pattern of early human-to-human transmission of Wuhan 2019-nCoV](https://raw.githubusercontent.com/jriou/wcov/master/manuscript_v2.pdf)* (2020-01-24)
   * __We found the basic reproduction number, R0, to be around 2.2 (90% high density interval 1.4–3.8), indicating the potential for sustained human-to-human transmission. Transmission characteristics appear to be of similar magnitude to [severe acute respiratory syndrome-related coronavirus (SARS-CoV)](https://en.wikipedia.org/wiki/Severe_acute_respiratory_syndrome) and the [1918 pandemic influenza](https://en.wikipedia.org/wiki/1918_flu_pandemic).__
 * *[Novel coronavirus 2019-nCoV: early estimation of epidemiological parameters and epidemic predictions](https://www.medrxiv.org/content/10.1101/2020.01.23.20018549v1.full.pdf)* (2020-01-23)
   * __Only 5% of cases are likely reported in official figures of confirmed cases.__

## Other projects

 * [BlankerL's DXY-2019-nCoV-Crawler](https://github.com/BlankerL/DXY-2019-nCoV-Crawler) and [API](http://lab.isaaclin.cn/nCoV/)
 * [yitao94's 2019-nCoV python-based DXY crawler](https://github.com/yitao94/2019-nCoV)

## How this was built (non-technical explanation)

This section is written for the curious / non-technical user.

The general approach to problems such as these is as follows:
 1. Gather the data
 2. Modify and store it
 3. Do something with it.

### Gather the data

The area of programming surrounding gathering data from websites that were not explicitly designed for it is called [web scraping](https://en.wikipedia.org/wiki/Web_scraping).

In general, web scraping consists of making an [HTTP](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol) (web) request to the website in question, [parsing](https://en.wikipedia.org/wiki/Parsing#Computer_languages) (or interpreting) the response, and [extracting](https://en.wikipedia.org/wiki/Extract,_transform,_load#Extract) the data of interest. Thereafter some modification may be required.

### Modify and store it

We translate some Chinese and English information ([toponyms]() or geographic region names) in to a known format by matching against a [static database file for countries](country-codes.csv) and a [similar file for regions in or near China](greater-china-region-names.csv).

We then store the data in various formats, mostly [CSV](https://en.wikipedia.org/wiki/Comma-separated_values) and [JSON](https://en.wikipedia.org/wiki/JSON), which are [timestamped](https://en.wikipedia.org/wiki/Timestamp) in a [most to least significance](https://en.wikipedia.org/wiki/Significant_figures#In_computing) format, inspired by the [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) standard, to aid in [sorting](https://en.wikipedia.org/wiki/Sorting_algorithm).

### Do something with it

Finally, we further interpret and process the data in two stages.

#### Static image generation

First, we transform some reference [SVG](https://en.wikipedia.org/wiki/SVG) maps gathered from [Wikimedia Commons](http://commons.wikimedia.org) by applying the data we have captured.

#### Combine in to an animation

Finally we animate multiple such resulting images in to two formats, [animated GIF](https://en.wikipedia.org/wiki/GIF#Animated_GIF) and the greatly superior and far more modern [webm container format](https://en.wikipedia.org/wiki/Webm) with [VP9 encoding](https://en.wikipedia.org/wiki/VP9). This is done using the open source tools [imagemagick](https://imagemagick.org/) and [ffmpeg](https://ffmpeg.org/).
