---
title: Resolução de problemas
description: Troubleshooting Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/25/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b2a9a7b0b759f5853d83a4b1999887414fd5f430
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483205"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Troubleshooting Azure SQL Data Warehouse
This article lists common troubleshooting question.

## <a name="connecting"></a>Connecting
| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| falha no início de sessão do utilizador "NT AUTHORITY\ANONYMOUS LOGON". (Microsoft SQL Server, Error: 18456) | Este erro ocorre quando um utilizador do AAD tenta ligar à base de dados mestra, mas não tem um utilizador nessa base de dados.  Para corrigir este problema, especifique o SQL Data Warehouse a que pretende ligar na hora de ligação ou adicione o utilizador à base de dados mestra.  See [Security overview][Security overview] article for more details. |
| o principal do servidor "MyUserName" não consegue aceder à base de dados "mestra" no contexto de segurança atual. Não é possível abrir a base de dados predefinida do utilizador. O início de sessão falhou. O início de sessão falhou para o utilizador"MyUserName". (Microsoft SQL Server, Error: 916) | Este erro ocorre quando um utilizador do AAD tenta ligar à base de dados mestra, mas não tem um utilizador nessa base de dados.  Para corrigir este problema, especifique o SQL Data Warehouse a que pretende ligar na hora de ligação ou adicione o utilizador à base de dados mestra.  See [Security overview][Security overview] article for more details. |
| CTAIP error                                                  | This error can occur when a login has been created on the SQL server master database, but not in the SQL Data Warehouse database.  If you encounter this error, take a look at the [Security overview][Security overview] article.  This article explains how to create a login and user on master, and then how to create a user in the SQL Data Warehouse database. |
| Blocked by Firewall                                          | Azure SQL databases are protected by server and database level firewalls to ensure only known IP addresses have access to a database. The firewalls are secure by default, which means that you must explicitly enable and IP address or range of addresses before you can connect.  To configure your firewall for access, follow the steps in [Configure server firewall access for your client IP][Configure server firewall access for your client IP] in the [Provisioning instructions][Provisioning instructions]. |
| Cannot connect with tool or driver                           | SQL Data Warehouse recommends using [SSMS][SSMS], [SSDT for Visual Studio][SSDT for Visual Studio], or [sqlcmd][sqlcmd] to query your data. For more information on drivers and connecting to SQL Data Warehouse, see [Drivers for Azure SQL Data Warehouse][Drivers for Azure SQL Data Warehouse] and [Connect to Azure SQL Data Warehouse][Connect to Azure SQL Data Warehouse] articles. |

## <a name="tools"></a>Ferramentas
| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Visual Studio object explorer is missing AAD users           | Este é um problema conhecido.  As a workaround, view the users in [sys.database_principals][sys.database_principals].  See [Authentication to Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse] to learn more about using Azure Active Directory with SQL Data Warehouse. |
| Manual scripting, using the scripting wizard, or connecting via SSMS is slow, not responding, or producing errors | Ensure that users have been created in the master database. In scripting options, also make sure that the engine edition is set as “Microsoft Azure SQL Data Warehouse Edition” and engine type is “Microsoft Azure SQL Database”. |
| Generate scripts fails in SSMS                               | Generating a script for SQL Data Warehouse fails if the option "Generate script for dependent objects" option is set to "True." As a workaround, users must manually go to Tools -> Options ->SQL Server Object Explorer -> Generate script for dependent options and set to false |

