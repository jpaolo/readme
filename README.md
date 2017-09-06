# DocsOrganizer
This project provides RESTful APIs to simulate documents (specifically, Email messages in this demo) organized by tags.

## How to Deploy
	1. Install Maven
	2. Install Jboss (tested with Jboss 6.2)
	3. Download project DocsOrganizer
	4. Run 'mvn clean install' from root directory
	5. Start Jboss and add target/doc-organizer-service.war to deployments

## More Project Info

### Entities
- Email Message: An Email message has a message in string format and a set of tags, as well as a unique id in UUID format
- Tag: A tag has a tag name and an access boolean property called readOnly


### APIs
- @GET	/v1/mails/	 								- returns all emails
		response codes:
			200 OK

- @GET	/v1/mails/query?tag={tag name}				- returns all emails which contain a tag with the given tag name
		response codes:
			200 OK
			400 BAD_REQUEST: tag name is invalid
			404 NOT_FOUND: tag not found
			500 INTERNAL_SERVER_ERROR: unexpected errors

- @GET	/v1/mails/{mail id}							- This simulates user opening an email message.
		response codes:
			200 OK
			400 BAD_REQUEST: mail id is invalid
			404 NOT_FOUND: email not found
			500 INTERNAL_SERVER_ERROR: unexpected errors

- @PUT	/v1/mails/{mail id}/add?tag={tag name}		- add a tag to an email
		response codes:
			200 OK
			400 BAD_REQUEST: mail id or tag name is invalid
			404 NOT_FOUND: email or tag not found
			409 CONFLICT: email already has this tag
			500 INTERNAL_SERVER_ERROR: unexpected errors

- @PUT	/v1/mails/{mail id}/remove?tag={tag name}	- remove a tag from an email
		response codes:
			200 OK
			400 BAD_REQUEST: mail id or tag name is invalid, or email doesn't have this tag
			404 NOT_FOUND: email or tag not found
			500 INTERNAL_SERVER_ERROR: unexpected errors

- @DELETE /v1/mails/{mail id}						- delete an email
		response codes:
			204 NO_CONTENT
			400 BAD_REQUEST: mail id is invalid
			404 NOT_FOUND: email not found
			500 INTERNAL_SERVER_ERROR: unexpected errors

- @GET	/v1/tags											- returns all existing tags
		response codes:
			200 OK
			
- @POST	/v1/tags/	payload with tag in json format			- creates a new tag
		header: 
			Content-Type: application/json
		response codes:
			200 OK
			409 CONFLICT: a tag with the same name already exists
			500 INTERNAL_SERVER_ERROR: unexpected errors

- @PUT	/v1/tags/{tag name}/rename?toname={new tag name}	- rename a tag
		response codes:
			200 OK
			400 BAD_REQUEST: tag name is invalid
			403 FORBIDDEN: tag is read-only
			404 NOT_FOUND: tag not found
			500 INTERNAL_SERVER_ERROR: unexpected errors

- @PUT	/v1/tags/{tag name}/access?readonly={true | false}	- modify access of a tag (i.e. toggle value of readOnly property)
		response codes:
			200 OK
			400 BAD_REQUEST: tag name is invalid
			403 FORBIDDEN: tag is read-only
			404 NOT_FOUND: tag not found
			409 CONFLICT: tag already has the same access value as input
			500 INTERNAL_SERVER_ERROR: unexpected errors

- @DELETE /v1/tags/{tag name}								- delete a tag (as a confirmation: this requires a header docs_with_tag_count set to the number of mails containing this tag)
		header: 
			docs_with_tag_count: {number of emails containing this tag}
		response codes:
			204 NO_CONTENT
			400 BAD_REQUEST: tag name is invalid
			403 FORBIDDEN: tag is read-only
			404 NOT_FOUND: tag not found
			500 INTERNAL_SERVER_ERROR: unexpected errors


### Rules
- If a tag is read-only (readOnly property set to true), it cannot be deleted or renamed
- The access property for a tag can be modified via one of the RESTful APIs. Once a tag has the value of this property changed to 'false', it can be deleted or renamed
- Each Email may have 0 to multiple tags
- To delete a tag, client should first check with emails containing the tag, then pass the number of such emails to a header docs_with_tag_count as a confirmation

