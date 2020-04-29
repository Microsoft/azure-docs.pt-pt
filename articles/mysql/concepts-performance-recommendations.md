---
title: Recomendações de desempenho - Base de Dados Azure para MySQL
description: Este artigo descreve a funcionalidade de Recomendação de Desempenho na Base de Dados Azure para mySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c7779d82ddd6e5fd1bf7fcd983937ea6c10dab1c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79537079"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Performance Recommendations in Azure Database for MySQL (Recomendações de desempenho na Base de Dados do Azure para MySQL)

**Aplica-se a:** Base de Dados Azure para MySQL 5.7

A funcionalidade De Recomendações de Desempenho analisa as suas bases de dados para criar sugestões personalizadas para um melhor desempenho. Para produzir as recomendações, a análise analisa várias características da base de dados, incluindo o esquema. Ative a [Consulta Store](concepts-query-store.md) no seu servidor para utilizar totalmente a funcionalidade Desativação de Desempenho. Se o esquema de desempenho estiver DESLIGADO, ligar a Consulta Store permite performance_schema e um subconjunto de instrumentos de esquema de desempenho necessários para a funcionalidade. Após a implementação de qualquer recomendação de desempenho, deve testar o desempenho para avaliar o impacto dessas alterações.

## <a name="permissions"></a>Permissões

As permissões **Proprietário** ou **Contribuidor** necessárias para executar a análise que utiliza a funcionalidade Recomendações de Desempenho.

## <a name="performance-recommendations"></a>Recomendações de desempenho

A funcionalidade [Recomendações de Desempenho](concepts-performance-recommendations.md) analisa as cargas de trabalho no servidor para identificar índices com o potencial de melhorar o desempenho.

Open **Performance Recomendações** da secção **de Desempenho Inteligente** da barra de menuna página do portal Azure para o seu servidor MySQL.

![Página de destino das Recomendações de Desempenho](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Selecione **Analisar** e escolher uma base de dados, que começará a análise. Dependendo da sua carga de trabalho, a análise pode demorar vários minutos a ser concluída. Quando a análise estiver concluída, será apresentada uma notificação no portal. A análise é feita na sua base de dados. Recomendamos que efetue análises durante períodos fora do pico.

A janela **de recomendações** mostrará uma lista de recomendações se alguma foi encontrada e a identificação de consulta relacionada que gerou esta recomendação. Com a identificação da consulta, você pode usar a visão [mysql.query_store](concepts-query-store.md#mysqlquery_store) para saber mais sobre a consulta.

![Recomendações de Desempenho nova página](./media/concepts-performance-recommendations/performance-recommendations-result.png)

As recomendações não são aplicadas automaticamente. Para aplicar a recomendação, copie o texto de consulta e execute-o a partir do seu cliente de eleição. Lembre-se de testar e monitorizar para avaliar a recomendação.

## <a name="recommendation-types"></a>Tipos de recomendação

Atualmente, apenas são apoiadas recomendações do *Create Index.*

### <a name="create-index-recommendations"></a>Criar recomendações do Índice

*As* recomendações do Create Index sugerem novos índices para acelerar as consultas mais frequentes ou demoradas na carga de trabalho. Este tipo de recomendação requer que a [Consulta Store](concepts-query-store.md) seja ativada. A Consulta Store recolhe informações de consulta e fornece as estatísticas detalhadas de tempo de execução e frequência que a análise utiliza para fazer a recomendação.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [monitorização e sintonização](concepts-monitoring.md) na Base de Dados Azure para mySQL.