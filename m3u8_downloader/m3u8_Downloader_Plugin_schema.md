# m3u8 Downloader Plugin schema

## 1. Import `.aidl` files
Create package path `\aidl\com\leavjenn\m3u8downloader` in `[packageName]\src\main` package, create following 2 `.aidl` files in this package.

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


## 2. Create a Service class
The service name should follow the website name [WebsiteName]. For example, rtllatenight should be: RtlLateNight. We'll talk about this service later in step 4.


## 3. Edit Manifest file
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

## 4. Edit the Service
Get the URL from the `IExtractor.extract(in Map input, IExtractorCallback callback)` callback. The map type is `Map<String, String>`. The key for the URL is `"url"`.

You should check the URL first. If it doesn't match, return `"URL not match"` in `IExtractorCallback.onFailure()`.

If success, return the result via `IExtractorCallback.onSuccess(inout List result)` callback. The list type is `List<String>`. The first item in the list should be the m3u8 URL, others are the HTTP Headers if necessary.

For example: 
```
IExtractorCallback.onSuccess(arrayListOf(
		"https://www.the_m3u8_file_link.com",
		"Cookie", "abcdefg",
		"Referer", "https://www.example.com"
	))
```

