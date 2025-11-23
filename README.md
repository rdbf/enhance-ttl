# enhance-ttl

A utility to update DNS record TTL values across one or all domains in your Enhance Hosting organization and its customers, enforcing consistent TTL policies via the Enhance API.

---

## Features

- Reads configuration from `enhance-ttl.conf`
- Updates TTLs for specified DNS record types: A, AAAA, MX, NS, CNAME, TXT, SRV
- Supports filtering by a single domain or all domains
- Handles both master organization and customer domains
- Implements retry logic and adaptive rate limiting to avoid API throttling
- Provides logging and summary output

---

## Configuration

This is an example of the config file:

```
#Hosting Control Panel URL with "/api"
BASE_URL = "https://yourdomain.com/api"

#API credentials (from Enhance interface)
ORG_ID = "your-org-id"
ACCESS_TOKEN = "your-access-token"

#Domains to update: "ALL" for all domains or specify one domain
DOMAIN = "ALL"

#TTL values per DNS record type (in seconds)
TTL_A = 3600
TTL_AAAA = 3600
TTL_MX = 3600
TTL_NS = 3600
TTL_CNAME = 21600
TTL_TXT = 10800
TTL_SRV = 86400
```

---

## Installation and Usage

You will need to run this script on only one server in your cluster, or on any other linux device capable of running this script and communicating with the Control Panel.
Ensure Python 3 and `requests` library are installed.

As root, run `git clone https://github.com/rdbf/enhance-ttl.git /opt/enhance-ttl` and `chmod +x /opt/enhance-ttl/enhance-ttl`. 

Copy `enhance-ttl.conf.example` to `enhance-ttl.conf` and edit it with your API URL, credentials and desired TTL values:

Run the script from the command line:

`/opt/enhance-ttl/enhance-ttl`

The script will process applicable domains and update TTLs according to the config.

If you want to run this daily, add a line to cron:

`crontab -e`

Add this to run it once a day at 02:00

```
# Run enhance-ttl daily at 02:00
0 2 * * * /opt/enhance-ttl/enhance-ttl >> /var/log/enhance-ttl.log 2>&1
```

---

## Notes

- The script adds delays between API calls to respect rate limits, automatically slowing down if needed.
- Records with types not configured in the TTL settings will be ignored.
- The script handles both master organization and customer domains to ensure all relevant DNS zones are updated.
- Tested compatibility up to and including v12.12.0, where custom Default TTL settings were introduced in Enhance.
