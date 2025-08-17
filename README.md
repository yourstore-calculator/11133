# 11133
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8" />
  <title>حاسبة الأسعار المطورة</title>
  
  <style>
    body {
      font-family: 'Segoe UI', Arial, sans-serif;
      background-color: #1e1e1e;
      color: white;
      direction: rtl;
      padding: 30px;
    }
    .logo { text-align: center; margin-bottom: 20px; }
    .logo img { max-height: 100px; }
    select, input {
      padding: 8px; margin: 5px 0; width: 100%; border: none;
      border-radius: 4px; box-sizing: border-box; background-color: #333; color: white;
    }
    input[type="file"] { padding: 3px; }
    input:disabled { background-color: #555; cursor: not-allowed; color: #aaa; }
    .section {
      background-color: #252526; padding: 15px; border-radius: 8px;
      margin-bottom: 20px; border: 1px solid #333;
    }
    .addon-section { background-color: #2a2d2e; padding: 10px; border-radius: 6px; margin-top: 10px; }
    label { font-weight: bold; color: #00e676; }
    button {
      background-color: #007acc; color: white; padding: 12px 22px; border: none;
      margin-top: 10px; border-radius: 5px; cursor: pointer; font-size: 16px;
      transition: background-color 0.3s;
    }
    button:hover { background-color: #005a9e; }
    .btn-secondary { background-color: #555; }
    .btn-secondary:hover { background-color: #777; }
    .btn-danger { background-color: #c0392b; }
    .btn-danger:hover { background-color: #a52f22; }
    .results { background: #2b2b2b; padding: 20px; margin-top: 20px; border-radius: 8px; }
    .result-item {
      border-bottom: 1px solid #555; padding: 15px 0; line-height: 1.9;
    }
    .result-item:last-child { border-bottom: none; }
    .result-item h3 { color: #00e676; margin-top: 0; font-size: 1.4em; }
    .result-item ul { list-style-type: disc; padding-right: 20px; }
    .summary {
      font-weight: bold; color: #00e676; padding-top: 15px; border-top: 2px solid #00e676;
      margin-top: 15px; font-size: 1.2em; line-height: 2;
    }
    .final-total {
      background-color: #00e676; color: #1e1e1e; padding: 10px; border-radius: 5px;
      font-size: 1.5em; text-align: center; margin-top: 10px;
    }
  </style>
</head>
<body>

<div class="logo">
  <img src="https://i.imgur.com/ih5zjVj.png" alt="شعار الشركة">
</div>

<div class="section">
    <label for="customerName">اسم الزبون:</label>
    <input type="text" id="customerName" placeholder="مثال: خلف الفطيسي" />
    <label for="customerPhone">رقم هاتف الزبون:</label>
    <input type="text" id="customerPhone" placeholder="مثال: 99455082" />
</div>

<div class="section">
  <label for="mainCategory">الفئة الرئيسية:</label>
  <select id="mainCategory" onchange="loadSubTypes()"></select>
</div>

<div class="section">
  <label for="subType">النوع الفرعي:</label>
  <select id="subType" onchange="updateUIBasedOnType()"></select>
</div>

<div class="section">
  <label for="customItemName">الاسم المخصص للعنصر (اختياري):</label>
  <input type="text" id="customItemName" placeholder="اتركه فارغًا لاستخدام الاسم الافتراضي" />
  <label for="height">الطول (متر):</label>
  <input type="number" id="height" step="0.01" value="1" />
  <label for="width">العرض (متر):</label>
  <input type="number" id="width" step="0.01" value="1" />
  <label for="quantity">الكمية:</label>
  <input type="number" id="quantity" value="1" />
  <label for="styleImage">صورة الستايل:</label>
  <input type="file" id="styleImage" accept="image/*" />
  <div id="openingDirectionContainer" style="display: none; margin-top: 10px;">
    <label for="openingDirection">اتجاه الفتح (RH/LH):</label>
    <select id="openingDirection">
        <option value="left inward">Left Inward (يسار للداخل)</option>
        <option value="right inward">Right Inward (يمين للداخل)</option>
    </select>
  </div>
</div>

<div class="section" id="addonsHost"></div>

<div class="section">
    <label for="installationCost">تكلفة التركيب (اختياري):</label>
    <input type="number" id="installationCost" value="0" step="1" />
</div>

<button onclick="calculate()">➕ أضف للنتائج</button>
<button onclick="clearResults()" class="btn-danger">🗑️ مسح النتائج</button>
<button onclick="saveAsWord()" class="btn-secondary">💾 حفظ كـ Word</button>

<div class="results" id="results"></div>

<script>
// --- **بداية دمج المكتبات المساعدة مباشرة في الكود** ---

// 1. مكتبة FileSaver.js
var saveAs=function(e){"use strict";if(typeof e==="undefined"||typeof navigator!=="undefined"&&/MSIE [1-9]\./.test(navigator.userAgent)){return}var t=e.document,n=function(){return e.URL||e.webkitURL||e},r=t.createElementNS("http://www.w3.org/1999/xhtml","a"),o="download"in r,a=function(e){var t=new MouseEvent("click");e.dispatchEvent(t)},i=/constructor/i.test(e.HTMLElement)||e.safari,f=/CriOS\/[\d]+/.test(navigator.userAgent),u=function(t){(e.setImmediate||e.setTimeout)(function(){throw t},0)},s="application/octet-stream",c=1e3*40,d=function(e){var t=function(){if(typeof e==="string"){n().revokeObjectURL(e)}else{e.remove()}};setTimeout(t,c)},l=function(e,t,n){t=[].concat(t);var r=t.length;while(r--){var o=e["on"+t[r]];if(typeof o==="function"){try{o.call(e,n||e)}catch(a){u(a)}}}},p=function(e){if(/^\s*(?:text\/\S*|application\/xml|\S*\/\S*\+xml)\s*;.*charset\s*=\s*utf-8/i.test(e.type)){return new Blob([String.fromCharCode(65279),e],{type:e.type})}return e},v=function(t,u,c){if(!c){t=p(t)}var v=this,g=t.type,h=g===s,m,y=function(){l(v,"writestart progress write writeend".split(" "))},S=function(){if((f||h&&i)&&e.FileReader){var r=new FileReader;r.onloadend=function(){var t=f?r.result:r.result.replace(/^data:[^;]*;/,"data:attachment/file;");var n=e.open(t,"_blank");if(!n)e.location.href=t;t=undefined;v.readyState=v.DONE;y()};r.readAsDataURL(t);v.readyState=v.INIT;return}if(!m){m=n().createObjectURL(t)}if(h){location.href=m}else{var o=e.open(m,"_blank");if(!o){e.location.href=m}}v.readyState=v.DONE;y();d(m)};v.readyState=v.INIT;if(o){m=n().createObjectURL(t);setTimeout(function(){r.href=m;r.download=u;a(r);y();d(m);v.readyState=v.DONE});return}S()},g=v.prototype,h=function(e,t,n){return new v(e,t||e.name||"download",n)};if(typeof module!=="undefined"&&module.exports){module.exports.saveAs=h}else if(typeof define!=="undefined"&&define!==null&&define.amd!==null){define([],function(){return h})}else{e.saveAs=h}}(typeof self!=="undefined"&&self||typeof window!=="undefined"&&window||this.content);

// 2. مكتبة html-docx.js (نسخة مدمجة)
!function(e,t){"object"==typeof exports&&"object"==typeof module?module.exports=t():"function"==typeof define&&define.amd?define([],t):"object"==typeof exports?exports.htmlDocx=t():e.htmlDocx=t()}(window,function(){return function(e){var t={};function n(r){if(t[r])return t[r].exports;var o=t[r]={i:r,l:!1,exports:{}};return e[r].call(o.exports,o,o.exports,n),o.l=!0,o.exports}return n.m=e,n.c=t,n.d=function(e,t,r){n.o(e,t)||Object.defineProperty(e,t,{enumerable:!0,get:r})},n.r=function(e){"undefined"!=typeof Symbol&&Symbol.toStringTag&&Object.defineProperty(e,Symbol.toStringTag,{value:"Module"}),Object.defineProperty(e,"__esModule",{value:!0})},n.t=function(e,t){if(1&t&&(e=n(e)),8&t)return e;if(4&t&&"object"==typeof e&&e&&e.__esModule)return e;var r=Object.create(null);if(n.r(r),Object.defineProperty(r,"default",{enumerable:!0,value:e}),2&t&&"string"!=typeof e)for(var o in e)n.d(r,o,function(t){return e[t]}.bind(null,o));return r},n.n=function(e){var t=e&&e.__esModule?function(){return e.default}:function(){return e};return n.d(t,"a",t),t},n.o=function(e,t){return Object.prototype.hasOwnProperty.call(e,t)},n.p="",n(n.s=0)}([function(e,t,n){e.exports=n(1)},function(e,t,n){"use strict";n.r(t);var r=n(2),o=n.n(r);t.asBlob=function(e,t){void 0===t&&(t={});var n=o()(e,t);return new Blob([n],{type:"application/vnd.openxmlformats-officedocument.wordprocessingml.document"})}},function(e,t){e.exports=function(e,t){var n=t.orientation||"portrait",r={top:720,right:720,bottom:720,left:720,header:720,footer:720,gutter:0};return function(e,t){for(var n=Object.keys(t),r=0;r<n.length;r++){var o=n[r];void 0===e[o]&&(e[o]=t[o])}return e}(t.margins||{},r),'<?xml version="1.0" encoding="UTF-8" standalone="yes"?><?mso-application progid="Word.Document"?><pkg:package xmlns:pkg="http://schemas.microsoft.com/office/2006/xmlPackage"><pkg:part pkg:name="/_rels/.rels" pkg:contentType="application/vnd.openxmlformats-package.relationships+xml"><pkg:xmlData><Relationships xmlns="http://schemas.openxmlformats.org/package/2006/relationships"><Relationship Id="rId1" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/officeDocument" Target="word/document.xml"/></Relationships></pkg:xmlData></pkg:part><pkg:part pkg:name="/word/document.xml" pkg:contentType="application/vnd.openxmlformats-officedocument.wordprocessingml.document.main+xml"><pkg:xmlData><w:document xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main" xmlns:m="http://schemas.openxmlformats.org/officeDocument/2006/math" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing" xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main" xmlns:pic="http://schemas.openxmlformats.org/drawingml/2006/picture" ><w:body><w:altChunk r:id="htmlChunk" /></w:body></w:document></pkg:xmlData></pkg:part><pkg:part pkg:name="/word/_rels/document.xml.rels" pkg:contentType="application/vnd.openxmlformats-package.relationships+xml"><pkg:xmlData><Relationships xmlns="http://schemas.openxmlformats.org/package/2006/relationships"><Relationship Id="htmlChunk" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/aFChunk" Target="htmlChunk.dat"/></Relationships></pkg:xmlData></pkg:part><pkg:part pkg:name="/word/htmlChunk.dat" pkg:contentType="text/html;charset=utf-8"><pkg:xmlData><![CDATA['+function(e,t,n){return'<!DOCTYPE html><html><head><meta http-equiv="Content-Type" content="text/html; charset=utf-8" /><style>html * {font-family: "Times New Roman"; font-size: 12pt;} table, th, td {border: 1px solid black; border-collapse: collapse;}</style></head><body>'+e+"</body></html>"}(e)+"
