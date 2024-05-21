# PWA Installation Customized
We belive the best UX for PWA installation is to have a button in the menu, which is only available, if it is not installed yet.

The current way to do it is using the "beforeinstallationprompt" event - prevent the default behaviour and store it for later, when the user encounters the option in the menu and decides: "Yhea I could use this app with an shortcut on my home screen."

Now for Chromium based browsers this works nicely, unfortunately yet nobody else implemented the required events.
For now it seems the only solution is to provide an installation guide for the cases where we don't have these events.

For the implementation this looks like:

- Check if we have the events
- If we have the events and we don't have the app installed already then show the installation button + installation button directly installs the PWA.
- If we have the app installed already - don't show the button at all.(onyl detectable in chromium based browsers)
- If we are on firefox on iOS (detectable via) `iphone`, `ipad` or `ipod` be


- Desktop firefox needs an extension - display explaination
- Android Firefox has instructions which may be followed
- Chromium based browsers have same flow for android and desktop versions
- Safari is Similar but slightly different, in both cases there are instructions to be followed
- Opera does not support PWAs installation at all


So this means we have 4 different Instructions and 1 Standard working way and all other situations we don't even bother.

1. Standard working way: "beforeinstallationprompt" exists and we may detect if the App has been installed
2. Instruction Android Firefox on button click - don't show the button in standalone mode
3. Instruction Desktop Firefox on button click - don't show the button in standalone mode
4. Instruction iOS on Button Click - don't show the button in standalone mode
5. Instruction MacOS on Button Click - don't show the button in standalone mode
6. All other cases ignore and never show the button


This means we need to discriminate Android Firefox, Desktop Firefox, iOS and MacOS.

The `navigator.userAgent` and `navigator.platform` should be the right parts to ask - however it is not as straighforward as we might hope.
To make life easier for us we better use `userAgent = navigator.userAgent.toLowerCase()`.

### Firefox Android
- userAgent includes "firefox" and "android"
### Firefox Desktop(and Ipad)
- userAgent include "firefox" but not "android" (Probably it does not matter if Linux, MacOS or Windows)
### iOS
- Either "ipad", "iphone" or "ipod" in userAgent, or "macintosh" in userAgent with maxTouchPoints > 1
- On iOS other Apps cannot have "add to Homescreen functionality" ... maybe later
### MacOS
- "macintosh" in userAgent and maxTouchPoints  = 0
