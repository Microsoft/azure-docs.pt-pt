---
title: Recomendações de desempenho do consultor de base de dados para bases de dados individuais e reunidas
description: A Base de Dados Azure SQL fornece recomendações para bases de dados individuais e reunidas que podem melhorar o desempenho da consulta na base de dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: f5b0aeec851c8f514492e32792f48e955597ced5
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096576"
---
# <a name="database-advisor-performance-recommendations-for-single-and-pooled-databases"></a>Recomendações de desempenho do Database Advisor para bases de dados individuais e reunidas

A Base de Dados Azure SQL aprende e adapta-se à sua aplicação. Para bases de dados individuais e reunidas, a SQL Database dispõe de uma série de consultores de base de dados que fornecem recomendações personalizadas que lhe permitem maximizar o desempenho. Estes consultores de base de dados avaliam e analisam continuamente o histórico de utilização e fornecem recomendações baseadas em padrões de carga de trabalho que ajudam a melhorar o desempenho.

## <a name="performance-overview"></a>Visão geral do desempenho

A visão geral do desempenho fornece um resumo do desempenho da sua base de dados e ajuda-o com a sintonização de desempenho e resolução de problemas.

![Visão geral de desempenho para base de dados Azure SQL](./media/sql-database-performance/performance-overview-annotated.png)

