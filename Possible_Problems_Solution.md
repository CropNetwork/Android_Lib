# Possible problems

## Ad's block integration

If the banner is not displayed inside the WebView or is displayed strange? For normal work of the banner inside WebView **it's necessary to activate JavaScript support**:

``` java
WebView adWebView = (WebView) findViewById(R.id.crop_ad_webview);

adWebView.getSettings().setJavaScriptEnabled(true);
```

If, when taping to a banner, the link of the advertisement opens within the WebView window, the following settings must be added:

``` java
WebView adWebView = (WebView) findViewById(R.id.crop_ad_webview);
adWebView.getSettings().setJavaScriptEnabled(true);

adWebView.setWebViewClient(new WebViewClient() {
	@Override
	public boolean shouldOverrideUrlLoading(WebView view, String url) {
		view.loadUrl(url);
		return true;
	}
});
```

or

``` java
WebView adWebView = (WebView) findViewById(R.id.crop_ad_webview);
adWebView.getSettings().setJavaScriptEnabled(true);

adWebView.setWebViewClient(new WebViewClient() {
	@Override
	public boolean shouldOverrideUrlLoading(WebView view, String url) {
		view.getContext().startActivity(
			new Intent(Intent.ACTION_VIEW, Uri.parse(url))
		);
		return true;
	}
});
```
