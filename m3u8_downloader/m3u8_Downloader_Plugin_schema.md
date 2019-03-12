# m3u8 Downloader Plugin schema

Note: you don't nned to create any `Activity` for a plugin. Normally, a `Service` is enough.

## 1. Import `.aidl` files
Create a new project. In folder `[packageName]\src\main` , create new folder path `\aidl\com\leavjenn\m3u8downloader`. Then, add the following 2 `.aidl` files in this folder.

__IExtractor.aidl__
```
package com.leavjenn.m3u8downloader;

import com.leavjenn.m3u8downloader.IExtractorCallback;

interface IExtractor {
    void extract(in Map input, IExtractorCallback callback);
}
```

__IExtractorCallback.aidl__
```
package com.leavjenn.m3u8downloader;

interface IExtractorCallback {
    void onSuccess(inout List result);

    void onFailure(String msg);
}
```


## 2. Create a `Service` class
The `Service` name should follow the website name `[WebsiteName]`. For example, the name for rtllatenight.nl should be: `RtlLateNight.kt` or `RtlLateNight.java`. We'll talk about this `Service` later in step 4.


## 3. Edit `AndroidManifest.xml` file
Add below code in the service block of the `AndroidManifest.xml`:
```
<intent-filter>
	<action android:name="com.leavjenn.m3u8downloader.intent.action.UPDATE_PLUGIN"/>
</intent-filter>
```
After this change:

__AndroidManifest.xml__
```
...
<application
	...
	<service
			android:name=".[WebsiteName]"
			android:enabled="true"
			android:exported="true">
		<intent-filter>
			<action android:name="com.leavjenn.m3u8downloader.intent.action.UPDATE_PLUGIN"/>
		</intent-filter>
	</service>
	...
</application>
```


## 4. Edit the `Service`
Get the URL from the `IExtractor.extract(in Map input, IExtractorCallback callback)` callback. The map type is `Map<String, String>`. The key for the URL is `"url"`.

You should check if the URL matches the website URL schame first. If it doesn't, the plugin must return `"URL not match"` via `IExtractorCallback.onFailure(String msg)`.

If get the m3u8 URL successfully, return the result via `IExtractorCallback.onSuccess(inout List result)` callback. The list type is `List<String>`. The first item in the list must be the m3u8 URL, others are the HTTP Headers if necessary.

For example: 
```
IExtractorCallback.onSuccess(arrayListOf(
		"https://www.the_m3u8_file_link.com",
		"Cookie", "abcdefg",
		"Referer", "https://www.example.com"
	))
```

Contect me to add this plugin in the community list: `leavjenn+m3u8downloader@gmail.com`.