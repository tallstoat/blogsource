+++
date = "2010-08-16T20:11:32+05:30"
title = "BBC breaking news on logging-in to your favorite shell"
categories = ["code"]
tags = ["awk", "shell"]
+++
Crude yet working one-liner; Add to your favorite .rc file and Enjoy!
<!--more-->

> curl -silent -A="BBC News 1.2.1 (iPad; iPhone OS 3.2; en_US)" http://cdnedge.bbc.co.uk/nol/ukfs_news/hi/front_page/ticker.json | awk -F "headline\":" '{ for (i=2; i<6; i++) { split( $i, a, /,\"/); print a[1] } }' | sed '/^$/d'

