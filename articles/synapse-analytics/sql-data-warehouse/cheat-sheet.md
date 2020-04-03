---
title: Folha de batota para Azure Synapse Analytics (anteriormente SQL DW)
description: Encontre links e boas práticas para construir rapidamente as suas soluções Azure Synapse Analytics (anteriormente SQL DW).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 8e0515727c2155b91f18398bd9def700f4a15b34
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619416"
---
# <a name="cheat-sheet-for-azure-synapse-analytics-formerly-sql-dw"></a>Folha de batota para Azure Synapse Analytics (anteriormente SQL DW)

Esta folha de batota fornece dicas úteis e boas práticas para a construção de soluções Azure Synapse. 

O gráfico seguinte mostra o processo de estruturação de um armazém de dados:

![Desenho](./media/cheat-sheet/picture-flow.png)

## <a name="queries-and-operations-across-tables"></a>Consultas e operações em tabelas

Se já souber de antemão as principais operações e consultas que vão ser executadas no seu armazém de dados, pode priorizar a arquitetura do mesmo para essas operações. Estas operações e consultas poderão incluir:
* Associar uma ou duas tabelas de factos com tabelas de dimensão, filtrar a tabela combinada e, em seguida, anexar os resultados a um data mart.
* Fazer atualizações grandes ou pequenas às tabelas de factos.
* Anexar apenas dados às tabelas.

Saber os tipos de operações antecipadamente ajuda-o a otimizar o design das tabelas.

## <a name="data-migration"></a>Migração de dados

Em primeiro lugar, carregue os seus dados no Armazenamento do [Lago Azure Data](../../data-factory/connector-azure-data-lake-store.md) ou no Armazenamento de Blob Azure. Em seguida, utilize a PolyBase para carregar os seus dados em mesas de preparação. Utilize a seguinte configuração:

| Design | Recomendação |
|:--- |:--- |
| Distribuição | Round Robin |
| Indexação | Área dinâmica para dados |
| Criação de partições | Nenhuma |
| Classe de Recursos | largerc ou xlargerc |

Saiba mais sobre a [migração de dados](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-data-to-azure-sql-data-warehouse-in-practice/), o [carregamento de dados](design-elt-data-loading.md) e o [processo de Extração, Carregamento e Transformação (ELT)](design-elt-data-loading.md). 

## <a name="distributed-or-replicated-tables"></a>Tabelas distribuídas ou replicadas

Utilize as seguintes estratégias, consoante as propriedades da tabela:

| Tipo | Excelente opção para...| Tenha atenção se...|
|:--- |:--- |:--- |
| Replicada | * Pequenas tabelas de dimensão num esquema estelar com menos de 2 GB de armazenamento após compressão (~5x compressão) |* Muitas transações de escrita estão em cima da mesa (tais como inserir, serrantes, excluir, atualizar)<br></br>* Altera frequentemente o fornecimento de Unidades de Armazém de Dados (DWU)<br></br>* Só se usam 2-3 colunas, mas a sua mesa tem muitas colunas<br></br>* Você indexa uma tabela replicada |
| Round Robin (predefinição) | * Mesa temporária/de encenação<br></br> * Nenhuma chave de junção óbvia ou boa coluna de candidatos |* O desempenho é lento devido ao movimento de dados |
| Hash | * Tabelas de factos<br></br>* Grandes tabelas de dimensão |* A chave de distribuição não pode ser atualizada |

**Dicas:**
* Comece com Round Robin, mas aspire a chegar à estratégia de distribuição de hash para tirar partido de uma arquitetura paralela em escala.
* Certifique-se de que as chaves de hash comuns têm o mesmo formato de dados.
* Não distribua no formato varchar.
* As tabelas de dimensões com chaves de hash comuns para tabelas de factos com operações de associação frequentes podem ser distribuídas com hash.
* Utilize *[sys.dm_pdw_nodes_db_partition_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql)* para analisar eventuais assimetrias nos dados.
* Utilize *[sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)* para analisar movimentos de dados por detrás de consultas e monitorizar a duração das operações de difusão e ordem. Esta opção é útil para rever a estratégia de distribuição.

Saiba mais sobre as [tabelas replicadas](design-guidance-for-replicated-tables.md) e as [tabelas distribuídas](sql-data-warehouse-tables-distribute.md).

## <a name="index-your-table"></a>Indexar as tabelas

A indexação é útil para ler as tabelas rapidamente. Com base nas suas necessidades, pode utilizar um conjunto único de tecnologias:

| Tipo | Excelente opção para... | Tenha atenção se...|
|:--- |:--- |:--- |
| Área dinâmica para dados | * Mesa de encenação/temporária<br></br>* Pequenas mesas com pequenas mesas |* Qualquer olhar para a mesa completa |
| Índice em cluster | * Tabelas com até 100 milhões de filas<br></br>* Mesas grandes (mais de 100 milhões de linhas) com apenas 1-2 colunas fortemente utilizadas |* Usado numa mesa replicada<br></br>* Você tem consultas complexas envolvendo múltiplas operações de adesão e grupo por operações<br></br>* Faz atualizações nas colunas indexadas: requer memória |
| Índice columnstore em cluster (CCI) (predefinição) | * Mesas grandes (mais de 100 milhões de filas) | * Usado numa mesa replicada<br></br>* Você faz operações de atualização massiva na sua mesa<br></br>* Você sobrepartia a sua mesa: grupos de remo não se estendem por diferentes nódeos de distribuição e divisórias |

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

