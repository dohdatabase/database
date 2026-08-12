# Upgrade PDB Using Unplug-Plug

## Introduction

In this lab, you will upgrade a single PDB using unplug-plug upgrades. You unplug the PDB from the 19c CDB, plug into a 26ai CDB and perform the upgrade. Unplug-plug upgrades are faster than full CDB upgrades, because you only need to upgrade PDB; not the full CDB. You will copy the data files during plug-in, which is slower but leaves you with a good rollback option. 

You will unplug the *ORANGE* PDB from *CDB19*, plug it into the *CDB26* database and upgrade.

Estimated Time: 10 minutes

### Objectives

In this lab, you will:

* Upgrade a PDB
* Plug in to an existing 26ai CDB
* Copy the data files

### Prerequisites

None.

## Task 1: Prepare for upgrade

You can plug in to an existing 26ai CDB on the same machine. AutoUpgrade handles the entire process. You start by checking the source database for upgrade readiness.

1. Use the *yellow* 🟨 terminal. Start *CDB19*. 

    ``` bash
    <copy>
    . cdb19
    sqlplus / as sysdba<<EOF
        startup
    EOF
    </copy>

    # Be sure to hit RETURN
    ```

2. Switch to the *ORANGE* PDB and check the `COMPATIBLE` parameter.

    ``` sql
    <copy>
    alter session set container=ORANGE;
    select value from v$parameter where name='compatible';
    </copy>

    # Be sure to hit RETURN
    ```

    * The `COMPATIBLE` parameter is set to `19.0.0`. 
    * We'll discuss the parameter in a later lab.

    <details>
    <summary>*click to see the output*</summary>

    ``` text
        VALUE
    _________
    19.0.0
    ```

    </details>

2. For this lab, you use a pre-created config file. Examine the pre-created config file.

    ``` bash
    <copy>
    cat /home/oracle/scripts/pdb-unplug-plug-orange.cfg
    </copy>
    ```

    * `sid` and `target_cdb` specify the SID of the source and target CDB.
    * `pdbs` is a comma-separated list of PDBs to upgrade
    * `target_pdb_copy_option` instructs AutoUpgrade how to copy the data files during plug-in. I have Oracle Managed Files (OMF), and I decide to use that with `file_name_convert=none`. 
    
    <details>
    <summary>*click to see the output*</summary>

    ``` text
    global.global_log_dir=/home/oracle/logs/pdb-unplug-plug-orange
    upg1.source_home=/u01/app/oracle/product/19
    upg1.target_home=/u01/app/oracle/product/26
    upg1.sid=CDB19
    upg1.target_cdb=CDB26
    upg1.pdbs=ORANGE
    upg1.target_pdb_copy_option.ORANGE=file_name_convert=none
    ```

    </details>

2. Start AutoUpgrade in *analyze* mode. The check usually completes very fast. Wait for it to complete.

    ``` bash
    <copy>
    java -jar autoupgrade.jar -config /home/oracle/scripts/pdb-unplug-plug-orange.cfg -mode analyze
    </copy>
    ```

    * You can use the `lsj` command to get details.

3. When AutoUpgrade completes, it prints the path to the summary report. Check the summary report.

    ``` bash
    <copy>
    cat /home/oracle/logs/pdb-unplug-plug-orange/cfgtoollogs/upgrade/auto/status/status.log
    </copy>
    ```

    * The report states *Check passed and no manual intervention needed*.

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    ==========================================
              Autoupgrade Summary Report
    ==========================================
    [Date]           Wed Aug 12 05:06:33 GMT 2026
    [Number of Jobs] 1
    ==========================================
    [Job ID] 100
    ==========================================
    [DB Name]                cdb19
    [Version Before Upgrade] 19.31.0.0.0
    [Version After Upgrade]  23.26.3.0.0
    ------------------------------------------
    [Stage Name]    PRECHECKS
    [Status]        SUCCESS
    [Start Time]    2026-08-12 05:06:19
    [Duration]      0:00:14
    [Log Directory] /home/oracle/logs/pdb-unplug-plug-orange/CDB19/100/prechecks
    [Detail]        /home/oracle/logs/pdb-unplug-plug-orange/CDB19/100/prechecks/cdb19_preupgrade.log
                    Check passed and no manual intervention needed
    ------------------------------------------
    ```

    </details>

## Task 2: Upgrade and Convert

Inside your maintenance window, you start AutoUpgrade to perform the upgrade.

1. Use the *yellow* 🟨 terminal. Start AutoUpgrade in *deploy* mode.

    ``` bash
    <copy>
    java -jar autoupgrade.jar -config /home/oracle/scripts/pdb-unplug-plug-orange.cfg -mode deploy
    </copy>
    ```

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    Processing config file ...
    +--------------------------------+
    | Starting AutoUpgrade execution |
    +--------------------------------+
    1 PDB(s) will be processed
    Type 'help' to list console commands
    upg>
    ```

    </details>

2. Monitor the upgrade. Use the `status` command.

    ``` bash
    <copy>
    status -job 101 -a 10
    </copy>
    ```

    * AutoUpgrade now unplugs the database from *CDB19*. 
    * When plugging in to *CDB26*, AutoUpgrade instructs the CDB to copy the data files.
    * This leaves a copy of the data files that can be used for rollback.
    * Finally, it upgrades the PDB. 

3. Leave the upgrade running. Do not exit AutoUpgrade. 

4. You return to this upgrade in a later lab.

You may now [*proceed to the next lab*](#next).

## Learn More

Upgrading a single PDB using unplug-plug upgrades is the fastest way to upgrade a database - compared to a full CDB upgrade. However, you can't use Flashback Database for rollbacks and it has implications on Data Guard. You can reuse the data files for faster upgrades or copy the data files for better rollback options. AutoUpgrade supports both methods.

* Webinar, [Move to Oracle Database 23ai – Everything you need to know about Oracle Multitenant – Part 2](https://www.youtube.com/watch?v=Sm75OIWagkE&t=3185s)
* Slides, [Move to Oracle Database 23ai – Everything you need to know about Oracle Multitenant – Part 2](https://dohdatabase.com/wp-content/uploads/2024/06/vc20_multitenant_part2-1.pdf)
* Blog post, [Upgrade Oracle Database 19c PDB to 26ai](https://dohdatabase.com/2026/02/17/upgrade-oracle-database-19c-pdb-to-26ai/)


## Acknowledgements

* **Author** - Daniel Overby Hansen
* **Contributors** - Rodrigo Jorge, Alex Zaballa, Mike Dietrich, Alejandro Diaz
* **Last Updated By/Date** - Daniel Overby Hansen, August 2026