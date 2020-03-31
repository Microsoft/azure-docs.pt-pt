---
title: Recomendações de Desempenho - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve a funcionalidade de Recomendação de Desempenho na Base de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a30af0c8bef47a37fe3439e885d3895a2c826225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768474"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Recomendações de desempenho na Base de Dados Azure para PostgreSQL - Servidor Único

**Aplica-se a:** Base de Dados Azure para PostgreSQL - Versões Single Server 9.6, 10, 11

A funcionalidade De Recomendações de Desempenho analisa as suas bases de dados para criar sugestões personalizadas para um melhor desempenho. Para produzir as recomendações, a análise analisa várias características da base de dados, incluindo o esquema. Ative a [Consulta Store](concepts-query-store.md) no seu servidor para utilizar totalmente a funcionalidade Desativação de Desempenho. Após a implementação de qualquer recomendação de desempenho, deve testar o desempenho para avaliar o impacto dessas alterações. 

## <a name="permissions"></a>Permissões
As permissões **Proprietário** ou **Contribuidor** necessárias para executar a análise que utiliza a funcionalidade Recomendações de Desempenho.

## <a name="performance-recommendations"></a>Recomendações de desempenho
A funcionalidade [Recomendações de Desempenho](concepts-performance-recommendations.md) analisa as cargas de trabalho no servidor para identificar índices com o potencial de melhorar o desempenho.

Open **Performance Recomendações** da secção **de Desempenho Inteligente** da barra de menu na página do portal Azure para o seu servidor PostgreSQL.

![Página de destino das Recomendações de Desempenho](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Selecione **Analisar** e escolher uma base de dados, que começará a análise. Dependendo da sua carga de trabalho, a análise pode demorar vários minutos a ser concluída. Quando a análise estiver concluída, será apresentada uma notificação no portal. A análise é feita na sua base de dados. Recomendamos que efetue análises durante períodos fora do pico. 

A janela **de recomendações** mostrará uma lista de recomendações se alguma for encontrada.

![Recomendações de Desempenho nova página](./media/concepts-performance-recommendations/performance-recommendations-result.png)

As recomendações não são aplicadas automaticamente. Para aplicar a recomendação, copie o texto de consulta e execute-o a partir do seu cliente de eleição. Lembre-se de testar e monitorizar para avaliar a recomendação. 

## <a name="recommendation-types"></a>Tipos de recomendação

Atualmente, são apoiados dois tipos de recomendações: *Criar Índice* e Índice *de Queda*.

### <a name="create-index-recommendations"></a>Criar recomendações do Índice
*As* recomendações do Create Index sugerem novos índices para acelerar as consultas mais frequentes ou demoradas na carga de trabalho. Este tipo de recomendação requer que a [Consulta Store](concepts-query-store.md) seja ativada. A Consulta Store recolhe informações de consulta e fornece as estatísticas detalhadas de tempo de execução e frequência que a análise utiliza para fazer a recomendação.

### <a name="drop-index-recommendations"></a>Recomendações do Índice de Queda
Além de detetar índices em falta, a Base de Dados Azure para PostgreSQL analisa o desempenho dos índices existentes. Se um índice raramente for utilizado ou redundante, o analisador recomenda que o abandone.

## <a name="considerations"></a>Considerações
* Recomendações de Desempenho não estão disponíveis para [réplicas de leitura](concepts-read-replicas.md).
## <a name="next-steps"></a>Passos seguintes
- Saber mais sobre [monitorização e otimização](concepts-monitoring.md) na Base de Dados do Azure para PostgreSQL.

