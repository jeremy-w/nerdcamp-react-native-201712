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

Next:


### React Native: The How, Statically
Let's look at how apps get built.


### React Native: The How, Dynamically
Let's look at how:

- Display update happens: This traces from the React view decl => native UI.
- Action dispatching happens: This traces from the native UI => React handler.
- Web request handling works: This is a side concern, but an important one for many mobile apps.
