+++
title = 'Forex Strong Weak Analysis - Trading the trend'
date = 2025-06-08T15:47:14+12:00
draft = false
showToc = false
[params]
  ShareButtons = ["linkedin", "reddit"]
+++

## A consolidated approach to filter your options

Some time ago I got into [this post](https://forums.babypips.com/t/trading-the-trend-with-strong-weak-analysis/77959/1) from Dennis3450, and I liked the idea enough to turn it into a small coding project. What started as a way to automate one type of market analysis became a nice exercise in Python, data storage, scheduled jobs, and static site generation.

The project itself is intentionally simple. A Python script fetches the daily close and 4h candles for a set of JPY pairs, calculates the percentage distance from the 4h 200 MA, and stores each result with its date. These days I am using Turso for that storage layer, which makes it easier to keep the historical dataset available without depending on a single local database file. The script still supports the older SQLite-based setup, but [Turso](https://turso.tech/) is now the main path.

The same script that processes the data also generates the site output. After inserting and reading the latest results, it builds a static `index.html` dashboard directly from Python. That means there is no application server, no separate frontend build step, and not much operational overhead. The generated file just gets published to the server and served behind a reverse proxy (using [SWAG](https://docs.linuxserver.io/general/swag/) here) that also manages the SSL cert and, as another layer on top, [Cloudflare proxied DNS](https://developers.cloudflare.com/dns/proxy-status/).

I still see it as a work in progress, but that is part of the point. Projects like this are useful to me because they let me experiment with a full chain, from data collection to storage to publishing, without turning everything into a much bigger platform than it needs to be.

## Technology notes

- [Turso](https://turso.tech/) is the current cloud database layer I use to keep the historical dataset available without depending on a single local file.
- [SQLite](https://sqlite.org/) is the older embedded database option that the script still supports as a simpler local storage path.
- [SWAG](https://docs.linuxserver.io/general/swag/) is the LinuxServer.io reverse proxy stack I use to serve the generated site and manage SSL certificates.
- [Cloudflare](https://www.cloudflare.com/) provides the proxied DNS layer in front of the site, which adds protection and traffic handling before requests reach the server.
