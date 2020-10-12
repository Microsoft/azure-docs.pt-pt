---
title: Desempenho automatizado, custo, recomendações de segurança para Azure Cosmos DB
description: Saiba como ver desempenho personalizado, custo, segurança e outras recomendações para a Azure Cosmos DB com base nos seus padrões de carga de trabalho.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/28/2020
ms.reviewer: sngun
ms.openlocfilehash: 8fa2fdf23a0d71b854e043b66c0aed7e944c5f39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87450836"
---
# <a name="automated-recommendations-for-azure-cosmos-db"></a>Recomendações automatizadas para Azure Cosmos DB

Todos os serviços na nuvem, incluindo o Azure Cosmos DB, obtêm atualizações frequentes com novas funcionalidades, capacidades e melhorias. É importante que a sua aplicação acompanhe as últimas atualizações de desempenho e segurança. O portal Azure oferece recomendações personalizadas que lhe permitem maximizar o desempenho da sua aplicação. O motor consultivo da Azure Cosmos DB analisa continuamente a história de utilização dos seus recursos DB Azure Cosmos e fornece recomendações baseadas nos seus padrões de carga de trabalho. Estas recomendações correspondem a áreas como a partição, a indexação, a rede, a segurança, etc. Estas recomendações personalizadas ajudam-no a melhorar o desempenho da sua aplicação.

## <a name="view-recommendations"></a>Ver recomendações

Pode ver as recomendações para Azure Cosmos DB das seguintes formas:

