## List Methods

<details class="api_doc_details request_post">
<summary><span class="method">POST</span>/list</summary>
<div>

### Description

Creates a list of files that can be viewed together on the file viewer page.

### Parameters

POST body should be a JSON object, example below. A list can contain at most
10000 files. If you try to add more the request will fail.

#### Example
```
{
	"title": "My beautiful photos", // Defaults to "Pixeldrain List"
	"anonymous": false / true, // If true this list will not be linked to your user account. Defaults to "false"
	"files": [ // Ordered array of files to add to the list
		{
			"id": "abc123"
		},
		{
			"id": "123abc"
		}
	]
}
```

### Returns

HTTP 201: Created
```
{
	"success": true,
	"id": "yay137" // ID of the newly created list
}
```

Example error:

HTTP 422: Unprocessable Entity
```
{
	"success": false,
	"value": "list_file_not_found",
	"message": "File was not found in the database",
	"extra": {
		"file_not_found": "0h42No" // The file you tried to add with this ID does not exist
	}
}
```

#### Possible errors

Value                      | HTTP status | Description
---------------------------|-------------|----------------------------------------------------------------
json_parse_failed          | 422         | The JSON object in the request body could not be parsed
list_file_not_found        | 422         | One of the files in the list does not exist, see `extra.file_not_found`
too_many_files             | 413         | The list contains more than 10000 files
title_too_long             | 413         | The title of the list is longer than 300 characters
cannot_create_empty_list   | 422         | You cannot make a list with no files
internal                   | 500         | An internal server error occurred
</div>
</details>

<details class="api_doc_details request_get">
<summary><span class="method">GET</span>/list/{id}</summary>
<div>

### Description

Returns information about a file list and the files in it.

### Parameters


Param | Required | Location | Description
------|----------|----------|---------------
id    | true     | URL      | ID of the list

### Returns

The API will return some basic information about every file. Every file also has
a "detail_href" field which contains a URL to the info API of the file. Follow
that link to get more information about the file like size, checksum, mime type,
etc. The address is relative to the API URL and should be appended to the end.

HTTP 200: OK
```
{
	"success": true,
	"id": "L8bhwx",
	"title": "Rust in Peace",
	"date_created": "2020-02-04T18:34:13.466276Z",
	"file_count": 3,
	// If the current logged in user can edit the list
	"can_edit": false,
	"files": [
		// These structures are the same as the file info response, except for
		// the detail_href and description fields
		{
			"detail_href": "/file/_SqVWi/info",
			"description": "",
			"success": true,
			"id": "_SqVWi",
			"name": "01 Holy Wars... The Punishment Due.mp3",
			"size": 123456,
			"date_upload": "2020-02-04T18:34:13.466276Z",
			"date_last_view": "2020-02-04T18:34:13.466276Z",
			"mime_type": "audio/mp3",
			"views": 1,
			"bandwidth_used": 1234567890,
			"thumbnail_href": "/file/_SqVWi/thumbnail"
		},
		{
			"detail_href": "/file/RKwgZb/info",
			"description": "",
			"success": true,
			"id": "RKwgZb",
			"name": "02 Hangar 18.mp3",
			"size": 123456,
			"date_upload": "2020-02-04T18:34:13.466276Z",
			"date_last_view": "2020-02-04T18:34:13.466276Z",
			"mime_type": "audio/mp3",
			"views": 2,
			"bandwidth_used": 1234567890,
			"thumbnail_href": "/file/RKwgZb/thumbnail"
		},
		{
			"detail_href": "/file/DRaL_e/info",
			"description": "",
			"success": true,
			"id": "DRaL_e",
			"name": "03 Take No Prisoners.mp3",
			"size": 123456,
			"date_upload": "2020-02-04T18:34:13.466276Z",
			"date_last_view": "2020-02-04T18:34:13.466276Z",
			"mime_type": "audio/mp3",
			"views": 3,
			"bandwidth_used": 1234567890,
			"thumbnail_href": "/file/DRaL_e/thumbnail"
		}
	]
}
```

HTTP 404: Not Found
```
{
	"success": false,
	"value": "not_found",
	"message": "The entity you requested could not be found"
}
```
</div>
</details>
