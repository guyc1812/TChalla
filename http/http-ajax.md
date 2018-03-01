# AJAX

Asynchronous JavaScript And XML.<br>
AJAX is a misleading name. AJAX applications might use XML to transport data, but it is equally common to transport data as plain text or JSON text.

* Update a web page without reloading the page
* Request data from a server - after the page has loaded
* Receive data from a server - after the page has loaded
* Send data to a server - in the background

```
function loadDoc() {
  var xhttp = new XMLHttpRequest();
  xhttp.onreadystatechange = function() {
    if (this.readyState == 4 && this.status == 200) {
     document.getElementById("demo").innerHTML = this.responseText;
    }
  };
  xhttp.open("GET", "ajax_info.txt", true);
  xhttp.send();
}
```

ajax in jquery
```
$("#div1").load("demo_test.txt #p1");
```
```
$.get(URL,callback);
```
```
$.post(URL,data,callback);
```
```
$.ajax({
    url: url,
    type: 'PUT',  //'DELETE','GET','POST'
    success: callback,
    data: data,
    contentType: type
});
```
```
var data = new FormData();  
data.append('file', file, file.name);
var xhr = new XMLHttpRequest();     
xhr.open('POST', 'upload.php', true);  
xhr.send(data);
xhr.onload = function () {
    //get response and show the uploading status
    var response = JSON.parse(xhr.responseText);
    if(xhr.status === 200 && response.status == 'ok'){
        //ok
    }
};
```

# The XMLHttpRequest Object

All modern browsers support the XMLHttpRequest object.<br>
The XMLHttpRequest object can be used to exchange data with a server behind the scenes. <br>
This means that it is possible to update parts of a web page, without reloading the whole page.

* XMLHttpRequest Object Properties

    | Property	          | Description  |
    |:-------------------|:-------------|
    | onreadystatechange | Defines a function to be called when the readyState property changes |
    | readyState	     | Holds the status of the XMLHttpRequest.<br>0: request not initialized <br>1: server connection established<br>2: request received <br>3: processing request <br>4: request finished and response is ready |
    | responseText	     | Returns the response data as a string |
    | responseXML	     | Returns the response data as XML data |
    | status	         | Returns the status-number of a request<br>200: "OK"<br>403: "Forbidden"<br>404: "Not Found"<br>For a complete list go to the Http Messages Reference |
    | statusText	     | Returns the status-text (e.g. "OK" or "Not Found") |

* XMLHttpRequest Object Methods

    | Method	                        | Description |
    |:----------------------------------|:------------|
    | new XMLHttpRequest()              | Creates a new XMLHttpRequest object |
    | abort()	                        | Cancels the current request |
    | getAllResponseHeaders()	        | Returns header information |
    | getResponseHeader()	            | Returns specific header information |
    | open(method,url,async,user,psw)	| Specifies the request<br>method: the request type GET or POST<br>url: the file location<br>async: true (asynchronous) or false (synchronous)<br>user: optional user name<br>psw: optional password |
    | send()	                        | Sends the request to the server<br>Used for GET requests |
    | send(string)	                    | Sends the request to the server<br>Used for POST requests |
    | setRequestHeader()	            | Adds a label/value pair to the header to be sent |

