[AJAX Async Await](#ajax-async-await)

[Web Worker](#web-worker)

[Async Await And Promise](#async-await-and-promise)

[Async Await](#async-await)

[Generic HTML Template](#generic-html-template)

<hr/>

#### [AJAX Async Await](#ajax-async-await)

> In Chrome Debugger, Insert These Lines To Include jQuery Library

```javascript
var script = document.createElement('script');
script.type = 'text/javascript';
script.src = 'https://code.jquery.com/jquery-3.6.0.min.js';
document.head.appendChild(script);
```

> Async Await For AJAX GET Request

```javascript
function asyncAjax(my_url){
    var ajax_time_out = 60000; // 60 seconds timeout;

    return new Promise(function(resolve, reject) {
        $.ajax({
            type: "GET",
            contentType: "application/json",
            dataType: "json",
            timeout: ajax_time_out,
            url: my_url,
            data: JSON.stringify({ "data": "hello_world" }),
            beforeSend: function() {            
                // add loader
            },
            complete: function() {
                // remove loader
            },
            success: function(data) {
                console.log("success : url : (" + my_url + ")");
                // console.log(data);
                var json_data = {};
                // json_data = JSON.parse(data);
                json_data = data;

                if(!json_data.hasOwnProperty("name")) {
                    console.log("JSON does not have key 'name' !");
                    reject(err) // Reject the promise and go to catch()
                }

                console.log(json_data);

                /*
                $("#results").html(JSON.stringify(json_data));
                */
                resolve(json_data); // Resolve promise and when success
            },
            error: function(data) {
                console.log("error : url : (" + my_url + ")");
                console.log(data);
                reject(err) // Reject the promise and go to catch()
            },
        }); // $.ajax({
    });
}

try {
    let resp = await asyncAjax("https://api.agify.io/?name=bella");
} catch(e){
    console.log(e);
}
```

#### [Web Worker](#web-worker)

`worker.js`

```javascript
var i = BigInt(0);

while (i < 2000000) {
    postMessage("Web Worker Counter: " + i);
    i++;
}
````

`index.html`

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width">
    <title>Web Workers</title>

    <style type="text/css">

    body {
        padding-top:28px;
    }
    
    .output-cont {
        margin-left:12%; margin-top:28px;
    }

    .output-cont h3 {
        width:200px; height:100%;
    }

    .output-cont button {
        padding:4px 8px; font-size:1.1rem; font-family:sans-serif;
    }
    </style>
</head>

<body>

<div class="output-cont"><button onclick="testWorker()">start worker</button><h3 id="workerOutput"></h3><button onclick="terminateWorker()">terminate worker</button></div>
<br/>

<div class="output-cont"><button onclick="testMainThread()">start blocking thread</button><h3 id="mainThreadOutput"></h3></div>
<br/>

<div class="output-cont"><button onclick="alert('browser responsive!')">test browser responsiveness</button></div>


<script>

var worker;

function testWorker() {
    if (typeof(Worker) !== "undefined") {

        if (typeof(worker) == "undefined") {
            worker = new Worker("worker.js");
        }

        worker.onmessage = function(event) {
            document.getElementById("workerOutput").innerHTML = event.data;
        };

    } else {
        document.getElementById("workerOutput").innerHTML = "Web Workers are not supported in your browser";
    }
}

function terminateWorker() { 
    worker.terminate();
    worker = undefined;
}



function testMainThread() {
    var i = BigInt(0);
    for (i = 0; i < 2000000; i++) { 
        document.getElementById("mainThreadOutput").innerHTML = "Main Thread Counter: " + i;

    }
}
</script>

</body>

</html>
```

#### [Async Await And Promise](#async-await-and-promise)
```javascript
async function do_something(time=1) {

    const sleepMilliseconds = time * 1000

    return new Promise(resolve => {

        
        setTimeout(() => {
            resolve({"data": time});
        }, sleepMilliseconds);
        

        /*
        resolve({"data": time});
        */
        
    });
}

async function main() {
    // 1. 
    console.time('main')

    // 2.
    const [firstCall, secondCall, thirdCall] = await Promise.all([
        do_something(3), 
        do_something(6),
        do_something(9)
    ])
    console.log(`First call  : ${firstCall}`);
    console.log(`Second call : ${secondCall}`);
    console.log(`Third call  : ${thirdCall}`);

    // 3.
    console.timeEnd('main');
    let results = {"res1": secondCall, "res2": secondCall, "res3": thirdCall};
    return results
}

let results = await main();

console.log("results >");
console.log(results);
```

#### [Async Await](#async-await)

Assume, Making HTTP GET Call To:
`http://localhost:5000/api/v1/test` takes about 5 seconds to respond

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Document</title>
  <script src="https://code.jquery.com/jquery-3.6.0.min.js" crossorigin="anonymous"></script>
</head>
<body>
  <button id="myBtn">fetch it</button>
  <script>
    </script>
</body>
</html>

<script type="text/javascript">

  async function doAjax(args) {

    console.log("(inside) async function doAjax(args) ...");

    console.log("---[args]---");
    console.log(args);

    let result;
    let myUrl = "http://localhost:5000/api/v1/test";
    try {
      result = await $.ajax({
        url: myUrl,
        type: "GET",
      });
      return result;
    } catch(error) {
      console.log("error : myUrl : (" + myUrl + ")");
      console.log(error);
    }
  }

  async function makeMultipleAjaxCalls() {
    let result1;
    let result2;
    let result3;
    let returnData = {};

    console.log("(inside) async function makeMultipleAjaxCalls() ...");

    result1 = await doAjax("call_1");
    console.log("---[result1]---");
    console.log(result1);

    result2 = await doAjax("call_2");
    console.log("---[result2]---");
    console.log(result2);

    result3 = await doAjax("call_3");
    console.log("---[result3]---");
    console.log(result3);

    returnData["result1"] = result1;
    returnData["result2"] = result2;
    returnData["result3"] = result3;

    return returnData;
  }

  document.getElementById("myBtn").onclick = async () => {
    console.log("clicked !");
    var final_result = await makeMultipleAjaxCalls();
    
    console.log("---[final_result]---");
    console.log(final_result);
  };
</script>
```

The Order Of Output Is:

```
clicked !
enhancements.html:45 (inside) async function makeMultipleAjaxCalls() ...
enhancements.html:20 (inside) async function doAjax(args) ...
enhancements.html:22 ---[args]---
enhancements.html:23 call_1
enhancements.html:48 ---[result1]---
enhancements.html:49 {"error":"","serverResponse":{"response":"ok"}}

enhancements.html:20 (inside) async function doAjax(args) ...
enhancements.html:22 ---[args]---
enhancements.html:23 call_2
enhancements.html:52 ---[result2]---
enhancements.html:53 {"error":"","serverResponse":{"response":"ok"}}

enhancements.html:20 (inside) async function doAjax(args) ...
enhancements.html:22 ---[args]---
enhancements.html:23 call_3
enhancements.html:56 ---[result3]---
enhancements.html:57 {"error":"","serverResponse":{"response":"ok"}}

enhancements.html:70 ---[final_result]---
enhancements.html:71 {result1: '{"error":"","serverResponse":{"response":"ok"}}\n', result2: '{"error":"","serverResponse":{"response":"ok"}}\n', result3: '{"error":"","serverResponse":{"response":"ok"}}\n'}
```

The Order Of Exection Is

```
- call_1
    + Then the program waits for result1
- call_2
    + Then the program waits for result2
- call_3
    + Then the program waits for result3
- Then final_result is printed.
```

#### [Generic HTML Template](#generic-html-template)

```html
<!DOCTYPE html>
<html>
<head>
    <title>Generic HTML5</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://www.w3schools.com/w3css/4/w3.css">

    <link rel="stylesheet" href="https://www.w3schools.com/lib/w3-colors-2021.css">
    <link rel="stylesheet" href="https://www.w3schools.com/lib/w3-colors-2020.css">
    <link rel="stylesheet" href="https://www.w3schools.com/lib/w3-colors-win8.css">

    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">

    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>

    <link rel="stylesheet" type="text/css" href="https://cdn.datatables.net/v/dt/jszip-2.5.0/dt-1.10.25/b-1.7.1/b-colvis-1.7.1/b-html5-1.7.1/cr-1.5.4/fh-3.1.9/kt-2.6.2/r-2.2.9/rr-1.2.8/sc-2.0.4/sb-1.1.0/sp-1.3.0/sl-1.3.3/datatables.min.css"/>

    <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/pdfmake/0.1.36/pdfmake.min.js"></script>
    <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/pdfmake/0.1.36/vfs_fonts.js"></script>
    <script type="text/javascript" src="https://cdn.datatables.net/v/dt/jszip-2.5.0/dt-1.10.25/b-1.7.1/b-colvis-1.7.1/b-html5-1.7.1/cr-1.5.4/fh-3.1.9/kt-2.6.2/r-2.2.9/rr-1.2.8/sc-2.0.4/sb-1.1.0/sp-1.3.0/sl-1.3.3/datatables.min.js"></script>
    <script type="text/javascript" src="https://cdn.datatables.net/select/1.3.3/js/dataTables.select.min.js"></script>
    <script type="text/javascript" src="https://cdn.datatables.net/buttons/1.7.1/js/dataTables.buttons.min.js"></script>

    <script type="text/javascript" src="https://cdn.datatables.net/buttons/1.7.1/js/dataTables.buttons.min.js"></script>
    <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/jszip/3.1.3/jszip.min.js"></script>
    <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/pdfmake/0.1.53/vfs_fonts.js"></script>
    <script type="text/javascript" src="https://cdn.datatables.net/buttons/1.7.1/js/buttons.html5.min.js"></script>
    <script type="text/javascript" src="https://cdn.datatables.net/buttons/1.7.1/js/buttons.print.min.js"></script>


    <link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Roboto">

    <script type="text/javascript" src="https://www.gstatic.com/charts/loader.js"></script>


    <style type="text/css">

    body, h1, h2, h3, h4, h5, table {
        font-family: Roboto, Helvetica,sans-serif !important;
    }

    .main-body {
        margin-top: 10px;
        margin-botton: 10px;
    }


    </style>

    <script type="text/javascript">
        var ajax_time_out = 60000; // 60 secs
        var json_data = {};

    </script>

</head>
<body class="main-body">
    <div class="w3-container">
        <button id="id_btn_pull_data" class="w3-button w3-blue w3-round">Pull Data</button>
        <button id="id_btn_redirect" class="w3-button w3-blue w3-round">Click Here To Redirect To Another URL</button>
    </div>


    <div class="w3-container">
        <div id="results"></div>
    </div>

    <div class="w3-container">
        <h1 class="hover-for-cursor">Hover Over This</h1>
    </div>

</body>
</html>

<script>
function checkHealth() {
    console.log("health check...");
} // function checkHealth() {

function http_call_get() {
    /*
    var my_url = window.location.origin + "/api/v1/test";
    */

    var my_url = "https://api.agify.io/?name=bella";

    try {
        $.ajax({
            type: "GET",
            contentType: "application/json",
            dataType: "json",
            timeout: ajax_time_out,
            url: my_url,
            data: JSON.stringify({ "data": "hello_world" }),
            success: function(data) {
                console.log("success : url : (" + my_url + ")");
                // console.log(data);
                json_data = {};
                // json_data = JSON.parse(data);
                json_data = data;

                if(!json_data.hasOwnProperty("name")) {
                    console.log("JSON does not have key 'name' !");
                    return false;
                }

                console.log(json_data);

                $("#results").html(JSON.stringify(json_data));
            },
            error: function(data) {
                console.log("error : url : (" + my_url + ")");
                console.log(data);
            },
        }); // $.ajax({

    } catch(err) {
        console.log("error : (AJAX call threw exception) : url : (" + my_url + ")");
    }
} // function http_call_get() {
</script>

<script>
window.addEventListener('DOMContentLoaded', (event) => {
    let myVar = setInterval(checkHealth, 3000);
});
</script>

<script>
function hide_my_div(div_id) {
    document.getElementById(div_id).style.display = 'none';
}

function show_my_div(div_id) {
    document.getElementById(div_id).style.display = 'block';
}

function ValidateIPaddress(ipaddress) {
    var result = {};
    if (/^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/.test(ipaddress)) {
        result["valid"] = true;
        result["ipaddress"] = ipaddress;
        return result;
    } else {
        result["valid"] = false;
        result["ipaddress"] = ipaddress;
        return result;
    }
}


$("#id_btn_pull_data").click(function(){
    http_call_get();
});


$('.hover-for-cursor').hover(function () {
    $(this).css('cursor', 'pointer'); /* code for mouseover */
}, function () {
    /* code for mouseout */
});

$("#id_btn_redirect").click(function(){
    /*
    window.location.replace(window.location.origin + "/go-here");
    */
    window.location.replace("https://api.agify.io/?name=bella")
});

</script>
```