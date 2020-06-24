---
title: Escala Azure Cosmos DB em um horário usando o temporizador de funções Azure
description: Aprenda a escalar as mudanças de produção em Azure Cosmos DB utilizando PowerShell e Azure Functions.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: 9f538b02e81d885e22a6417d7c1f139c22635b0d
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85262485"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Escala A produção DB do Cosmos da escala utilizando o gatilho do temporizador de funções Azure

O desempenho de uma conta Azure Cosmos baseia-se no montante de produção provisitada expressa nas Unidades de Pedido por segundo (RU/s). O provisionamento encontra-se numa segunda granularidade e é faturado com base no RU/s mais elevado por hora. Este modelo de capacidade a provisionada permite que o serviço forneça uma produção previsível e consistente, baixa latência garantida e elevada disponibilidade. A maioria das cargas de trabalho de produção destas características. No entanto, em ambientes de desenvolvimento e testes onde a Azure Cosmos DB só é usada durante o horário de trabalho, pode aumentar a produção de manhã e reduzir a escala à noite após o horário de trabalho.

Pode definir a produção através de [Modelos de Gestor de Recursos Azure,](resource-manager-samples.md) [Azure CLI](cli-samples.md)e [PowerShell,](powershell-samples-sql.md)para contas API core (SQL) ou utilizando os DB SDKs Azure Cosmos específicos da língua. O benefício da utilização de modelos de gestores de recursos, Azure CLI ou PowerShell é que suportam todos os APIs de modelos DB do Azure Cosmos.

## <a name="throughput-scheduler-sample-project"></a>Projeto de amostra de programador de saída

Para simplificar o processo para escalar a Azure Cosmos DB numa programação, criámos um projeto de amostra chamado [Azure Cosmos.](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) Este projeto é uma aplicação Azure Functions com dois gatilhos temporizadores: "ScaleUpTrigger" e "ScaleDownTrigger". Os gatilhos executam um script PowerShell que define a produção de cada recurso tal como definido no `resources.json` ficheiro em cada gatilho. O ScaleUpTrigger está configurado para funcionar às 8 AM UTC e o ScaleDownTrigger está configurado para funcionar às 18:00 UTC e estes tempos podem ser facilmente atualizados dentro do `function.json` ficheiro para cada gatilho.

Você pode clonar este projeto localmente, modificá-lo para especificar os recursos DB Azure Cosmos para escalar para cima e para baixo e o horário a executar. Mais tarde, pode implantá-lo numa subscrição do Azure e garanti-lo utilizando a identidade de serviço gerida com permissões de Controlo de Acesso (RBAC) [baseadas em funções](role-based-access-control.md) com o papel de "Azure Cosmos DB operator" para definir a produção nas suas contas Azure Cosmos.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais e descarregue a amostra do programador de [saída Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler).
