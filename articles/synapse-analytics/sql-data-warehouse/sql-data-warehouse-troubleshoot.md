---
title: Resolução de problemas piscina SQL dedicada (anteriormente SQL DW)
description: Resolução de problemas piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: gaursa
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 949a89dd4e84bdb44ad15eaff9ca160fdd21b395
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602133"
---
# <a name="troubleshooting-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Resolução de problemas piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics

Este artigo lista problemas comuns de resolução de problemas em piscina sql dedicada (anteriormente SQL DW) em Azure Synapse Analytics.

## <a name="connecting"></a>Ligação

| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| falha no início de sessão do utilizador "NT AUTHORITY\ANONYMOUS LOGON". (Microsoft SQL Server, Error: 18456) | Este erro ocorre quando um utilizador AZure AD tenta ligar-se à base de dados principal, mas não tem um utilizador em mestre.  Para corrigir este problema, especifique a piscina SQL dedicada (anteriormente SQL DW) a que pretende ligar-se na hora da ligação ou adicione o utilizador à base de dados principal.  Consulte o artigo [visão geral da segurança](sql-data-warehouse-overview-manage-security.md) para mais detalhes. |
| o principal do servidor "MyUserName" não consegue aceder à base de dados "mestra" no contexto de segurança atual. Não é possível abrir a base de dados predefinida do utilizador. O início de sessão falhou. O início de sessão falhou para o utilizador"MyUserName". (Microsoft SQL Server, Error: 916) | Este erro ocorre quando um utilizador AZure AD tenta ligar-se à base de dados principal, mas não tem um utilizador em mestre.  Para corrigir este problema, especifique a piscina SQL dedicada (anteriormente SQL DW) a que pretende ligar-se na hora da ligação ou adicione o utilizador à base de dados principal.  Consulte o artigo [visão geral da segurança](sql-data-warehouse-overview-manage-security.md) para mais detalhes. |
| Erro do CTAIP                                                  | Este erro pode ocorrer quando um login foi criado na base de dados principal sql Database, mas não na base de dados SQL específica.  Se encontrar este erro, veja o artigo [Visão geral](sql-data-warehouse-overview-manage-security.md) de Segurança.  Este artigo explica como criar um login e um utilizador na base de dados principal e, em seguida, como criar um utilizador numa base de dados SQL. |
| Bloqueado pela Firewall                                          | O pool SQL dedicado (anteriormente SQL DW) está protegido por firewalls para garantir que apenas endereços IP conhecidos tenham acesso a uma base de dados. As firewalls são seguras por padrão, o que significa que deve ativar explicitamente e endereço IP ou intervalo de endereços antes de poder ligar.  Para configurar a sua firewall para acesso, siga os passos no acesso à firewall do [servidor Configure para](create-data-warehouse-portal.md) o seu cliente IP nas [instruções de Provisionamento](create-data-warehouse-portal.md). |
| Não é possível ligar-se com a ferramenta ou o condutor                           | A piscina de SQL dedicada (anteriormente SQL DW) recomenda a utilização [de SSMS,](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [SSDT para Visual Studio,](sql-data-warehouse-install-visual-studio.md)ou [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) para consultar os seus dados. Para obter mais informações sobre os condutores e ligar-se à Azure Synapse, consulte [os artigos drivers for Azure Synapse](sql-data-warehouse-connection-strings.md) e [Connect to Azure Synapse.](sql-data-warehouse-connect-overview.md) |

## <a name="tools"></a>Ferramentas

| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Explorador de objetos do Estúdio Visual está desaparecido utilizadores de Azure AD           | Este é um problema conhecido.  Como solução alternativa, veja os utilizadores em [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Consulte [a Autenticação para Azure Synapse](sql-data-warehouse-authentication.md) para saber mais sobre a utilização do Azure Ative Directory com piscina SQL dedicada (anteriormente SQL DW). |
| A scriptagem manual, utilizando o assistente de script, ou ligando via SSMS é lenta, não responde ou produz erros | Certifique-se de que os utilizadores foram criados na base de dados principal. Nas opções de script, certifique-se também de que a edição do motor está definida como "Microsoft Azure Synapse Analytics Edition" e o tipo de motor é "Microsoft Azure SQL Database". |
| Gerar scripts falha em SSMS                               | Gerar um script para piscina SQL dedicada (anteriormente SQL DW) falha se a opção "Gerar script para objetos dependentes" for definida como "Verdadeiro". Como uma solução alternativa, os utilizadores devem ir manualmente para **Ferramentas -> Opções ->SQL Server Object Explorer -> Gerar script para opções dependentes e definido para falso** |

## <a name="data-ingestion-and-preparation"></a>Ingestão e preparação de dados

| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| A exportação de cordas vazias utilizando o CETAS resultará em valores NULOS nos ficheiros Parquet e ORC. Note que se estiver a exportar cordas vazias de colunas com restrições NÃO NULAS, o CETAS resultará em registos rejeitados e a exportação pode potencialmente falhar. | Remova as cordas vazias ou a coluna ofensiva na declaração SELECT do seu CETAS. |
| O carregamento de um valor fora do intervalo de 0-127 numa coluna minúscula para o formato de ficheiro Parquet e ORC não é suportado. | Especifique um tipo de dados maior para a coluna-alvo.           |

## <a name="performance"></a>Desempenho

| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Resolução de problemas de desempenho de consulta                            | Se estiver a tentar resolver problemas numa determinada consulta, comece por [aprender a monitorizar as suas consultas](sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| Problemas espaciais TempDB | Monitorar a utilização do espaço [TempDB.](sql-data-warehouse-manage-monitor.md#monitor-tempdb)  Causas comuns para ficar sem espaço tempDB são:<br>- Não são suficientes recursos atribuídos à consulta que faz com que os dados derramem para o TempDB.  Ver [gestão da carga de trabalho](resource-classes-for-workload-management.md) <br>- Faltam estatísticas ou desatualizados que causam movimentos excessivos de dados.  Ver [Manter estatísticas de tabelas](sql-data-warehouse-tables-statistics.md) para detalhes sobre como criar estatísticas<br>- O espaço TempDB é atribuído por nível de serviço.  [Escalar a sua piscina DE SQL dedicada (anteriormente SQL DW)](sql-data-warehouse-manage-compute-overview.md#scaling-compute) para uma definição DWU mais alta aloca mais espaço TempDB.|
| Mau desempenho de consulta e planos muitas vezes é o resultado de estatísticas em falta | A causa mais comum de mau desempenho é a falta de estatísticas nas tabelas.  Consulte [a Manutenção de estatísticas de tabelas](sql-data-warehouse-tables-statistics.md) para obter detalhes sobre como criar estatísticas e por que razão são fundamentais para o seu desempenho. |
| Baixa concordância / consultas em questão                             | Compreender [a gestão da carga de trabalho](resource-classes-for-workload-management.md) é importante para entender como equilibrar a atribuição de memória com concordância. |
| Como implementar as melhores práticas                              | O melhor lugar para começar a aprender formas de melhorar o desempenho da consulta é o artigo [de boas práticas dedicadas à piscina SQL (anteriormente SQL DW).](sql-data-warehouse-best-practices.md) |
| Como melhorar o desempenho com escala                      | Por vezes, a solução para melhorar o desempenho é simplesmente adicionar mais poder de computação às suas [consultas, escalando a sua piscina DE SQL dedicada (anteriormente SQL DW)](sql-data-warehouse-manage-compute-overview.md). |
| Mau desempenho da consulta em resultado da má qualidade do índice     | Algumas vezes as consultas podem abrandar devido à qualidade do índice de [colunas pobres.](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)  Consulte este artigo para obter mais informações e como [reconstruir índices para melhorar a qualidade do segmento.](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality) |

## <a name="system-management"></a>Gestão do sistema

| Problema                                                        | Resolução                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: Não foi possível efetuar a operação porque o servidor excederia a quota permitida da Unidade de Transação de Bases de Dados de 45000. | Ou reduz a [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) da base de dados que está a tentar criar ou [solicitar um aumento de quota.](sql-data-warehouse-get-started-create-support-ticket.md) |
| Investigação da utilização do espaço                              | Consulte [os tamanhos da tabela](sql-data-warehouse-tables-overview.md#table-size-queries) para entender a utilização do espaço do seu sistema. |
| Ajudar na gestão de tabelas                                    | Consulte o artigo [de visão geral](sql-data-warehouse-tables-overview.md) da Tabela para obter ajuda na gestão das suas tabelas.  Este artigo também inclui ligações a tópicos mais detalhados como [tipos de dados de tabela,](sql-data-warehouse-tables-data-types.md) [Distribuição de uma tabela,](sql-data-warehouse-tables-distribute.md) [Indexação de uma tabela,](sql-data-warehouse-tables-index.md) [Partição de uma tabela,](sql-data-warehouse-tables-partition.md) [Manutenção de estatísticas de tabelas](sql-data-warehouse-tables-statistics.md) e [tabelas temporárias.](sql-data-warehouse-tables-temporary.md) |
| A barra de avanço transparente de encriptação de dados (TDE) não está a ser atualizada no portal Azure | Pode ver o estado do TDE via [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |

## <a name="differences-from-sql-database"></a>Diferenças da Base de Dados SQL

| Problema                                 | Resolução                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Funcionalidades de base de dados SQL não suportadas     | Consulte [as características da tabela não suportada](sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Tipos de dados de base de dados SQL não suportados   | Consulte [os tipos de dados não suportados.](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types)        |
| Limitações de procedimentos armazenados          | Consulte [as limitações do procedimento armazenadas](sql-data-warehouse-develop-stored-procedures.md#limitations) para compreender algumas das limitações dos procedimentos armazenados. |
| Os UDFs não suportam declarações SELECT | Esta é uma limitação atual dos nossos UDFs.  Consulte [a função CREATE](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para a sintaxe que suportamos. |
| sp_rename (pré-visualização) para colunas não funciona em esquemas fora do *dbo* | Esta é uma limitação atual da Sinapse [sp_rename (pré-visualização) para colunas](/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Colunas em objetos que não fazem parte do esquema *dbo* podem ser renomeadas através de um CTAS para uma nova tabela. |

## <a name="next-steps"></a>Passos seguintes

Para mais ajuda na procura de solução para o seu problema, aqui estão alguns outros recursos que pode tentar.

* [Blogues](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Pedidos de funcionalidades](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Criar pedido de suporte](sql-data-warehouse-get-started-create-support-ticket.md)
* [Microsoft Q&Uma página de perguntas](/answers/topics/azure-synapse-analytics.html)
* [Fórum de Transbordamento de Pilhas](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)