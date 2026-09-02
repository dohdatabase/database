# Install Oracle Home

Out-of-place patching starts with creation of a new Oracle home. In this lab, you will install an Oracle home using AutoUpgrade. This method is simple and easy.

## Introduction

Estimated Time: 5 Minutes

### Objectives

In this lab, you will:

* Install Oracle home using AutoUpgrade

### Prerequisites

None.

## Task 1: Install using AutoUpgrade

1. Use the *yellow* terminal 🟨. Examine the following AutoUpgrade config file:

    ``` bash
    <copy>
    cd
    cat scripts/pt-02-install-oracle-home.cfg
    </copy>

    # Be sure to press RETURN
    ```

    * `source_home` is an existing Oracle home that you will use as a template to install the new Oracle home. AutoUpgrade installs the new Oracle home using the same settings as this Oracle home.
    * `target_home` is where you want to install the new Oracle home. Notice the placeholders *%RELEASE%* and *%UPDATE%* which AutoUpgrade translates based on what patches are installed.
    * `download_folder` is the location where AutoUpgrade can find and store patch files. Ideally, this location is a network share accessible to all your database hosts.
    * `patch` informs AutoUpgrade which patches you want to apply. You can specify a specific Release Update using the *RU* keyword with the Release Update specified after the colon. The keywords *OPATCH*, *OJVM* and *DPBP* installs the lastest version of OPatch and the OJVM and Data Pump bundle patches. *OCW* updates the OCW component in the Oracle home. In addition, you are also installing a one-off patch, *29213893*.
    * `download` tells whether AutoUpgrade should attempt to download the patches from My Oracle Support using your My Oracle Support credentials. This is not possible inside this lab environment, so all patches have been pre-downloaded.
    * `create_gold_image` instructs AutoUpgrade to create a new gold image once it has applied all the patches. You will use the gold image later.

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    $ cat scripts/pt-02-install-oracle-home.cfg
    global.global_log_dir=/home/oracle/logs/install-oracle-home
    patch1.source_home=/u01/app/oracle/product/19
    patch1.target_home=/u01/app/oracle/product/dbhome_%RELEASE%_%UPDATE%_au
    patch1.download_folder=/home/oracle/patch-repo
    patch1.patch=RECOMMENDED,OCW,JDK,SDOBP,29213893
    patch1.download=no
    patch1.create_gold_image=gold_image_dbhome_1932.zip
    ```

    </details>

2. Start AutoUpgrade to create the Oracle home.

    ``` bash
    <copy>
    java -jar autoupgrade.jar -config scripts/pt-02-install-oracle-home.cfg -patch -mode create_home
    </copy>
    ```

    * It may stay a while on *Processing config file ...*, while AutoUpgrade reads and catalog the zip files on */home/oracle/patch-repo* folder.

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    $ java -jar autoupgrade.jar -config scripts/pt-02-install-oracle-home.cfg -patch -mode create_home
    Processing config file ...
    +-----------------------------------------+
    | Starting AutoUpgrade Patching execution |
    +-----------------------------------------+
    Type 'help' to list console commands
    patch>
    ```

    </details>

3. You're now in the AutoUpgrade console. Monitor the progress.

    ``` bash
    <copy>
    lsj -a 10
    </copy>
    ```

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    patch> lsj -a 10
    patch> +----+-------------+-------+---------+-------+----------+-------+---------------------+
    |Job#|      DB_NAME|  STAGE|OPERATION| STATUS|START_TIME|UPDATED|              MESSAGE|
    +----+-------------+-------+---------+-------+----------+-------+---------------------+
    | 100|create_home_1|EXTRACT|EXECUTING|RUNNING|  08:04:08|33s ago|Extracting gold image|
    +----+-------------+-------+---------+-------+----------+-------+---------------------+
    Total jobs 1

    The command lsj is running every 10 seconds. PRESS ENTER TO EXIT
    ```

    </details>

4. When you have an existing Oracle home specified using `source_home` parameter, AutoUpgrade checks the settings of the source Oracle home and creates the target Oracle home with the same settings.

    * You can override the Oracle home settings using config file parameters. For instance, you could enable the OLAP option by using `patch1.home_settings.binopt.olap=yes`.
    * Some prefer to give the Oracle home a custom name which you can do with `patch1.home_settings.home_name=your_custom_home_name`.

5. If you have a brand-new server with no existing Oracle home, you can still using `-mode create_home`. AutoUpgrade creates the Oracle home with the default settings rather than copying from a source Oracle home. In this case, you can specify most of the runInstaller settings using `patch1.home_settings`.

6. **It takes around 15 minutes to install a new Oracle home, patch it, and create a gold image.**

7. Leave AutoUpgrade running and move on with the next lab.

You may now [*proceed to the next lab*](#next).

## Acknowledgements

* **Author** - Daniel Overby Hansen
* **Contributors** - Rodrigo Jorge, Alex Zaballa, Mike Dietrich, Alejandro Diaz
* **Last Updated By/Date** - Daniel Overby Hansen, September 2026

