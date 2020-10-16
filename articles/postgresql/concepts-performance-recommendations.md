---
title: Recomendações de desempenho - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve a função de Recomendação de Desempenho na Base de Dados Azure para PostgreSQL - Servidor Único.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: d547844671e6485e71be7dd8c355de08f3dec5e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710571"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Recomendações de desempenho na base de dados Azure para PostgreSQL - Servidor Único

**Aplica-se a:** Azure Database para PostgreSQL - Versões single Server 9.6, 10, 11

O recurso 'Recomendações de Desempenho' analisa as suas bases de dados para criar sugestões personalizadas para um melhor desempenho. Para produzir as recomendações, a análise analisa várias características da base de dados, incluindo o esquema. Ativar [a Loja de Consultas](concepts-query-store.md) no seu servidor para utilizar plenamente a função de Recomendações de Desempenho. Depois de implementar qualquer recomendação de desempenho, deve testar o desempenho para avaliar o impacto dessas alterações. 

## <a name="permissions"></a>Permissões
As permissões **Proprietário** ou **Contribuidor** necessárias para executar a análise que utiliza a funcionalidade Recomendações de Desempenho.

## <a name="performance-recommendations"></a>Recomendações de desempenho
A funcionalidade [Recomendações de Desempenho](concepts-performance-recommendations.md) analisa as cargas de trabalho no servidor para identificar índices com o potencial de melhorar o desempenho.

**Recomendações** de desempenho **abertas** a partir da secção de Desempenho Inteligente da barra de menu na página do portal Azure para o seu servidor PostgreSQL.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-page.png" alt-text="Página de destino das Recomendações de Desempenho":::

Selecione **Analisar** e escolher uma base de dados, que começará a análise. Dependendo da sua carga de trabalho, a análise pode demorar vários minutos a ser concluída. Quando a análise estiver concluída, será apresentada uma notificação no portal. A análise faz um exame profundo da sua base de dados. Recomendamos que efetue análises durante períodos fora do pico. 

A janela **de recomendações** apresentará uma lista de recomendações se alguma for encontrada.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-result.png" alt-text="Página de destino das Recomendações de Desempenho":::

As recomendações não são aplicadas automaticamente. Para aplicar a recomendação, copie o texto de consulta e execute-o a partir do seu cliente de eleição. Lembre-se de testar e monitorizar para avaliar a recomendação. 

## <a name="recommendation-types"></a>Tipos de recomendação

Atualmente, dois tipos de recomendações são suportados: *Criar Índice* e Índice *de Queda*.

### <a name="create-index-recommendations"></a>Criar recomendações de Índice
*Criar* recomendações de Índice sugerem novos índices para acelerar as consultas mais frequentemente executadas ou demoradas na carga de trabalho. Este tipo de recomendação requer que a [Loja de Consultas](concepts-query-store.md) seja ativada. A Consulta Store recolhe informações de consulta e fornece as estatísticas detalhadas de tempo de consulta e frequência que a análise utiliza para fazer a recomendação.

### <a name="drop-index-recommendations"></a>Recomendações do Índice de Queda
Além de detetar índices em falta, a Base de Dados Azure para PostgreSQL analisa o desempenho dos índices existentes. Se um índice for raramente utilizado ou redundante, o analisador recomenda que o abandone.

## <a name="considerations"></a>Considerações
* As recomendações de desempenho não estão disponíveis para [réplicas de leitura.](concepts-read-replicas.md)
## <a name="next-steps"></a>Passos seguintes
- Saber mais sobre [monitorização e otimização](concepts-monitoring.md) na Base de Dados do Azure para PostgreSQL.

