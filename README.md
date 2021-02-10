<!DOCTYPE html>
<meta charset="utf-8" />
<meta http-equiv="X-UA-Compatible" content="IE=Edge" />
<title>Arnab Barua</title>
<link rel="shortcut icon" href="https://cdn0.iconfinder.com/data/icons/classic-icons/512/087.png" />
<link
  rel="stylesheet"
  href="https://play.pokemonshowdown.com/style/client.css"
/>
<link
  rel="stylesheet"
  href="https://play.pokemonshowdown.com/style/sim-types.css"
/>
<link
  rel="stylesheet"
  href="https://play.pokemonshowdown.com/style/battle.css"
/>
<link
  rel="stylesheet"
  href="https://play.pokemonshowdown.com/style/utilichart.css"
/>
<link
  rel="stylesheet"
  href="https://play.pokemonshowdown.com/style/font-awesome.css"
/>
<style>
  #header h1 {
    color: black;
    padding-left: 0.5em;
    text-shadow: 2px 2px 1px #888;
  }
  #html-input,
  #html-output {
    display: block;
  }
  #html-input {
    left: 0;
    right: auto;
    width: 500px;
  }
  #html-output {
    width: auto;
    right: 0;
    left: 501px;
  }
  #html-editor {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
  }
</style>
<div id="header" class="header">
  
  <b
    ><h1><font color="#FF3361 ">Arnab Barua's Coding Website.</font></h1></b
  >
  <div class="maintabbarbottom"></div>
  <div class="userbar">
    <button class="button" id="dark">Toggle Dark Mode</button>
  </div>
</div>
<div class="ps-room ps-room-light scrollable" id="html-input">
  <div id="html-editor"><b>Add html/css and preview it at right.</b></div>
</div>
<div class="ps-room ps-room-light scrollable" id="html-output">
  <div class="chat-log hasuserlist">
    <div class="inner">
      <div class="notice">
        <div class="infobox infobox-limited" id="html-caja-output"></div>
      </div>
      <div class="notice">Source:</div>
      <div class="notice">
        <div class="infobox">
          <code>Your Code: <span id="html-intro-code"></span></code>
        </div>
      </div>
      <br />
      <br />
      <br />
      <br />
      <center>
        <marquee
          style="background: yellow ; padding: 5px 5px ; border: 1px solid black ; border-radius: 30% ; width: 75% ; text-shadow: 0px -1px black ; box-shadow: 1px 1px 5px rgba(178 , 178 , 216) inset"
          ><b
            ><font color="#FF3361 "
              >Thank you for joining my app. Your code will be saved!</font
            ></b
          ></marquee
        >
      </center>
      <div class="notice"></div>
    </div>
  </div>
  <ul class="userlist">
    <li
      class="userlist-count"
      id="upperstaff-userlist-users"
      style="text-align:center;padding:2px 0"
    >
      <small><span id="upperstaff-usercount-users">1</span> user</small>
    </li>
    <li>
      <button class="userbutton username">
        <em class="group staffgroup">~</em
        ><strong
          ><em
            ><b><font color="#FF3361 ">Arnab Barua</font></b></em
          ></strong
        >
      </button>
    </li>
  </ul>
  <div class="chat-log-add hasuserlist"></div>
</div>
<script src="https://cdnjs.cloudflare.com/ajax/libs/ace/1.3.3/ace.js"></script>
<script>
  window.global = window;
</script>
<script src="https://play.pokemonshowdown.com/js/lib/jquery-2.1.4.min.js"></script>
<script src="https://play.pokemonshowdown.com/js/lib/html-css-sanitizer-minified.js"></script>
<script src="https://play.pokemonshowdown.com/js/battledata.js"></script>
<script>
  function parseCSS(code) {
    var parsed = document.createElement("template");
    parsed.innerHTML = code;

    var implDocument = document.implementation.createHTMLDocument(
      "Temporary CSS storage"
    );

    var stylesheets = parsed.getElementsByTagName("style");
    for (var i = 0; i < stylesheets.length; i++) {
      var element = stylesheets[i];
      // CSS element needs to be in document to be actually parsed
      implDocument.body.appendChild(element);
      var rules = element.sheet.cssRules;
      for (var j = 0; j < rules.length; j++) {
        var rule = rules[j];
        var elements = parsed.querySelectorAll(rule.selectorText);
        for (var k = 0; k < elements.length; k++) {
          var element = elements[k];
          var newValue =
            (element.getAttribute("style") || "") + rule.style.cssText;
          element.setAttribute("style", newValue);
        }
      }
    }

    return parsed.innerHTML;
  }
  var removableListeners = [];
  function modifyRoomIntro(action, editor) {
    for (var i = 0; i < removableListeners.length; i++) {
      removableListeners[i]();
    }
    removableListeners = [];
    var rawValue = editor.getSession().getValue();
    localStorage.setItem("code", rawValue);

    var value = parseCSS(rawValue);
    var output = document.getElementById("html-caja-output");
    output.innerHTML = BattleLog.sanitizeHTML(value);
    var images = output.getElementsByTagName("img");
    for (var i = 0; i < images.length; i++) {
      var image = images[i];
      var listener = function listener() {
        this.setAttribute("width", this.width);
        this.setAttribute("height", this.height);
        updateCode(output);
      };
      removableListeners.push(
        (function removeListenerIife(image, listener) {
          return function removeListener() {
            image.removeEventListener("load", listener);
          };
        })(image, listener)
      );
      image.addEventListener("load", listener);
    }
    updateCode(output);
  }
  function updateCode(output) {
    document.getElementById("html-intro-code").textContent = output.innerHTML;
  }
  var editor = ace.edit("html-editor");
  var session = editor.getSession();
  var Range = ace.require("ace/range").Range;
  session.setOption("useWorker", false);
  session.setMode("ace/mode/html");
  session.setUseWrapMode(true);
  editor.on("change", modifyRoomIntro);
  editor.selection.setRange(new Range(0, 0, true));
  var code = localStorage.getItem("code");
  if (code) {
    editor.getSession().setValue(code);
  }
  editor.focus();
  modifyRoomIntro(null, editor);
  $("#dark").on("click", function() {
    var $html = $("html");
    if ($html.hasClass("dark")) {
      editor.setTheme("ace/theme/textmate");
      $html.removeClass("dark");
    } else {
      editor.setTheme("ace/theme/twilight");
      $html.addClass("dark");
    }
  });
</script>
