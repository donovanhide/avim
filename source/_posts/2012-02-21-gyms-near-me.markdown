---
layout: post
title: "Gyms Near Me"
date: 2012-02-21 22:30
comments: true
categories: [Code,SEO]
---

I've been very busy working on [Superfastmatch](http://superfastmatch.org/), but I wanted to spend a day or two trying out a few ideas with the [Fusion Tables API](https://developers.google.com/fusiontables/) and static hosting on [App Engine](http://code.google.com/appengine/docs/python/gettingstarted/staticfiles.html). One of the interesting things with App Engine is that you get 1GB a day of bandwidth for free. If you use static files, then no CPU cost is incurred, effectively giving you a high performance CDN for zero pence!

[{% img left /images/gymsnear.jpg 320 175 'Gyms Near Me' 'Gyms Near Me' %}](http://www.gymsnear.com)
Fusion tables are intriguing because they offer [spatial query processing](https://developers.google.com/fusiontables/docs/developers_reference) and very thorough Google Maps integration. What this means is that you can completely avoid setting up any database infrastructure such as [PostGIS](http://postgis.refractions.net/) and any tile generation services like [Tilestache](http://tilestache.org/).

I employed these two services to come up with a simple app called [Gyms Near Me](http://www.gymsnear.com) which does what it says on the tin! It uses Google Maps with a [Fusion Tables Layer](http://code.google.com/apis/maps/documentation/javascript/reference.html#FusionTablesLayer) to show colour-coded pins for all the gyms in your vicinity. When you select a gym, a JSONP call finds the five nearest gyms by ordering by ST_DISTANCE from the selected latitude/longitude with a LIMIT of 5. The really cool thing about Fusion Tables Layers is that they are delivered as PNG tiles which gives the potential to a have huge number of points on a map, avoiding the issues you might encounter if you were creating individual markers via javascript.

The app is now live, so I'll soon see if it gets picked up for any Google search keywords. Looking at autocomplete it seems like [Gyms Near Me](http://www.google.co.uk/search?&q=gyms+near+me) is the top term so maybe it will get picked up! In the future I might also look at feeding in gyms for global locations and saving map positions like [Gyms in London](http://www.gymsnear.com/london/).

