---
title: Folha de batota para piscina SQL dedicada (anteriormente SQL DW)
description: Encontre links e boas práticas para construir rapidamente a sua piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: a75e1fb5b250be1004195d3a77301c73eac94b02
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98043562"
---
# <a name="cheat-sheet-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytic"></a>Folha de batota para piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytic

Esta folha de batota fornece dicas úteis e boas práticas para a construção de soluções dedicadas de piscina SQL (anteriormente SQL DW).

O gráfico a seguir mostra o processo de conceção de um armazém de dados com piscina SQL dedicada (anteriormente SQL DW):

![Desenho](./media/cheat-sheet/picture-flow.png)

## <a name="queries-and-operations-across-tables"></a>Consultas e operações em tabelas

Se já souber de antemão as principais operações e consultas que vão ser executadas no seu armazém de dados, pode priorizar a arquitetura do mesmo para essas operações. Estas operações e consultas poderão incluir:

* Associar uma ou duas tabelas de factos com tabelas de dimensão, filtrar a tabela combinada e, em seguida, anexar os resultados a um data mart.
* Fazer atualizações grandes ou pequenas às tabelas de factos.
* Anexar apenas dados às tabelas.

Saber os tipos de operações antecipadamente ajuda-o a otimizar o design das tabelas.

## <a name="data-migration"></a>Migração de dados