- O azulejo **das Recomendações** fornece uma desagregação das recomendações de afinação para a sua base de dados (as três principais recomendações são mostradas se houver mais). Clicar neste azulejo leva-o às opções de recomendação de **[Desempenho.](sql-database-advisor-portal.md#viewing-recommendations)**
- O azulejo da **atividade tuning** fornece um resumo das ações de afinação contínuas e completas para a sua base de dados, dando-lhe uma visão rápida do histórico da atividade de afinação. Clicar neste azulejo leva-o à visão completa do histórico de afinação para a sua base de dados.
- O azulejo **de afinação automática** mostra a **[configuração de afinação automática](sql-database-automatic-tuning-enable.md)** para a sua base de dados (opções de afinação que são automaticamente aplicadas à sua base de dados). Clicar neste azulejo abre o diálogo de configuração da automatização.
- As **consultas de base** de dados mostram o resumo do desempenho da consulta para a sua base de dados (utilização geral do DTU e consultas de consumo de recursos superiores). Clicar neste azulejo leva-o à **[Consulta Performance Insight](sql-database-query-performance.md)**.

## <a name="performance-recommendation-options"></a>Opções de recomendação de desempenho

As opções de recomendação de desempenho disponíveis para bases de dados individuais e reunidas na Base de Dados Azure SQL são:

| Recomendação de desempenho | Base de dados única e suporte de base de dados agréis | Suporte de base de dados de instâncias |
| :----------------------------- | ----- | ----- |
| **Crie recomendações** de índice - Recomenda a criação de índices que possam melhorar o desempenho da sua carga de trabalho. | Sim | Não |
| **Recomendações** do índice de queda - Recomenda a remoção de índices redundantes e duplicados diariamente, com exceção de índices únicos, e índices que não foram utilizados durante muito tempo (>90 dias). Por favor, note que esta opção não é compatível com aplicações que usam comutação de divisórias e dicas de índice. A queda de índices não utilizados não é suportada para os níveis de serviço Premium e Business Critical. | Sim | Não |
| **Parametrize recomendações de consultas (pré-visualização)** - Recomenda a parametrização forçada nos casos em que tem uma ou mais consultas que estão constantemente a ser recompiladas, mas acabam com o mesmo plano de execução de consultas. | Sim | Não |
| **Corrigir recomendações de problemas de esquema (pré-visualização)** - As recomendações para a correção do esquema surgem quando o serviço de base de dados SQL nota uma anomalia no número de erros SQL relacionados com o esquema que estão a acontecer na sua base de dados SQL. A Microsoft está neste momento a depreciar as recomendações de "Corrigir o problema do esquema". | Sim | Não |

![Recomendações de desempenho para base de dados Azure SQL](./media/sql-database-performance/performance-recommendations-annotated.png)

Para aplicar recomendações de desempenho, consulte recomendações de [aplicação](sql-database-advisor-portal.md#applying-recommendations). Para ver o estado das recomendações, consulte as operações de [monitorização.](sql-database-advisor-portal.md#monitoring-operations)

Você também pode encontrar um histórico completo de ações de afinação que foram aplicadas no passado.

## <a name="create-index-recommendations"></a>Criar recomendações de índice

A Base de Dados Azure SQL monitoriza continuamente as consultas que estão a decorrer e identifica os índices que podem melhorar o desempenho. Depois de haver confiança suficiente de que um determinado índice está em falta, é criada uma nova recomendação do **índice Create.**

A Base de Dados Azure SQL constrói confiança estimando o ganho de desempenho que o índice traria através do tempo. Dependendo do ganho estimado de desempenho, as recomendações são classificadas como altas, médias ou baixas.

Os índices que são criados através de recomendações são sempre sinalizados como índices criados automaticamente. Pode ver quais os índices que são auto-criados olhando para a [vista sys.indexes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql). Os índices criados automaticamente não bloqueiam comandos ALTER/RENAME.

Se tentar largar a coluna que tem um índice criado automaticamente sobre ela, o comando passa. O índice auto-criado também é retirado com o comando. Os índices regulares bloqueiam o comando ALTER/RENAME nas colunas indexadas.

Após a aplicação da recomendação do índice de criação, a Base de Dados Azure SQL compara o desempenho das consultas com o desempenho da linha de base. Se o novo índice melhorar o desempenho, a recomendação é assinalada como bem sucedida e o relatório de impacto está disponível. Se o índice não melhorou o desempenho, é automaticamente revertido. A Base de Dados Azure SQL utiliza este processo para garantir que as recomendações melhorem o desempenho da base de dados.

Qualquer recomendação de **índice de criação** tem uma política de back-off que não permite aplicar a recomendação se o uso de recursos de uma base de dados ou piscina é elevado. A política de back-off tem em conta CPU, Data IO, Log IO e armazenamento disponível.

Se o CPU, os dados IO, ou o log IO for superior a 80% nos 30 minutos anteriores, a recomendação do índice de criação é adiada. Se o armazenamento disponível for inferior a 10% após a criação do índice, a recomendação entra num estado de erro. Se, passados alguns dias, a afinação automática ainda acredita que o índice seria benéfico, o processo recomeça.

Este processo repete-se até que haja armazenamento disponível suficiente para criar um índice, ou até que o índice não seja mais visto como benéfico.

## <a name="drop-index-recommendations"></a>Recomendações do índice de queda

Além de detetar índices em falta, a Base de Dados Azure SQL analisa continuamente o desempenho dos índices existentes. Se não for utilizado um índice, a Base de Dados Azure SQL recomenda que o abandone. A queda de um índice é recomendada em dois casos:

- O índice é uma duplicação de outro índice (coluna indexada e incluída, esquema de partição e filtros).
- O índice não é utilizado durante um período prolongado (93 dias).

As recomendações do índice de queda também passam pela verificação após a implementação. Se o desempenho melhorar, o relatório de impacto está disponível. Se o desempenho se degradar, a recomendação é revertida.

## <a name="parameterize-queries-recommendations-preview"></a>Parametrizar recomendações de consultas (pré-visualização)

*As recomendações de parametrização* aparecem quando se tem uma ou mais consultas que estão constantemente a ser recompiladas, mas acabam com o mesmo plano de execução de consultas. Esta condição cria uma oportunidade de aplicar a parametrização forçada. A parametrização forçada, por sua vez, permite que os planos de consulta sejam em cache e reutilizados no futuro, o que melhora o desempenho e reduz o uso de recursos.

Todas as consultas emitidas contra o SQL Server precisam inicialmente de ser compiladas para gerar um plano de execução. Cada plano gerado é adicionado à cache do plano. As execuções subsequentes da mesma consulta podem reutilizar este plano a partir da cache, o que elimina a necessidade de compilação adicional.

Consultas com valores não parametrizados podem levar a sobrecarga de desempenho porque o plano de execução é recompilado cada vez que os valores não parametrizados são diferentes. Em muitos casos, as mesmas consultas com diferentes valores de parâmetros geram os mesmos planos de execução. Estes planos, no entanto, ainda são adicionados separadamente à cache do plano.

O processo de recompilação dos planos de execução utiliza recursos de base de dados, aumenta o tempo de duração da consulta e transborda a cache do plano. Estes eventos, por sua vez, fazem com que os planos sejam despejados da cache. Este comportamento do Servidor SQL pode ser alterado definindo a opção de parametrização forçada na base de dados.

Para ajudá-lo a estimar o impacto desta recomendação, é-lhe fornecida uma comparação entre o uso real do CPU e a utilização projetada do CPU (como se a recomendação fosse aplicada). Esta recomendação pode ajudá-lo a obter poupanças de CPU. Também pode ajudá-lo a diminuir a duração da consulta e a sobrecarga para a cache do plano, o que significa que mais dos planos podem permanecer na cache e ser reutilizados. Pode aplicar esta recomendação rapidamente selecionando o comando **Aplicar.**

Depois de aplicar esta recomendação, permite a parametrização forçada em poucos minutos na sua base de dados. Inicia o processo de monitorização, que dura aproximadamente 24 horas. Após este período, pode ver o relatório de validação. Este relatório mostra o uso da CPU da sua base de dados 24 horas antes e depois de a recomendação ter sido aplicada. O SQL Database Advisor tem um mecanismo de segurança que reverte automaticamente a recomendação aplicada se tiver sido detetada a regressão do desempenho.

## <a name="fix-schema-issues-recommendations-preview"></a>Corrigir recomendações de problemas de esquema (pré-visualização)

> [!IMPORTANT]
> A Microsoft está neste momento a depreciar as recomendações de "Corrigir o problema do esquema". Recomendamos que utilize [Insights Inteligentes](sql-database-intelligent-insights.md) para monitorizar os problemas de desempenho da sua base de dados, incluindo questões de esquemas que as recomendações "Corrigir esquemas" anteriormente abrangidas.

Corrigir recomendações de **problemas de esquemas** surgem quando o serviço de base de dados Azure SQL nota uma anomalia no número de erros SQL relacionados com o esquema que estão a ocorrer na sua base de dados SQL. Esta recomendação aparece tipicamente quando a sua base de dados encontra múltiplos erros relacionados com esquemas (nome de coluna inválido, nome de objeto inválido, e assim por diante) dentro de uma hora.

"Problemas de schema" são uma classe de erros de sintaxe no SQL Server. Ocorrem quando a definição da consulta SQL e a definição do esquema de base de dados não estão alinhadas. Por exemplo, uma das colunas que é esperada pela consulta pode estar faltando na tabela alvo ou vice-versa.

A recomendação "Corrigir problema sema" surge quando o serviço de base de dados Azure SQL nota uma anomalia no número de erros SQL relacionados com o esquema que estão a ocorrer na sua base de dados SQL. O quadro seguinte mostra os erros relacionados com questões de esquema:

| Código de erro SQL | Mensagem |
| --- | --- |
| 201 |Procedimento ou função *' espera parâmetro ''* que não foi fornecido. |
| 207 |Nome de coluna inválido '*'. |
| 208 |Nome de objeto inválido '*'. |
| 213 |O nome da coluna ou o número dos valores fornecidos não coincidecom a definição da tabela. |
| 2812 |Não consegui encontrar o procedimento armazenado '*'. |
| 8144 |Procedimento ou função * tem demasiados argumentos especificados. |

## <a name="custom-applications"></a>Aplicações personalizadas

Os desenvolvedores podem considerar o desenvolvimento de aplicações personalizadas usando recomendações de desempenho para a Base de Dados Azure SQL. Todas as recomendações listadas no portal para uma base de dados podem ser acedidas através da [API Get-AzSqlDatabaseRecommendedAction.](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre a afinação automática de índices de base de dados e planos de execução de consultas, consulte a afinação automática da Base de [Dados Azure SQL](sql-database-automatic-tuning.md).
- Para obter mais informações sobre a monitorização automática do desempenho da base de dados com diagnósticos automatizados e análise de causas fundamentais de problemas de desempenho, consulte [O Azure SQL Intelligent Insights](sql-database-intelligent-insights.md).
- Consulte [a Query Performance Insights](sql-database-query-performance.md) para aprender e ver o impacto de desempenho das suas principais consultas.
