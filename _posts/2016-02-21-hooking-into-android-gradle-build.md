---
layout: post
title: "Hooking into Android Studio's Gradle build process"
tags: programming android java gradle coding
permalink: /blog/hooking-into-android-gradle-build/
author: "Hariharan Srinath"
---
If you want to automate steps to re-build data-files or assets for an Android app every time
the app gets built in Android Studio, you need to hook into the Gradle based build process.
While Gradle itself has [impressive documentation](https://docs.gradle.org/current/userguide/userguide.html),
it was not very clear initially how to hook into an Android app's build process. This article shows how I 
managed to make it work. 

Issue
-----
I want to make an Android app which uses a [WebView](https://developer.android.com/guide/webapps/webview.html)
to display a webapp built on [React Framework](https://facebook.github.io/react/). [Create React App](https://github.com/facebookincubator/create-react-app)
is a simple way to bootstrap a react app using [WebPack](https://webpack.github.io/) and 
[BabelJS](https://babeljs.io/) to translate JSX to plain JS and bundle the code. The 
Create React App scripts provides a simple live-rebuild and webserver feature for local development
and makes an `npm run build` command available to generate an optimized build.

Rather than host this webapp on a server, I would like to make it available locally under the `assets`
folder in the Android app to minimize latency. I would also like the webapp automatically rebuilt
everytime I compile the android app and refreshed in the `assets` folder.  

Solution
--------
The [Android Gradle Plugin](http://tools.android.com/tech-docs/new-build-system/user-guide#TOC-Android-tasks)
unfortunately uses fancy features of the Gradle DSL to build a task based on build target configurations
at runtime making it difficult to hook onto one of the `:assemble` or `:build` tasks while compiling.

However, one of the early tasks that runs during Android App compilation with Gradle is 
called `:preBuild` and allows us to reliably hook into it for running or own asset generation
routines. 

To do this, I appended the following at the bottom of `build.gradle` of the android application. 
I first create a Gradle [Exec](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.Exec.html)
task which runs the `npm run build` command in the directory containing the webapp sources. I then
create a Gradle [Sync](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.Sync.html) that copies
the generated webapp files to the `assets` folder. The `Sync` task depends on the `Exec` task. Finally
I make the Android build's `:preBuild` task depend on our `Sync` task so that the assets are build
and copied before the Android app is compiled.  

```gradle
task buildWebApp(type:Exec) {
    workingDir '../../webapp'
    commandLine 'npm', 'run', 'build'
}

task syncWebApp(type: Sync, dependsOn:'buildWebApp') {
    from '../../webapp/build'
    into 'src/main/assets/build'
}

preBuild.dependsOn(syncWebApp)
```