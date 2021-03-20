---
title: Escala Azure Cosmos DB em um horário usando o temporizador de funções Azure
description: Aprenda a escalar as mudanças de produção em Azure Cosmos DB utilizando PowerShell e Azure Functions.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: c60f3fc6b4ce4a1aead273fedb81e39de697f576
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339262"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Escala A produção DB do Cosmos da escala utilizando o gatilho do temporizador de funções Azure
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O desempenho de uma conta Azure Cosmos baseia-se no montante de produção provisitada expressa nas Unidades de Pedido por segundo (RU/s). O provisionamento encontra-se numa segunda granularidade e é faturado com base no RU/s mais elevado por hora. Este modelo de capacidade a provisionada permite que o serviço forneça uma produção previsível e consistente, baixa latência garantida e elevada disponibilidade. A maioria das cargas de trabalho de produção destas características. No entanto, em ambientes de desenvolvimento e testes onde a Azure Cosmos DB só é usada durante o horário de trabalho, pode aumentar a produção de manhã e reduzir a escala à noite após o horário de trabalho.

Pode definir a produção através de [Modelos de Gestor de Recursos Azure,](./templates-samples-sql.md) [Azure CLI](cli-samples.md)e [PowerShell,](powershell-samples.md)para contas API core (SQL) ou utilizando os DB SDKs Azure Cosmos específicos da língua. O benefício da utilização de modelos de gestores de recursos, Azure CLI ou PowerShell é que suportam todos os APIs de modelos DB do Azure Cosmos.

## <a name="throughput-scheduler-sample-project"></a>Projeto de amostra de programador de saída

Para simplificar o processo para escalar a Azure Cosmos DB numa programação, criámos um projeto de amostra chamado [Azure Cosmos.](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) Este projeto é uma aplicação Azure Functions com dois gatilhos temporizadores: "ScaleUpTrigger" e "ScaleDownTrigger". Os gatilhos executam um script PowerShell que define a produção de cada recurso tal como definido no `resources.json` ficheiro em cada gatilho. O ScaleUpTrigger está configurado para funcionar às 8 AM UTC e o ScaleDownTrigger está configurado para funcionar às 18:00 UTC e estes tempos podem ser facilmente atualizados dentro do `function.json` ficheiro para cada gatilho.

Você pode clonar este projeto localmente, modificá-lo para especificar os recursos DB Azure Cosmos para escalar para cima e para baixo e o horário a executar. Mais tarde, pode implantá-lo numa subscrição da Azure e garanti-la utilizando a identidade de serviço gerida com permissões [de controlo de acesso baseado em funções Azure (Azure RBAC)](role-based-access-control.md) com o papel de "Azure Cosmos DB operator" para definir a produção nas suas contas Azure Cosmos.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais e descarregue a amostra do programador de [saída Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler).