# Personal version of `https://github.com/widget-/slack-black-theme`


# Preview 

![alt text](https://raw.githubusercontent.com/C0axx/slack/master/Preview.PNG)
# Installing into Slack

Find your Slack's application directory.

    Windows: %homepath%\AppData\Local\slack\
    Mac: /Applications/Slack.app/Contents/
    Linux: /usr/lib/slack/ (Debian-based)

Open up the most recent version (e.g. app-2.5.1) then open resources\app.asar.unpacked\src\static\index.js

For versions after and including 3.0.0 the same code must be added to the following file resources\app.asar.unpacked\src\static\ssb-interop.js

At the very bottom, add
```
// First make sure the wrapper app is loaded
document.addEventListener("DOMContentLoaded", function() {
   // Then get its webviews
   let webviews = document.querySelectorAll(".TeamView webview");
   // Fetch our CSS in parallel ahead of time
   const cssPath = 'https://raw.githubusercontent.com/C0axx/slack/master/black.css';
   let cssPromise = fetch(cssPath).then(response => response.text());
   let customCustomCSS = `
   :root {
      /* Modify these to change your theme colors: */
      --primary: #09F;
      --text: #CCC;
      --background: #080808;
      --background-elevated: #222;
   }
   `
   // Insert a style tag into the wrapper view
   cssPromise.then(css => {
      let s = document.createElement('style');
      s.type = 'text/css';
      s.innerHTML = css + customCustomCSS;
      document.head.appendChild(s);
   });
   // Wait for each webview to load
   webviews.forEach(webview => {
      webview.addEventListener('ipc-message', message => {
         if (message.channel == 'didFinishLoading')
            // Finally add the CSS into the webview
            cssPromise.then(css => {
               let script = `
                     let s = document.createElement('style');
                     s.type = 'text/css';
                     s.id = 'slack-custom-css';
                     s.innerHTML = \`${css + customCustomCSS}\`;
                     document.head.appendChild(s);
                     `
               webview.executeJavaScript(script);
            })
      });
   });
});
```
