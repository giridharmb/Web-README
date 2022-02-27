[Async Await](#async-await)

<hr/>

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