# SecSpider Website – FastAPI Rewrite

> **Note:** This repository is a project showcase and contains no source code. The code is part of the SecSpider project at TU Dresden and will be published in the official SecSpider repository. A link will be added here once it is public.

A complete rewrite of the website for **SecSpider**, a long-running DNSSEC monitoring system that tracks the global deployment of DNS security across thousands of zones. The original website was built with Perl, static HTML, and CGI scripts in the mid-2000s. I rebuilt it as a modern web application with a REST API, interactive charts, and a responsive design, while keeping all original pages, metrics, and statistics.

This was a team project at TU Dresden in the winter semester 2025/26 to modernize SecSpider. My part was the website; other team members worked on the database, the pollers, monitoring, and later extensions of the API.

<table> 
  <tr> 
    <td><img alt="Old SecSpider website" src="https://github.com/user-attachments/assets/a54c0abd-9c45-4c63-ace8-3ef6d1ee2ba3"></td> 
    <td><img alt="New SecSpider website" src="https://github.com/user-attachments/assets/d5850d75-b6d3-4d47-bbbe-b24235fd6483"></td> 
  </tr> 
  <tr> 
    <td align="center">Before: Perl and static HTML</td> 
    <td align="center">After: FastAPI and Apache ECharts</td> 
  </tr> 
</table>


## What I built

- **Rewrote the website** from Perl, static HTML, and CGI into a FastAPI application with Jinja2 templates
- **Replaced static pages with API endpoints**, so all statistics and chart data are served as JSON
- **Reimplemented the Perl algorithms in Python**, including the DNSSEC verification metric and the "islands of security" analysis, which follow each zone's chain of trust up the DNS hierarchy
- **Replaced the old CGI-based zone search**, which was very slow, with a fast search and six detail tabs per zone: verifiability, availability, validity, status, DANE/TLSA, and the chain of trust
- **Built the main pages** with multiple interactive charts each: status, deployment stats, deployment growth, hierarchy, pollers, and docs
- **Fixed broken features** of the old site, such as the poller map, now built with Leaflet
- **Designed a new UI** inspired by Cloudflare's dashboard: clean layout, collapsible sidebar, and a responsive design for desktop, tablet, and mobile

## Performance work

The database holds around 2 million data points, so rendering charts directly from raw data wasn't feasible. To keep the pages fast:

- **Aggregation in SQL instead of Python**: time-series charts are bucketed in the database (e.g. with `NTILE` window functions and fixed-width buckets), so the API returns a few hundred points instead of millions of rows
- **Efficient tree traversal**: the verification and island computations use memoization, so each zone's path up the DNS hierarchy is resolved only once
- **Response caching**: I implemented the initial caching of expensive endpoints with in-memory TTL caches; it was later moved to a Redis backend by another team member
- **Frontend rendering**: charts use Apache ECharts with downsampling (LTTB) and progressive rendering for large series

## Screenshots

<table> 
  <tr> 
    <td><
          img alt="CDF of DNSSEC zones chart" src="https://github.com/user-attachments/assets/9d691f63-5cd1-4d69-9ec7-8afd8f6d356f">
    </td> 
    <td>
      <img alt="Zone search" src="https://github.com/user-attachments/assets/093cbe94-bb47-40a0-b4ba-a7ed5ba4cd34">
    </td> 
  </tr> 
  <tr> 
    <td align="center">Interactive chart on about 2 million data points
    </td> 
    <td align="center">Zone search with detail tabs
    </td> 
  </tr> 
</table> 
<p> 
  <img height="320" alt="Poller map" src="https://github.com/user-attachments/assets/88d46d57-db8f-4425-90b3-8371535b929f"> 
  <img height="320" alt="Mobile view" src="https://github.com/user-attachments/assets/d9b6d10b-16c8-4c94-98ad-e8d13a96a0e7"> 
</p>


## Tech stack

- **Backend:** Python, FastAPI, Jinja2, MySQL/MariaDB
- **Frontend:** HTML, CSS, JavaScript, Bootstrap 5
- **Charts and maps:** Apache ECharts, Leaflet
- **Deployment:** Docker

## Credits

SecSpider was originally created by Eric Osterweil, Dan Massey, and Lixia Zhang. See their paper: *Deploying and Monitoring DNS Security (DNSSEC)*, ACSAC 2009, [doi:10.1109/ACSAC.2009.47](https://doi.org/10.1109/ACSAC.2009.47).
