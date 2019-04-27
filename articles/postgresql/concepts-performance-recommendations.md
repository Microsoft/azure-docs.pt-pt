---
title: Recomendações de desempenho na base de dados do Azure para PostgreSQL
description: Este artigo descreve a funcionalidade de recomendação de desempenho na base de dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/28/2018
ms.openlocfilehash: c5324618eeda90b4ef1a512385fb2f14bf391215
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564463"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Recomendações de desempenho na base de dados do Azure para PostgreSQL

**Aplica-se a:** Base de dados do Azure para PostgreSQL 9.6 e 10

A funcionalidade de recomendações de desempenho analisa as bases de dados para criar sugestões personalizadas para um melhor desempenho. Para produzir as recomendações, a análise aborda várias características de base de dados, incluindo o esquema. Ativar [Query Store](concepts-query-store.md) no seu servidor para utilizar totalmente a funcionalidade de recomendações de desempenho. Depois de implementar quaisquer recomendações de desempenho, deve testar o desempenho para avaliar o impacto dessas alterações. 

## <a name="permissions"></a>Permissões
As permissões **Proprietário** ou **Contribuidor** necessárias para executar a análise que utiliza a funcionalidade Recomendações de Desempenho.

## <a name="performance-recommendations"></a>Recomendações de desempenho
A funcionalidade [Recomendações de Desempenho](concepts-performance-recommendations.md) analisa as cargas de trabalho no servidor para identificar índices com o potencial de melhorar o desempenho.

Open **recomendações de desempenho** partir o **desempenho inteligente** secção da barra de menus na página do portal do Azure para o servidor PostgreSQL.

![Página de destino das Recomendações de Desempenho](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Selecione **Analyze** e escolha uma base de dados, começará a análise. Dependendo da sua carga de trabalho, análise de th pode demorar vários minutos a concluir. Quando a análise estiver concluída, será apresentada uma notificação no portal. Análise realiza uma análise profunda da sua base de dados. Recomendamos que executar análises durante períodos de pico. 

O **recomendações** janela mostrará uma lista de recomendações se for detetada qualquer.

![Nova página recomendações de desempenho](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Recomendações não são aplicadas automaticamente. Para aplicar a recomendação, copie o texto da consulta e executá-lo a partir do seu cliente da preferência. Lembre-se de testar e monitorizar para avaliar a recomendação. 

## <a name="recommendation-types"></a>Tipos de recomendação

Atualmente, são suportados dois tipos de recomendações: *Criar índice* e *remover o índice*.

### <a name="create-index-recommendations"></a>Criar recomendações de índice
*Criar índice* recomendações sugerem índices novo para acelerar as consultas mais frequentemente executadas ou demoradas na carga de trabalho. Este tipo de recomendação requer [Query Store](concepts-query-store.md) seja ativado. Consulta Store recolhe informações de consulta e fornece as estatísticas de tempo de execução e a frequência de consulta detalhadas a análise utiliza para fazer a recomendação.

### <a name="drop-index-recommendations"></a>Recomendações de índice de lista
Além de detectar índices ausentes, a base de dados do Azure para PostgreSQL analisa o desempenho de índices existentes. Se um índice é raramente utilizado ou redundantes, o analisador de recomenda soltando-o.


## <a name="next-steps"></a>Passos Seguintes
- Saber mais sobre [monitorização e otimização](concepts-monitoring.md) na Base de Dados do Azure para PostgreSQL.

