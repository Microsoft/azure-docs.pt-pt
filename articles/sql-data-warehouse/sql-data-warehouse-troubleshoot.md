---
title: Solução de problemas do Azure SQL Data Warehouse | Microsoft Docs
description: Solução de problemas do Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 7/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: a6a6fdf6e63bf8c063f8dd6f23ae380e9ce7b98d
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575506"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Solução de problemas do Azure SQL Data Warehouse
Este artigo lista perguntas comuns de solução de problemas.

## <a name="connecting"></a>A ligar
| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Falha de logon do usuário ' NT AUTHORITY\ANONYMOUS LOGON '. (Microsoft SQL Server, Erro: 18456) | Esse erro ocorre quando um usuário do AAD tenta se conectar ao banco de dados mestre, mas não tem um usuário no mestre.  Para corrigir esse problema, especifique o SQL Data Warehouse ao qual você deseja se conectar no momento da conexão ou adicione o usuário ao banco de dados mestre.  Consulte o artigo [visão geral de segurança][Security overview] para obter mais detalhes. |
| A entidade de segurança do servidor "myusername" não é capaz de acessar o banco de dados "mestre" no contexto de segurança atual. Não é possível abrir o banco de dados padrão do usuário. Falha no logon. Falha de logon do usuário ' MyUserName '. (Microsoft SQL Server, Erro: 916) | Esse erro ocorre quando um usuário do AAD tenta se conectar ao banco de dados mestre, mas não tem um usuário no mestre.  Para corrigir esse problema, especifique o SQL Data Warehouse ao qual você deseja se conectar no momento da conexão ou adicione o usuário ao banco de dados mestre.  Consulte o artigo [visão geral de segurança][Security overview] para obter mais detalhes. |
| Erro de CTAIP                                                  | Esse erro pode ocorrer quando um logon é criado no banco de dados mestre do SQL Server, mas não no banco de dados SQL Data Warehouse.  Se você encontrar esse erro, dê uma olhada no artigo [visão geral de segurança][Security overview] .  Este artigo explica como criar um logon e um usuário no mestre e como criar um usuário no banco de dados do SQL Data Warehouse. |
| Bloqueado pelo firewall                                          | Os bancos de dados SQL do Azure são protegidos por firewalls de nível de servidor e de banco de dados para garantir que apenas endereços IP conhecidos tenham acesso a um banco de dados. Os firewalls são seguros por padrão, o que significa que você deve habilitar explicitamente o endereço IP ou o intervalo de endereços antes de poder se conectar.  Para configurar o firewall para acesso, siga as etapas em [Configurar o acesso de firewall do servidor para o IP do cliente][Configure server firewall access for your client IP] nas [instruções de provisionamento][Provisioning instructions]. |
| Não é possível conectar com a ferramenta ou o driver                           | SQL Data Warehouse recomenda usar o [SSMS][SSMS], o [SSDT para Visual Studio][SSDT for Visual Studio]ou o [sqlcmd][sqlcmd] para consultar seus dados. Para obter mais informações sobre drivers e como se conectar a SQL Data Warehouse, confira [drivers do azure SQL data warehouse][Drivers for Azure SQL Data Warehouse] e [Conecte-se aos artigos do Azure SQL data warehouse][Connect to Azure SQL Data Warehouse] . |

## <a name="tools"></a>Ferramentas
| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| O pesquisador de objetos do Visual Studio está sem usuários do AAD           | Este é um problema conhecido.  Como alternativa, exiba os usuários em [Sys. database_principals][sys.database_principals].  Consulte [autenticação no Azure SQL data warehouse][Authentication to Azure SQL Data Warehouse] para saber mais sobre como usar Azure Active Directory com SQL data warehouse. |
| O script manual, o uso do assistente para criação de scripts ou a conexão via SSMS são lentos, não respondem ou produzem erros | Verifique se os usuários foram criados no banco de dados mestre. Em opções de script, verifique também se a edição do mecanismo está definida como "Edição do SQL Data Warehouse do Microsoft Azure" e se o tipo de mecanismo é "Banco de Dados SQL do Microsoft Azure". |
| Gerar scripts falha no SSMS                               | A geração de um script para SQL Data Warehouse falhará se a opção "gerar script para objetos dependentes" estiver definida como "true". Como alternativa, os usuários devem ir manualmente para ferramentas-> Opções-> Pesquisador de Objetos do SQL Server-> gerar script para opções dependentes e definir como false |

