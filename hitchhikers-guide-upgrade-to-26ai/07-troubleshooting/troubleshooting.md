# Troubleshooting

## Introduction

Some times you find a glitch in our code or a a bug. In this lab, you learn a few troubleshooting techniques.

Estimated Time: 10 minutes

### Objectives

In this lab, you will:

* Create an AutoUpgrade zip file
* Check logs and views 
* Disable replay upgrade

### Prerequisites

None.

## Task 1: AutoUpgrade zip file

You may now [*proceed to the next lab*](#next).


ALTER DATABASE UPGRADE SYNC OFF
upg1.replay=yes


You may now [*proceed to the next lab*](#next).

## Learn More

Upgrading a single PDB using replay upgrade is a convenient method. Originally developed for Oracle Autonomous AI Database it fits very well in uniform environments where PDBs are alike. However, there's no customization of the upgrade like you know from AutoUpgrade and the logging is not as extensive.

* Webinar, [Move to Oracle Database 23ai – Everything you need to know about Oracle Multitenant – Part 2](https://www.youtube.com/watch?v=Sm75OIWagkE&t=4469s)
* Slides, [Move to Oracle Database 23ai – Everything you need to know about Oracle Multitenant – Part 2](https://dohdatabase.com/wp-content/uploads/2024/06/vc20_multitenant_part2-1.pdf)

## Acknowledgements

* **Author** - Daniel Overby Hansen
* **Contributors** - Rodrigo Jorge, Alex Zaballa, Mike Dietrich, Alejandro Diaz
* **Last Updated By/Date** - Daniel Overby Hansen, August 2026