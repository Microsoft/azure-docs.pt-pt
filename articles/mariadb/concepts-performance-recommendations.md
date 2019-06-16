---
title: Recomendações de desempenho na base de dados do Azure para MariaDB
description: Este artigo descreve a funcionalidade de recomendação de desempenho na base de dados do Azure para MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: ad3e2157e7b8748174cc8b81d1debd5de0ba77b9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079486"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Recomendações de desempenho na base de dados do Azure para MariaDB

**Aplica-se a:** Base de dados do Azure para MariaDB 10.2s

> [!NOTE]
> Recomendações de desempenho está em pré-visualização. Suporte para recomendações de desempenho no portal do Azure está a ser lançado e talvez ainda não estar disponível na sua região.

A funcionalidade de recomendações de desempenho analisa as bases de dados para criar sugestões personalizadas para um melhor desempenho. Para produzir as recomendações, a análise aborda várias características de base de dados, incluindo o esquema. Ativar [Query Store](concepts-query-store.md) no seu servidor para utilizar totalmente a funcionalidade de recomendações de desempenho. Se o esquema de desempenho estiver desligado, Ativando a consulta Store permite performance_schema e um subconjunto de instrumentos de esquema de desempenho necessário para a funcionalidade. Depois de implementar quaisquer recomendações de desempenho, deve testar o desempenho para avaliar o impacto dessas alterações.

## <a name="permissions"></a>Permissões

As permissões **Proprietário** ou **Contribuidor** necessárias para executar a análise que utiliza a funcionalidade Recomendações de Desempenho.

## <a name="performance-recommendations"></a>Recomendações de desempenho

A funcionalidade [Recomendações de Desempenho](concepts-performance-recommendations.md) analisa as cargas de trabalho no servidor para identificar índices com o potencial de melhorar o desempenho.

Open **recomendações de desempenho** da **desempenho inteligente** secção da barra de menus na página do portal do Azure do seu servidor MariaDB.

![Página de destino das Recomendações de Desempenho](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Selecione **Analyze** e escolha uma base de dados, começará a análise. Dependendo da sua carga de trabalho, a análise pode demorar vários minutos a concluir. Quando a análise estiver concluída, será apresentada uma notificação no portal. Análise realiza uma análise profunda da sua base de dados. Recomendamos que executar análises durante períodos de pico.

O **recomendações** janela mostrará uma lista de recomendações se for detetada qualquer e o ID de consulta relacionados que gerou esta recomendação. Com o ID de consulta, pode utilizar o [mysql.query_store](concepts-query-store.md#mysqlquery_store) vista para saber mais sobre a consulta.

![Nova página recomendações de desempenho](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Recomendações não são aplicadas automaticamente. Para aplicar a recomendação, copie o texto da consulta e executá-lo a partir do seu cliente da preferência. Lembre-se de testar e monitorizar para avaliar a recomendação.

## <a name="recommendation-types"></a>Tipos de recomendação

Atualmente, apenas *Create Index* recomendações são suportadas.

### <a name="create-index-recommendations"></a>Criar recomendações de índice

*Criar índice* recomendações sugerem índices novo para acelerar as consultas mais frequentemente executadas ou demoradas na carga de trabalho. Este tipo de recomendação requer [Query Store](concepts-query-store.md) seja ativado. Consulta Store recolhe informações de consulta e fornece as estatísticas de tempo de execução e a frequência de consulta detalhadas a análise utiliza para fazer a recomendação.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [monitoramento e ajuste](concepts-monitoring.md) na base de dados do Azure para MariaDB.