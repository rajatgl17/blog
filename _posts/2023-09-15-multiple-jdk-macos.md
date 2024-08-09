---
layout: post
title: How to Easily Switch Between Multiple Java Versions on macOS Using Homebrew
description: How to Easily Switch Between Multiple Java Versions on macOS Using Homebrew
author: Rajat Goel
---

If you're a developer working on various projects, you might find yourself needing different versions of Java JDK installed on your system. This is especially true if you're working with legacy code or testing across different environments. Fortunately, on macOS, managing multiple Java versions can be a breeze with Homebrew. Here's a simple guide on how to install multiple Java versions and switch between them effortlessly.

## Step 1: Install Multiple Java JDK Versions Using Homebrew
First, let's install the versions of Java you need using Homebrew. Open your terminal and run the following commands:

        brew install openjdk@8
        brew install openjdk@11
        brew install openjdk@17
        brew install openjdk@20

These commands will install JDK 8, 11, 17, and 20 on your system.


## Step 2: Configure Your Shell Environment
Next, you need to set up your environment so you can easily switch between these Java versions. Add the following code to your ~/.zshrc file:

        export JAVA_HOME=$(/usr/libexec/java_home -v1.8)
        export JAVA_8_HOME=$(/usr/libexec/java_home -v1.8)
        export JAVA_11_HOME=$(/usr/libexec/java_home -v11)
        export JAVA_20_HOME=$(/usr/libexec/java_home -v20)
        export JAVA_17_HOME=$(/usr/libexec/java_home -v17)
        
        alias java8='export JAVA_HOME=$JAVA_8_HOME'
        alias java11='export JAVA_HOME=$JAVA_11_HOME'
        alias java20='export JAVA_HOME=$JAVA_20_HOME'
        alias java17='export JAVA_HOME=$JAVA_17_HOME'

This code sets up environment variables for each version of Java and creates aliases to make switching between them quick and easy.

## Step 3: Reload Your ~/.zshrc File
To apply the changes you've made, reload your ~/.zshrc file by running the following command in your terminal:
   
        source ~/.zshrc

## Step 4: Switch Between Java Versions
Now, switching between different versions of the Java JDK is as simple as typing a command in your terminal. For example, to switch to Java 17, you would run:

       java17

Want to switch to Java 8? Just run command:

       java8

And that's it! You've now set up your macOS system to easily manage and switch between multiple versions of Java JDK. This setup can save you a lot of time and hassle, especially if you're working on diverse projects that require different Java versions.
