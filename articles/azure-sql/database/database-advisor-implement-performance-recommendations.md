---
title: Recomendações de desempenho do consultor de base de dados para a Base de Dados Azure SQL
description: A Azure SQL Database fornece recomendações para bases de dados que podem melhorar o desempenho de consulta na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: fasttrack-edit, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/10/2020
ms.openlocfilehash: 79ccf0f8aae7e915601081f875cea294de52d787
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96500857"
---
# <a name="database-advisor-performance-recommendations-for-azure-sql-database"></a>Recomendações de desempenho do Advisor da Base de Dados para a Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A Azure SQL Database aprende e adapta-se com a sua aplicação. A Azure SQL Database dispõe de vários consultores de bases de dados que fornecem recomendações personalizadas que lhe permitem maximizar o desempenho. Estes consultores de base de dados avaliam e analisam continuamente o histórico de utilização e fornecem recomendações baseadas em padrões de carga de trabalho que ajudam a melhorar o desempenho.

## <a name="performance-overview"></a>Visão geral do desempenho

A visão geral do desempenho fornece um resumo do desempenho da sua base de dados e ajuda-o com a afinação de desempenho e resolução de problemas.

![Visão geral do desempenho para Azure SQL Database](./media/database-advisor-implement-performance-recommendations/performance-overview-annotated.png)

