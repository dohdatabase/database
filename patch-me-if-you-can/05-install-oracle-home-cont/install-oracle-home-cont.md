# Install Oracle Home - Continued

In this lab, you will install an Oracle home using AutoUpgrade. This method is simple and easy.

## Introduction

Estimated Time: 20 Minutes

### Objectives

In this lab, you will:

* Check Oracle home
* Create Oracle home using gold image

### Prerequisites

This lab assumes:

* You have completed Lab 2: Install Oracle Home

## Task 1: Check AutoUpgrade

Ensure that AutoUpgrade installed the Oracle home and perform a few checks.

1. Switch back to the *yellow* terminal 🟨. AutoUpgrade should be done by now. Otherwise, wait for it to complete. In the end, AutoUpgrade prints the following information and exists.

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    Job 100 completed
    ------------------- Final Summary --------------------
    Number of databases            [ 1 ]

    Jobs finished                  [1]
    Jobs failed                    [0]
    Jobs restored                  [0]
    Jobs pending                   [0]



    Please check the summary report at:
    /home/oracle/autoupgrade-patching/install-oracle-home/log/cfgtoollogs/patch/auto/status/status.html
    /home/oracle/autoupgrade-patching/install-oracle-home/log/cfgtoollogs/patch/auto/status/status.log
    ```

    </details>

2. Check the inventory and find the newly installed Oracle home.

    ``` bash
    <copy>
    cat /u01/app/oraInventory/ContentsXML/inventory.xml
    </copy>
    ```

    * You should be able to find the XML element matching the Oracle home you just installed. The Oracle home ends with *au*.

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    $ cat /u01/app/oraInventory/ContentsXML/inventory.xml
    <?xml version="1.0" standalone="yes" ?>
    <INVENTORY>
    <VERSION_INFO>
       <SAVED_WITH>12.2.0.7.0</SAVED_WITH>
       <MINIMUM_VER>2.1.0.6.0</MINIMUM_VER>
    </VERSION_INFO>
    <HOME_LIST>
    <HOME NAME="OraDB19Home1" LOC="/u01/app/oracle/product/19" TYPE="O" IDX="1"/>
    <HOME NAME="OraDB21Home1" LOC="/u01/app/oracle/product/21" TYPE="O" IDX="2"/>
    <HOME NAME="OraDB23Home1" LOC="/u01/app/oracle/product/23" TYPE="O" IDX="3"/>
    <HOME NAME="OraDB19Home2" LOC="/u01/app/oracle/product/19_28" TYPE="O" IDX="4"/>
    <HOME NAME="OraDB19Home3" LOC="/u01/app/oracle/product/19_28_au" TYPE="O" IDX="5"/>
    </HOME_LIST>
    <COMPOSITEHOME_LIST>
    </COMPOSITEHOME_LIST>
    </INVENTORY>
    ```

    </details>

