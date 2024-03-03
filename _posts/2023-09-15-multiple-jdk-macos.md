---
layout: post
title: Installing and switching multiple JDK versions on MacOS
description: Installing and switching multiple JDK versions on MacOS
author: Rajat Goel
---

1. First install multiple versions of Java JDK using brew:

        brew install openjdk@8
        brew install openjdk@11
        brew install openjdk@17
        brew install openjdk@20



3. Add below code in ~/.zshrc file:

        export JAVA_HOME=$(/usr/libexec/java_home -v1.8)
        export JAVA_8_HOME=$(/usr/libexec/java_home -v1.8)
        export JAVA_11_HOME=$(/usr/libexec/java_home -v11)
        export JAVA_20_HOME=$(/usr/libexec/java_home -v20)
        export JAVA_17_HOME=$(/usr/libexec/java_home -v17)
        
        alias java8='export JAVA_HOME=$JAVA_8_HOME'
        alias java11='export JAVA_HOME=$JAVA_11_HOME'
        alias java20='export JAVA_HOME=$JAVA_20_HOME'
        alias java17='export JAVA_HOME=$JAVA_17_HOME'


4. Reload zshrc file in terminal using command:
   
        source ~/.zshrc

5. Now to switch to different JDK versions in your terminal, just use linked alias. For eg. running command java17 in terminal will swithc JDK version to Java 17

       java17
