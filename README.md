# Elasticsearch-Crawler

Found this in _Unredacted_ magazine issue 006.

## Getting started

Install the necessary requirements:

`python3 -m pip install -r requirements.txt`

## Running the search

To search for databases:

`python3 elastichunt.py 192.168.0.0/16 9200 --elastictimeout 16 --scannertimeout 16`

The first part of the command specifes the IP address or IP range you want to search. The second part specifies the port number to search for databases on. The last two parts specify the timeout for the elastic API and the scanner, respectively. You can adjust these values as needed, but it’s generally best to keep them above 10.

If you want to scan a large part of the internet, it’s important to be careful so that you don’t crash your computer or get disappointing results. To help with this, Elastichunt provides the option to split the scan into smaller “subscans” using the `--staged` option. This splits the IP range into smaller CIDR ranges to make the scan more manageable. For example, 192.0.0.0/8 would be split into 256 stages of the /16 subnet, 192.0.0.0/16, 192.1.0.0/16 ... 192.255.0.0/16.

## Using filters

The most powerful option is using filters. You can define your own filters, and you can quickly get the indices that are most important to you. To make a filter, you just need to make a `filters.json` file and you can define filters like this:

```
[
  {
    “filter_name”: “allowed_indices”,
    “filter_type”: “regex”,
    “field_name”: “index”,
    “filter_items”: [“customer”, “user”,
    “spreadlog”, “leads”, “resume”, “employee”,
    “hospital”, “passport”, “voter”, “resident”,
    “billing”, “debt”]
  },
  {
    “filter_name”: “allowed_filesizes”,
    “filter_type”: “regex”,
    “field_name”: “store_size”,
    “filter_items”: [“mb”, “gb”]
  }
]
```

The `filter_name` can be anything you want, it’s just there to help you remember what the filter is for. The `filter_type` tells the program what type of filter to use (currently the only supported one is regex). `Field_name` is the field you want to filter on. This has nothing to do with the field names contained inside the index, it only has to do with the index itself. Possible field names are: `health`, `status`, `index`, `uuid`, `pri`, `rep`, `docs_count`, `docs_deleted`, `store_size` and `pri_store_size`. The `filter_items` are the things you’re interested in. The program will look for indices that have those items in the `field_name` you specify.

Once you’ve created your `filters.json` file, you can include it in your searches by adding the `--filters=filters.json` option to the Elastichunt command. For example, your search command might look like this:

`python3 elastichunt.py 192.168.0.0/16 9200 --elastictimeout 16 --scannertimeout 16 --filters=filters.json`
