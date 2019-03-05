---
title: Resolução de problemas de armazém de dados SQL do Azure | Documentos da Microsoft
description: Resolução de problemas do armazém de dados SQL do Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 12/04/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 5c9d8330e857a8f76e143631f17e29ba44244a88
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57307926"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Resolução de problemas de armazém de dados SQL do Azure
Este artigo apresenta uma lista de perguntas de resolução de problemas comuns.

## <a name="connecting"></a>A ligar
| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Falha de logon do usuário 'Início de sessão do NT AUTHORITY\ANONYMOUS'. (Microsoft SQL Server, Error: 18456) | Este erro ocorre quando um utilizador do AAD tenta ligar à base de dados mestra, mas não tem um utilizador no ramo principal.  Para corrigir este problema, ou especificar o que pretende ligar ao tempo de ligação ou adicionar o utilizador à base de dados mestra do SQL Data Warehouse.  Ver [descrição geral da segurança] [ Security overview] artigo para obter mais detalhes. |
| O servidor principal "MyUserName" não é capaz de acessar o banco de dados "master" no contexto de segurança atual. Não é possível abrir a base de dados do usuário padrão. O início de sessão falhou. Início de sessão falhou para o utilizador 'MyUserName'. (Microsoft SQL Server, Error: 916) | Este erro ocorre quando um utilizador do AAD tenta ligar à base de dados mestra, mas não tem um utilizador no ramo principal.  Para corrigir este problema, ou especificar o que pretende ligar ao tempo de ligação ou adicionar o utilizador à base de dados mestra do SQL Data Warehouse.  Ver [descrição geral da segurança] [ Security overview] artigo para obter mais detalhes. |
| Erro CTAIP                                                  | Este erro pode ocorrer quando tiver sido criado um início de sessão no SQL server base de dados mestra, mas não na base de dados do SQL Data Warehouse.  Se encontrar este erro, veja a [descrição geral da segurança] [ Security overview] artigo.  Este artigo explica como criar um início de sessão e um utilizador no mestre e, em seguida, como criar um utilizador na base de dados SQL Data Warehouse. |
| Bloqueado pela Firewall                                          | Bases de dados SQL do Azure estão protegidos por firewalls ao nível, servidor e base de dados, para garantir que só conhecida endereços IP têm acesso a uma base de dados. As firewalls são seguras por padrão, o que significa que terá de permitir explicitamente e endereço IP ou intervalo de endereços antes de poder ligar.  Para configurar seu firewall para acesso, siga os passos em [configurar o acesso de firewall do servidor para o seu IP de cliente] [ Configure server firewall access for your client IP] no [aprovisionamento instruções] [Provisioning instructions]. |
| Não é possível ligar com a ferramenta ou um controlador                           | O SQL Data Warehouse recomenda o uso [SSMS][SSMS], [SSDT para Visual Studio][SSDT for Visual Studio], ou [sqlcmd] [ sqlcmd] para consultar os seus dados. Para obter mais informações sobre drivers e ligar ao SQL Data Warehouse, consulte [Drivers para o Azure SQL Data Warehouse] [ Drivers for Azure SQL Data Warehouse] e [ligar ao Azure SQL Data Warehouse] [ Connect to Azure SQL Data Warehouse] artigos. |

## <a name="tools"></a>Ferramentas
| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Explorador de objetos do Visual Studio está em falta os utilizadores do AAD           | Este é um problema conhecido.  Como solução, ver os usuários [database_principals][sys.database_principals].  Ver [Authentication ao Azure SQL Data Warehouse] [ Authentication to Azure SQL Data Warehouse] para saber mais sobre como utilizar o Azure Active Directory com o SQL Data Warehouse. |
| Manual de criação de scripts, utilizando o Assistente de criação de scripts ou pode ligar através do SSMS é lentos, travados ou produção de erros | Certifique-se de que os utilizadores foram criados na base de dados mestra. Em Opções de script, certifique-se também que a edição do motor está definida como "Microsoft Azure SQL Data Warehouse Edition" e tipo de mecanismo é "Microsoft Azure SQL Database". |
| Gerar scripts falhar no SSMS                             | Gerar um script do SQL data warehouse falha se a opção "Gerar o script para objetos dependentes" da opção estiver definida como "True". Como solução, os utilizadores tem manualmente de aceder ao ferramentas -> Opções -> SQL Server Object Explorer -> gerar scripts para opções dependentes e definido como false |

