# Troubleshooting

## Introduction

Some times you find a glitch in our code or a a bug. In this lab, you learn a few troubleshooting techniques.

Estimated Time: 10 minutes

### Objectives

In this lab, you will:

* Create an AutoUpgrade zip package
* Replay upgrade details

### Prerequisites

You have completed:
* Lab 4: Upgrade PDB Using Unplug-Plug
* Lab 5: Upgrade PDB Using Replay Upgrade

## Task 1: AutoUpgrade Zip Package

AutoUpgrade logs extensively. If you need to engage with Oracle, create a zip package.

1. Use the *yellow* 🟨 terminal. Generate a zip package from the upgrade of the *ORANGE* PDB.

    ``` bash
    <copy>
    cd
    mkdir auzip
    java -jar autoupgrade.jar -config /home/oracle/scripts/upg-unplug-plug-orange.cfg -zip -d auzip
    </copy>

    # Be sure to hit RETURN
    ```

    * The `-zip` parameter instructs AutoUpgrade to create a zip package.
    * The `-d` parameter tells where to create the package.
    * There is no `-mode` parameter. 

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    Getting [CDB26] trace logs by [cdb26/CDB26]
    Getting [CDB19] trace logs by [oBuffer]
    | 100%
    Zipped successfully at /home/oracle/auzip/AUPG_260812_0929_040.zip
    ```

    </details>

2. Take a closer look at the zip package.

    ``` bash
    <copy>
    cd auzip
    unzip AUPG*zip
    ls -l
    </copy>

    # Be sure to hit RETURN
    ```

    * It looks similar to the AutoUpgrade log directory.
    * But notice the *trace* directory.

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    total 15356
    -rw-r--r--. 1 oracle oinstall 15713761 Aug 12 09:29 AUPG_260812_0929_040.zip
    -rw-r--r--. 1 oracle oinstall      501 Aug 12 09:29 autoupgrade_config_pointers.log
    drwxr-xr-x. 5 oracle oinstall       40 Aug 12 09:30 CDB19
    drwxr-xr-x. 3 oracle oinstall       21 Aug 12 09:30 cfgtoollogs
    -rw-r--r--. 1 oracle oinstall      263 Aug 12 09:29 upg-unplug-plug-orange.cfg
    drwxr-xr-x. 2 oracle oinstall       79 Aug 12 09:30 trace    
    ```

    </details>