- Uma forma de ver as recomendações está dentro do separador notificações. Se houver novas recomendações, verá uma barra de mensagens. Inscreva-se no seu [portal Azure](https://portal.azure.com) e navegue para a sua conta Azure Cosmos. Dentro da sua conta Azure Cosmos, abra o painel **de Notificações** e, em seguida, selecione o separador **Recomendações.** Pode selecionar a mensagem e ver recomendações.  

   :::image type="content" source="./media/automated-recommendations/cosmos-db-pane-recommendations.png" alt-text="Ver recomendações do painel DB de Azure Cosmos":::

- Você também pode encontrar as recomendações através do [Azure Advisor](../advisor/advisor-overview.md) categorizados por diferentes baldes tais como custo, segurança, fiabilidade, desempenho e excelência operacional. Pode selecionar subscrições específicas e filtrar pelo tipo de recurso, que são **as contas DB da Azure Cosmos.**  Ao selecionar uma recomendação específica, exibe as ações que pode tomar para beneficiar as suas cargas de trabalho.

   :::image type="content" source="./media/automated-recommendations/advisor-pane-recommendations.png" alt-text="Ver recomendações do painel DB de Azure Cosmos":::

Nem todas as recomendações apresentadas no painel DB do Azure Cosmos estão disponíveis no Azure Advisor e vice-versa. Isto porque, com base no tipo de recomendação que se enquadram no painel do Azure Advisor, no painel DB de Azure Cosmos ou em ambos.

Atualmente, a Azure Cosmos DB apoia recomendações sobre as seguintes áreas. Cada uma destas recomendações inclui um link para a secção relevante da documentação, por isso é fácil para si dar os próximos passos.

## <a name="sdk-usage-recommendations"></a>Recomendações de utilização da SDK

Nesta categoria, o conselheiro deteta o uso de uma versão antiga de SDKs e recomenda que faça o upgrade para uma versão mais recente para alavancar as mais recentes correções de bugs e melhorias de desempenho. Atualmente estão disponíveis as seguintes recomendações específicas da SDK:

|Nome  |Descrição  |
|---------|---------|
| Conector de faísca antiga | Deteta a utilização de versões antigas do conector Spark e recomenda a atualização. |
| SDK antigo .NET | Deteta a utilização de versões antigas do .NET SDK e recomenda a atualização. |
| SDK java velho | Deteta a utilização de versões antigas do conector Java e recomenda a atualização. |

## <a name="indexing-recommendations"></a>Recomendações de indexação

Nesta categoria, o consultor deteta o modo de indexação, a política de indexação, os caminhos indexados e recomenda a alteração se a configuração atual tiver impacto no desempenho da consulta. Atualmente estão disponíveis as seguintes recomendações específicas de indexação:

|Nome  |Descrição  |
|---------|---------|
| Indexação preguiçosa | Deteta o uso do modo de indexação preguiçoso e recomenda a utilização de um modo de indexação consistente. O objetivo do modo de indexação preguiçosa da Azure Cosmos DB é limitado e pode impactar a frescura da consulta resulta em algumas situações pelo modo de indexação consistente. |
| Indexação composta| Deteta as contas onde as consultas podem beneficiar de índices compósitos e recomenda a sua utilização. Os índices compostos podem melhorar drasticamente o desempenho e o consumo de produção de algumas consultas.|
| Política de indexação padrão com muitos caminhos indexados | Deteta recipientes em execução em indexação padrão com muitos caminhos indexados e recomenda a personalização da política de indexação.|
| ENCOMENDA POR consultas com alta carga RU/s| Deteta recipientes que emitem ORDER BY consultas com elevada carga RU/s e recomenda a exploração de índices compósitos.|
| Contas MongoDB 3.6 sem índice e consumo elevado de RU/s| Deteta a API da Azure Cosmos para a MongoDB com a versão 3.6 dos contentores que emitem consultas com alta carga RU/s e recomenda a adição de índices.|

## <a name="cost-optimization-recommendations"></a>Recomendações de otimização de custos

Nesta categoria, o consultor deteta o uso do RU/s e determina que pode otimizar o preço fazendo algumas alterações nos seus recursos ou alavancando um modelo de preços diferente. Atualmente estão disponíveis as seguintes recomendações específicas de otimização de custos:

|Nome  |Descrição  |
|---------|---------|
| Capacidade reservada | Deteta a utilização do RU/s e recomenda casos reservados aos utilizadores que possam beneficiar dele. |
| Recipientes inativos | Deteta os recipientes que não são utilizados há mais de 30 dias e recomenda a redução da produção para esses recipientes ou a sua eliminação.|
| Novas subscrições com alta produção | Deteta novas subscrições com contas gastando RU/s invulgarmente elevados por dia e fornece-lhes uma notificação. Esta notificação destina-se especificamente a sensibilizar os novos clientes de que a Azure Cosmos DB opera com um modelo baseado em produção e não em modelo baseado no consumo. |

## <a name="migration-recommendations"></a>Recomendações de migração

Nesta categoria, o conselheiro deteta que está a usar funcionalidades antigas que recomenda migrar para que possa alavancar a escalabilidade massiva da Azure Cosmos DB e outros benefícios. Atualmente estão disponíveis as seguintes recomendações específicas para a migração:

|Nome  |Descrição  |
|---------|---------|
| Recipientes não divididos | Deteta recipientes de tamanho fixo que se aproximam do seu limite máximo de armazenamento e recomenda a sua migração para recipientes divididos.|

## <a name="query-usage-recommendations"></a>Recomendações de utilização de consultas

Nesta categoria, o conselheiro deteta a execução de consultas e identifica que o desempenho da consulta pode ser afinado com algumas alterações. Atualmente estão disponíveis as seguintes recomendações de utilização de consultas:

|Nome  |Descrição  |
|---------|---------|
| Consultas com tamanho de página fixa | Deteta consultas emitidas com um tamanho de página fixa e recomenda a utilização de -1 (sem limite no tamanho da página) em vez de definir um valor específico. Esta opção reduz o número de viagens redondas de rede necessárias para obter todos os resultados. |

## <a name="next-steps"></a>Passos seguintes

* [Desempenho de consulta de afinação em Azure Cosmos DB](sql-api-query-metrics.md)
* [Problemas de consulta de resolução de problemas](troubleshoot-query-performance.md) ao utilizar o Azure Cosmos DB