3. Check the patches installed.

    ``` bash
    <copy>
    export ORACLE_HOME=/u01/app/oracle/product/19_28_au
    $ORACLE_HOME/OPatch/opatch lspatches
    </copy>

    # Be sure to press RETURN
    ```

    * AutoUpgrade installed the Release Update you specified including the other patches. 
    * Notice how the OCW component has been updated as well. It is now on *19.28.0.0.0*. 

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    $ /u01/app/oracle/product/19_28_au/OPatch/opatch lspatches

    29213893;DBMS_STATS FAILING WITH ERROR ORA-01422 WHEN GATHERING STATS FOR USER$ TABLE
    37847857;OJVM RELEASE UPDATE: 19.28.0.0.250715 (37847857)
    37962946;OCW RELEASE UPDATE 19.28.0.0.0 (37962946)
    38170982;DATAPUMP BUNDLE PATCH 19.28.0.0.0
    37960098;Database Release Update : 19.28.0.0.250715 (37960098)

    OPatch succeeded.
    ```

    </details>

## Task 4: Optional: Use Gold Image

Gold images are a convenient way of installing Oracle homes on many different servers. You prepare and patch an Oracle home only once, and then distribute the patched Oracle home to all other servers.

1. **This is an optional lab that takes around 10 minutes**. If you are short on time, you can skip executing the commands, but do read on.

2. Still in the *yellow* terminal 🟨. Set the environment to the new Oracle home.

    ``` bash
    <copy>
    export ORACLE_HOME=/u01/app/oracle/product/19_28_au
    export PATH=$ORACLE_HOME/bin:$PATH
    </copy>

    # Be sure to press RETURN
    ```

    * This is the Oracle home you created using AutoUpgrade.
    * The Oracle home is patched with Release Update 28.

3. Create the gold image.

    ``` bash
    <copy>
    $ORACLE_HOME/runInstaller -createGoldImage \
       -destinationLocation /home/oracle/patch-repo \
       -name goldImage_dbHome_19_28_0.zip \
       -silent
    </copy>
    ```

    * It takes a few minutes to create the gold image.
    * The installer puts the Oracle home into a zip file.
    * `destinationLocation` determines where the gold image is placed.
    * `name` tells the installer the name of the zip file.
    * While the installer creates a gold image, reflect on the differences between creating the new Oracle home using AutoUpgrade and manually?
    * You can move on with the next lab while the installer completes.

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    Launching Oracle Database Setup Wizard...

    Successfully Setup Software.
    Gold Image location: /home/oracle/patch-repo/goldImage_dbHome_19_28_0.zip
    ```

    </details>

4. Find the gold image.

    ``` bash
    <copy>
    ls -l /home/oracle/patch-repo/goldImage*
    </copy>
    ```

    <details>
    <summary>*click to see the output*</summary>

    ``` text
    $ ls -l /home/oracle/patch-repo/goldImage*
    -rw-r--r--. 1 oracle oinstall 4625522638 Nov  5 11:43 /home/oracle/patch-repo/goldImage_dbHome_19_28_0.zip
    ```

    </details>

5. In this lab, you won't use the gold image. But you can review the commands needed to install it.

    ``` bash
    # Set environment to new Oracle home
    export ORACLE_HOME=/u01/app/oracle/product/19_28_gold

    # Extract gold image
    unzip /home/oracle/patch-repo/goldImage_dbHome_19_28_0.zip -d $ORACLE_HOME

    # Install the Oracle home
    export PATH=$ORACLE_HOME/bin:$PATH
    export ORAINVENTORY=/u01/app/oraInventory
    export ORACLE_BASE=/u01/app/oracle
    export CV_ASSUME_DISTID=OEL7.6
    cd $ORACLE_HOME
    ./runInstaller \
       -silent -ignorePrereqFailure -waitforcompletion \
       oracle.install.option=INSTALL_DB_SWONLY \
       UNIX_GROUP_NAME=oinstall \
       INVENTORY_LOCATION=$ORAINVENTORY \
       ORACLE_HOME=$ORACLE_HOME \
       ORACLE_BASE=$ORACLE_BASE \
       oracle.install.db.InstallEdition=EE \
       oracle.install.db.OSDBA_GROUP=dba \
       oracle.install.db.OSOPER_GROUP=dba \
       oracle.install.db.OSBACKUPDBA_GROUP=dba \
       oracle.install.db.OSDGDBA_GROUP=dba \
       oracle.install.db.OSKMDBA_GROUP=dba \
       oracle.install.db.OSRACDBA_GROUP=dba \
       SECURITY_UPDATES_VIA_MYORACLESUPPORT=false \
       DECLINE_SECURITY_UPDATES=true
    ```

    * Notice that you don't use the `-applyRU` or `-applyOneOffs` parameters.
    * The Oracle home is already patched, so you can skip that part.
    * OPatch is also already updated.
    * By using a gold image in your environment, you know that the same set of patches are in all of your databases.
    * You patch only once, then create a gold image, and use that to distribute to all systems.

What are you thoughts about installing a new Oracle home using AutoUpgrade? How do you think it compares to installing Oracle homes manually?

You may now [*proceed to the next lab*](#next).

## Acknowledgements

* **Author** - Daniel Overby Hansen
* **Contributors** - Rodrigo Jorge, Alex Zaballa, Mike Dietrich, Alejandro Diaz
* **Last Updated By/Date** - Daniel Overby Hansen, September 2026

