# Familiarize With Patching

## Introduction

In this lab, you will familiarize with some of the tools used to patch Oracle Database. 

Estimated Time: 5 Minutes


### Objectives

In this lab, you will:

* Use OPatch
* Use Datapatch
* Check an Oracle Database

### Prerequisites

This lab assumes:

- You have completed Lab 1: Initialize Environment

This is an optional lab. You can skip it if you are already familiar with patching Oracle Database.

## Task 1: Use OPatch from shell

You use *OPatch* to perform the first part of patching an Oracle Database; patching the Oracle home. OPatch replaces some files in the Oracle home and might also add new files. If the Oracle home is in use, for instance by a database instance or listener, you must stop those processes. 

1. Use the *yellow* terminal 🟨. Set the environment to *FTEX* and change to the Oracle home.

    ```
    <copy>
    . ftex
    cd $ORACLE_HOME
    </copy>
    ```

2. You find OPatch in a subdirectory. Check the version of OPatch.
    
    ```
    <copy>
    cd OPatch
    ./opatch version
    </copy>

    -- Be sure to hit RETURN
    ```

    <details>
    <summary>*click to see the output*</summary>
    ``` text
    $ cd OPatch
    $ ./opatch version
    OPatch Version: 12.2.0.1.42
    
    OPatch succeeded.
    ```
    </details>        

3. There are other means of finding the OPatch version.

    ```
    <copy>
    cat version.txt
    </copy>
    ```

    <details>
    <summary>*click to see the output*</summary>
    ``` text
    $ cat version.txt
    OPATCH_VERSION:12.2.0.1.42
    ```
    </details>  

4. Update OPatch by unzipping the OPatch patch file. Keep the old Oracle home as back.
    
    ```
    <copy>
    cd $ORACLE_HOME
    mv OPatch OPatch_backup
    unzip /home/oracle/patch-repo/p6880880_190000_Linux-x86-64.zip
    </copy>

    -- Be sure to hit RETURN
    ```

    * You should always use the latest version of OPatch. 
    * You can download the latest of OPatch from My Oracle Support. Search for patch *6880880*.

    <details>
    <summary>*click to see the output*</summary>
    ``` text
    $ cd $ORACLE_HOME
    $ mv OPatch OPatch_backup
    $ unzip /home/oracle/patch-repo/p6880880_190000_Linux-x86-64.zip
    Archive:  /home/oracle/patch-repo/p6880880_190000_Linux-x86-64.zip
       creating: OPatch/
      inflating: OPatch/opatchauto
      ...
      (output truncated)
      ...
      inflating: OPatch/modules/com.sun.xml.bind.jaxb-jxc.jar
      inflating: OPatch/modules/javax.activation.javax.activation.jar
    ```
    </details>    

5. Check the new version of OPatch
    
    ```
    <copy>
    $ORACLE_HOME/OPatch/opatch version
    </copy>
    ```

    * The previous version of OPatch was *12.2.0.1.42*.

    <details>
    <summary>*click to see the output*</summary>
    ``` text
    $ $ORACLE_HOME/OPatch/opatch version
    OPatch Version: 12.2.0.1.44
    
    OPatch succeeded.
    ```
    </details>     

6. Check the patches currently installed.

    ```
    <copy>
    $ORACLE_HOME/OPatch/opatch lspatches
    </copy>
    ```

    * Currently, the Oracle home on Release Update 19.21.0.
    * The OJVM and Data Pump bundle patches are installed as well.
    * You can ignore the OCW Release Update.

    <details>
    <summary>*click to see the output*</summary>
    ``` text
    $ $ORACLE_HOME/OPatch/opatch lspatches
    35648110;OJVM RELEASE UPDATE: 19.21.0.0.231017 (35648110)
    35787077;DATAPUMP BUNDLE PATCH 19.21.0.0.0
    35643107;Database Release Update : 19.21.0.0.231017 (35643107)
    29585399;OCW RELEASE UPDATE 19.3.0.0.0 (29585399)
    
    OPatch succeeded.
    ```
    </details>   

7. Get detailed information about the patches in the Oracle home.

    ```
    <copy>
    cd $ORACLE_HOME
    OPatch/opatch lsinventory > opatch_lsinventory.txt
    </copy>

    -- Be sure to hit RETURN
    ```

    * You spool the contents to a file.
    * If you create a service request in My Oracle Support, it is often a good idea to attach the file.
    * The file is mandatory in many cases, e.g., when requesting a merge patch or backport.

    <details>
    <summary>*click to see the output*</summary>
    ``` text
    $ cd $ORACLE_HOME
    $ OPatch/opatch lsinventory > opatch_lsinventory.txt
    ```
    </details>  

8. Examine the contents of the file.

    ```
    <copy>
    more opatch_lsinventory.txt
    </copy>
    ```

    * Use *Space* to browse through the pages.

    <details>
    <summary>*click to see the output*</summary>
    ``` text
    $ more opatch_lsinventory.txt
    Oracle Interim Patch Installer version 12.2.0.1.44
    Copyright (c) 2024, Oracle Corporation.  All rights reserved.
    
    
    Oracle Home       : /u01/app/oracle/product/19
    Central Inventory : /u01/app/oraInventory
       from           : /u01/app/oracle/product/19/oraInst.loc
    OPatch version    : 12.2.0.1.44
    OUI version       : 12.2.0.7.0
    Log file location : /u01/app/oracle/product/19/cfgtoollogs/opatch/opatch2024-10-31_11-00-40AM_1.log
    
    Lsinventory Output file location : /u01/app/oracle/product/19/cfgtoollogs/opatch/lsinv/lsinventory2024-10-31_11-00-40AM.txt
    --------------------------------------------------------------------------------
    Local Machine Information::
    Hostname: holserv1.livelabs.oraclevcn.com
    ARU platform id: 226
    ARU platform description:: Linux x86-64
    
    Installed Top-level Products (1):
    
    Oracle Database 19c                                                  19.0.0.0.0
    There are 1 products installed in this Oracle Home.
    
    
    Interim patches (4) :
    
    Patch  35648110     : applied on Wed Jul 10 15:00:04 GMT 2024
    Unique Patch ID:  25365038
    Patch description:  "OJVM RELEASE UPDATE: 19.21.0.0.231017 (35648110)"
       Created on 25 Aug 2023, 10:22:03 hrs UTC
       Bugs fixed:
         26716835, 28209601, 28674263, 28777073, 29224710, 29254623, 29415774

    ....
    (output truncated)         
    ....

         29380527, 29381000, 29382296, 29391301, 29393649, 29402110, 29411931
         29413360, 29457319, 29465047, 3
    
    
    
    --------------------------------------------------------------------------------
    
    OPatch succeeded.
    ```
    </details>      

## Task 2: Use OPatch inside the database


You may now *proceed to the next lab*.

## Acknowledgements

* **Author** - Daniel Overby Hansen
* **Contributors** - Klaus Gronau, Rodrigo Jorge, Alex Zaballa, Mike Dietrich
* **Last Updated By/Date** - Daniel Overby Hansen, August 2024
