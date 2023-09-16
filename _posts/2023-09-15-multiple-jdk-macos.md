---
layout: post
title: Installing and switching multiple JDK versions on MacOS
description: Installing and switching multiple JDK versions on MacOS
author: Rajat Goel
---

brew install openjdk@11

~/.zshrc

export JAVA_HOME=$(/usr/libexec/java_home -v1.8)
export JAVA_8_HOME=$(/usr/libexec/java_home -v1.8)
export JAVA_11_HOME=$(/usr/libexec/java_home -v11)
export JAVA_20_HOME=$(/usr/libexec/java_home -v20)
export JAVA_17_HOME=$(/usr/libexec/java_home -v17)

alias java8='export JAVA_HOME=$JAVA_8_HOME'
alias java11='export JAVA_HOME=$JAVA_11_HOME'
alias java20='export JAVA_HOME=$JAVA_20_HOME'
alias java17='export JAVA_HOME=$JAVA_17_HOME'
