# Patch a Database in Two Steps

## Introduction

In this lab, you will patch an Oracle AI Database in two steps using AutoUpgrade. First, you create the Oracle home and then patch it. By creating the Oracle home in advance, you can save time in your maintenance window.

Estimated Time: 20 Minutes

### Objectives

In this lab, you will:

* Create new 26ai Oracle home with the latest Release Update
* Analyze and patch the *CDB26* database

### Prerequisites

None.

## Task 1: Create Oracle home

1. Examine the following AutoUpgrade config file:

    ``` bash
    <copy>
    cd
    cat scripts/pt-b-two-step-patching.cfg
    </copy>

    # Be sure to press RETURN
    ```

    * `source_home` is an existing Oracle home that you will use as a template to install the new Oracle home. AutoUpgrade installs the new Oracle home using the same settings as this Oracle home.
    * `target_home` is where you want to install the new Oracle home and `home_settings.home_name` instructs AutoUpgrade to give the new Oracle home a custom name.
    * `sid` is the database that you want to patch.
    * `download_folder` is the location where AutoUpgrade can find and store patch files. Ideally, this location is a network share accessible to all your database hosts.
    * `patch` informs AutoUpgrade which patches you want to apply. *RECOMMENDED* gives you the latest Release Update with the newest MRP on top. In 26ai, it also includes the Data Pump bundle patch, but not the OJVM bundle patch. The OJVM patches are now part of the Release Update.
    * `download` tells whether AutoUpgrade should attempt to download the patches from My Oracle Support using your My Oracle Support credentials. This is not possible inside this lab environment, so all patches have been pre-downloaded.

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    global.global_log_dir=/home/oracle/logs/two-step-patching
    patch1.source_home=/u01/app/oracle/product/26
    patch1.target_home=/u01/app/oracle/product/dbhome_26_3_1
    patch1.home_settings.home_name=OraDbHome2631
    patch1.sid=CDB26
    patch1.download_folder=/home/oracle/patch-repo
    patch1.patch=RECOMMENDED
    patch1.download=no
    ```

    </details>

2. Start AutoUpgrade to create the Oracle home.

    ``` bash
    <copy>
    java -jar autoupgrade.jar -config scripts/pt-b-two-step-patching.cfg -patch -mode create_home
    </copy>
    ```

    * It may stay a while on *Processing config file ...*, while AutoUpgrade reads and catalog the zip files on */home/oracle/patch-repo* folder.

    <details>
    <summary>*click to see the output*</summary>

    ``` text
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

6. **It takes around 5 minutes to install a new 26ai Oracle home.**
  * It is faster to create a 26ai Oracle home compared to 19c.
  * From 26ai Oracle delivers up-to-date gold images that contain the newest Release Update, JDK and OCW component. You avoid the time it takes to apply those patches.

7. Wait for AutoUpgrade to complete.

8. Examine the Oracle Inventory and check the new Oracle home. 

    ``` bash
    <copy>
    cat /u01/app/oraInventory/ContentsXML/inventory.xml
    </copy>
    ```

    * You can find the Oracle home by the *NAME* attribute.
    * Look for *OraDbHome2631*.
    * The name looks similar to the other Oracle homes, but there is a difference.

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    <?xml version="1.0" standalone="yes" ?>
    <!-- Copyright (c) 1999, 2026, Oracle and/or its affiliates.
    All rights reserved. -->
    <!-- Do not modify the contents of this file by hand. -->
    <INVENTORY>
    <VERSION_INFO>
       <SAVED_WITH>12.2.0.9.0</SAVED_WITH>
       <MINIMUM_VER>2.1.0.6.0</MINIMUM_VER>
    </VERSION_INFO>
    <HOME_LIST>
    <HOME NAME="OraDB19Home1" LOC="/u01/app/oracle/product/19" TYPE="O" IDX="1"/>
    <HOME NAME="OraDB19Home2" LOC="/u01/app/oracle/product/dbhome_19_32" TYPE="O" IDX="2"/>
    <HOME NAME="OraDB21Home1" LOC="/u01/app/oracle/product/21" TYPE="O" IDX="3"/>
    <HOME NAME="OraDB23Home1" LOC="/u01/app/oracle/product/26" TYPE="O" IDX="4"/>
    <HOME NAME="OraDB19Home3" LOC="/u01/app/oracle/product/dbhome_19_32_lab_a" TYPE="O" IDX="5"/>
    <HOME NAME="OraDbHome2631" LOC="/u01/app/oracle/product/dbhome_26_3_1" TYPE="O" IDX="6"/>
    </HOME_LIST>
    <COMPOSITEHOME_LIST>
    </COMPOSITEHOME_LIST>
    </INVENTORY>
    ```

    </details>

9. Examine the new Oracle home.

    ``` bash
    <copy>
    export ORACLE_HOME=/u01/app/oracle/product/dbhome_26_3_1
    $ORACLE_HOME/OPatch/opatch lspatches
    </copy>

    # Be sure to press RETURN
    ```

    * The Oracle home has the latest Release Update. 26.3 was the newest Release Update when this lab was created.
    * The *Fix for Bug* entries are security fixes that were applied as part of the MRP.
    * The OCW component is also up-to-date even though you didn't specify that.

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    39788260;Fix for Bug 39788260
    39779540;39779540:Fix for Bug 39779540
    39779336;Fix for Bug 39779336
    39739695;Fix for Bug 39739695
    39661089;Fix for Bug 39661089
    39593097;DATAPUMP BUNDLE PATCH 23.26.3.0.0
    39578859;OCW RELEASE UPDATE 23.26.3.0.0 (39578859) Gold Image
    39578879;Database Release Update : 23.26.3.0.0 (39578879) Gold Image
    
    OPatch succeeded.
    ```

    </details>