Em primeiro lugar, carregue os seus dados no [Azure Data Lake Storage](../../data-factory/connector-azure-data-lake-store.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ou no Azure Blob Storage. Em seguida, utilize a [declaração COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para carregar os seus dados em tabelas de preparação. Utilize a seguinte configuração:

| Design | Recomendação |
|:--- |:--- |
| Distribuição | Round Robin |
| Indexação | Área dinâmica para dados |
| Criação de partições | Nenhum |
| Classe de Recursos | largerc ou xlargerc |

Saiba mais sobre a [migração de dados](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-data-to-azure-sql-data-warehouse-in-practice/), o [carregamento de dados](design-elt-data-loading.md) e o [processo de Extração, Carregamento e Transformação (ELT)](design-elt-data-loading.md).

## <a name="distributed-or-replicated-tables"></a>Tabelas distribuídas ou replicadas

Utilize as seguintes estratégias, consoante as propriedades da tabela:

| Tipo | Excelente opção para...| Tenha atenção se...|
|:--- |:--- |:--- |
| Replicada | * Pequenas tabelas de dimensão num esquema estelar com menos de 2 GB de armazenamento após compressão (compressão~5x) |* Muitas transações de escrita estão em cima da mesa (tais como inserir, aumentar, apagar, atualizar)<br></br>* Altera o fornecimento de Unidades de Armazém de Dados (DWU) com frequência<br></br>* Só usas colunas 2-3, mas a tua mesa tem muitas colunas<br></br>* Você indexa uma tabela replicada |
| Round Robin (predefinição) | * Tabela temporária/de encenação<br></br> * Nenhuma chave de junção óbvia ou uma boa coluna de candidatos |* O desempenho é lento devido ao movimento de dados |
| Hash | * Tabelas de factos<br></br>* Mesas de grande dimensão |* A chave de distribuição não pode ser atualizada |

**Dicas:**

* Comece com Round Robin, mas aspire a chegar à estratégia de distribuição de hash para tirar partido de uma arquitetura paralela em escala.
* Certifique-se de que as chaves de hash comuns têm o mesmo formato de dados.
* Não distribua no formato varchar.
* As tabelas de dimensões com chaves de hash comuns para tabelas de factos com operações de associação frequentes podem ser distribuídas com hash.
* Utilize *[sys.dm_pdw_nodes_db_partition_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)* para analisar eventuais assimetrias nos dados.
* Utilize *[sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)* para analisar os movimentos de dados por trás de consultas, monitorizar a transmissão do tempo e baralhar as operações. Esta opção é útil para rever a estratégia de distribuição.

Saiba mais sobre as [tabelas replicadas](design-guidance-for-replicated-tables.md) e as [tabelas distribuídas](sql-data-warehouse-tables-distribute.md).

## <a name="index-your-table"></a>Indexar as tabelas

A indexação é útil para ler as tabelas rapidamente. Com base nas suas necessidades, pode utilizar um conjunto único de tecnologias:

| Tipo | Excelente opção para... | Tenha atenção se...|
|:--- |:--- |:--- |
| Área dinâmica para dados | * Encenação/tabela temporária<br></br>* Pequenas mesas com pequenas procuras |Qualquer procura digitaliza a mesa completa |
| Índice em cluster | * Tabelas com até 100 milhões de filas<br></br>* Mesas grandes (mais de 100 milhões de filas) com apenas 1-2 colunas fortemente utilizadas |* Usado numa mesa replicada<br></br>* Você tem consultas complexas envolvendo múltiplas adere e operações do Grupo By<br></br>* Faz atualizações nas colunas indexadas: é preciso memória |
| Índice columnstore em cluster (CCI) (predefinição) | * Mesas grandes (mais de 100 milhões de filas) | * Usado numa mesa replicada<br></br>* Você faz operações de atualização massivas na sua mesa<br></br>* Você sobrepartiu a sua tabela: os grupos de linha não se estendem por diferentes nosmos de distribuição e divisórias |

**Dicas:**

* A partir de um índice em cluster, poderá ser útil adicionar um índice não em cluster a uma coluna utilizada muitas vezes para filtragem.
* Tenha cuidado com a forma como gere a memória em tabelas com CCI. Quando carrega dados, é importante que o utilizador (ou a consulta) beneficie de uma grande classe de recursos. Certifique-se de que evita cortar e criar vários grupos pequenos de linhas comprimidas.
* No Gen2, as tabelas CCI são colocadas em cache localmente nos nós de computação para maximizar o desempenho.
* Em CCI, a compressão incorreta dos grupos de linhas pode provocar um desempenho lento. Se tal ocorrer, reconstrua ou reorganize o CCI. Recomenda-se, pelo menos, 100 000 linhas por grupo de linhas comprimido. O ideal é 1 milhão de linhas num grupo de linhas.
* Com base no tamanho e na frequência de carga incremental, é útil automatizar a reorganização ou a recriação dos índices. A limpeza minuciosa é sempre útil.
* Corte os grupos de linhas de forma estratégica. Até que ponto os grupos de linhas abertos são grandes? Quantos dados espera carregar nos próximos dias?

Saiba mais sobre os [índices](sql-data-warehouse-tables-index.md).

## <a name="partitioning"></a>Criação de partições

Pode criar partições da tabela se tiver uma tabela de factos grande (mais de mil milhões de linhas). Em 99 por cento dos casos, a chave de partição deve basear-se numa data. Tenha cuidado para não criar demasiadas partições, especialmente se tiver um índice columnstore em cluster.

Com as tabelas de teste que requerem ELT, pode tirar partido das partições. Facilitam a gestão do ciclo de vida dos dados.
Tenha atenção para não criar demasiadas partições dos dados, especialmente em índices columnstore em cluster.

Saiba mais sobre as [partições](sql-data-warehouse-tables-partition.md).

## <a name="incremental-load"></a>Carregamento incremental

Se pretender carregar os seus dados de forma incremental, comece por garantir que aloca mais classes de recursos ao carregamento.  Isto é particularmente importante quando se carrega em tabelas com índices de lojas de colunas agrupadas.  Consulte [as aulas de recursos](resource-classes-for-workload-management.md) para mais detalhes.  

Recomendamos a utilização da PolyBase e da ADF V2 para automatizar os seus gasodutos ELT no seu armazém de dados.

Para um grande lote de atualizações nos seus dados históricos, considere utilizar um [CTAS](sql-data-warehouse-develop-ctas.md) para escrever os dados que pretende manter numa tabela em vez de utilizar INSERT, UPDATE e DELETE.

## <a name="maintain-statistics"></a>Manter as estatísticas

É importante atualizar as estatísticas à medida que ocorrem alterações *significativas* nos seus dados. Consulte [as estatísticas de atualização](sql-data-warehouse-tables-statistics.md#update-statistics) para determinar se ocorreram alterações *significativas.* As estatísticas atualizadas otimizam os seus planos de consulta. Se achar que manter todas as estatísticas demora muito tempo, seja mais seletivo quanto às colunas que as têm.

Também pode definir a frequência das atualizações. Por exemplo, poderá querer atualizar as colunas de data, onde podem ser adicionados novos valores diariamente. Vai beneficiar mais com estatísticas em colunas envolvidas em associações, colunas utilizadas na cláusula WHERE e colunas que se encontram em GROUP BY.

Saiba mais sobre as [estatísticas](sql-data-warehouse-tables-statistics.md).

## <a name="resource-class"></a>Classe de recursos

Os grupos de recursos são usados como forma de alocar a memória a consultas. Se precisar de mais memória para melhorar a velocidade das consultas ou do carregamento, deve alocar mais classes de recursos. Como contrapartida, a utilização de classes de recursos maiores afeta a simultaneidade. É preciso ter este facto em conta antes de mudar todos os utilizadores para uma classe de recursos grande.

Se reparar em que as consultas demoram demasiado tempo, confirme se os utilizadores não são executados em classes de recursos grandes. Estas classes consomem muitos blocos de simultaneidade e podem fazer com que outras consultas sejam colocadas em fila.

Finalmente, ao utilizar a Gen2 de [piscina SQL dedicada (anteriormente SQL DW),](sql-data-warehouse-overview-what-is.md)cada classe de recursos recebe 2,5 vezes mais memória do que a Gen1.

Saiba mais como trabalhar com [classes de recursos e a simultaneidade](resource-classes-for-workload-management.md).

## <a name="lower-your-cost"></a>Reduza os custos

Uma característica chave do Azure Synapse é a capacidade de [gerir recursos computacional](sql-data-warehouse-manage-compute-overview.md). Você pode parar a sua piscina de SQL dedicada (anteriormente SQL DW) quando você não está usando-o, o que impede a faturação de recursos compute. Pode dimensionar os recursos para satisfazer as suas necessidades em termos de desempenho. Par pôr em pausa, utilize o [portal do Azure](pause-and-resume-compute-portal.md) ou o [PowerShell](pause-and-resume-compute-powershell.md). Para escalar, utilize o [portal Azure](quickstart-scale-compute-portal.md), [PowerShell,](quickstart-scale-compute-powershell.md) [T-SQL](quickstart-scale-compute-tsql.md)ou uma [API REST](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Agora, dimensione automaticamente sempre que quiser com as Funções do Azure:

[![Imagem mostrando um botão marcado "Implementar para Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json)

## <a name="optimize-your-architecture-for-performance"></a>Otimizar a arquitetura para desempenho

Recomendamos olhar para a Base de Dados SQL e o Azure Analysis Services numa arquitetura hub-and-spoke. Esta solução pode proporcionar isolamento de cargas de trabalho entre diferentes grupos de utilizadores e utilizar, ao mesmo tempo, funcionalidades avançadas de segurança da Base de Dados SQL e do Azure Analysis Services. Também é uma forma de dar simultaneidade ilimitada aos seus utilizadores.

Saiba mais sobre [arquiteturas típicas que aproveitam a piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics.](https://blogs.msdn.microsoft.com/sqlcat/20../../common-isv-application-patterns-using-azure-sql-data-warehouse/)

Implemente em um clique nos seus porta-vozes em bases de dados SQL a partir de pool SQL dedicado (anteriormente SQL DW):

[![Imagem mostrando um botão marcado "Implementar para Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json)
