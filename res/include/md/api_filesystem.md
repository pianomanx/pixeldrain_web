## Filesystem Methods

These methods are used to interact with the [pixeldrain
filesystem](https://docs.pixeldrain.com/filesystem/). The filesystem is
available to accounts with a paid subscription.

### Paths

Every filesystem endpoint works on a path, and every path starts with a bucket.
There are two ways to address a bucket:

 - `me` is your own filesystem, this requires authentication with an API key.
   Example: `/filesystem/me/documents/todo.txt`
 - The 8 character ID of a shared directory or file. This is the same ID that's
   at the end of a sharing link like `https://pixeldrain.com/d/abcd1234`.
   Example: `/filesystem/abcd1234/photos/cat.jpg`

Special characters in path components need to be URL-encoded.

The owner of a filesystem can always read and write. What other users are
allowed to do is determined by the permissions configured on the shared
directory with the `update` action documented below.

### Node objects

Information about files and directories is returned as a 'node' object. This is
what a node looks like:

```
{
	// Type is either "file" or "dir"
	"type": "file",
	"path": "/documents/todo.txt",
	"name": "todo.txt",
	"created": "2024-02-04T18:34:13.466276Z",
	"modified": "2024-02-04T18:34:13.466276Z",
	// Unix file mode in symbolic and octal form
	"mode_string": "-rw-r--r--",
	"mode_octal": "644",
	// Username of the account which created the node
	"created_by": "some_user",

	// Only present on files
	"file_size": 9757269,
	"file_type": "text/plain",
	"sha256_sum": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855",

	// Only present if the node, or one of its parents, is shared. This is the
	// ID which can be used to access the node without authentication
	"id": "abcd1234",

	// Only present if the node received an abuse report. Nodes with an abuse
	// type cannot be downloaded
	"abuse_type": "",
	"abuse_report_time": "2024-02-04T18:34:13.466276Z",

	// Branding properties configured with the update action, only present if
	// branding is configured
	"properties": {
		"branding_enabled": "true",
		"brand_header_image": "abcd1234"
	},

	// Sharing permissions, only visible to the owner of the filesystem
	"link_permissions": {"owner": false, "read": true, "write": false, "delete": false},
	"user_permissions": {"friendly_user": {"owner": false, "read": true, "write": true, "delete": false}},
	"password_permissions": {"hunter2": {"owner": false, "read": true, "write": false, "delete": false}}
}
```

<details class="api_doc_details request_get">
<summary><span class="method">GET</span>/filesystem/{path}</summary>
<div>

### Description

Reads a file or directory. If the path is a file the file contents are
returned, byte range requests are supported. If the path is a directory a stat
response is returned, see below.

This endpoint also has a collection of sub-APIs which can be accessed by adding
query parameters to the URL:

Param           | Description
----------------|---------------------------------------------------------------
attach          | Sends an attachment header instead of inline rendering, which causes the browser to show a 'Save File' dialog
stat            | Returns information about the node instead of the file contents, see the stat response below
thumbnail       | Returns a PNG thumbnail of the file. Use the `width` and `height` parameters to set the size, multiples of 16 between 16 and 256, default is 256
bulk_download   | Downloads the directory and everything in it as a zip archive
search          | Searches the shared directory for files matching the search term. The term goes in the parameter value: `?search=term`. The term needs to be between 2 and 100 characters. Use `limit` to set the number of results, between 1 and 1000, default is 10. Returns an array of matching paths
torrent_info    | Returns the parsed metadata of a torrent file. The file needs to be of type application/x-bittorrent and at most 16 MiB large
zip_info        | Returns the file listing of a zip archive
zip_file        | Extracts a single file from a zip or 7z archive and returns its contents. The path of the file within the archive goes in the parameter value: `?zip_file=/dir/file.txt`
render_markdown | Renders a markdown file to HTML. The file needs to be of type text/markdown or text/plain and at most 2 MiB large
download_stats  | Returns the total download and transfer statistics of the node. Can also be upgraded to a websocket connection which streams updated statistics in real time
timeseries      | Returns historic download and transfer statistics. Requires the `start` and `end` parameters in RFC 3339 format and `interval` in minutes between 1 and 1440
change_log      | Returns the activity log of a directory which has `logging_enabled` set. Requires the `start` and `end` parameters in RFC 3339 format, up to 30 days at a time

### Returns

When the path is a file and no query parameters are used the raw file contents
are returned.

When the path is a directory, or the `stat` parameter is used, a stat object is
returned. The `path` array contains a node object (described at the top of this
chapter) for every path component, so you can show a breadcrumb navigation bar
with it. `base_index` is the index of the requested node in the `path` array.
`children` contains the nodes inside the directory:

HTTP 200: OK
```
{
	"path": [
		{
			"type": "dir",
			"path": "/",
			"name": "some_dir",
			... // Remaining node fields
		}
	],
	"base_index": 0,
	"children": [
		{
			"type": "file",
			"path": "/todo.txt",
			"name": "todo.txt",
			... // Remaining node fields
		}
	],
	// The permissions you have on this path
	"permissions": {
		"owner": false,
		"read": true,
		"write": false,
		"delete": false
	},
	"context": {
		// Whether downloads from this filesystem use premium transfer
		"premium_transfer": true
	}
}
```

#### Possible errors

Value                           | HTTP status | Description
--------------------------------|-------------|----------------------------------------------------------------
not_found                       | 404         | The bucket in the first path component does not exist
path_not_found                  | 404         | No file or directory exists at this path
authentication_required         | 401         | The `me` bucket was requested without an API key
permission_denied               | 400         | You do not have read permission on this path
node_is_a_directory             | 400         | A file operation was requested on a directory
node_is_not_a_directory         | 400         | A directory operation was requested on a file
unavailable_for_legal_reasons   | 451         | The file received a takedown report and cannot be downloaded, see `extra.abuse_type`
embed_not_allowed               | 403         | Embedding files from this filesystem is not allowed
invalid_referrer                | 400         | The URL in the Referer header could not be parsed
not_a_torrent                   | 400         | The `torrent_info` target is not a torrent file
torrent_too_large               | 400         | The `torrent_info` target is larger than 16 MiB
torrent_parse_failed            | 400         | The torrent file could not be parsed
not_a_zip                       | 400         | The `zip_info` or `zip_file` target is not a zip or 7z archive
invalid_zip_file                | 400         | The zip archive could not be parsed
unsupported_zip_compression     | 400         | The zip archive uses a compression method which is not supported
not_a_markdown_file             | 400         | The `render_markdown` target is not a markdown or plain text file
file_too_large                  | 413         | The `render_markdown` target is larger than 2 MiB
search_not_a_directory          | 400         | The `search` target is not a directory
search_term_too_short           | 400         | The search term is shorter than 2 characters
search_term_too_long            | 400         | The search term is longer than 100 characters
log_timespan_too_long           | 400         | The `change_log` time span is longer than 30 days
internal                        | 500         | An internal server error occurred
</div>
</details>

<details class="api_doc_details request_put">
<summary><span class="method">PUT</span>/filesystem/{path}</summary>
<div>

### Description

Writes a file to the filesystem. The file is sent as the raw request body, do
not use multipart form encoding. If a file already exists at the path it is
overwritten. Parameters are sent as URL query parameters.

### Parameters

Param        | Type    | Required | Default | Description
-------------|---------|----------|---------|----------------------------
path         | string  | true     | none    | Path to store the file at, the file name is the last path component
make_parents | boolean | false    | false   | Create the parent directories of the path if they don't exist
mode         | octal   | false    | 644     | Unix file mode of the new file

Besides these, all the node properties from the `update` action of the POST
endpoint can be set with query parameters as well.

### Returns

On success the node object of the new file is returned.

HTTP 200: OK
```
{
	"type": "file",
	"path": "/documents/todo.txt",
	"name": "todo.txt",
	... // Remaining node fields
}
```

#### Possible errors

Value                       | HTTP status | Description
----------------------------|-------------|----------------------------------------------------------------
not_found                   | 404         | The bucket in the first path component does not exist
path_not_found              | 404         | The parent directory does not exist and `make_parents` is not enabled
authentication_required     | 401         | The `me` bucket was requested without an API key
permission_denied           | 400         | You do not have write permission on this path
invalid_content_type        | 400         | You sent multipart form data, send the file as the raw request body instead
error_reading_input         | 400         | The upload was interrupted before it completed
name_too_long               | 422         | The file name is longer than 255 bytes
path_too_long               | 422         | The resulting path would be longer than 4095 bytes
too_many_nested_levels      | 400         | The path contains more than 64 nested directories
too_many_children           | 400         | The target directory has too many files
node_is_not_a_directory     | 400         | One of the parent path components is a file
user_out_of_space           | 400         | The filesystem owner's account has run out of storage space
out_of_transfer             | 400         | The filesystem owner has run out of premium data transfer
internal                    | 500         | An internal server error occurred
</div>
</details>

<details class="api_doc_details request_post">
<summary><span class="method">POST</span>/filesystem/{path}</summary>
<div>

### Description

Modifies the filesystem. The form parameter `action` selects the operation to
perform. Parameters are sent as form values, either URL-encoded or multipart.

### Action mkdir / mkdirall

Creates a new directory at the path. `mkdir` requires the parent directory to
exist, `mkdirall` also creates the missing parent directories.

Param  | Type    | Required | Default | Description
-------|---------|----------|---------|----------------------------
action | enum    | true     | none    | 'mkdir' or 'mkdirall'
mode   | octal   | false    | 700     | Unix file mode of the new directory

HTTP 201: Created
```
{
	"success": true,
	"value": "created",
	"message": "The submitted resource was created"
}
```

### Action rename

Moves or renames a node. Note that the target path must include the bucket as
the first path component, like `/me/documents/new_name.txt`.

Param        | Type    | Required | Default | Description
-------------|---------|----------|---------|----------------------------
action       | enum    | true     | none    | 'rename'
target       | string  | true     | none    | The new path of the node, including the bucket
make_parents | boolean | false    | false   | Create the parent directories of the target path if they don't exist

On success the node object of the renamed node is returned.

### Action update

Changes the metadata of a node. Only the parameters which are sent with the
request are updated. On success the updated node object is returned.

Param                | Type    | Description
---------------------|---------|----------------------------
action               | enum    | 'update'
created              | time    | Creation time of the node, RFC 3339 format
modified             | time    | Modification time of the node, RFC 3339 format
mode                 | octal   | Unix file mode
shared               | boolean | When set to true the node gets a public ID which can be used to access it without an account. When set to false the sharing link is disabled
logging_enabled      | boolean | Enables the activity log on a directory, see the `change_log` API. Logging is automatically disabled again 24 hours after the log was last read
link_permissions     | JSON    | The permissions for people who open the sharing link, example: `{"read": true, "write": false, "delete": false}`
user_permissions     | JSON    | Map of pixeldrain usernames to permission objects, these users get access to the node when logged in
password_permissions | JSON    | Map of passwords to permission objects
custom_domain_name   | string  | Custom domain name to serve this node on, requires a paid plan with the custom domain feature
custom_domain_cert   | string  | TLS certificate for the custom domain, PEM encoded
custom_domain_key    | string  | TLS private key for the custom domain, PEM encoded

There are also branding parameters which can be used to customize the look of
shared directories: `branding_enabled` ('true' or 'false'),
`brand_input_color`, `brand_highlight_color`, `brand_danger_color`,
`brand_background_color`, `brand_body_color`, `brand_card_color` (all
hexadecimal color codes like '#ff0000'), `brand_header_image`,
`brand_background_image` (IDs of image files in the same filesystem) and
`brand_header_link` (URL to open when the header is clicked). Sending an empty
value removes the property.

The permissions parameters can only be changed by the owner of the filesystem.

### Action import

Copies files from your pixeldrain file list to a directory in the filesystem.

Param  | Type | Required | Description
-------|------|----------|----------------------------
action | enum | true     | 'import'
files  | JSON | true     | JSON array of file IDs to import, example: `["abc123", "123abc"]`

HTTP 201: Created
```
{
	"success": true,
	"value": "created",
	"message": "The submitted resource was created"
}
```

### Possible errors

Value                       | HTTP status | Description
----------------------------|-------------|----------------------------------------------------------------
not_found                   | 404         | The bucket in the first path component does not exist
path_not_found              | 404         | No file or directory exists at this path
authentication_required     | 401         | The `me` bucket was requested without an API key
permission_denied           | 400         | You do not have write permission on this path
invalid_action              | 400         | The `action` parameter is not one of the documented actions
cannot_modify_root_dir      | 400         | The root directory of a filesystem cannot be modified
node_already_exists         | 400         | A file or directory with this name already exists
name_too_long               | 422         | The new name is longer than 255 bytes
path_too_long               | 422         | The resulting path would be longer than 4095 bytes
too_many_nested_levels      | 400         | The path contains more than 64 nested directories
too_many_children           | 400         | The target directory has too many files
node_is_not_a_directory     | 400         | The operation requires a directory but the path is a file
circular_dependency         | 400         | A directory cannot be moved into itself
user_not_found              | 404         | A username in `user_permissions` does not exist
invalid_x509_keypair        | 400         | The custom domain certificate and key could not be parsed
list_file_not_found         | 422         | One of the file IDs in the `import` action does not exist
json_parse_failed           | 422         | A JSON parameter could not be parsed
internal                    | 500         | An internal server error occurred
</div>
</details>

<details class="api_doc_details request_delete">
<summary><span class="method">DELETE</span>/filesystem/{path}</summary>
<div>

### Description

Deletes a file or directory. Directories can only be deleted when they are
empty, unless the `recursive` parameter is used.

### Parameters

Param     | Required | Location | Description
----------|----------|----------|-------------------------
path      | true     | URL      | Path of the node to delete
recursive | false    | URL      | Also delete the contents of a directory

### Returns

HTTP 200: OK
```
{
	"success": true,
	"value": "ok",
	"message": "The requested action was successfully performed"
}
```

#### Possible errors

Value                     | HTTP status | Description
--------------------------|-------------|----------------------------------------------------------------
not_found                 | 404         | The bucket in the first path component does not exist
path_not_found            | 404         | No file or directory exists at this path
authentication_required   | 401         | The `me` bucket was requested without an API key
permission_denied         | 400         | You do not have delete permission on this path
directory_not_empty       | 400         | The directory is not empty and `recursive` is not enabled
internal                  | 500         | An internal server error occurred
</div>
</details>
