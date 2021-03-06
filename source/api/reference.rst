===========================
Keen API v2.0 Reference
===========================

.. note:: This API is part of a developer preview and may change without notice!

.. note:: We recognize that changing APIs on you is really shitty, so while we’ll do it when in developer preview, we’ll never ever do it once the developer preview is over.

If you’re looking for the Keen Service API Usage Guide, go here. If you want a tutorial / quick-start, check it out!

Version Resource
================

----
URL
----

/

-----------
Description
-----------

Returns the available API versions. Please only use API version 2.0. Version 1.0 will work but will be deprecated shortly.

-------
Payload
-------

None

----------------
Example Response
----------------

::

	[
		{
  		 "url": "/beta",
  		 "is_public": false,
  		 "version": "beta"
 		},
 		{
  		 "url": "/1.0",
  		 "is_public": false,
  		 "version": "1.0"
 		},
 		{
  		 "url": "/2.0",
  		 "is_public": true,
  		 "version": "2.0"
 		}
	]


Discovery Resource
==================

----
URL
----

/:version:

-----------------
Supported Methods
-----------------

GET, HEAD

-----------
Description
-----------

Returns the available child resources. Currently, the only child resource is the Projects Resource.

-------
Payload
-------

None

----------------
Example Response
----------------

::

	{
	 "projects_resource_url": "/2.0/projects"
	}


Projects Resource
=================

----
URL
----

/:version:/projects

-----------------
Supported Methods
-----------------

GET, HEAD

-----------
Description
-----------

Returns the projects accessible to the API user, as well as links to project sub-resources for discovery.

-------
Payload
-------

None

----------------
Example Response
----------------

::

	[
 		{
  		 "api_key": ":API_KEY:",
  	 	 "_id": ":PROJECT_ID:",
  		 "collections": [
   			{
   			 "url": "/2.0/projects/:PROJECT_ID:/purchases",
   			 "name": "purchases"
   			},
   			{
   			 "url": "/2.0/projects/:PROJECT_ID:/level_ups",
   			 "name": "level_ups"
   			},
   			{
   			 "url": "/2.0/projects/:PROJECT_ID:/inventory_changes",
   			 "name": "inventory_changes"
   			}
   			]
   		}
	]

Project Row Resource
====================

----
URL
----

/:VERSION:/projects/:PROJECT_ID:

-----------------
Supported Methods
-----------------

GET, HEAD

-----------
Description
-----------

Returns detailed information about the specific project, as well as links to related resources.

-------
Payload
-------

None

----------------
Example Response
----------------

::

	{
	 "api_key": ":API_KEY:",
 	 "_id": ":PROJECT_ID":",
 	 "collections": [
		{
		 "url": "/2.0/projects/:PROJECT_ID:/purchases",
		 "name": "purchases"
		},
		{
	 	 "url": "/2.0/projects/:PROJECT_ID:/level_ups",
	 	 "name": "level_ups"
		},
  		{
  		 "url": "/2.0/projects/:PROJECT_ID:/inventory_changes",
  		 "name": "inventory_changes"
  		}
  		]
  	}

Collection Resource
===================

----
URL
----

/:VERSION:/projects/:PROJECT_ID:/:COLLECTION_NAME:

-----------------
Supported Methods
-----------------

GET, HEAD, POST

-----------
Description
-----------

GET returns available schema information for this collection, including properties and their type and frequency. It also returns links to sub-resources.

POST adds a new resource to this collection.

-------
Payload
-------

A namespaced JSON object. There are two namespaces that matter. The "body" namespace is required and is where the properties you define and their values are placed. The "header" namespace is optional and is where several standard properties are placed. Some of them can be overridden.

The "header" namespace currently supports a single property: "timestamp", which has an ISO-8601 formatted datetime value. If not provided, we'll automatically generate a timestamp.

The "body" namespace is completely user-defined. It must not be empty.

::

	{
		"header": {
			"timestamp": "2012-06-06T19:10:39.205000"
		},
		"body": {
			"type": "mouse_click",
			"x_coord": 720,
			"y_coord": 640
		}
	}

----------------
Example Response
----------------

GET

::

	{
		"property_names": ["body:type", "body:x_coord", "body:y_coord"],
		"inferred_column_types": {
			"body:type": "string",
        	"body:x_coord": "num",
        	"body:y_coord": "num"
    	},
    	"body:type": {
    		"num_appearances": 1,
    		"type_appearances": {
    			"string": 1
    			}
    	},
    		"body:x_coord": {
    			"num_appearances": 1,
    			"type_appearances": {
    				"num": 1
    			}
    		},
    		"body:y_coord": {
    			"num_appearances": 1,
    			"type_appearances": {
    				"num": 1
    			}
    		},
    		"urls": {
    			"extractions": "/2.0/projects/:PROJECT_ID:/:COLLECTION_NAME:/_extracts"
    		}
    }