Recomendamos a utilização da PolyBase e da ADF V2 para automatizar os seus oleodutos ELT no seu armazém de dados.

Para um grande lote de atualizações nos seus dados históricos, considere utilizar um [CTAS](sql-data-warehouse-develop-ctas.md) para escrever os dados que pretende manter numa tabela em vez de utilizar INSERT, UPDATE e DELETE.

## <a name="maintain-statistics"></a>Manter as estatísticas
 Até que as estatísticas automáticas estejam geralmente disponíveis, é necessária a manutenção manual das estatísticas. É importante atualizar as estatísticas à medida que ocorrem alterações *significativas* nos seus dados. As atualizações ajudam a otimizar os planos de consultas. Se achar que manter todas as estatísticas demora muito tempo, seja mais seletivo quanto às colunas que as têm. 

Também pode definir a frequência das atualizações. Por exemplo, poderá querer atualizar as colunas de data, onde podem ser adicionados novos valores diariamente. Vai beneficiar mais com estatísticas em colunas envolvidas em associações, colunas utilizadas na cláusula WHERE e colunas que se encontram em GROUP BY.

Saiba mais sobre as [estatísticas](sql-data-warehouse-tables-statistics.md).

## <a name="resource-class"></a>Classe de recursos
Os grupos de recursos são usados como forma de alocar memória a consultas. Se precisar de mais memória para melhorar a velocidade das consultas ou do carregamento, deve alocar mais classes de recursos. Como contrapartida, a utilização de classes de recursos maiores afeta a simultaneidade. É preciso ter este facto em conta antes de mudar todos os utilizadores para uma classe de recursos grande.

Se reparar em que as consultas demoram demasiado tempo, confirme se os utilizadores não são executados em classes de recursos grandes. Estas classes consomem muitos blocos de simultaneidade e podem fazer com que outras consultas sejam colocadas em fila.

Finalmente, usando a Gen2 de [piscina SQL,](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse)cada classe de recursos recebe 2,5 vezes mais memória do que a Gen1.

Saiba mais como trabalhar com [classes de recursos e a simultaneidade](resource-classes-for-workload-management.md).

## <a name="lower-your-cost"></a>Reduza os custos
Uma característica chave do Azure Synapse é a capacidade de [gerir os recursos da computação.](sql-data-warehouse-manage-compute-overview.md) Você pode parar a piscina SQL quando você não está usando, o que impede a faturação de recursos computacionais. Pode dimensionar os recursos para satisfazer as suas necessidades em termos de desempenho. Par pôr em pausa, utilize o [portal do Azure](../../synapse-analytics/sql-data-warehouse/pause-and-resume-compute-portal.md) ou o [PowerShell](../../synapse-analytics/sql-data-warehouse/pause-and-resume-compute-powershell.md). Para dimensionar, utilize o [portal do Azure](quickstart-scale-compute-portal.md), o [Powershell](quickstart-scale-compute-powershell.md), o [T-SQL](quickstart-scale-compute-tsql.md) ou uma [API REST](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).


Uma característica chave do Azure Synapse é a capacidade de [gerir os recursos da computação.](sql-data-warehouse-manage-compute-overview.md) Você pode parar a piscina SQL quando você não está usando, o que impede a faturação de recursos computacionais. Pode dimensionar os recursos para satisfazer as suas necessidades em termos de desempenho. Par pôr em pausa, utilize o [portal do Azure](../../sql-data-warehouse/pause-and-resume-compute-portal.md) ou o [PowerShell](../../sql-data-warehouse/pause-and-resume-compute-powershell.md). Para dimensionar, utilize o [portal do Azure](quickstart-scale-compute-portal.md), o [Powershell](quickstart-scale-compute-powershell.md), o [T-SQL](quickstart-scale-compute-tsql.md) ou uma [API REST](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Agora, dimensione automaticamente sempre que quiser com as Funções do Azure:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-your-architecture-for-performance"></a>Otimizar a arquitetura para desempenho

Recomendamos olhar para a Base de Dados SQL e o Azure Analysis Services numa arquitetura hub-and-spoke. Esta solução pode proporcionar isolamento de cargas de trabalho entre diferentes grupos de utilizadores e utilizar, ao mesmo tempo, funcionalidades avançadas de segurança da Base de Dados SQL e do Azure Analysis Services. Também é uma forma de dar simultaneidade ilimitada aos seus utilizadores.

Saiba mais sobre [arquiteturas típicas que aproveitam o Azure Synapse.](https://blogs.msdn.microsoft.com/sqlcat/20../../common-isv-application-patterns-using-azure-sql-data-warehouse/)

Implemente num clique nos seus porta-vozes nas bases de dados SQL a partir da piscina SQL:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>