## Task 2: Analyze database

Oracle recommends that you first check your database. AutoUpgrade in *analyze* mode is a lightweight and non-intrusive check of an Oracle AI Database. 

1. Analyze the database.

    ``` bash
    <copy>
    cd
    java -jar autoupgrade.jar -config scripts/pt-b-two-step-patching.cfg -mode analyze
    </copy>

    # Be sure to press RETURN
    ```

    * AutoUpgrade informs me that two PDBs are not open.

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    Processing config file ...
    Pluggable database BLUE in CDB26 is MOUNTED and it will not be processed
    Pluggable database GREEN in CDB26 is MOUNTED and it will not be processed
    +--------------------------------+
    | Starting AutoUpgrade execution |
    +--------------------------------+
    1 CDB(s) plus 2 PDB(s) will be analyzed
    Type 'help' to list console commands
    upg>
    ```

    </details>

2. Wait for AutoUpgrade to complete the analysis.

3. Set the environment and connect to the database.

    ``` bash
    <copy>
    . cdb26
    sql / as sysdba
    </copy>

    # Be sure to press RETURN
    ```

4. Open all PDBs.

    ``` bash
    <copy>
    alter pluggable database all open;
    </copy>
    ```

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    SQL> alter pluggable database all open;
    
    Pluggable database altered.
    ```

    </details>

5. Exit SQLcl.

    ``` sql
    <copy>
    exit
    </copy>
    ```    

6. Re-analyze the database. 

    ``` bash
    <copy>
    cd
    java -jar autoupgrade.jar -config scripts/pt-b-two-step-patching.cfg -mode analyze
    </copy>

    # Be sure to press RETURN
    ```

    * The message about closed PDBs is no longer shown.
    * All 4 PDBs are included.

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    Processing config file ...
    +--------------------------------+
    | Starting AutoUpgrade execution |
    +--------------------------------+
    1 CDB(s) plus 4 PDB(s) will be analyzed
    Type 'help' to list console commands
    upg>
    ```

    </details>

7. Wait a minute or two until AutoUpgrade completes. Don't exit from the AutoUpgrade console.

8. When the job completes, AutoUpgrade prints the location of the *summary report* which contains information about the analysis. Check the summary report.

    ``` bash
    <copy>
    cat /home/oracle/logs/two-step-patching/cfgtoollogs/upgrade/auto/status/status.log
    </copy>
    ```

    * Both *Version Before Upgrade* and *Version After Upgrade* is *23.26.3* so why patch this database? The new Oracle home contained the Monthly Recommended Patches (MRP #1), so the new Oracle home is more up-to-date.
    * This database was found to be ready for patching.
    * AutoUpgrade reports *Check passed and no manual intervention needed*. 

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    ==========================================
              Autoupgrade Summary Report
    ==========================================
    [Date]           Thu Sep 03 08:24:31 GMT 2026
    [Number of Jobs] 1
    ==========================================
    [Job ID] 101
    ==========================================
    [DB Name]                cdb26
    [Version Before Upgrade] 23.26.3.0.0
    [Version After Upgrade]  23.26.3.0.0
    ------------------------------------------
    [Stage Name]    PRECHECKS
    [Status]        SUCCESS
    [Start Time]    2026-09-03 08:24:11
    [Duration]      0:00:19
    [Log Directory] /home/oracle/logs/two-step-patching/CDB26/101/prechecks
    [Detail]        /home/oracle/logs/two-step-patching/CDB26/101/prechecks/cdb26_preupgrade.log
                    Check passed and no manual intervention needed
    ------------------------------------------
    ```

    </details>

