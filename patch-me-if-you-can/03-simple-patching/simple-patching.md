# Simple Patching With AutoUpgrade

## Introduction

In this lab, you will patch an Oracle Database in the most simple way using AutoUpgrade. AutoUpgrade not only patches the database but also downloads the right patches and builds a new Oracle home. This allows you to apply patches using the *out-of-place* method according to our best practices. 

In the lab environment there is no connected to download patches from My Oracle Support, so all patches are already downloaded.

Estimated Time: 20 Minutes


### Objectives

In this lab, you will:

* Assert the patch readiness of a database
* Patch a database

### Prerequisites

This lab assumes:

- You have completed Lab 1: Initialize Environment

## Task 1: Analyze database

Oracle recommends that you first check your database. AutoUpgrade in *analyze* mode is a lightweight and non-intrusive check of an Oracle Database.

1. Use the *yellow* terminal 🟨. AutoUpgrade requires a *config* file with information about the database that you want to patch. In this lab, you use a pre-created config file. Examine the config file.

    ```
    <copy>
    cd
    cat scripts/simple-patching.cfg
    </copy>

    -- Be sure to hit RETURN
    ```

    <details>
    <summary>*click to see the output*</summary>
    ``` text
    $ cat scripts/simple-patching.cfg
    global.global_log_dir=/home/oracle/autoupgrade-patching/simple-patching/log
    patch1.source_home=/u01/app/oracle/product/19
    patch1.target_home=/u01/app/oracle/product/19_22
    patch1.sid=FTEX
    patch1.folder=/home/oracle/patch-repo
    patch1.patch=RU:19.22,OPATCH,OJVM,DPBP
    patch1.download=no
    ```
    </details>    

2. Analyze the database.

    ```
    <copy>
    java -jar autoupgrade.jar -config scripts/simple-patching.cfg -patch -mode analyze
    </copy>
    ```


## Task 2: Patch database

You may now *proceed to the next lab*.

## Acknowledgements

* **Author** - Daniel Overby Hansen
* **Contributors** - Klaus Gronau, Rodrigo Jorge, Alex Zaballa, Mike Dietrich
* **Last Updated By/Date** - Daniel Overby Hansen, August 2024