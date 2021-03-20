---
title: Recomendações de desempenho - Azure Database for MySQL
description: Este artigo descreve a funcionalidade de Recomendação de Desempenho na Base de Dados Azure para o MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 1445ad3d6be020326c34d60c555c24c435548fd0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94537650"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Performance Recommendations in Azure Database for MySQL (Recomendações de desempenho na Base de Dados do Azure para MySQL)

**Aplica-se a:** Base de Dados Azure para MySQL 5.7, 8.0

A funcionalidade De Recomendações de Desempenho analisa as suas bases de dados para criar sugestões personalizadas para um melhor desempenho. Para produzir as recomendações, a análise analisa várias características da base de dados, incluindo o esquema. Ativar [a Loja de Consultas](concepts-query-store.md) no seu servidor para utilizar plenamente a função de Recomendações de Desempenho. Se o esquema de desempenho estiver desligado, ligar a Query Store permite performance_schema e um subconjunto de instrumentos de esquema de desempenho necessários para a funcionalidade. Depois de implementar qualquer recomendação de desempenho, deve testar o desempenho para avaliar o impacto dessas alterações.

## <a name="permissions"></a>Permissões

As permissões **Proprietário** ou **Contribuidor** necessárias para executar a análise que utiliza a funcionalidade Recomendações de Desempenho.

## <a name="performance-recommendations"></a>Recomendações de desempenho

A funcionalidade [Recomendações de Desempenho](concepts-performance-recommendations.md) analisa as cargas de trabalho no servidor para identificar índices com o potencial de melhorar o desempenho.

**Recomendações** de desempenho **abertas** a partir da secção de Desempenho Inteligente da barra de menu na página do portal Azure para o seu servidor MySQL.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-page.png" alt-text="Página de destino das Recomendações de Desempenho":::

Selecione **Analisar** e escolher uma base de dados, que começará a análise. Dependendo da sua carga de trabalho, a análise pode demorar vários minutos a ser concluída. Quando a análise estiver concluída, será apresentada uma notificação no portal. A análise faz um exame profundo da sua base de dados. Recomendamos que efetue análises durante períodos fora do pico.

A janela **de recomendações** apresentará uma lista de recomendações se alguma for encontrada e o ID de consulta relacionado que gerou esta recomendação. Com o ID de consulta, você pode usar a vista [mysql.query_store](concepts-query-store.md#mysqlquery_store) para saber mais sobre a consulta.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-result.png" alt-text="Nova página de recomendações de desempenho":::

As recomendações não são aplicadas automaticamente. Para aplicar a recomendação, copie o texto de consulta e execute-o a partir do seu cliente de eleição. Lembre-se de testar e monitorizar para avaliar a recomendação.

## <a name="recommendation-types"></a>Tipos de recomendação

### <a name="index-recommendations"></a>Recomendações de índice

*Criar* recomendações de Índice sugerem novos índices para acelerar as consultas mais frequentemente executadas ou demoradas na carga de trabalho. Este tipo de recomendação requer que a [Loja de Consultas](concepts-query-store.md) seja ativada. A Consulta Store recolhe informações de consulta e fornece as estatísticas detalhadas de tempo de consulta e frequência que a análise utiliza para fazer a recomendação.

### <a name="query-recommendations"></a>Recomendações de consulta

Recomendações de consulta sugerem otimizações e reescritas para consultas na carga de trabalho. Ao identificar os anti-padrões de consulta mySQL e fixá-los sintactamente, o desempenho de consultas demoradas pode ser melhorado. Este tipo de recomendação requer que a Loja de Consultas seja ativada. A Consulta Store recolhe informações de consulta e fornece as estatísticas detalhadas de tempo de consulta e frequência que a análise utiliza para fazer a recomendação.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [monitorização e afinação](concepts-monitoring.md) na Base de Dados Azure para o MySQL.