## <a name="performance"></a>Desempenho
| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Resolução de problemas de desempenho de consulta                            | Se estiver a tentar resolver uma consulta específica, começar com [aprender a monitorizar as suas consultas][Learning how to monitor your queries]. |
| Planos e desempenho de consulta fraca, muitas vezes, é o resultado das estatísticas não encontradas | A causa mais comum de fraco desempenho é a falta de estatísticas em suas tabelas.  Ver [manutenção de estatísticas de tabela] [ Statistics] para obter detalhes sobre como criar estatísticas e por que elas são vitais para o desempenho. |
| Simultaneidade baixo / consultas em fila                             | Noções básicas sobre [gerenciamento de carga de trabalho] [ Workload management] é importante para compreender como equilibrar a alocação de memória com a simultaneidade. |
| Como implementar as práticas recomendadas                              | O melhor lugar para começar a aprender de formas de melhorar o desempenho de consulta é [práticas recomendadas do SQL Data Warehouse] [ SQL Data Warehouse best practices] artigo. |
| Como melhorar o desempenho com o dimensionamento                      | Por vezes, a solução para melhorar o desempenho é simplesmente adicionar mais poder às suas consultas através de computação [dimensionar o SQL Data Warehouse][Scaling your SQL Data Warehouse]. |
| Desempenho de consultas ruim como resultado da qualidade do índice fraca     | Algumas vezes consultas podem abrandar devido [qualidade do índice columnstore fraco][Poor columnstore index quality].  Veja este artigo para obter mais informações e como [reconstruir índices para melhorar a qualidade do segmento][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>Gestão do sistema
| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: Não foi possível efetuar a operação porque o servidor iria exceder a quota permitida da unidade de transação de base de dados de 45000. | Reduza a [DWU] [ DWU] da base de dados está a tentar criar ou [pedir um aumento de quota][request a quota increase]. |
| Investigar a utilização de espaço                              | Ver [tamanhos de tabela] [ Table sizes] para compreender a utilização de espaço do seu sistema. |
| Ajuda a gerir a tabelas                                    | Consulte a [descrição geral da tabela] [ Overview] artigo para obter ajuda com o gerenciamento de suas tabelas.  Este artigo também contém ligações para tópicos mais detalhados, como [tipos de dados de tabelas][Data types], [distribuição de uma tabela][Distribute], [Uma tabela de indexação][Index], [criação de partições de uma tabela][Partition], [manutenção de estatísticas de tabela] [ Statistics] e [tabelas temporárias][Temporary]. |
| Barra de progresso de criptografia (TDE) de dados transparente não está a atualizar no Portal do Azure | Pode ver o estado de TDE via [powershell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |

## <a name="polybase"></a>Polybase
| Problema                                           | Resolução                                                   |
| :---------------------------------------------- | :----------------------------------------------------------- |
| Carga falha devido de linhas grandes                | Atualmente o suporte de linha grandes não está disponível para o Polybase.  Isso significa que, se a tabela contiver varchar (Max), nvarchar (Max) ou varbinary (Max), tabelas externas não podem ser usadas para carregar os dados.  O carregamento de linhas grandes é atualmente suportada apenas através do Azure Data Factory (com o BCP), Azure Stream Analytics, SSIS, BCP ou a classe do .NET SQLBulkCopy. Suporte de PolyBase para linhas grandes será adicionado numa versão futura. |
| carga de BCP de tabela com o tipo de dados de máx. está a falhar | Existe um problema conhecido que requer que varchar (Max), nvarchar (Max) ou varbinary (Max) sejam colocados no final da tabela em alguns cenários.  Experimente a mover as colunas de máx. para o fim da tabela. |

## <a name="differences-from-sql-database"></a>Diferenças da base de dados SQL
| Problema                                 | Resolução                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Funcionalidades de base de dados SQL não suportadas     | Ver [tabela de funcionalidades não suportadas][Unsupported table features]. |
| Tipos de dados de base de dados SQL não suportados   | Ver [não suportados tipos de dados][Unsupported data types].        |
| ELIMINAR e limitações de ATUALIZAÇÃO         | Ver [soluções alternativas de ATUALIZAÇÃO][UPDATE workarounds], [soluções alternativas de eliminação] [ DELETE workarounds] e [utilizar CTAS para contornar não suportada a ATUALIZAÇÃO e Sintaxe de eliminação][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| Instrução MERGE não é suportada      | Ver [soluções alternativas de intercalação][MERGE workarounds].                  |
| Limitações do procedimento armazenado          | Ver [armazenados limitações do procedimento] [ Stored procedure limitations] para compreender algumas das limitações de procedimentos armazenados. |
| UDFs não suportam instruções SELECT | Esta é uma limitação atual dos nossos UDFs.  Ver [CREATE FUNCTION] [ CREATE FUNCTION] para a sintaxe suportamos. |

## <a name="next-steps"></a>Passos Seguintes
Para mais ajuda para encontrar a solução para o seu problema, aqui estão alguns outros recursos, que pode experimentar.

* [Blogues]
* [Pedidos de funcionalidades]
* [Vídeos]
* [Blogues da equipa CAT]
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
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
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
[Blogues da equipa CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Pedidos de funcionalidades]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Fórum do Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
