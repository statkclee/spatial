# 데이터 과학


> ## 학습 목표 {.objectives}
>
> *  지리정보 시각화를 위한 기본 개념을 학습한다.



[지도학(Cartography)](https://en.wikipedia.org/wiki/Cartography)는 지도를 제작하는 방식이다. 특정한 목적에 따라 특수한 주제 혹은 내용만을 나타내어 그린 지도를 **[주제도(Thematic Map)](https://ko.wikipedia.org/wiki/지도의_종류)** 라고 한다.  어느 시점에서의 일기 상황을 나타낸 기상도, 운전할 때 쓰이는 도로도, 항해할 때 쓰이는 해도, 통계값을 지도에 그려 넣은 통계 지도 등이 좋은 사례다.

지리정보를 표현하기 위해서는 다음과 같은 기본 요소가 필요하다.

1. `.shp` 확장자를 갖는 지도정보 파일
2. 대한민국에 대한 기본 지리정보: 위도 경도 

### 1. 지리정보 파일(SHP)

대한민국 센서스용 행정구역경계(시군구) 자료는 [통계지리정보서비스](http://sgis.kostat.go.kr/contents/shortcut/shortcut_05.jsp) 사이트에서 자료신청을 하면 얻을 수 있다. 그리고, 제공되는 자세한 형식에 대한 정보는 *자료신청* &rarr; *자료제공 목록*을 참조한다. 혹은, [Encaion](https://goo.gl/KyHR46) 구글 드라이브에서 직접 다운로드 받을 수도 있다. 통계청으로 신청하면 승인까지 수일이 소요되며 행정처리일 기준으로 처리되며 다운로드 가능한 기간도 1주일이다.

  * [GADM](http://www.gadm.org/) 데이터베이스에서 *Country*에서 **South Korea*을 선택하고, *File Format*에서 *Shapefile*을 선택하여 다운로드한다.
  * [DIVA-GIS](http://www.diva-gis.org/gdata) 사이트에서도 자유로이 한국을 포함한 여러국가 지도를 다운로드 받을 수 있다.

- [통계지역경계](http://sgis.kostat.go.kr/contents/shortcut/shortcut_05.jsp)
- [Global Administrative Areas](http://www.gadm.org/country)
- [팀 포퐁 GitHub](https://github.com/southkorea/southkorea-maps)

~~~ {.python}
FILEMAP = {
    'shp': [('skorea-shp.zip','http://biogeo.ucdavis.edu/data/gadm2/shp/KOR_adm.zip')],
    'kmz': [('skorea.kmz','http://biogeo.ucdavis.edu/data/gadm2/kmz/KOR_adm0.kmz'),
            ('skorea-provinces.kmz','http://biogeo.ucdavis.edu/data/gadm2/kmz/KOR_adm1.kmz'),
            ('skorea-municipalities.kmz','http://biogeo.ucdavis.edu/data/gadm2/kmz/KOR_adm2.kmz')],
    'r'  : [('skorea.RData','http://biogeo.ucdavis.edu/data/gadm2/R/KOR_adm0.RData'),
            ('skorea-provinces.RData','http://biogeo.ucdavis.edu/data/gadm2/R/KOR_adm1.RData'),
            ('skorea-municipalities.RData','http://biogeo.ucdavis.edu/data/gadm2/R/KOR_adm2.RData')]
~~~

### 2.지리정보 시각화를 위한 대한민국 정보

대한민국 지도에 관한 일반정보의 경도범위는 124 -- 132, 위도범위는 33 -- 43 이다. 

> ### 대한민국 위도와 경도 [^kor-lonlat] {.callout}
>
> #### 대한민국 전체
> - 극동: 경상북도 울릉군의 독도 동단 동경 131° 52′20", 
> - 극서: 평안북도 용천군 신도면 마안도 서단 동경 124° 11′45"
> - 극남: 제주도 남제주군 대정읍 마라도 남단 북위 33° 06′40"
> - 극북: 함경북도 온성군 남양면 북단 북위 43° 00′35"
>
> #### 북한 제외
> - 극동: 경상북도 울릉군의 독도(獨島)로 동경 131° 52′, 
> - 극서: 전라남도 신안군의 소흑산도(小黑山島)로 동경 125° 04′, 
> - 극북: 강원도 고성군 현내면 송현진으로 북위 38° 27′, 
> - 극남: 제주도 남제주군 마라도(馬羅島)로 북위 33° 06′이다.

#### 2.1 `rworldmap` 팩키지를 활용한 지도 그리기 기초

지도가 구해졌으면 범위를 한정하기 위해 극점(extreme point) 정보를 알아내는 것이 중요하다.
[남한 극단정보](https://en.wikipedia.org/wiki/Extreme_points_of_South_Korea)
위키피디아에서 확인한 극점정보를 사용하여 남한해 지도를 그려본다. 


~~~{.r}
#install.packages(rworldmap)
library(rworldmap)
~~~



~~~{.output}
FALSE Error: package or namespace load failed for 'rworldmap'

~~~



~~~{.r}
library(ggmap)
korea.map <- getMap(resolution = "high")
~~~



~~~{.output}
FALSE Error in eval(expr, envir, enclos): 함수 "getMap"를 찾을 수 없습니다

~~~



~~~{.r}
south.korea.limits <- geocode(c(
  "Daegang-ri, Hyeonnae-myeon, County of Goseong, Gangwon",
  "Marado, Daejeong-eup, Seogwipo, Jeju",
  "Dokdo-ri,Ulleung-eup, County of Ulleung, North Gyeongsang",
  "Baengnyeongdo, Baengnyeong-myeon,    County of Ongjin, Incheon")
)  

south.korea.limits
~~~



~~~{.output}
FALSE        lon      lat
FALSE 1 128.3445 38.60602
FALSE 2 126.2671 33.12064
FALSE 3       NA       NA
FALSE 4 124.6560 37.95097

~~~



~~~{.r}
plot(korea.map,
     xlim = range(south.korea.limits$lon),
     ylim = range(south.korea.limits$lat),
     asp = 1
)
~~~



~~~{.output}
FALSE Error in plot(korea.map, xlim = range(south.korea.limits$lon), ylim = range(south.korea.limits$lat), : 객체 'korea.map'를 찾을 수 없습니다

~~~

[남한 극단정보](https://en.wikipedia.org/wiki/Extreme_points_of_South_Korea) 중 섬을 제외한 
본토를 기준으로 지리정보를 시각화하면 다음과 같다.


~~~{.r}
south.korea.mainland.limits <- geocode(c(
  "Daegang-ri, Hyeonnae-myeon, County of Goseong, Gangwon",
  "Songho-ri, Songji-myeon, Haenam, South Jeolla",
  "Guryongpo-eup, Pohang, North Gyeongsang",
  "Mohang-ri, Sowon-myeon, Taean, Chungcheong")
)  
south.korea.mainland.limits
~~~



~~~{.output}
FALSE        lon      lat
FALSE 1 128.3445 38.60602
FALSE 2 126.5351 34.32605
FALSE 3 129.5471 35.98531
FALSE 4 126.1413 36.76856

~~~



~~~{.r}
plot(korea.map,
     xlim = range(south.korea.mainland.limits$lon),
     ylim = range(south.korea.mainland.limits$lat),
     asp = 1
)
~~~



~~~{.output}
FALSE Error in plot(korea.map, xlim = range(south.korea.mainland.limits$lon), : 객체 'korea.map'를 찾을 수 없습니다

~~~

[^kor-lonlat]: [대한민국의 위도와 경도를 알고 싶어요](http://tip.daum.net/question/3092152)

#### 2.2 `ggmap` 팩키지를 활용한 지도 그리기 기초

`ggmap` API를 활용하여 지도를 그린다.


~~~{.r}
library(ggmap)
# 위치를 지정한다.
krLocation <- c(124.11, 33.06, 131.52, 43.00) #좌측하단경도, 좌측하단위도, 우측상단경도, 우측상단위도
southKrLocation <- c(125.04, 33.06, 131.52, 38.27)
#krLocation <- c(lon=126, lat=37) # 대한민국 서울
krMap <- get_map(location=krLocation, source="stamen", maptype="toner", crop=FALSE) #terrain, toner, watercolor
ggmap(krMap)
~~~

<img src="fig/geo-info-ggmap-index-1.png" style="display: block; margin: auto;" />

`googlemap`이 위도경도 지도중앙, `stamen`, `openstreetmap`, `cloudmade`는 
위도경도 상자표기를 권장한다.


~~~{.r}
krMap <- get_map(location=krLocation, source="stamen", maptype="toner", crop=FALSE) #terrain, satellite, roadmap, hybrid, toner, watercolor
ggmap(krMap)
~~~

### 3. 지리정보처리 툴체인 [^1]

<img src="fig/R_SAGA_GE_logo.jpg" alt="LAMP와 Docker가 설치된 우분투 공용 이미지" width="40%" /> 

[^1]: http://spatial-analyst.net/wiki/index.php?title=Main_Page

- 오픈 GIS 소프트웨어
    - [System for Automated Geoscientific Analyses (SAGA GIS)](https://en.wikipedia.org/wiki/SAGA_GIS)
    - [Geographic Resources Analysis Support System (GRASS GIS)](https://en.wikipedia.org/wiki/GRASS_GIS)
- [TileMill](https://www.mapbox.com/tilemill/)
- KML 마크업 언어
    - [Keyhole Markup Language, KML](https://en.wikipedia.org/wiki/Keyhole_Markup_Language)


### 4. 지리정보 시각화 사례

- 미국 대선(2012) [뉴욕타임즈 President Map](http://elections.nytimes.com/2012/results/president)
- 미국 빈곤율 정보 시각화 [The Topography of Poverty in the United States](http://www.cdc.gov/pcd/issues/2007/oct/07_0091.htm)
- [http://indiemapper.com/](http://indiemapper.com/app/learnmore.php?l=choropleth)


### 참고자료 

- [Thematic Cartography and Geovisualization](http://www.amazon.com/Thematic-Cartography-Geovisualization-3rd-Edition/dp/0132298341)
- [Web Cartography: Map Design for Interactive and Mobile Devices](https://www.crcpress.com/Web-Cartography-Map-Design-for-Interactive-and-Mobile-Devices/Muehlenhaus/9781439876220)
- [R Development Translation Team (Korean)](http://www.openstatistics.net/) 

### R 언어 참고 웹사이트

- [spatial.ly](http://spatial.ly/r/)
- [Spatial data in R: Using R as a GIS](https://pakillo.github.io/R-GIS-tutorial/)
- [Introduction to Spatial Data and ggplot2](http://rpubs.com/RobinLovelace/intro-spatial)
- [Spatial analysis in R: 랭커스터 대학](http://www.maths.lancs.ac.uk/~rowlings/Teaching/- Sheffield2013/index.html)
- [Notes on Spatial Data Operations in R](https://dl.dropboxusercontent.com/u/9577903/- broomspatial.pdf)
- [Making maps with R](http://www.molecularecologist.com/2012/09/making-maps-with-r/)