3. Take a closer look at the *trace* directory.

    ``` bash
    <copy>
    cd trace
    ls -l
    </copy>

    # Be sure to hit RETURN
    ```

    * AutoUpgrade included the alert log of both source and target CDB.
    * Also, it included the attention log.
    * In a Data Guard configuration, AutoUpgrade include Data Guard broker log files as well.

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    total 324
    -rw-r--r--. 1 oracle oinstall  86166 Aug 12 09:29 alert_CDB19.log
    -rw-r--r--. 1 oracle oinstall 225867 Aug 12 09:29 alert_CDB26.log
    -rw-r--r--. 1 oracle oinstall  10403 Aug 12 09:29 attention_CDB26.log    
    ```

    </details>

4. You can send the file to Oracle for further examination. Remove the package again.

    ``` bash
    <copy>
    cd
    rm -rf auzip
    </copy>

    # Be sure to hit RETURN
    ```

## Task 2: Replay Upgrade

Replay upgrade have fewer log files than a classic upgrade, and you need to look in other locations.

1. Use the *yellow* 🟨 terminal. In a previous lab, you upgraded the *TERRACOTTA* PDB using replay upgrade.Examine the alert log of *CDB26*.

    ``` bash
    <copy>
    cd /u01/app/oracle/diag/rdbms/cdb26/CDB26/trace
    sed -n '/alter pluggable database terracotta open/,$p' alert_CDB26.log | less
    </copy>

    # Be sure to hit RETURN
    ```

    * Scroll through the output with *page up* and *page down*.
    * Press *q* to exit.
    * After the `open` command, you can see that the database writes intensively into the alert log as the replay upgrade begins.

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    alter pluggable database terracotta open
    TERRACOTTA(9):Autotune of undo retention is turned on.
    2026-08-12T06:31:17.395941+00:00
    TERRACOTTA(9):Deleting old file#14 from file$
    TERRACOTTA(9):Deleting old file#15 from file$
    TERRACOTTA(9):Deleting old file#16 from file$
    TERRACOTTA(9):Adding new file#32 to file$(old file#14).             fopr-1, newblks-110080, oldblks-26880
    TERRACOTTA(9):Adding new file#33 to file$(old file#15).             fopr-1, newblks-21120, oldblks-21120
    TERRACOTTA(9):Adding new file#34 to file$(old file#16).             fopr-1, newblks-30720, oldblks-7680
    TERRACOTTA(9):Bug 38433601: Check for update service name
    TERRACOTTA(9):Bug 38433601: Call ksws_update_pdb_attr
    ****************************************************************
    Post plug operations are now complete.
    Pluggable database TERRACOTTA with pdb id - 9 is now marked as NEW.
    ****************************************************************
    TERRACOTTA(9):Resource Manager disabled during database migration: plan '' not set
    Violations: Type: 1, Count: 5
    Violations: Type: 2, Count: 5
    TERRACOTTA(9):***************************************************************
    TERRACOTTA(9):WARNING: Pluggable Database TERRACOTTA with pdb id - 9 is
    TERRACOTTA(9):         altered with errors or warnings. Please look into
    TERRACOTTA(9):         PDB_PLUG_IN_VIOLATIONS view for more details.
    TERRACOTTA(9):***************************************************************
    --ATTENTION--
    Errors reported while opening PDB (ContainerId: 1) and have been recorded in pdb_alert$ table.
    TERRACOTTA(1) Error Violation: OPTION, Cause: Database option CATALOG mismatch: PDB installed version 19.0.0.0. 0. CDB installed version 23.0.0.0.0., Action: Fix the database option in the PDB or the CDB
    --ATTENTION--
    Errors reported while opening PDB (ContainerId: 1) and have been recorded in pdb_alert$ table.
    TERRACOTTA(1) Error Violation: OPTION, Cause: Database option CATPROC mismatch: PDB installed version 19.0.0.0. 0. CDB installed version 23.0.0.0.0., Action: Fix the database option in the PDB or the CDB
    --ATTENTION--
    Errors reported while opening PDB (ContainerId: 1) and have been recorded in pdb_alert$ table.    
    TERRACOTTA(1) Error Violation: OPTION, Cause: Database option OWM mismatch: PDB installed version 19.0.0.0.0.   CDB installed version 23.0.0.0.0., Action: Fix the database option in the PDB or the CDB
    --ATTENTION--
    Errors reported while opening PDB (ContainerId: 1) and have been recorded in pdb_alert$ table.
    TERRACOTTA(1) Error Violation: OPTION, Cause: Database option XDB mismatch: PDB installed version 19.0.0.0.0.   CDB installed version 23.0.0.0.0., Action: Fix the database option in the PDB or the CDB
    TERRACOTTA(9):--ATTENTION--
    TERRACOTTA(9):Errors reported while opening PDB (ContainerId: 9) and have been recorded in pdb_alert$ table.
    TERRACOTTA(9) Error Violation: VSN not match, Cause: PDB's version does not match CDB's version: PDB's version  19.0.0.0.0. CDB's version 23.0.0.0.0., Action: Either upgrade the PDB or reload the components in the PDB.
    2026-08-12T06:31:18.602450+00:00
    TERRACOTTA(9):Opening pdb with no Resource Manager plan active
    TERRACOTTA(9):joxcsys_required_dirobj_exists: directory object does not exist, pid 277779 cid 9
    TERRACOTTA(9):joxcsys_ensure_directory_object: created directory object with path /u01/app/oracle/product/26/   javavm/admin/, pid 277779 cid 9
    2026-08-12T06:31:18.740949+00:00
    TERRACOTTA(9):alter pluggable database application app$cdb$pdbonly$catalog_prechecks begin install '1.0' on     error capture
    TERRACOTTA(9):Completed: alter pluggable database application app$cdb$pdbonly$catalog_prechecks begin install '1.   0' on error capture
    TERRACOTTA(9):alter pluggable database application app$cdb$pdbonly$catalog_prechecks end install '1.0'
    TERRACOTTA(9):Completed: alter pluggable database application app$cdb$pdbonly$catalog_prechecks end install '1.0'
    TERRACOTTA(9):Starting Upgrade on PDB Open
    TERRACOTTA(9):alter pluggable database application APP$CDB$CATALOG begin install '19.0.0.0.0' on error capture
    TERRACOTTA(9):Completed: alter pluggable database application APP$CDB$CATALOG begin install '19.0.0.0.0' on     error capture
    TERRACOTTA(9):alter pluggable database application APP$CDB$CATALOG end install '19.0.0.0.0'
    TERRACOTTA(9):Completed: alter pluggable database application APP$CDB$CATALOG end install '19.0.0.0.0'
    2026-08-12T06:31:19.815977+00:00
    TERRACOTTA(9):alter pluggable database application APP$CDB$CATALOG begin upgrade
      '19.0.0.0.0' to '23.0.0.0.0.partial' on error capture
    TERRACOTTA(9):Completed: alter pluggable database application APP$CDB$CATALOG begin upgrade
      '19.0.0.0.0' to '23.0.0.0.0.partial' on error capture

    (output truncated)  
    ```

    </details>

2. Replay upgrade also logs to background trace files. Examine the commands that were executed during the upgrade.

    ``` bash
    <copy>
    grep -i "replay upgrade.*text:" *.trc | less
    </copy>
    ```

    * Scroll through the output with *page up* and *page down*.
    * Press *q* to exit.

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    ```

    </details>

3. There are also views in the database that you can examine.

    ``` bash
    SELECT * FROM dba_replay_upgrade_errors;
    SELECT * FROM dba_applications WHERE app_name='APP$CDB$CATALOG';
    ```

    * Don't run the queries. It's beyond the scope of this exercise.

4. You also disable replay upgrade complete.

    ``` bash
    ALTER DATABASE UPGRADE SYNC OFF;
    ```

    * Don't run the command. It's beyond the scope of this exercise.

5. AutoUpgrade also have a config file parameter to control the use of replay upgrade.

    ``` bash
    upg1.replay=no
    ```

    * By default, AutoUpgrade uses classic upgrade because it allows more customizations.

You may now [*proceed to the next lab*](#next).

## Acknowledgements

* **Author** - Daniel Overby Hansen
* **Contributors** - Rodrigo Jorge, Alex Zaballa, Mike Dietrich, Alejandro Diaz
* **Last Updated By/Date** - Daniel Overby Hansen, August 2026