9. You can find even more details in the precheck reports. You find them in the *Log Directory* mentioned in the above summary report. Examine the HTML report.

    ``` bash
    <copy>
    cd /home/oracle/logs/two-step-patching/CDB26/101/prechecks
    firefox cdb26_preupgrade.html &
    </copy>

    # Be sure to press RETURN
    ```
10. Close Firefox.

## Task 3: Patch database

Patching a single instance Oracle AI Database requires downtime. Downtime starts now.

1. Start patching the database.

    ``` bash
    <copy>
    cd
    java -jar autoupgrade.jar -config scripts/pt-b-two-step-patching.cfg -mode deploy
    </copy>

    # Be sure to press RETURN
    ```

    * Deploy mode is the complete automation which performs all parts of a patch process.

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    Processing config file ...
    +--------------------------------+
    | Starting AutoUpgrade execution |
    +--------------------------------+
    1 CDB(s) plus 4 PDB(s) will be processed
    Type 'help' to list console commands
    upg>
    ```

    </details>

2. Get the status of the job.

    ``` bash
    <copy>
    status -job 102 -a 10
    </copy>
    ```

    * The `status` command require the job number as input. You can find that in the list of active jobs.
    * The `-a 10` parameter refreshes the information every 10 seconds.

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    Details
    
        Job No           102
        Oracle SID       CDB26
        Start Time       26/09/03 08:29:53
        Elapsed (min):   0
        End time:        N/A
    
    Logfiles
    
        Logs Base:    /home/oracle/logs/two-step-patching/CDB26
        Job logs:     /home/oracle/logs/two-step-patching/CDB26/102
        Stage logs:   /home/oracle/logs/two-step-patching/CDB26/102/prefixups
        TimeZone:     /home/oracle/logs/two-step-patching/CDB26/temp
        Remote Dirs:
    
    Stages
        SETUP            <1 min
        GRP              <1 min
        PREUPGRADE       <1 min
        PRECHECKS        <1 min
        PREFIXUPS        ~0 min (RUNNING)
        DRAIN
        DBUPGRADE
        POSTCHECKS
        POSTFIXUPS
        POSTUPGRADE
        SYSUPDATES
    
    Stage-Progress Per Container
    
        +--------+---------+
        |Database|PREFIXUPS|
        +--------+---------+
        |CDB$ROOT|    0  % |
        |PDB$SEED|    0  % |
        |     RED|    0  % |
        |    BLUE|    0  % |
        |   GREEN|    0  % |
        +--------+---------+
    
    The command status is running every 10 seconds. PRESS ENTER TO EXIT
    ```

    </details>

6. It takes just a few minutes to patch the database. Leave AutoUpgrade running.

7. When patching completes, AutoUpgrade exists.

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    Job 101 completed
    ------------------- Final Summary --------------------
    Number of databases            [ 1 ]
    
    Jobs finished                  [1]
    Jobs failed                    [0]
    Jobs restored                  [0]
    Jobs pending                   [0]
    
    
    Please check the summary report at:
    /home/oracle/logs/simple-patching-existing-home/cfgtoollogs/upgrade/auto/status/status.html
    /home/oracle/logs/simple-patching-existing-home/cfgtoollogs/upgrade/auto/status/status.log
    ```

    </details>

8. Update the profile script. This lab has a profile script for each database that configures the environment accordingly. Since the database now runs out of a new Oracle home, you must update the profile script. This command replaces the `ORACLE_HOME` variable in the profile script.

    ``` bash
    <copy>
    sed -i 's|^ORACLE_HOME=.*|ORACLE_HOME=/u01/app/oracle/product/dbhome_26_3_1|' /usr/local/bin/upgr
    </copy>
    ```

9. That's it. You just patched your Oracle AI Database.

You may now [*proceed to the next lab*](#next).

## Acknowledgements

* **Author** - Daniel Overby Hansen
* **Contributors** - Rodrigo Jorge, Alex Zaballa, Mike Dietrich, Alejandro Diaz
* **Last Updated By/Date** - Daniel Overby Hansen, September 2026

