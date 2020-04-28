---
title: Scale Azure Cosmos DB em horário utilizando o temporizador de funções Azure
description: Aprenda a escalar as mudanças na produção em Azure Cosmos DB utilizando funções PowerShell e Azure.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: 68ba40ea212c061fa5c8bbddc47ea0dfc6d8caa4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75935171"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Scale Azure Cosmos DB, utilizando o gatilho do temporizador de funções Azure

O desempenho de uma conta Azure Cosmos baseia-se no montante do rendimento provisionado expresso nas Unidades de Pedido por segundo (RU/s). O fornecimento encontra-se numa segunda granularidade e é faturado com base no RU/s mais elevado por hora. Este modelo de capacidade provisionado permite ao serviço fornecer uma entrada previsível e consistente, baixa latência garantida e elevada disponibilidade. A maioria das cargas de trabalho de produção estas características. No entanto, em ambientes de desenvolvimento e teste onde o Azure Cosmos DB é utilizado apenas durante o horário de trabalho, pode aumentar a produção de manhã e reduzir a escala à noite após o horário de trabalho.

Pode definir a produção através de modelos de gestor de [recursos Azure,](resource-manager-samples.md) [Azure CLI,](cli-samples.md)e [PowerShell,](powershell-samples-sql.md)para contas API Core (SQL) ou utilizando os SDKs Db Do MB em língua específicas. O benefício de usar modelos de gestor de recursos, Azure CLI ou PowerShell é que suportam todas as APIs modelo saem do Modelo Azure Cosmos DB.

## <a name="throughput-scheduler-sample-project"></a>Projeto de amostra de programador de entrada

Para simplificar o processo para escalar o Azure Cosmos DB numa programação, criámos um projeto de amostra chamado Programador de [entradas Azure Cosmos.](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) Este projeto é uma aplicação Azure Functions com dois gatilhos temporizadores: "ScaleUpTrigger" e "ScaleDownTrigger". Os gatilhos executam um script PowerShell que define a `resources.json` entrada em cada recurso, conforme definido no ficheiro em cada gatilho. O ScaleUpTrigger está configurado para ser executado às 8:00 UTC e o ScaleDownTrigger está configurado para ser executado às 18:00 UTC e estes tempos podem ser facilmente atualizados dentro do `function.json` ficheiro para cada gatilho.

Pode clonar este projeto localmente, modificá-lo para especificar os recursos da Azure Cosmos DB para escalar para cima e para baixo e o calendário para executar. Mais tarde, pode implantá-lo numa subscrição Azure e garanti-la utilizando a identidade de serviço gerida com permissões de Controlo de Acesso (RBAC) [baseadas em funções](role-based-access-control.md) com a função "Operador A Db Azure Cosmos" para definir a entrada nas suas contas Azure Cosmos.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais e baixe a amostra do programador de [entrada de Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler).
