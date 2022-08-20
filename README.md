# Technical Deep Dive

# Redis

## What is Redis?
Redis (“REmote DIctionary Service”) is an **open-source key-value database server**.

The most accurate description of Redis is that it's a **data structure server**. This specific nature of Redis has led to much of its popularity and adoption amongst developers.

<image1>

Primarily, Redis is an in-memory database **used as a cache in front of another "real" database like MySQL** or PostgreSQL to help improve application performance. It leverages the speed of memory and alleviates load off the central application database for:

- Data that changes infrequently  and is requested often
- Data that is less mission-critical and is frequently evolving.

Examples of above data can include session or data caches and leaderboard or roll-up analytics for dashboards.

<image-2>