- O azulejo **recomendações** fornece uma desagregação das recomendações de sintonização para a sua base de dados (as três principais recomendações são mostradas se houver mais). Clicar neste azulejo leva-o às **[opções de recomendação de desempenho](database-advisor-find-recommendations-portal.md#viewing-recommendations)**.
- O azulejo da **atividade de Afinação** fornece um resumo das ações de afinação em curso e concluídas para a sua base de dados, dando-lhe uma visão rápida da história da atividade de afinação. Clicar neste azulejo leva-o à visualização completa do histórico de sintonização para a sua base de dados.
- O **azulejo de afinação automática** mostra a **[configuração de afinação automática](automatic-tuning-enable.md)** da sua base de dados (opções de afinação que são automaticamente aplicadas na sua base de dados). Clicar neste azulejo abre o diálogo de configuração da automatização.
- O azulejo **de consultas de base de dados** mostra o resumo do desempenho da consulta para a sua base de dados (utilização geral do DTU e consultas de consumo de recursos de topo). Clicar neste azulejo leva-o a consultar o **[Insight de Desempenho da Consulta](query-performance-insight-use.md)**.

## <a name="performance-recommendation-options"></a>Opções de recomendação de desempenho

As opções de recomendação de desempenho disponíveis na Base de Dados Azure SQL são:

| Recomendação de desempenho | Base de dados única e suporte de base de dados em conjunto | Suporte de base de dados de casos |
| :----------------------------- | ----- | ----- |
| **Crie recomendações de índice -** Recomenda a criação de índices que possam melhorar o desempenho da sua carga de trabalho. | Yes | No |
| **Recomendações de índice de queda** - Recomenda a remoção de índices redundantes e duplicados diariamente, com exceção de índices únicos, e índices que não foram utilizados durante muito tempo (>90 dias). Por favor, note que esta opção não é compatível com aplicações usando comutação de partição e dicas de índice. A queda de índices não suportados não é suportada para os níveis de serviço Premium e Business Critical. | Yes | No |
| **Parametrizar recomendações de consultas (pré-visualização)** - Recomenda a parametrização forçada em casos em que tenha uma ou mais consultas que estão constantemente a ser recompiliadas, mas que acabam com o mesmo plano de execução de consultas. | Yes | No |
| **Corrigir recomendações de problemas de esquema (pré-visualização)** - As recomendações para a correção do esquema aparecem quando a Base de Dados Azure SQL nota uma anomalia no número de erros SQL relacionados com esquemas que estão a acontecer na sua base de dados. A Microsoft está atualmente a deprepar as recomendações de "Fix Schema issue". | Yes | No |

![Recomendações de desempenho para Azure SQL Database](./media/database-advisor-implement-performance-recommendations/performance-recommendations-annotated.png)

Para aplicar recomendações de desempenho, consulte [a aplicação de recomendações](database-advisor-find-recommendations-portal.md#applying-recommendations). Para visualizar o estado das recomendações, consulte [as operações de monitorização.](database-advisor-find-recommendations-portal.md#monitoring-operations)

Você também pode encontrar um histórico completo de ações de afinação que foram aplicadas no passado.

## <a name="create-index-recommendations"></a>Criar recomendações de índice

A Azure SQL Database monitoriza continuamente as consultas que estão em execução e identifica os índices que podem melhorar o desempenho. Depois de haver confiança suficiente de que um determinado índice está em falta, uma nova recomendação **de índice create** é criada.

A Azure SQL Database constrói confiança estimando o ganho de desempenho que o índice traria através do tempo. Dependendo do ganho de desempenho estimado, as recomendações são categorizadas como altas, médias ou baixas.

Os índices criados através de recomendações são sempre sinalizados como índices criados automaticamente. Pode ver quais os índices que são criados automaticamente olhando para a [vista sys.indexes](/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql). Os índices criados automaticamente não bloqueiam os comandos ALTER/RENAME.

Se tentar largar a coluna que tem um índice auto-criado sobre ela, o comando passa. O índice auto-criado também é reduzido com o comando. Os índices regulares bloqueiam o comando ALTER/RENAME nas colunas indexadas.

Após a aplicação da recomendação do índice de criação, a Base de Dados Azure SQL compara o desempenho das consultas com o desempenho da linha de base. Se o novo índice melhorar o desempenho, a recomendação é sinalizada como bem sucedida e o relatório de impacto está disponível. Se o índice não melhorou o desempenho, é automaticamente revertido. A Azure SQL Database utiliza este processo para garantir que as recomendações melhorem o desempenho da base de dados.

Qualquer recomendação de **índice de criação** tem uma política de back-off que não permite aplicar a recomendação se o uso de recursos de uma base de dados ou piscina é elevado. A política de back-off tem em conta CPU, Data IO, Log IO e armazenamento disponível.

Se o CPU, o IO de dados ou o IO de registo for superior a 80% nos 30 minutos anteriores, a recomendação do índice de criação é adiada. Se o armazenamento disponível for inferior a 10% após a criação do índice, a recomendação entra num estado de erro. Se, passados alguns dias, a sintonização automática ainda acreditar que o índice seria benéfico, o processo recomeça.

Este processo repete-se até que haja armazenamento disponível suficiente para criar um índice, ou até que o índice não seja mais visto como benéfico.

## <a name="drop-index-recommendations"></a>Recomendações de índice de queda

Além de detetar índices em falta, a Base de Dados Azure SQL analisa continuamente o desempenho dos índices existentes. Se não for utilizado um índice, a Base de Dados Azure SQL recomenda a sua queda. A queda de um índice é recomendada em dois casos:

- O índice é uma duplicação de outro índice (coluna indexada e incluída, esquema de partição e filtros).
- O índice não é utilizado por um período prolongado (93 dias).

As recomendações do índice drop também passam pela verificação após a implementação. Se o desempenho melhorar, o relatório de impacto estará disponível. Se o desempenho se degradar, a recomendação é revertida.

## <a name="parameterize-queries-recommendations-preview"></a>Parametrizar recomendações de consultas (pré-visualização)

*Parametrizar recomendações de consultas* aparecem quando você tem uma ou mais consultas que estão constantemente sendo recompiladas, mas acabam com o mesmo plano de execução de consultas. Esta condição cria uma oportunidade para aplicar a parametrização forçada. A parametrização forçada, por sua vez, permite que os planos de consulta sejam em cache e reutilizados no futuro, o que melhora o desempenho e reduz o uso de recursos.

Todas as consultas precisam de ser compiladas para gerar um plano de execução. Cada plano gerado é adicionado à cache do plano. Execuções subsequentes da mesma consulta podem reutilizar este plano a partir da cache, o que elimina a necessidade de compilação adicional.

As consultas com valores não parametrizados podem levar a custos de desempenho, pois o plano de execução é recompilado sempre que os valores não parametrizados são diferentes. Em muitos casos, as mesmas consultas com valores de parâmetros diferentes geram os mesmos planos de execução. No entanto, estes planos são adicionados em separado à cache de planos.

O processo de recompilação de planos de execução utiliza recursos da base de dados, aumenta o tempo de duração da consulta e excede a capacidade da cache de planos. Por sua vez, estes eventos fazem com que os planos sejam removidos da cache. Este comportamento pode ser alterado definindo a opção de parametrização forçada na base de dados.

Para ajudá-lo a estimar o impacto desta recomendação, é-lhe fornecida uma comparação entre a utilização real do CPU e a utilização projetada do CPU (como se a recomendação fosse aplicada). Esta recomendação pode ajudá-lo a obter poupanças de CPU. Também pode ajudá-lo a diminuir a duração da consulta e a sobrecarga para a cache do plano, o que significa que mais dos planos podem permanecer na cache e ser reutilizados. Pode aplicar esta recomendação rapidamente selecionando o comando **Aplicar.**

Depois de aplicar esta recomendação, permite a parametrização forçada em poucos minutos na sua base de dados. Inicia o processo de monitorização, que dura aproximadamente 24 horas. Após este período, pode ver o relatório de validação. Este relatório mostra o uso do CPU da sua base de dados 24 horas antes e depois da recomendação ter sido aplicada. O Azure SQL Database Advisor dispõe de um mecanismo de segurança que reverte automaticamente a recomendação aplicada se tiver sido detetada uma regressão de desempenho.

## <a name="fix-schema-issues-recommendations-preview"></a>Corrigir recomendações de problemas de esquema (pré-visualização)

> [!IMPORTANT]
> A Microsoft está atualmente a deprepar as recomendações de "Fix Schema issue". Recomendamos que utilize [Insights Inteligentes](intelligent-insights-overview.md) para monitorizar os problemas de desempenho da sua base de dados, incluindo problemas de esquema que as recomendações de "Corrigir esquema" previamente abrangidas.

**Corrigir problemas de esquema** as recomendações aparecem quando a Base de Dados Azure SQL nota uma anomalia no número de erros SQL relacionados com esquemas que estão a acontecer na sua base de dados. Esta recomendação aparece normalmente quando a sua base de dados encontra vários erros relacionados com esquemas (nome de coluna inválida, nome de objeto inválido, e assim por diante) dentro de uma hora.

"Problemas de esquema" são uma classe de erros de sintaxe. Ocorrem quando a definição da consulta SQL e a definição do esquema de base de dados não estão alinhadas. Por exemplo, uma das colunas que é esperada pela consulta pode estar faltando na tabela alvo ou vice-versa.

A recomendação "Fix schema issue" aparece quando a Base de Dados Azure SQL nota uma anomalia no número de erros SQL relacionados com esquemas que estão a acontecer na sua base de dados. A tabela a seguir mostra os erros relacionados com questões de esquema:

| Código de erro SQL | Mensagem |
| --- | --- |
| 201 |Procedimento ou função *' ' espera parâmetro ',* que não foi fornecido. |
| 207 |Nome da coluna inválida '*'. |
| 208 |Nome de objeto inválido '*'. |
| 213 |O nome da coluna ou o número de valores fornecidos não correspondem à definição da tabela. |
| 2812 |Não foi possível encontrar o procedimento armazenado '*'. |
| 8144 |Procedimento ou função * tem muitos argumentos especificados. |

## <a name="custom-applications"></a>Aplicações personalizadas

Os desenvolvedores podem considerar desenvolver aplicações personalizadas usando recomendações de desempenho para Azure SQL Database. Todas as recomendações listadas no portal para uma base de dados podem ser acedidas através da [API get-AzSqlDataBaseRecommendedAction.](/powershell/module/az.sql/get-azsqldatabaserecommendedaction)

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre a sintonização automática dos índices de base de dados e planos de execução de consultas, consulte [a afinação automática da Base de Dados Azure SQL](automatic-tuning-overview.md).
- Para obter mais informações sobre a monitorização automática do desempenho da base de dados com diagnósticos automatizados e análise de causa de raiz de problemas de desempenho, consulte [Azure SQL Intelligent Insights](intelligent-insights-overview.md).
- Consulte [a Consulta Performance Insights](query-performance-insight-use.md) para conhecer e ver o impacto de desempenho das suas principais consultas.