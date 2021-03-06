# clean-stream-filters

This repository contains filters for editing movies for inappropriate content. These filters are designed to be used with the chrome extension [Clean Stream](https://chrome.google.com/webstore/detail/clean-stream/cppacmdbokpnibcconbcniibodcfgdba) but can be used freely by other filtering services.

## The .filter File Format

The .filter files are simple text files with the following layout:

```
title //this signifies that the title of the filter is on the following line
filter1 //this is the title of the filter
type //this signifies that the type of the filter is on the following line
audio //this is the type of filter1. The type can be either "audio" or "audiovisual"
category //this signifies that the category of the filter is on the following line
test //this is the category of filter1
description //this signifies that the description of the filter is on the following line
This is a test filter.
startTime //this signifies that the startTime of the filter is on the following line
3 //this is the start time of filter1
endTime //this signifies that the endTime of the filter is on the following line
8 //this is the end time of filter1

title //the above space signifies that the first filter is defined and the next filter has now being defined
filter2
type
audiovisual
category
test
description
This is a test filter.
startTime
10
endTime
15
```

## Retrieving and Parsing the Filters

Below are some functions in javascript that will get a filter file and parse it into an array of objects.

```javascript
getFilterFileContent = function (url, callback) {
	//get the contents of the file
	var request = new XMLHttpRequest();
	request.open('GET', url, true);
	request.send(null);
	request.onreadystatechange = function () {
		if (request.readyState === 4 && request.status === 200) {
			var type = request.getResponseHeader('Content-Type');
			if (type.indexOf("text") !== 1) {
				//calls the specified callback method and passes the content of the filter file as a string
				callback(request.responseText);
			}
		}
	}
}

parseFilterFileContent = function (ffc) {
	//get an array containing each line
	var lines = ffc.split("\n");
	
	var filters = new Array();
	var l = 0;
	var filter = new Object;
	while (l<lines.length) {
		if (lines[l]!=="") {
			//adds a property to the filter with its value
			filter[lines[l++]] = lines[l++];
		}
		else {
			//adds the filter to the array of filters and resets the filter variable
			filters.push(filter);
			filter = new Object;
			l++;
		}
	}
	
	//returns the filters as an array
	return filters;
}
```

The code below uses the two functions above and prints the results to the console in chrome.

```javascript
getFilterFileContent("https://raw.githubusercontent.com/yolodevelopers/clean-stream-filters/master/test.filter", function (ffc) {
	var filters = parseFilterFileContent(ffc);
	console.log(filters);
});
```