## <a name="performance"></a>Desempenho
| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Query performance troubleshooting                            | If you are trying to troubleshoot a particular query, start with [Learning how to monitor your queries][Learning how to monitor your queries]. |
| TempDB space issues | [Monitor TempDB](sql-data-warehouse-manage-monitor.md#monitor-tempdb) space usage.  Common causes for running out of TempDB space are:<br>- Not enough resources allocated to the query causing data to spill to TempDB.  See [Workload management](resource-classes-for-workload-management.md) <br>- Statistics are missing or out of date causing excessive data movement.  See [Maintaining table statistics][Statistics] for details on how to create statistics<br>- TempDB space is allocated per service level.  [Scaling your SQL Data Warehouse][Scaling your SQL Data Warehouse] to a higher DWU setting allocates more TempDB space.|
| Poor query performance and plans often is a result of missing statistics | The most common cause of poor performance is lack of statistics on your tables.  See [Maintaining table statistics][Statistics] for details on how to create statistics and why they are critical to your performance. |
| Low concurrency / queries queued                             | Understanding [Workload management][Workload management] is important in order to understand how to balance memory allocation with concurrency. |
| How to implement best practices                              | The best place to start to learn ways to improve query performance is [SQL Data Warehouse best practices][SQL Data Warehouse best practices] article. |
| How to improve performance with scaling                      | Sometimes the solution to improving performance is to simply add more compute power to your queries by [Scaling your SQL Data Warehouse][Scaling your SQL Data Warehouse]. |
| Poor query performance as a result of poor index quality     | Some times queries can slow down because of [Poor columnstore index quality][Poor columnstore index quality].  See this article for more information and how to [Rebuild indexes to improve segment quality][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>System management
| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: Could not perform the operation because server would exceed the allowed Database Transaction Unit quota of 45000. | Either reduce the [DWU][DWU] of the database you are trying to create or [request a quota increase][request a quota increase]. |
| Investigating space utilization                              | See [Table sizes][Table sizes] to understand the space utilization of your system. |
| Help with managing tables                                    | See the [Table overview][Overview] article for help with managing your tables.  This article also includes links into more detailed topics like [Table data types][Data types], [Distributing a table][Distribute], [Indexing a table][Index],  [Partitioning a table][Partition], [Maintaining table statistics][Statistics] and [Temporary tables][Temporary]. |
| Transparent data encryption (TDE) progress bar is not updating in the Azure portal | You can view the state of TDE via [powershell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |


## <a name="differences-from-sql-database"></a>Differences from SQL Database
| Problema                                 | Resolução                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Unsupported SQL Database features     | See [Unsupported table features][Unsupported table features]. |
| Unsupported SQL Database data types   | See [Unsupported data types][Unsupported data types].        |
| DELETE and UPDATE limitations         | See [UPDATE workarounds][UPDATE workarounds], [DELETE workarounds][DELETE workarounds] and [Using CTAS to work around unsupported UPDATE and DELETE syntax][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| MERGE statement is not supported      | See [MERGE workarounds][MERGE workarounds].                  |
| Stored procedure limitations          | See [Stored procedure limitations][Stored procedure limitations] to understand some of the limitations of stored procedures. |
| UDFs do not support SELECT statements | This is a current limitation of our UDFs.  See [CREATE FUNCTION][CREATE FUNCTION] for the syntax we support. |

## <a name="next-steps"></a>Passos seguintes
For more help in finding solution to your issue, here are some other resources you can try.

* [Blogues]
* [Pedidos de funcionalidades]
* [Vídeos]
* [CAT team blogs]
* [Criar pedido de suporte]
* [Fórum do MSDN]
* [Fórum do Stack Overflow]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SSMS]: /sql/ssms/download-sql-server-management-studio-ssms
[SSDT for Visual Studio]: sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Criar pedido de suporte]: sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: sql-data-warehouse-manage-compute-overview.md
[DWU]: sql-data-warehouse-overview-what-is.md
[request a quota increase]: sql-data-warehouse-get-started-create-support-ticket.md
[Learning how to monitor your queries]: sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: sql-data-warehouse-get-started-provision.md
[SQL Data Warehouse best practices]: sql-data-warehouse-best-practices.md
[Table sizes]: sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: sql-data-warehouse-tables-overview.md
[Data types]: sql-data-warehouse-tables-data-types.md
[Distribute]: sql-data-warehouse-tables-distribute.md
[Index]: sql-data-warehouse-tables-index.md
[Partition]: sql-data-warehouse-tables-partition.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[Temporary]: sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: resource-classes-for-workload-management.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: sql-data-warehouse-develop-ctas.md
[UPDATE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: sql-data-warehouse-authentication.md


<!--MSDN references-->
[sys.database_principals]: /sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql
[CREATE FUNCTION]: /sql/t-sql/statements/create-function-sql-data-warehouse
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Blogues]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[CAT team blogs]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Pedidos de funcionalidades]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Fórum do Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[Databricks]: https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse
