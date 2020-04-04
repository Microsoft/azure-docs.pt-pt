---
title: Resolução de problemas
description: Resolução de problemas Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: b24706943cdf59fba89a8007c4914b628b9e34d5
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632970"
---
# <a name="troubleshooting-sql-analytics-in-azure-synapse"></a>Resolução de problemas sQL Analytics em Azure Synapse

Este artigo enumera uma pergunta comum de resolução de problemas.

## <a name="connecting"></a>Ligação

| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| falha no início de sessão do utilizador "NT AUTHORITY\ANONYMOUS LOGON". (Microsoft SQL Server, Error: 18456) | Este erro ocorre quando um utilizador da AD Azure tenta ligar-se à base de dados principal, mas não tem um utilizador em mestre.  Para corrigir este problema, especifique o pool SQL a que pretende ligar-se no momento da ligação ou adicione o utilizador à base de dados principal.  Consulte o artigo [de visão geral da Segurança](sql-data-warehouse-overview-manage-security.md) para mais detalhes. |
| o principal do servidor "MyUserName" não consegue aceder à base de dados "mestra" no contexto de segurança atual. Não é possível abrir a base de dados predefinida do utilizador. O início de sessão falhou. O início de sessão falhou para o utilizador"MyUserName". (Microsoft SQL Server, Error: 916) | Este erro ocorre quando um utilizador da AD Azure tenta ligar-se à base de dados principal, mas não tem um utilizador em mestre.  Para corrigir este problema, especifique o pool SQL a que pretende ligar-se no momento da ligação ou adicione o utilizador à base de dados principal.  Consulte o artigo [de visão geral da Segurança](sql-data-warehouse-overview-manage-security.md) para mais detalhes. |
| Erro CTAIP                                                  | Este erro pode ocorrer quando foi criado um login na base de dados principal do servidor SQL, mas não na base de dados SQL.  Se encontrar este erro, veja o artigo sobre a visão geral da [Segurança.](sql-data-warehouse-overview-manage-security.md)  Este artigo explica como criar um login e utilizador no master, e depois como criar um utilizador na base de dados SQL. |
| Bloqueado por Firewall                                          | As piscinas SQL estão protegidas por firewalls para garantir que apenas os endereços IP conhecidos tenham acesso a uma base de dados. As firewalls são seguras por defeito, o que significa que deve ativar explicitamente o endereço IP ou o alcance dos endereços antes de poder ligar.  Para configurar a sua firewall para acesso, siga as etapas no acesso à firewall do [servidor Configure para o seu IP do cliente](create-data-warehouse-portal.md) nas [instruções de provisionamento](create-data-warehouse-portal.md). |
| Não pode ligar-se com ferramenta ou controlador                           | O pool SYnapse SQL recomenda a utilização de [SSMS,](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15) [SSDT para Estúdio Visual,](sql-data-warehouse-install-visual-studio.md)ou [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) para consultar os seus dados. Para obter mais informações sobre os condutores e a ligação ao Azure Synapse, consulte [os condutores de Azure Synapse](sql-data-warehouse-connection-strings.md) e [connect to Azure Synapse.](sql-data-warehouse-connect-overview.md) |

## <a name="tools"></a>Ferramentas

| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| O explorador de objetos do Estúdio Visual está a faltar aos utilizadores da AD Azure           | Este é um problema conhecido.  Como uma suposição, veja os utilizadores em [sys.database_principals](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=sql-server-ver15).  Consulte [a Autenticação para Azure Synapse](sql-data-warehouse-authentication.md) para saber mais sobre a utilização do Diretório Ativo Azure com piscina SQL Synapse. |
| A escrita manual, utilizando o assistente de scripts, ou a ligação via SSMS é lenta, não responde ou produz erros | Certifique-se de que os utilizadores foram criados na base de dados principal. Nas opções de script, certifique-se também de que a edição do motor está definida como "Microsoft Azure SQL Data Warehouse Edition" e o tipo de motor é "Microsoft Azure SQL Database". |
| Gerar scripts falha no SSMS                               | Gerar um guião para piscina Synapse SQL falha se a opção "Gerar script para objetos dependentes" for definida para "True". Como uma suposição, os utilizadores devem ir manualmente a **Tools -> Options ->SQL Server Object Explorer -> Gerar script para opções dependentes e definir para falsos** |

## <a name="performance"></a>Desempenho

| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Resolução de problemas de desempenho de consulta                            | Se está a tentar resolver uma consulta específica, comece por [Aprender a monitorizar as suas consultas](sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| Problemas espaciais tempDB | Monitorize o uso do espaço [TempDB.](sql-data-warehouse-manage-monitor.md#monitor-tempdb)  As causas comuns para o escoação do espaço TempDB são:<br>- Não foram suficientes os recursos atribuídos à consulta que faz com que os dados sejam derramados para tempDB.  Ver [Gestão de Carga de Trabalho](resource-classes-for-workload-management.md) <br>- Faltam estatísticas ou desatualizadas, causando movimentos excessivos de dados.  Consulte a Manutenção de estatísticas de [tabelas](sql-data-warehouse-tables-statistics.md) para detalhes sobre como criar estatísticas<br>- O espaço TempDB é atribuído por nível de serviço.  [Escalar a sua piscina SQL](sql-data-warehouse-manage-compute-overview.md#scaling-compute) para uma definição de DWU mais alta atribui mais espaço TempDB.|
| O fraco desempenho da consulta e os planos muitas vezes são resultado de estatísticas em falta | A causa mais comum de mau desempenho é a falta de estatísticas nas suas tabelas.  Consulte a Manutenção de estatísticas de [tabelas](sql-data-warehouse-tables-statistics.md) para obter detalhes sobre como criar estatísticas e por que são fundamentais para o seu desempenho. |
| Baixa conmoeda / consultas em fila                             | Compreender a [gestão da carga de trabalho](resource-classes-for-workload-management.md) é importante para entender como equilibrar a atribuição de memória com moeda. |
| Como implementar as melhores práticas                              | O melhor lugar para começar a aprender formas de melhorar o desempenho da consulta é o artigo [de boas práticas da Piscina SYnapse SQL.](sql-data-warehouse-best-practices.md) |
| Como melhorar o desempenho com escala                      | Por vezes, a solução para melhorar o desempenho é simplesmente adicionar mais poder de computação às suas consultas, [dimensionando a sua piscina SQL](sql-data-warehouse-manage-compute-overview.md). |
| Mau desempenho de consulta como resultado da má qualidade do índice     | Algumas vezes as consultas podem abrandar devido à má qualidade do índice da [coluna.](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)  Consulte este artigo para obter mais informações e como [reconstruir índices para melhorar a qualidade](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)do segmento. |

## <a name="system-management"></a>Gestão do sistema

| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: Não conseguiu executar a operação porque o servidor excederia a quota permitida da Unidade de Transações de Base de Dados de 45000. | Ou reduza o [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) da base de dados que está a tentar criar ou [solicite um aumento](sql-data-warehouse-get-started-create-support-ticket.md)de quota . |
| Investigar a utilização do espaço                              | Consulte os [tamanhos](sql-data-warehouse-tables-overview.md#table-size-queries) da tabela para entender a utilização do espaço do seu sistema. |
| Ajuda na gestão de tabelas                                    | Consulte o artigo de [visão geral](sql-data-warehouse-tables-overview.md) da Tabela para obter ajuda na gestão das suas tabelas.  Este artigo também inclui links para tópicos mais detalhados como tipos de dados de [tabela,](sql-data-warehouse-tables-data-types.md) [distribuição de uma tabela,](sql-data-warehouse-tables-distribute.md) [Indexação de uma tabela,](sql-data-warehouse-tables-index.md) [divisão de uma tabela,](sql-data-warehouse-tables-partition.md)manutenção de estatísticas de [tabelas](sql-data-warehouse-tables-statistics.md) e [tabelas temporárias.](sql-data-warehouse-tables-temporary.md) |
| A barra de progresso transparente de encriptação de dados (TDE) não está a ser atualizada no portal Azure | Pode ver o estado do TDE através [da powershell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |

## <a name="differences-from-sql-database"></a>Diferenças da Base de Dados SQL

| Problema                                 | Resolução                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Características da Base de Dados SQL não suportadas     | Ver características de [tabela não suportadas](sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Tipos de dados SQL não suportados   | Ver tipos de [dados não suportados](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| Eliminar e atualizar limitações         | Consulte [as sintetidades ATUALIZADAS,](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements) [ELIMINE as suposições](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements) e utilize ctas para trabalhar em torno de [atualizações não suportadas e sintaxe DELETE](sql-data-warehouse-develop-ctas.md). |
| A declaração da MERGE não é apoiada      | Ver [suposições de fusão](sql-data-warehouse-develop-ctas.md#replace-merge-statements).                  |
| Limitações de procedimento armazenadas          | Consulte [as limitações de procedimento armazenadas](sql-data-warehouse-develop-stored-procedures.md#limitations) para compreender algumas das limitações dos procedimentos armazenados. |
| Os UDFs não suportam declarações SELECT | Esta é uma limitação atual dos nossos UDFs.  Consulte a [FUNÇÃO CREATE](https://docs.microsoft.com/sql/t-sql/statements/create-function-sql-data-warehouse?view=aps-pdw-2016-au7) para a sintaxe que apoiamos. |

## <a name="next-steps"></a>Passos seguintes

Para mais ajuda na procura de solução para o seu problema, aqui estão outros recursos que pode tentar.

* [Blogues](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Pedidos de funcionalidades](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Criar pedido de suporte](sql-data-warehouse-get-started-create-support-ticket.md)
* [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse)
* [Fórum do Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
