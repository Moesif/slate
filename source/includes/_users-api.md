# Users API

## Update a User

**`POST https://api.moesif.net/v1/users`**

Updates an end user profile in Moesif.

A custom JSON object can be placed in the `metadata` object which
will be stored as part of the user profile.

If present, Moesif will detect special metadata fields like:

  - email
  - name
  - first_name
  - last_name
  - phone
  - photo_url

Updating an end user will create one if it does not exist,
also know as [upsert](https://en.wikipedia.org/wiki/Merge_(SQL))
If a user exists, it will be merged on top of existing fields.
Any new field set will override the existing fields.
This is done via recursive merge which merges inner objects.

<aside class="info">
Replace <i>my_application_id</i> with your real Application Id
</aside>

<blockquote class="lang-specific yaml">
<code><b>POST</b> https://api.moesif.net/v1/users</code>
<br><br><i>Example Request Body:</i><br>
</blockquote>
```yaml
  {
    "modified_time": "2017-02-22T04:45:42.914",
    "ip_address": "61.48.220.123",
    "session_token": "df32dkj32opxzfdmji4hf4fs98y18cx98q3yhwmnhcfx43f",
    "user_id": "123456",
    "user_agent_string": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36",
    "metadata": {
      "first_name": "John Doe",
      "last_name": "John Doe",
      "email": "john@gmail.com",
      "phone": "123-456-7890",
      "custom_string_field": "some_value"
      "custom_int_field": 55,
      "custom_obj_field": {
          "sub_a": "value_a",
          "sub_b": "value_b"
      }
    }
  }

```

```javascript
// 1. Import the module
var moesifapi = require('moesifapi');
var api = moesifapi.ApiController;

// 2. Configure the ApplicationId
var config = moesifapi.configuration;
config.ApplicationId = "my_application_id";

// 3. Generate a User Model
var user = {
    userId: "my_user_id",
    sessionToken: "23jdf0owekfmcn4u3qypxg09w4d8ayrcdx8nu2ng]s98y18cx98q3yhwmnhcfx43f",
    metadata: {
      email: "johndoe@acmeinc.com",
      string_field: "value_1",
      number_field: 0,
      object_field: {
        field_a: "value_a",
        field_b: "value_b"
      }
    }
};

// 4. Create a single user
api.updateUser(new UserModel(user), function(error, response, context) {
  // Do Something
});
```

```python
```

```ruby
```

```php
```

```go
```

```csharp
```

```java
MoesifAPIClient client = new MoesifAPIClient("my_application_id");
APIController api = getClient().getAPI();

UserModel user = new UserBuilder()
    .userId("12345")
    .modifiedTime(new Date())
    .ipAddress("29.80.250.240")
    .sessionToken("di3hd982h3fubv3yfd94egf")
    .userAgentString("Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2228.0 Safari/537.36")
    .metadata(APIHelper.deserialize("{" +
        "\"email\": \"johndoe@acmeinc.com\"," +
        "\"string_field\": \"value_1\"," +
        "\"number_field\": 0," +
        "\"object_field\": {" +
          "\"field_1\": \"value_1\"," +
          "\"field_2\": \"value_2\"" +
        "}" +
      "}"))
     .build();

// Asynchronous Call to update user
APICallBack<Object> callBack = new APICallBack<Object>() {
    public void onSuccess(HttpContext context, Object response) {
      // Do something
    }

    public void onFailure(HttpContext context, Throwable error) {
      // Do something else
    }
};

api.updateUserAsync(user, callBack);

// Synchronous Call to update user
api.updateUser(user, callBack);
```

### _user_id_ vs. _session_token_
Users in Moesif are identified by two attributes: _user_id_ and _session_token_.

- A `user_id` is a __permanent__ and unique identifier to track a user across platforms and services.
It is recommended to set the Moesif user_id field with the id used in your own databases and services.

- A `session_token` may be __temporary__ or can expire unlike a user_id.
Examples include [JSON Web Tokens's](https://jwt.io/introduction/) (JWT), API keys, and session ids.
Moesif can _alias_ multiple session_token's to the same user_id. To create a new alias,
call the update user API with at least the user_id and session_token set.
The new session_token will be appended to the alias table.

Fields | Required? | Description
--------- | -------- | -----------
user_id | __Required__ | Your application's `user_id` to identify this user.
modified_time | Optional | Last Modified Timestamp for the user in ISO 8601 format (Set automatically if not provided).
ip_address | Optional | Current IP address of the user, If not set, we use the IP address of the POST request.
session_token | Optional | Current end user session or API token such as a JWT. Setting this field does not remove old session tokens previously stored. Instead, Moesif will append the new value to an alias table for this user_id.
user_agent_string | Optional | If you want Moesif to parse a user agent string, you can do so via `user_agent_string`.
metadata | Optional | A JSON Object consisting of any custom metadata to be stored with this user.

## Update Users in Batch

**`POST https://api.moesif.net/v1/users/batch`**

Updates a list of end user profile in Moesif.

A custom JSON object can be placed in the `metadata` object of each user
which will be stored as part of the user profile.

If present, Moesif will detect special metadata fields like:

  - email
  - name
  - first_name
  - last_name
  - phone
  - photo_url

If user does not exist, a new one will be created.
If a user exists, it will be merged on top of existing fields.
Any new field set will override the existing fields.
This is done via recursive merge which merges inner objects.

<aside class="info">
Replace <i>my_application_id</i> with your real Application Id
</aside>

<blockquote class="lang-specific yaml">
<code><b>POST</b> https://api.moesif.net/v1/users/batch</code>
<br><br><i>Example Request Body:</i><br>
</blockquote>
```yaml
  [
    {
      "modified_time": "2017-02-22T04:45:42.914",
      "ip_address": "61.48.220.123",
      "session_token": "df32dkj32opxzfdmji4hf4fs98y18cx98q3yhwmnhcfx43f",
      "user_id": "12345",
      "user_agent_string": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36",
      "metadata": {
        "first_name": "John Doe",
        "last_name": "John Doe",
        "email": "john@gmail.com",
        "phone": "123-456-7890",
        "custom_string_field": "some_value"
        "custom_int_field": 55,
        "custom_obj_field": {
            "sub_a": "value_a",
            "sub_b": "value_b"
        }
      }
    },
    {
      "modified_time": "2017-02-22T04:45:42.914",
      "ip_address": "61.48.220.129",
      "session_token": "d2ewzcazchurvcqdevnhcuiyrgvru",
      "user_id": "7890",
      "user_agent_string": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36",
      "metadata": {
        "first_name": "Mary Doe",
        "last_name": "Mary Doe",
        "email": "mary@gmail.com",
        "phone": "123-456-7890",
        "custom_string_field": "some_value"
        "custom_int_field": 55,
        "custom_obj_field": {
            "sub_a": "value_a",
            "sub_b": "value_b"
        }
      }
    }
  ]

```

```javascript
var moesifapi = require('moesifapi');
var api = moesifapi.ApiController;

// 2. Configure the ApplicationId
var config = moesifapi.configuration;
config.ApplicationId = "my_application_id";

// 3. Generate an API Event Model
var userA = {
    userId: "12345",
    sessionToken: "23jdf0owekfmcn4u3qypxg09w4d8ayrcdx8nu2ng]s98y18cx98q3yhwmnhcfx43f",
    metadata: {
      email: "johndoe@acmeinc.com",
      string_field: "value_1",
      number_field: 0,
      object_field: {
        field_a: "value_a",
        field_b: "value_b"
      }
    }
};

var userB = {
    userId: "6789",
    sessionToken: "23jdf0oszfexfqe[lwjfiefovprewv4d8ayrcdx8nu2ng]zfeeadedefx43f",
    metadata: {
      email: "maryjane@acmeinc.com",
      string_field: "value_1",
      number_field: 1,
      object_field: {
        field_a: "value_a",
        field_b: "value_b"
      }
    }
};

var users = [new UserModel(userA), new UserModel(userB)];

// 4. Send batch of events
api.updateUsersBatch(users, function(error, response, context) {
  // Do Something
});
```

```java
MoesifAPIClient client = new MoesifAPIClient("my_application_id");
APIController api = getClient().getAPI();

List<UserModel> users = new ArrayList<UserModel>();

UserModel userA = new UserBuilder()
    .userId("12345")
    .modifiedTime(new Date())
    .ipAddress("29.80.250.240")
    .sessionToken("di3hd982h3fubv3yfd94egf")
    .userAgentString("Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2228.0 Safari/537.36")
    .metadata(APIHelper.deserialize("{" +
        "\"email\": \"johndoe@acmeinc.com\"," +
        "\"string_field\": \"value_1\"," +
        "\"number_field\": 0," +
        "\"object_field\": {" +
          "\"field_1\": \"value_1\"," +
          "\"field_2\": \"value_2\"" +
        "}" +
      "}"))
    .build();
users.add(userA);

UserModel userB = new UserBuilder()
    .userId("56789")
    .modifiedTime(new Date())
    .ipAddress("21.80.11.242")
    .sessionToken("zceadckekvsfgfpsakvnbfouavsdvds")
    .userAgentString("Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2228.0 Safari/537.36")
    .metadata(APIHelper.deserialize("{" +
        "\"email\": \"maryjane@acmeinc.com\"," +
        "\"string_field\": \"value_1\"," +
        "\"number_field\": 1," +
        "\"object_field\": {" +
          "\"field_1\": \"value_1\"," +
          "\"field_2\": \"value_2\"" +
        "}" +
      "}"))
    .build();
users.add(userB);

// Asynchronous call to update users
APICallBack<Object> callBack = new APICallBack<Object>() {
    public void onSuccess(HttpContext context, Object response) {
      // Do something
    }

    public void onFailure(HttpContext context, Throwable error) {
      // Do something else
    }
};

api.updateUsersBatchAsync(users, callBack);


//Synchronous call to update users
api.updateUsersBatch(users, callBack);
```

### _user_id_ vs. _session_token_
Users in Moesif are identified by two attributes: _user_id_ and _session_token_.

- A `user_id` is a __permanent__ and unique identifier to track a user across platforms and services.
It is recommended to set the Moesif user_id field with the id used in your own databases and services.

- A `session_token` may be __temporary__ or can expire unlike a user_id.
Examples include [JSON Web Tokens's](https://jwt.io/introduction/) (JWT), API keys, and session ids.
Moesif can _alias_ multiple session_token's to the same user_id. To create a new alias,
call the update user API with at least the user_id and session_token set.
The new session_token will be appended to the alias table.

Fields | Required? | Description
--------- | -------- | -----------
user_id | __Required__ | Your application's `user_id` to identify this user.
modified_time | Optional | Last Modified Timestamp for the user in ISO 8601 format (Set automatically if not provided).
ip_address | Optional | Current IP address of the user, If not set, we use the IP address of the POST request.
session_token | Optional | Current end user session or API token such as a JWT. Setting this field does not remove old session tokens previously stored. Instead, Moesif will append the new value to an alias table for this user_id.
user_agent_string | Optional | If you want Moesif to parse a user agent string, you can do so via `user_agent_string`.
metadata | Optional | A JSON Object consisting of any custom metadata to be stored with this user.