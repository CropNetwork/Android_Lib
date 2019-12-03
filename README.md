# CROP DMP Android Lib

Android module for sending application data to CROP Network API and getting ifrme URL with Ads.

## Installation

Import module `CROP_DMP_Android_Lib.aar` to project and to `build.gradle` of `app` set:

```plaintext
dependencies {
    implementation project(':CROP_DMP_Android_Lib')
    implementation 'com.google.code.gson:gson:2.8.5'
    implementation 'commons-codec:commons-codec:1.9'
}
```

Up-to-date versions of the dependencies should be set.

## Partnership key

To access the key to CROP Network API, adding an app at [the publisher cabinet](https://partners.crop.network/publisher/apps) on [the Partners Crop Network site](https://partners.crop.network) is necessary.

## Usage

Create an exemplar of the `CROPNetworkDMP` class and pass the parameter `partnership key` and `context`. Next, you can pass data in whole or in parts by entering `sendJSON()` or `sendMap()`.

Data transfer can be organized by events or as data occur.

```java
String partnerKey = "some partner's key";

final CROPNetworkDMP cn = new CROPNetworkDMP(partnerKey, getApplicationContext());

// you can pass JSON as a string
String data = "{\"phone\": \"1-844-984-1578\", \"email\": \"anonymous.user@gmail.com\"}";

cn.sendJSON(data);

// or you can pass HashMap object
Map<String, Object> map = new HashMap<>();
map.put("phone", "1-844-984-1578"); // IMPORTANT: field name must be "phone" and it value must be in international format "0-000-000-0000"
map.put("email", "anonymous.user@gmail.com");
map.put("occupation", new String[] {"designer", "architector", "writer"});

cn.sendMap(map);

// getting of iframe URL
cn.getResponse();

// getting of default iframe URL
cn.getDefault();
```

To check a positive response (not default iframe URL), use call method `isResponse()`:

```java
String iframeUrl;

if (cn.isResponse()) {
    iframeUrl = cn.getResponse();
} else {
    iframeUrl = "Ads from other provider";
}
```

To get a default iframe URL string, call method `getDefault()`.

To track conversions from application pages, use method `getUniqueId()`:

```java
// return String with unique id of user (equals internal user id in CROP Network system)
// if ID is absent, it returns an empty string
cn.getUniqueId();
// or the same
cn.getUniqueId("id");

// return the name of query param (`crntid`)
cn.getUniqueId("name");

// return the whole query string (`crntid=some_id`)
cn.getUniqueId("query");
```

More details about practical usage can be found in the [example](Reference_Implementation.md).

**To save traffic:** if the transfered data is the same as the one transfered before, no data will be sent to the remote API:

```plaintext
event1: {name: 'John'} // data will be sent
event2: {name: 'John'} // data won't be sent
event3: {name: 'John'} // data won't be sent
event4: {name: 'Jack'} // data will be sent
event5: {name: 'John'} // data will be sent
event6: {name: 'John'} // data won't be sent
event7: {name: 'Karol'} // data will be sent
event8: {name: 'Karol'} // data won't be sent
```

## Data structure and sending sensitive data

All sensitive data, including name, surname, address, email, different IDs, etc, must be entered in a separate field called `hashData`. **Options in `hashData` cannot contain nested options or arrays.**

Other data (insensitive) must put to field `data`.

```javascript
{
    hashData: { // optional
        appId: String,
        advId: String,
        email: String,
        ...
        address: String,
        phone: String,
        etc: ...
    },
    data: { // optional
        // insensitive options
    },
    adsOptions: {
        type: String, // optional,
        width: Number, // optional
        height: Number, // optional
        headline: {
            fontSize: Number, // optional
            lineHeight: Number, // optional
            textTransform: String, // optional
            textDecoration: String, // optional
            color: String // optional
        },
        description: {
            fontSize: Number, // optional
            lineHeight: Number, // optional
            textTransform: String, // optional
            textDecoration: String, // optional
            color: String // optional
        },
        brandName: {
            fontSize: Number, // optional
            textTransform: String, // optional
            textDecoration: String, // optional
            color: String // optional
        },
        actionText: {
            fontSize: Number, // optional
            textTransform: String, // optional
            textDecoration: String, // optional
            color: String, // optional
            background: String, // optional
            border: String, // optional
            borderRadius: Number, // optional
            padding: Number/String // optional
        },
        background: String, // optional
        secondBackground: String // optional
    },
    marketingOptions: { // optional
        screenId: String,
        keywords: [ String ]
    }
}
```

All data in `hashData` block is encrypted twice: 1) before sending data (client-side); 2) on the API-server. Then, it is stored in the database as an anonymous hash. `hashData` object can include any field with any number that needs to be anonymized.

`data` object can include any field with any number of nested objects levels.

**IMPORTANT:** Option names can include anything. However, in `hashData` block, you need to set particular names for certain fields:

* `email` - email
* `phone` - phone number in international format **"0-000-000-0000"**.
* `address` - address
* `advertisingId` - advertising ID

An example and the description of `adsOptions` object can be found [here](adsOptions_obj.md).

You can send data in an array format. The array must include collections with the following structure:

```javascript
[
    {
        hashData: {},
        data: {},
        adsOptions: {},
        ...
    },
    {
        hashData: {},
        data: {},
        adsOptions: {},
        ...
    },
    ...
]
```

**All data from array will be stored, but only the first collection will be analyzed for ads selection.**

## Advice

To maximize conversion, it is strongly recommended to set sending methods on each activity, including the starting one (first screen, MainActivity, etc).

If on the start screen there is no data for sending, you can pass an empty object/string to sending method, or call it without any arguments:

```java
Map<String, Object> map = new HashMap<>();
cn.sendMap(map);
// or
cn.sendMap();

// or
cn.sendJSON("");
// or
cn.sendJSON();
```

## Ads display

To display an ad, you need to:

1. Send data to CROP Network API with object `adsOptions` that consists of options `width` & `height`, or option `status` with the value `true`.
2. Check the existence of an ad for this user in API response with the method `isResponse()`.
3. If ad exists, get ad iframe URL by `getResponse()` method. Else: use URL from another provider.
4. Set WebView component with the ad iframe URL.

Ad iframe URL stores can be used on the next screens. For example, data can be sent (`sendMap()`) on the first (start) screen, and an ad block can be displayed on the second screen. This allows avoiding the delays with requests to API.

```java
// first screen (sending data & receiving iframe URL)
cn.sendMap(map);

// second screen
String iframeURL = cn.isResponse() ? cn.getResponse() : "Ad iframe URL from other provider";

// third screen (if necessary)
String iframeURL = cn.isResponse() ? cn.getResponse() : "Ad iframe URL from other provider";

// ...
```

Stored iframe URL can be changed by next sending data or expiry time of ad life (it sets by publisher through admin panel or by system). By default ad actual period equals 30 minutes.

## API

The library implements folowing public methods:

* `sendMap()` - sends an empty JSON string to API-server.
* `sendMap(Map map)` - accepts a Map object and sends JSON string to API-server.
* `sendJSON()` - sends an empty JSON string to API-server.
* `sendJSON(String str)` - sends a JSON string to API-server.
* `getResponse()` - returns the string with URL of iframe.
* `getDefault()` - returns the string with default URL of iframe.
* `isResponse()` - returns a boolean value of existing not default Ads iframe URL value.
* `getUniqueId()` - returns the string with unique id of user.
* `getUniqueId(String str)` - returns the string with unique id of user or query param or whole query string (see Usage section).

## Test mode

To test, use the flag `"test"`. This flag will prevent writing to the database and will return a URL to the test iframe:

```java
String partnerKey = "some partner's key";

final CROPNetworkDMP cn = new CROPNetworkDMP(partnerKey, getApplicationContext(), "test");
```

Don't forget to remove it for production.

## Advertising iframe URL caching

To cache an advertising iframe URL, use optional flag `"async"` (as the fourth param). The `"sync"` flag or its absence will return the API response result simultaneously (delay mechanisms or etc. may be needed). The `"sync"` value is a default flag.

```java
String partnerKey = "some partner's key";

final CROPNetworkDMP cn = new CROPNetworkDMP(partnerKey, getApplicationContext(), "prod", "async");
```

## Degraded cell network or disabled network connection

If can't one resolve hostname or send data to `api.crop.network` server, the data is gathered into local App storage and is sent later.

## Possible problems

[Read about possible problems with library integration and advertising.](Possible_Problems_Solution.md)

## Support

All issues/questions and suggestions can be sent to email: support@crop.network
