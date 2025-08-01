# Initialize Environment

## Introduction

In this lab, you will check all components required to successfully run this workshop.

Estimated Time: 5 Minutes

### Objectives

In this lab, you will:

* Familiarize yourself with the workshop environment
* Initialize the workshop environment

## Task 1: Familiarize yourself with the workshop environment

1. The easiest way to complete the lab is to copy/paste the lab instructions directly into a terminal. Be sure to execute all commands in a code block. After pasting, you must hit *RETURN*.

2. Before copy/pasting, take notice of the commands that you execute; it is important to understand what the commands will do.

3. Double-click on the *Terminal* shortcut on the desktop.

    ![Click shortcut to start a terminal](./images/initialize-environment-desktop-click-terminal.jpeg " ")

4. The terminal has two tabs, *yellow* 🟨 and *blue* 🟦. The instructions might tell you which tab to use. If not, you can use any of them. All labs start by setting the appropriate environment.

5. Optionally, in the terminal, you can zoom in to make the text larger.

    ![Zoom in to make the text larger in the terminal](./images/initialize-environment-terminal-zoom-in.png)

6. Throughout the labs you will open HTML documents in Firefox browser. If the text in the documents is too small, you can zoom in.

    ![Zoom in in Firefox to make text bigger](images/initialize-environment-firefox-zoom.png)

## Task 2: Initialize the workshop environment

1. When you start the lab, the following components should be started.

    * Database Listener
        * LISTENER
    * Database Server Instances
        * FTEX
        * UPGR
        * CDB23

2. Ensure the listener is started. Use the *yellow* terminal 🟨.

    ``` bash
    <copy>
    ps -ef | grep LISTENER | grep -v grep
    </copy>
    ```

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    $ ps -ef | grep LISTENER | grep -v grep
    oracle    2333     1  0 11:40 ?        00:00:00 /u01/app/oracle/product/19/bin/tnslsnr LISTENER -inherit
    ```

    </details>

3. Ensure that the databases (*FTEX*, *UPGR* and *CDB23*) are started.

    ``` bash
    <copy>
    ps -ef | grep ora_ | grep pmon | grep -v grep
    </copy>
    ```

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    $ ps -ef | grep ora_ | grep pmon | grep -v grep
    oracle      3851       1  0 20:19 ?        00:00:00 ora_pmon_UPGR
    oracle      5110       1  0 20:19 ?        00:00:00 ora_pmon_FTEX
    oracle      5345       1  0 20:19 ?        00:00:00 ora_pmon_CDB23
    ```

    </details>

You may now [*proceed to the next lab*](#next).

## Acknowledgements

* **Author** - Daniel Overby Hansen
* **Contributors** - Rodrigo Jorge, Mike Dietrich
* **Last Updated By/Date** - Daniel Overby Hansen, August 2025
