# Downgrading Mac App Store (MAS) Applications

An introduction to downgrade a specific application from MAS

## Requirements

- Already purchased the target application
- [`disable-ssl-pinning.js`](https://raw.githubusercontent.com/trungnghiatn/Downgrade-MAS-Applications/master/Scripts/disable-ssl-pinning.js)
- SIP disable
- Frida tools
```
pip install frida-tools
```
or 
```
pip3 install frida-tools
```
- [Proxyman](https://proxyman.io/) or [Charles Proxy](https://www.charlesproxy.com/). Installed and trusted its Root Certificate

## Downgrading

1. Launch Proxyman, then App Store.

- Search your target app on the App Store. You must already purchased that app to see the Download button instead of the Get or Purchase button.

2. Disable SSL Pinning on `appstoreagent`

- Run this command using Terminal:

```
frida appstoreagent -l path_to_disable_ssl_pinning_script
```

- For example:

```
frida appstoreagent -l ./Downloads/disable_ssl_pinning.js
```

3. Enable SSL Proxying on appstoreagent

- Expand the traffic records on Apps section of Proxyman. Enable SSL Proxying on `appstoreagent` process under right click menu.

![](https://raw.githubusercontent.com/trungnghiatn/Downgrade-MAS-Applications/master/Images/enable-ssl-proxying.png)

4. Capture a list of App Version IDs

- Click on the Download button from App Store to start download your app. After downloaded, MAS failed to install the app because of SSL verification.

- Look at response of a request like this: 

```
https://p41-buy.itunes.apple.com/WebObjects/MZBuy.woa/wa/buyProduct?guid=*
```
 from `appstoreagent` process on Proxyman, try to find the key `softwareVersionExternalIdentifiers`. There is a list of app version IDs under this key. The newest version is at the bottom. Choose the version that you want to downgrade.

![](https://raw.githubusercontent.com/trungnghiatn/Downgrade-MAS-Applications/master/Images/app-version-ids.png)

- Paste your desired app version id into that text below:

```
  <key>appExtVrsId</key>
  <string>app_version_here</string>
```

5. Create a traffic breakpoint

- Create a breakpoint for a request from App Store. From Proxyman menu, access `Tool` > `Breakpoint` > `Rules`. Make sure to check `Enable Breakpoint Tool`, then click on `+` button to make a new breakpoint with the following rule:

```
https://p*-buy.itunes.apple.com/WebObjects/MZBuy.woa/wa/buyProduct?guid*
```

![](https://raw.githubusercontent.com/trungnghiatn/Downgrade-MAS-Applications/master/Images/create-a-breakpoint.png)

6. Re-download the app again

- Re-download the app again, Proxyman will show a window of breakpoint. Insert the code block on Step 4 into request body

![](https://raw.githubusercontent.com/trungnghiatn/Downgrade-MAS-Applications/main/Images/modify-request-body.png)

7. Stop disable-ssl-pinning script

- Close terminal windows that disable SSL Pinning on `appstoreagent` from Step 2.

8. Execute the breakpoint

- Click on Execute button of Breakpoint windows from Step 6. Your target app will be downloaded and installed with the lower version that you choose.

## Acknowledgement

- [Azenla](https://gist.github.com/azenla/37f941de24c5dfe46f3b8e93d94ce909)

---

<a href="https://paypal.me/ltn119412" target="_blank"><img src="https://raw.githubusercontent.com/trungnghiatn/Downgrade-MAS-Applications/main/Images/buy-me-a-coffee.png" alt="Buy Me A Coffee" style="height: 60px !important;width: 217px !important;" ></a>
