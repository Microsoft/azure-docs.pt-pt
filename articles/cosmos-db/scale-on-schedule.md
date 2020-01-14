---
title: Dimensionar Azure Cosmos DB em uma agenda usando o temporizador Azure Functions
description: Saiba como dimensionar alterações na taxa de transferência em Azure Cosmos DB usando o PowerShell e o Azure Functions.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: 68ba40ea212c061fa5c8bbddc47ea0dfc6d8caa4
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75935171"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Dimensionar Azure Cosmos DB taxa de transferência usando Azure Functions gatilho de timer

O desempenho de uma conta do Azure cosmos é baseado na quantidade de produtividade provisionada expressa em unidades de solicitação por segundo (RU/s). O provisionamento é de uma granularidade de segundo e é cobrado com base na mais alta RU/s por hora. Esse modelo de capacidade aprovisionada permite que o serviço proporcionar um débito previsível e consistente, a garantia de baixa latência e elevada disponibilidade. A maioria das cargas de trabalho de produção esses recursos. No entanto, em ambientes de desenvolvimento e teste em que Azure Cosmos DB é usado somente durante o horário de trabalho, você pode escalar verticalmente a taxa de transferência na manhã e reduzir a velocidade da noite após o horário de trabalho.

Você pode definir a taxa de transferência por meio de [modelos de Azure Resource Manager](resource-manager-samples.md), [CLI do Azure](cli-samples.md)e [PowerShell](powershell-samples-sql.md), para contas de API de núcleo (SQL) ou usando os SDKs de Azure Cosmos DB específicos à linguagem. O benefício de usar modelos do Resource Manager, CLI do Azure ou PowerShell é que eles dão suporte a todas as APIs de modelo de Azure Cosmos DB.

## <a name="throughput-scheduler-sample-project"></a>Projeto de exemplo do Agendador de produtividade

Para simplificar o processo de dimensionamento de Azure Cosmos DB em uma agenda, criamos um projeto de exemplo chamado [Agendador de produtividade Cosmos do Azure](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler). Este projeto é um aplicativo Azure Functions com dois gatilhos de temporizador: "ScaleUpTrigger" e "ScaleDownTrigger". Os gatilhos executam um script do PowerShell que define a taxa de transferência em cada recurso, conforme definido no arquivo de `resources.json` em cada gatilho. O ScaleUpTrigger é configurado para ser executado às 8:00 UTC e o ScaleDownTrigger é configurado para ser executado às 18:00 UTC e essas horas podem ser facilmente atualizadas dentro do arquivo de `function.json` para cada gatilho.

Você pode clonar esse projeto localmente, modificá-lo para especificar os Azure Cosmos DB recursos para escalar verticalmente e para baixo e a agenda para execução. Posteriormente, você pode implantá-lo em uma assinatura do Azure e protegê-lo usando a identidade de serviço gerenciada com permissões de RBAC ( [controle de acesso baseado em função](role-based-access-control.md) ) com a função "operador de Azure Cosmos DB" para definir a taxa de transferência em suas contas do Azure Cosmos.

## <a name="next-steps"></a>Próximos Passos

- Saiba mais e baixe o exemplo do [Agendador de taxa de transferência Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler).