POST

::

	{
		"created": true
	}

Extractions Resource
====================

----
URL
----

/:VERSION:/projects/:PROJECT_ID:/:COLLECTION_NAME:/_extracts

-----------------
Supported Methods
-----------------

GET, HEAD, POST

-----------
Description
-----------

GET returns available extractions and their statuses.

POST creates a new extraction.

-------
Payload
-------

Body should be a JSON object. One property is "filters", which is a list of nested JSON objects with the following properties:

property_name (string)
operator (string, valid values are eq, lt, gt, lte, gte
value (primitive)
The other optional property is "email", which is an email address which will receive a notification of extraction completion. If this property is omitted, no email is sent.

Example:

::

	{
		"filters": [
			{
				"property_name": "body:amount",
				"operator": "gt",
				"property_value": 3.50
			}
		],
		"email": "alert@keen.io"
	}
		



----------------
Example Response
----------------

GET

::

	[
		{
			"_id": ":EXTRACTION_ID:",
			"status": "complete",
			"results_url": "https://s3.amazonaws.com/keen_service/..."
		},
		{
			"_id": ":EXTRACTION_ID:",
			"status": "complete",
			"results_url": "http://s3.amazonaws.com/keen_service/..."
		}
	]
			

POST

::

	{
		"_id": ":EXTRACTION_ID:",
		"status": "complete",
		"results_url": "http://s3.amazonaws.com/keen_service/..."
	}


Extraction Row Resource
=======================

----
URL
----

/:VERSION:/projects/:PROJECT_ID:/:COLLECTION_NAME:/_extracts/:EXTRACTION_ID:

-----------------
Supported Methods
-----------------

GET, HEAD

-----------
Description
-----------

GET returns detailed information about a particular extraction (including a link to its results if the extraction has completed).

-------
Payload
-------

None

----------------
Example Response
----------------

::

	{
		"status": "complete",
		"_id": ":EXTRACTION_ID:",
		"results_url": "https://s3.amazonaws.com/keen_service/..."
	}

Count Resource
==============

----
URL
----

/:VERSION:/projects/:PROJECT_ID:/:COLLECTION_NAME:/_count:

-----------------
Supported Methods
-----------------

GET, HEAD

-----------
Description
-----------

GET returns the number of resources in the collection matching the given criteria. The response will be a simple JSON
object with one key: result, which maps to the numeric result described previously.

-----------------------
Query String Parameters
-----------------------

Count supports two query string parameters: filters and api_key.

The "filters" parameter is optional. If specified, its value should be a URL-encoded JSON string that represents an
array of filters. These filters should look just like they do in the `Extractions Resource`_. Here's an example filter:

::

    {
        "property_name": "body:amount",
        "operator": "gt",
        "property_value": 3.50
    }

The "api_key" parameter is optional. It allows you to pass your api_key as a query string parameter rather than as an
HTTP header. This is to support embedding links to count APIs directly in HTML. If both the query string parameter
and the header are specified, Keen will try the API key in the query string first, then the header.

-------
Payload
-------

None

----------------
Example Response
----------------

::

    {
        "result": 10
    }

Count Unique Resource
=====================

----
URL
----

/:VERSION:/projects/:PROJECT_ID:/:COLLECTION_NAME:/_count_unique:

-----------------
Supported Methods
-----------------

GET, HEAD

-----------
Description
-----------

GET returns the number of UNIQUE resources in the collection matching the given criteria. The response will be a simple
JSON object with one key: result, which maps to the numeric result described previously.

-----------------------
Query String Parameters
-----------------------

Count unique supports two query string parameters: filters and api_key.

The "filters" parameter is optional. If specified, its value should be a URL-encoded JSON string that represents an
array of filters. These filters should look just like they do in the `Extractions Resource`_. Here's an example filter:

::

    {
        "property_name": "body:amount",
        "operator": "gt",
        "property_value": 3.50
    }

The "api_key" parameter is optional. It allows you to pass your api_key as a query string parameter rather than as an
HTTP header. This is to support embedding links to count APIs directly in HTML. If both the query string parameter
and the header are specified, Keen will try the API key in the query string first, then the header.

-------
Payload
-------

None

----------------
Example Response
----------------

::

    {
        "result": 7
    }

