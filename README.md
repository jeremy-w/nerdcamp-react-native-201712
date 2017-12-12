# NerdCamp: React Native
Jeremy W. Sherman, December 2017



## Main Question
- Given we have a lot of native skill already,
- Can we use React Native to reduce time to market and dev costs for
    - Single-platform projects:
        - iOS? **This is my main focus this week.**
        - Android?
    - Dual-platform projects
        - iOS & Android
            - I'd burn too much time getting up to speed on Android to look at this, most likely.
        - iOS & Frontend Web
            - I might be able to look at this if time allows!
- While being able to readily leap in and out of React Native and Native Native to best leverage the two approaches' strengths?



## My Background
- Professional macOS & iOS dev since 2008.
- On-and-off professional backend and frontend Web experience since 2013.
    - Clojure
    - Rails
    - jQuery
    - Ember
    - Angular 1.x
    - Elm
- No professional Android programming experience, though fairly comfortable with Kotlin.
    - I've read part way through the BNR guide to Android programming, but not far enough to be useful.

Based on this background, I'm going to be focusing on
**weaving together React Native and iOS native** this week.

If time allows, I'd also look at how using the same language in both Web app
(frontend and maybe backend using say Express) and iOS app might help us hit
both those platforms faster.



## 2017-12-11 (Monday)
**How does React Native work from top to bottom, both technologically and sociologically?**

Today:

- Looked at Community page and CONTRIBUTING docco for React Native.
    - Also looked at the Podspec. Lots o' deps, many of them Facebook-developed, unsurprisingly.

- Sketched out a very rough timeline.
    - Plan to look at CHANGELOG to flesh this out in more detail tomorrow.

- Investigated some of conferences in the React Native community.
    - This gave a sense of the companies publicly involved in the project.
    - It also looks like my concerns are very of-the-moment, and I can probably mine some of the talks given at recent conferences to help answer this NerdCamp's questions.
    - This is also a good way to get a sense for "what are common pain points that people are talkin about".
        - Like native component dev! Several starter-pack tools there, like CRNA but for native libs.
    - And what the community is interested in, like GraphQL.

- Got a sense for some of the major community initiatives.
    - Snack & Expo
    - CRNA
    - Navigation

- Didn't get down to who's who, though. Stuck at company-level. Good enough for my needs for now.

Next:

- Speed-read the React Native first-party docs. (timebox to 1.5 h)
- Review the RN Changelog to get a feel for how it's developed. (timebox to 2 x 20 min)
- Speed-read the RN codebase, to get a feel. (timebox to 1 h)
- Build and run an app on iOS, and root around at runtime.
- Dig into WalmartLabs' Electrode Native, which looks like a ready-made tool for jumping into (though not sure about back out of) RN in the context of a mostly-native app.


