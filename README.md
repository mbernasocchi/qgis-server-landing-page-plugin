# QGIS Server Landing Page Plugin

A proof of concept plugin for the QGIS server landing page.

![Landing Page](qgis-landing-page-plugin.gif)

## Motivation

The current out-of-the-box experience for a QGIS Server when you open your browser
to the root address `https://www.your-qgis-server.com/` is to show an ugly and
uninformative XML error.

Recently I implemented OAPIF/WFS3 for QGIS server, it provides a landing page in HTML
and a fully user-friendly browsable website with basic mapping capabilities to quickly
browse your datasets.

Inspired by this development and thanks to the new APIs that it provides I thought
that the same concept should have been extended to the whole QGIS Server.

## Goals

The goal of this plugin is to provide a fully functional, zero configuration (almost)
simple catalog that shows the list of QGIS projects served by the QGIS Server installation.

The landing page shows the catalog entries with their metadata and links to the
various services (WMS, WFS etc.) and a simple WebGIS map that allows for immediate
browsing of the project data.

## Limitations

This is currently a proof-of-concept that does not take into account caching or performances
in case of heavy load websites.

It is probably fine for a limited  (< 20) set of projects and where each projects has a
limited set of layers (<20) but there is certainly room for improvements in case we decide
to adopt this strategy for QGIS Server globally.


### Configuration

The projects that are made available are searched in one or more directories that
can be specified by the following configuration option passed as environment variables.


```bash
QGIS_SERVER_PROJECTS_DIRECTORIES=/plugins/landingpage/tests/projects||/plugins/landingpage/tests/projects2
```

Note: the `||` allows to specify multiple search paths.

If you decide to store your project into a PostGIS DB, you can also specify
the connection(s) using the "standard" notation used internally by QGIS:

```bash
QGIS_SERVER_PROJECTS_PG_CONNECTIONS=postgresql://myhost:myport?sslmode=disable&dbname=landing_page_test&schema=public&username=elpaso&password=mypassword
```


## Testing docker

The provided Docker compose can be used to test this plugin.

### Sample .env file

The configuration for the Docker compose:


```bash
DOCKER_SHARED_VOLUME=/home/dev/Dockers/qgis-server-standalone-docker/shared-volume
POSTGRES_DBNAME=geo
POSTGRES_USER=geo
POSTGRES_PASS=geo
# 2 is critical 3 is no logs
QGIS_SERVER_LOG_LEVEL=0
PUBLIC_HOSTNAME=test_qgis_server
PLUGINS_VOLUME=/home/dev/Dockers/qgis-server-standalone-docker/plugins
PUBLIC_PORT=8002
# Can be overridden, default to /usr/share/qgis/resources/server/api/
QGIS_SERVER_API_RESOURCES_DIRECTORY=/usr/share/qgis/resources/server/api/
# Landing page plugin (container paths, separated by || if more than one)
QGIS_SERVER_PROJECTS_DIRECTORIES=/plugins/landingpage/tests/projects||/plugins/landingpage/tests/projects2
```

`QGIS_SERVER_LANDINGPAGE_DEBUG` when activated will print all json information inside the templates.

## TODO

- PG PKI certs
- Map footer with CRS, scale etc. (zoom to predefined scale?)
- Relations and joins? (dependent onf WFW3 active)
- BBOX selection
- map themese
- base maps switcher
- open QgsProject without loading layers


## Development

From base directory:

1. launch `./run_local.sh`
2. open another terminal and: `cd plugins/landingpage/app && ../../../vue yarn serve`
