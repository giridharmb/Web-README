[Web Worker](#web-worker)

[Async Await And Promise](#async-await-and-promise)

[Async Await](#async-await)

<hr/>

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