### React Native: The Who
- Infrastructure:
    - Official:
        - [Site, on GitHub](https://facebook.github.io/react-native/)
        - [Blog, also a GH static site](http://facebook.github.io/react-native/blog/)
        - [CLA](https://code.facebook.com/cla)
        - BSD licensed
    - Repo:
        - [GitHub!facebook/react-native](https://github.com/facebook/react-native)
        - Some internal repo, apparently. You'll see commits synced over using
          [FBShipIt](https://github.com/facebook/fbshipit) from their Hg
          monorepo, [for example](https://github.com/facebook/react-native/commit/ed2bfcb35a2756eb700882ab8e21b6b273efa80a):

          > Skalarkify flags macros.
          > Reviewed By: adamjernst
          >
          > Differential Revision: D6528242
          >
          > fbshipit-source-id: f809cfb853d6615a1aa47e145063ef1d7f5838be
    - CI:
        - [CircleCI](https://github.com/facebook/react-native/blob/master/.circleci/config.yml)
          - That is some of the fanciest use of YAML features I've seen:
              - named anchors `&android-defaults`
              - references `*android-defaults`
              - [merge keys `<<: *android-defaults`](https://camel.readthedocs.io/en/latest/yamlref.html#yamlref-merge-keys)
              - Literal style multiline strings, intro'd with `|`
          - Builds: http://circleci.com/gh/facebook/react-native
        - I'm seeing TravisCI on their PRs, though. Hmm. No mention in CONTRIBUTING.
        - And also [**Docker** tests](https://github.com/facebook/react-native/blob/master/DockerTests.md) that can be run on [ContainerShip](https://www.containership.io/). This looks to be the heart of the Android build and test environment? It can also be used for JS tests. But not iOS, of course.
    - Bug tracker: Associated [GitHub Issues](https://github.com/facebook/react-native/issues) page.
        - Security bug bounty program: https://www.facebook.com/whitehat/
    - Feature requests: [Canny](https://react-native.canny.io/feature-requests)
    - Async communications:
        - Stack Overflow: react-native tag
        - Facebook Group: https://www.facebook.com/groups/react.native.community
        - React Native Core Contributors FB group: https://www.facebook.com/groups/reactnativeoss/
    - Sync communications:
        - [Discord Reactiflux chat](https://discord.gg/0ZcbPKXt5bZjGY5n)
        - Monthly meeting of core contributors:

          > Core contributors to React Native meet monthly and post their
          > meeting notes on the React Native blog. You can also find ad hoc
          > discussions in the React Native Core Contributors Facebook group. (from Contributing)
    - Governance:
        - Sounds like core contributors drives actual API changes. PRs are
          welcome for bug fixes and implementing missing features. Lots of work
          happens in community repos around React Native.

- Public users:
    - Per https://facebook.github.io/react-native/:
        - Facebook
        - Instagram (AKA Facebook)
        - F8 (AKA Facebook), though they have a nice write-up and sample app at: http://makeitopen.com/
        - AirBNB
        - Skype (AKA Microsoft)
        - Tesla Motors
        - Walmart:
            - https://medium.com/walmartlabs/react-native-at-walmartlabs-cdd140589560
                - They talk about actually using this to obsolete an old native app, and the pain points they ran into, and the wins they saw.
                - Recommend going Android-first, since it's the more limited platform.
                - Perf testing is annoying.
                - Debugging is complicated by switching JS engines (JSC normally, but Chrome's V8 for debug)
                - Upgrading the RN version is frustrating
                - OTA updates can be tricky, as you need to match RN version to OTA bundle version carefully, or you break the app (old host, new bundle / new host, old bundle), so not a clear win.
          - https://techcrunch.com/2017/09/29/walmart-labs-open-sources-its-tool-for-bringing-react-native-to-existing-mobile-apps/?utm_source=mobiledevweekly&utm_medium=email
                - [Electrode Native](http://www.electrode.io/site/native.html) "allows large companies like Walmart to take their existing apps and slowly migrate parts of their code to React Native". "React Native simply becomes a third-party library that’s used in the native app and the React Native code then lives in what Walmart Labs calls an “Electrode Native MiniApp.”"
    - UberEATS: https://eng.uber.com/ubereats-react-native/

- Corporate sponsors:
  - Microsoft, F8 2016, for Windows (https://medium.com/react-native-development/a-brief-history-of-react-native-aae11f4ca39) [repo](https://github.com/Microsoft/react-native-windows)
  - Samsung, F8 2016, for Tizen

- Conferences and their sponsors:
    - https://reactjs.org/community/conferences.html
    - React.js Conf, Facebook
    - [Chain React](https://infinite.red/ChainReactConf), InfiniteRed
        - [2017](https://infinite.red/ChainReactConf/2017) includes Amazon, AirBNB, PayPal, Netflix, Eventbrite, Microsoft, SalesForce, Capital One, Formidable, Gudog, Instrument, Qlik, callstack, MLS.
            - I've gone back and added in some companies I didn't recognize. Many of the speakers just happen to be associated with companies sponsoring the conference.
        - Sponsors:
            - Run by: Infinite Red
            - Platinum: Squarespace
            - Gold: NativeBase, Formidable, Modus Create, bugsnag
            - Silver: Amazon Web Services, react native training, Gudog, rangle.io, business Oregon, HealthSparq
            - Bronze: echo/bind, salesforce, capitalone, instrument, PayPal, Qlik Playground, callstack, MLS.
    - [React Native EU](https://react-native.eu/), 2017
      - Speakers from: Facebook, Artsy, Formidable, Callstack, Infinite Red, Wix, Netflix, BAM
        - BAM talk: [Bridges to React Native](http://slides.com/florianrival/bridges-to-react-native#/) about native modules
      - Run by: Callstack
      - Sponsors:
          - Coffee: NativeBase by GeekyAnts
          - Other: Formidable, challengerocket, Reindex, Infinite Red

- Community projects:
    - [Expo](https://expo.io/) is a toolchain atop React Native that makes build and deploy easier by pushing the native bits into an ExpoSDK and letting you just write JS. Their jsFiddle equivalent, [Snack](https://snack.expo.io/), is mentioned by the official CONTRIBUTING doc as the way to provide a [SSCE](http://sscce.org/).
      - Differences vs straight RN: https://docs.expo.io/versions/latest/introduction/already-used-react-native.html
      - Also adds Push Notifications and OTA updates.
      - Alternative OTA (and more) tooling: https://appcenter.ms/, which rolls in [CodePush](https://microsoft.github.io/code-push/) support.
    - Create React Native App, aka CRNA, has official backing per blog. https://github.com/react-community/create-react-native-app It appears to be built around Expo, though you can `eject` and then write your own native components as needed.
    - Component finder, linked by Microsoft: https://js.coach/?collection=React+Native
    - [Use React Native](http://www.reactnative.com/) with release history and links to lots of stuff.
    - Navigation libraries are apparently a pain point
        - [React Navigation](https://reactnavigation.org/), native nav and routing for iOS & Android
          - Officially recommended.
        - [AirBNB Native Navigation](http://airbnb.io/native-navigation/), beta, uses iOS & Android native nav components
        - [Wix React Native Navigation](https://github.com/wix/react-native-navigation) is recommended by AirBNB as more stable.
          - This has a good look at the way RN hooks into iOS and how it neglects view controllers, as well as how they tried to work around it: https://github.com/wix/react-native-controllers#why-do-we-need-this-package


### React Native: The When
Helpful: https://medium.com/react-native-development/a-brief-history-of-react-native-aae11f4ca39

- Summer 2013, internal Facebook Hackathon project

- Prelude: 2014-10-02, [ComponentKit](http://componentkit.org/), React-inspired native toolkit in Obj-C++ using flexbox for layout, introduced at @Scale 2014
  - Their docs are actually great, and I bet the Best Practices port over
    well to RN or Native for React. Some are even pretty good ideas in general.
  - They still point to AsyncDisplayKit and POP for heavily gesture-driven UIs and animation.

- Public preview, Jan 2015

- iOS announced:
  2015-03-26,
  [Bringing modern web techniques to mobile](https://code.facebook.com/posts/1014532261909640/react-native-bringing-modern-web-techniques-to-mobile/)

- Android announced:
  2015-09-14,
  [How we built the first cross-platform React Native app](https://code.facebook.com/posts/1189117404435352/react-native-for-android-how-we-built-the-first-cross-platform-react-native-app/)

- React Native passes iOS & Android on Google Trends, Sep 2016



## 2017-12-12 (Tues)
Today:

- Read the UberEATS React Web app => React Native app case study.
- Read most of the React Native first-party docs.

Next:

- Finish reading the Guides & Guides (iOS) sections. This has been valuable, and is worth continuing.
- Review the RN Changelog to get a feel for how it's developed. (timebox to 30 min)
- Build and run an app on iOS, and root around at runtime.
- Dig into WalmartLabs' Electrode Native, which looks like a ready-made tool for jumping into (though not sure about back out of) RN in the context of a mostly-native app.



### READ: Case Study: UberEats: From React/Flux to React Native
https://eng.uber.com/ubereats-react-native/

Points drawn from the article:

- Uber: two parties, driver & rider
- UberEats: three parties - adds restaurant
    - needs to send events to other parties

- Web App: React & Flux
- Problems:
    - Notifications: Used modal to force user interaction, which enabled sound notifications
    - Printing receipts: AirPrint-only from Web; native would let them print using printer SDKs directly
- Uber placed iPads in restaurants, so iOS only for now.

- Native App
    - Proof of Concept:
        - Can use Uber's native deps
            - Crash reports
            - User auth
            - Analytics

- Hosted Web app inside React Native shell
    - Hijacked NSURLProtocol to inject auth headers
    - Ran JS in window to alter Web app's Flux store
    - Enabled to gradually migrate features over to React Native

- Favored Web solutions over Native
    - react-router: Got it working, saw no reason to migrate to other navigation methods later.

- Gotcha: React Native to Native-Native communication was too easy!
    - Too easy to send work to Native-Native
    - Underused RN code, like for analytics and login
    - If expanding to Android, will migrate Obj-C => JS rather than writing a clone in Java.

- OTOH, used a small Native bridge to let the RN JS code decide when to update the code bundle.
    - SemVer: "a change is considered breaking if it changes the Native – JavaScript communication interface"
    - "it is important to have a resilient system which can detect and recover from unstable builds"
    - shipped bundle, safe bundle, untested bundle; if untested loads, graduates to safe
        - bootstrap always starts with the originally shipped bundle

- Tooling:
    - ES6 + [FlowJS](https://github.com/facebook/flow)
    - [Redux](https://github.com/reactjs/redux) for state management
        - Started with [Thunk middleware](https://github.com/gaearon/redux-thunk) for async actions, but coped awfully with periodic tasks.
        - Migrated entirely to the actor/task-based [Sagas middleware](https://redux-saga.github.io/redux-saga/) and its generator-centric approach, and happy with it and the simpler code that resulted.
Common themes I noted:

- OTA Update is a wheel that keeps being reinvented.
- The usual React Web app to React Native Better-Than-Web app migration pattern applies, with Web engineers driving app dev.


### READ: React Native docs
Timeboxed to 90 minutes.

https://facebook.github.io/react-native/

Current version is 0.51.

- **Web-First:** Assumes background of JS & React
- **The Real Thing:** Contrasts with Web app and Hybrid app approaches
    - Native UI building blocks, assembled using JS & React.
    - Focused on iOS & Android. Still talking about Obj-C & Java, no mention of Swift or Kotlin.
    - NOTE: Orchestrating _view_ components, but no discussion of other native non-view components.
- **Hot Reloading:** Because compile & deploy is really slow. They've got that right.
- **Native Escape Hatch:** Mentions Swift here, so probably touched more recently.
    - Swap RN component for NN component (optimization)
    - Build part of the app in RN, part in NN (example: Facebook itself)
    - NOTE: This suggests the docs will hopefully say more about this?

Testimonials - there's a link to a [Showcase page](https://facebook.github.io/react-native/showcase.html) I missed before that elaborates on app, platforms used, and links to testimonial articles.

And that links to a [list of open-source RN apps](https://github.com/ReactNativeNews/React-Native-Apps) maintained by ReactNativeNews.
    - Apparently, "Expo.io" was formerly "getexponent.com", and Expo was called Exponent.

"The Basics" tutorial:

- Nice interactive demos thanks to Expo / Snack.
- "Flexbox: Like CSS, but with these tweaks."
- "Styles: Like CSS, but kebab-case becomes snakeCase"
    - "And cascade and inheritance are manual."
- Props: Static inputs to parameterize a component's display. Provided by
  parent component.
- State: Allows a component's appearance to change after initial render.
- Touches: Use buttons, or gesture responders, or custom touchables.
- Networking: Fetch, or XHR, or libs built on XHR.
    - No CORS, but ATS applies on iOS.
    - CAUTION: Exceptions just get dropped on the floor unless you catch them.

"Components" and "APIs" surfing:

- Wow, a lot of these are suffixed as `IOS` or `Android`
- Some come with "native-code only" riders, so they don't work in CRAN apps unless you eject.
    - Including deep linking.
- Wow, a lot of "on Android, add this gunk", "on iOS, add this gunk, and mutate your project in these ways to weave in the library" (eg PushNotificationIOS)
- NetInfo: "Are you connected?" Oy. Not as recommended on iOS, it seems. And also not host-focused. Harrumph.
- Clipboard: Limited to a single string.
- AccessibilityInfo: You can tell if it's on/off. On iOS, you can get notified
  when an announcement ends, and push focus around, and announce things.
  Announcement is only for a string, not a string + focus tag.
- AppRegistry: "JS entry point to all RN apps". You can `runApplication(tag)`
  and later `unmountApplicationComponentAtRootTag(tag)` from the native code.
  So you can run multiple sub-apps!
    - Including headless tasks!
    - Docs are mostly not there. Stuff about sections, components, runnables, configs, app keys. Hmm.
- Vibration: No haptics support, it looks like.

"Contributing":

- Using an RC is highly encouraged.
- Development is fast, and the system is very actively used by Facebook.
- "By the time a release candidate is released, the changes it contains will
  have been shipped in production Facebook apps for over two weeks."

"Guides":

- Components & APIs: Wow, basic stuff like segmented control, tab bar, progress
  view, and date picker are iOS-specific. There are often Android-specific
  mirrors of those, but no platform-independent variation. You're stuck using
  the Platform APIs to swap in the correct code, I guess.
- Platform Specific Code:
    - Platform module exposes OS and version.
    - Has a `select` switch-statement function with a JS Object argument to map
      from OS to value. Can be used to swizzle imports by varying `require()`
      argument.
    - Also can use suffixing to swizzle imports: `require('./Something')` will
      load up `Something.ios.js` or `Something.android.js` as appropriate.
- Navigating Between Screens:
    - React Navigation:
        - Simple, with cross-platform support for common patterns, like tabs and stacks
        - JS, so flexible and plays nice with Redux
        - Community favorite
        - Supports nested routers
    - iOS-only: NavigatorIOS wraps UINavigationController.
    - Native on both: native-navigation (AirBNB) or react-native-navigation (Wix).
- Images (and other assets):
    - Local resources:
        - `require`d just like JS source. Handles @2x and @3x suffixes.
        - Only the used images are packaged with the app.
        - Dimensions are read in from the files.
    - Hybrid resources:
        - Image source becomes `{uri: 'image_name'}` for asset catalog or drawables.
        - Use `{uri: 'asset:/image_name.png'}` for Android assets.
        - Downsides:
            - Just trusts they're there.
            - Does not infer image dimensions.
    - Network resources:
        - Can be used for app-slimming or resources you don't have yet.
        - `{uri: 'https://example.com/img.png'}`
        - Otherwise acts like hybrid resources.
        - But the whole HTTP request used can be configured!
        - ???: What about "loading" view while request is running and "error" view should request fail?
            - "In practice, you already need to handle the case when the image
              is not downloaded yet, so displaying the placeholder for a few
              more frames while it is decoding does not require any code
              change."
     - data: URIs:
        - LOL don't do this unless it's really tiny.
     - Cache Control:
        - iOS only.
     - Local Images:
        - See CameraRoll for local resources that aren't assets.
     - Why require manual sizes?
        - To avoid jumpy reflow.
     - Source is an object:
        - Flexible
        - You're encouraged to "Feel free to use it as your data structure to store more information about your image."
        - XXX: No mention of reserved names. Uh, OK.
- Animations:
    - Animated: Time-based start/stop animations with transforms of an input and output. Composable with configurable easing.
        - Can target a dynamic value by targeting a `toValue` that is itself an `Animated`.
        - Can be driven by a gesture rather than time using `Animated.event`.
        - Driven on the native side mostly. You can hook in JS code to react to
          changes in values with a `stopAnimation` callback on completion, or
          a `listener` callback for infrequent (and non-blocking) updates with
          "recent-ish" values.
    - LayoutAnimation: Rigs up global create/update animations for the next render/layout cycle.
        - Used as a way to avoid coordination between components to animate them in sync.
        - Limited in its configurability.
    - Low-Level & Perf:
        - requestAnimationFrame: Polyfilled. Used to implement other APIs. You probably don't need it.
        - setNativeProps: See section "Direct Manipulation". Lets you poke a native component without going through `setState` and tripping a re-render on the whole hierarchy. You should probably implement `shouldComponentUpdate` instead.
            - InteractionManager can be used to defer work till after you've met your animation quota.
- Accessibility:
    - accessible=true: Bottoms out the hierarchy in this element. Like how a cell wouldn't want to show title + subtitle separately.
    - accessibilityLabel: Read aloud.
    - iOS: accessibilityTraits, accessibiliityViewIsModal, onAccessibilityTap,
      onMagicTap
    - Android: accessibilityComponentType, accessibilityLiveRegion (should changes be announced? like WAI-ARIA),
      importantForAccessibility (resolves what to focus when views overlap)
    - Android has synthetic AX event support: sendAccessibilityEvent
        - Needed to get it to report changes in some cases?
- Improving UX:
    - This is best practices and gotchas.
- Timers:
    - Have we ever got timers.
    - Use TimerMixin rather than setTimeout. "We found out that the primary
      cause of fatals in apps created with React Native was due to timers
      firing after a component was unmounted. To solve this recurring issue, we
      introduced TimerMixin."
        - XXX: ES6 giveth, and ES6 taketh away:
          "Keep in mind that if you use ES6 classes for your React components
          there is no built-in API for mixins. To use TimerMixin with ES6
          classes, we recommend react-mixin."
- Debugging:
    - Devtools: Chrome, React, Perf Monitor
    - Native code tools.
        - FB of course has their own for Android, Stetho.
- Performance:
    - Covers troubleshooting and common issues. Kinda long.
- Gesture Responder System:
    - Core lifecycle for input.
- JS Env:
    - Getting into how things work at runtime.
    - Dual environment, JSC vs Chrome V8. Fun.
    - Babel is shipped with React Native since 0.5.0.
    - And Polyfills are also shipped.
- Direct Manipulation:
    - "Like direct DOM node modification, only native."
    - Mostly setNativeProps: Re-rendering avoidance perf hack for continuous animations.
        - Also: Measuring stuff, focus, and blur.
    - Reach for setState & shouldComponentUpdate first.
    - Example: TouchableOpacity.
    - Gotcha: "Composite components are not backed by a native view, so you
      cannot call setNativeProps on them."
        - Error: "Touchable child must either be native or forward
          setNativeProps to a native component."
    - Gotcha: "If you update a property that is also managed by the render function, you might end up with some unpredictable and confusing bugs"
- Color Reference:
    - #fff, #aabbcc, #aabbcc00, hsl(), rgb(), rgba(), CSS3 named colors
- [Integration with Existing Apps:](https://facebook.github.io/react-native/docs/integration-with-existing-apps.html)
    - Hey, here we go!
    - "It also works well for adding a single view or user flow to existing
      native applications. With a few steps, you can add new React Native based
      features, screens, views, etc."
    - Broken out between Obj-C, Swift, and Java.
    - iOS:
        - Add deps and directory structure.
            - Just create a new RN app, then copy the iOS app into `ios/` inside it.
        - Pull in components using npm & CocoaPods.
        - Write code
            - "index.js is the starting point for React Native applications,
              and it is always required. It can be a small file that requires
              other file that are part of your React Native component or
              application, or it can contain all the code that is needed for
              it."
        - Host everything inside an RCTRootView.
            - Implicitly fires up a new RCTBridge with JSC VM.
            - You can share a single bridge instead.
            - You aim it at a URL for the script (use localhost during dev to
              vend for live reload), module name (declared via
              `AppRegistry.registerComponent(moduleName,
              functionToReturnAComponent)`), initial properties, and launch
              options.
        - Running is a two-step, npm start to run the dev server, then build
          and run.
    - Android:
        - Similar, but uses `ReactRootView`.
        - Needs some permissions and config to support all the dev features,
          like error overlays.
        - Instead of RCTBridge, there's a `ReactInstanceManager`. You have to
          manually forward some lifecycle callbacks in. And when you first
          configure it, you tell it the initial lifecycle state.
        - For release builds, you bundle the React stuff, then do a release
          build that pulls in the bundle.



## 2017-12-13 (Wed)
Today:

- Finish reading the Guides & Guides (iOS) sections. This has been valuable, and is worth continuing.
- Review the RN Changelog to get a feel for how it's developed. (timebox to 30 min)
- Build and run an app on iOS, and root around at runtime.
- Dig into WalmartLabs' Electrode Native, which looks like a ready-made tool for jumping into (though not sure about back out of) RN in the context of a mostly-native app.

Next:

- XXX


### Guides, Continued
- Running on Device:
- Upgrading:
- Troubleshooting:


### iOS Guides
XXX


### React Native: The How, Statically
Let's look at how apps get built.


### React Native: The How, Dynamically
Let's look at how:

- Display update happens: This traces from the React view decl => native UI.
- Action dispatching happens: This traces from the native UI => React handler.
- Web request handling works: This is a side concern, but an important one for many mobile apps.