## <a name="performance"></a>Desempenho
| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Solução de problemas de desempenho de consultas                            | Se você estiver tentando solucionar problemas de uma consulta específica, comece com o [aprendizado de como monitorar suas consultas][Learning how to monitor your queries]. |
| O desempenho e os planos de consulta insatisfatórios geralmente são resultado de estatísticas ausentes | A causa mais comum de baixo desempenho é a falta de estatísticas em suas tabelas.  Consulte [mantendo estatísticas de tabela][Statistics] para obter detalhes sobre como criar estatísticas e por que elas são críticas para o desempenho. |
| Baixa simultaneidade/consultas enfileiradas                             | Compreender o [Gerenciamento de carga de trabalho][Workload management] é importante para entender como balancear a alocação de memória com simultaneidade. |
| Como implementar práticas recomendadas                              | O melhor lugar para começar a aprender maneiras de melhorar o desempenho da consulta é SQL Data Warehouse artigo de [práticas recomendadas][SQL Data Warehouse best practices] . |
| Como melhorar o desempenho com o dimensionamento                      | Às vezes, a solução para melhorar o desempenho é simplesmente adicionar mais poder de computação às suas consultas, [dimensionando seu SQL data warehouse][Scaling your SQL Data Warehouse]. |
| Desempenho de consulta insatisfatório como resultado da baixa qualidade de índice     | Algumas vezes as consultas podem ficar lentas devido à [baixa qualidade do índice columnstore][Poor columnstore index quality].  Consulte este artigo para obter mais informações e como [recriar índices para melhorar a qualidade do segmento][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>Gerenciamento do sistema
| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| MSG 40847: Não foi possível executar a operação porque o servidor excederia a cota de unidade de transação de banco de dados permitida de 45000. | Reduza o [DWU][DWU] do banco de dados que você está tentando criar ou [solicite um aumento de cota][request a quota increase]. |
| Investigando a utilização de espaço                              | Consulte [tamanhos de tabela][Table sizes] para entender a utilização de espaço do seu sistema. |
| Ajuda com o gerenciamento de tabelas                                    | Consulte o artigo [visão geral da tabela][Overview] para obter ajuda com o gerenciamento de suas tabelas.  Este artigo também inclui links para tópicos mais detalhados, como [tipos de dados de tabela][Data types], [distribuição de uma tabela][Distribute], [indexação de uma tabela][Index], [particionamento de uma tabela][Partition], [manutenção de estatísticas de tabela][Statistics] e [tabelas temporárias][Temporary]. |
| A barra de progresso da TDE (Transparent Data Encryption) não está atualizando no portal do Azure | Você pode exibir o estado de TDE por meio do [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |


## <a name="differences-from-sql-database"></a>Diferenças do banco de dados SQL
| Problema                                 | Resolução                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Recursos de banco de dados SQL sem suporte     | Consulte [recursos de tabela sem suporte][Unsupported table features]. |
| Tipos de dados SQL Database sem suporte   | Consulte [tipos de dados sem suporte][Unsupported data types].        |
| Limitações de exclusão e atualização         | Consulte [soluções alternativas de atualização][UPDATE workarounds], [soluções alternativas de exclusão][DELETE workarounds] e [uso de CTAS para contornar a sintaxe de atualização e exclusão sem suporte][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| Não há suporte para a instrução MERGE      | Consulte [soluções alternativas][MERGE workarounds]de mesclagem.                  |
| Limitações de procedimento armazenado          | Consulte [limitações de procedimento armazenado][Stored procedure limitations] para entender algumas das limitações dos procedimentos armazenados. |
| UDFs não dão suporte a instruções SELECT | Essa é uma limitação atual de nossos UDFs.  Consulte [criar função][CREATE FUNCTION] para a sintaxe que damos suporte. |

## <a name="next-steps"></a>Passos seguintes
Para obter mais ajuda na localização da solução para seu problema, aqui estão alguns outros recursos que você pode experimentar.

* [Blogues]
* [Pedidos de funcionalidades]
* [Vídeos]
* [Blogs da equipe CAT]
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
[Blogs da equipe CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Pedidos de funcionalidades]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Fórum do Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[Databricks]: https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse
