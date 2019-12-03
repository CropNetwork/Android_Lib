# Reference Implementation

An example of using the CROP Network Library.

Usage of `sendMap()` method:

```java
import network.crop.dmp.CROPNetworkDMP;


public class MapActivity extends AppCompatActivity {

    @Override
    protected void onCreate() {
        final CROPNetworkDMP cn = new CROPNetworkDMP("partner's key string", getApplicationContext());

        // "hashData" object can include any fields that need to be anonymized, and any number of it
        Map<String, Object> hashDataMap = new HashMap<>();
        hashDataMap.put("...", "...");

        // "data" object can include any fields with any number of nested objects levels
        Map<String, Object> dataMap = new HashMap<>();
        dataMap.put("...", "...");

        // Ad options setting
        // API returns ad iframe URL if "adsOptions" field
        // with options (width & height, or status: true) contains in request body
        Map<String, Object> adsOptionsMap = new HashMap<>();
        adsOptionsMap.put("width", 325);
        adsOptionsMap.put("height", 250);
        adsOptionsMap.put("type", 'text');

        // result object for sending
        Map<String, Object> map = new HashMap<>();
        map.put("hashData", hashDataMap); // Sensitive data block
        map.put("data", dataMap); // Insensitive data block
        map.put("adsOptions", adsOptionsMap); // Visual settings for ads (set it if you need get ads blocks with custom design)

        cn.sendMap(map); // send data to CROP Network API

        setAdsWebView(cn); // pass CROP Network iframe URL to webView
    }

    private void setAdsWebView(final CROPNetworkDMP cn) {
        final WebView adsWebView = (WebView) findViewById(R.id.ad_block_id);

        // some adsWebView settings
        adsWebView.getSettings().setJavaScriptEnabled(true);
        adsWebView.post(new Runnable() {
            @Override
            public void run() {
                // if need display ad on the first screen - necessery to use timeout
                try {
                    TimeUnit.SECONDS.sleep(1); // can change dynamicaly in dependency from Network conditions
                } catch (InterruptedException e) {
                    Log.e("Timeout Error", e.toString());
                }

                // get result with special URL for iframe
                String src = cn.isResponse() ? cn.getResponse() : "Ads from other provider";

                adsWebView.loadUrl(src);
            }
        });
    }

}
```

Instead object creating, you can send data in JSON as a String, using `sendJSON()` method:

```java
String json = "{\"hashData\": {\"...\": \"...\"}, \"data\": {\"...\": \"...\"}}";
cn.sendJSON(json);
````
