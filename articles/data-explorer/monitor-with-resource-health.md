---
title: Monitor Azure Data Explorer usando a Saúde dos Recursos
description: Utilize a Azure Resource Health para monitorizar os recursos do Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: prvavill
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 9b8b1b40e972d7719ef2cf495ed12cb4ed3478fd
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479373"
---
# <a name="monitor-azure-data-explorer-using-resource-health-preview"></a>Monitor Azure Data Explorer utilizando a Saúde dos Recursos (Pré-visualização)

[A Resource Health](/azure/service-health/resource-health-overview) for Azure Data Explorer informa-o da saúde do seu recurso Azure Data Explorer e fornece recomendações atos para resolver problemas. A saúde dos recursos é atualizada a cada 1-2 minutos e reporta a saúde atual e passada dos seus recursos. 

A Resource Health determina a saúde do seu recurso Azure Data Explorer examinando vários controlos de estado de saúde tais como:
* Disponibilidade dos recursos
* Falhas de consulta

## <a name="access-resource-health-reporting"></a>Relatório de saúde de recursos de acesso

1. No [portal Azure,](https://portal.azure.com/)selecione **Monitor** da lista de serviços.
1. Selecione **saúde de recursos** > de**saúde**de serviço.
1. No dropdown **da Subscrição,** selecione a sua subscrição. No **dropdown** do tipo Recurso, selecione **Azure Data Explorer**.
1. A tabela resultante lista todos os recursos na subscrição escolhida. Cada recurso terá um ícone do estado de saúde indicando a saúde dos recursos.
1. Selecione o seu recurso para ver o seu estado de [saúde](#resource-health-status) de recursos e recomendações.

    ![Descrição geral](media/monitor-with-resource-health/resource-health-overview.png)

## <a name="resource-health-status"></a>Estado de saúde dos recursos

A saúde de um recurso é exibida com um dos seguintes estatutos, disponíveis, indisponíveis e desconhecidos.

### <a name="available"></a>Disponível

Um estado de saúde do **Disponível** indica que o seu recurso Azure Data Explorer é saudável e não tem quaisquer problemas.

![Disponível](media/monitor-with-resource-health/available.png)

### <a name="unavailable"></a>Indisponível

Um estado de saúde do **Indisponível** indica que há um problema contínuo com o seu recurso Azure Data Explorer que faz com que não esteja disponível para consultas e ingestão. Por exemplo, os nós no seu recurso Azure Data Explorer podem ter reiniciado inesperadamente. Se o seu recurso Azure Data Explorer permanecer neste estado por um longo período de tempo, [suporte]()de contato .

![Indisponível](media/monitor-with-resource-health/unavailable.png)

### <a name="unknown"></a>Desconhecido

Um estado de saúde de **Unknown** indica que a **Saúde** de Recursos não recebe informações sobre este recurso Azure Data Explorer há mais de 10 minutos. Este estado não é uma indicação definitiva da saúde dos recursos do Azure Data Explorer, mas é um importante ponto de dados no processo de resolução de problemas. Se o seu cluster Azure Data Explorer estiver a funcionar como esperado, o estado mudará para **Disponível** dentro de alguns minutos. O estado de saúde **desconhecido** pode sugerir que um evento na plataforma está a afetar o recurso. 

> [!TIP]
> A saúde do cluster Do Azure Data Explorer será **desconhecida** se estiver em estado de paragem.

![Desconhecido](media/monitor-with-resource-health/unknown.png)

## <a name="historical-information"></a>Informação histórica

Na **Saúde dos Recursos** > história da **Saúde,** aceda a até quatro semanas de informação sobre o estado da saúde dos recursos. Selecione a seta para obter informações adicionais sobre os problemas de eventos de saúde relatados neste painel. 

![Histórico](media/monitor-with-resource-health/healthhistory.png)

## <a name="next-steps"></a>Passos seguintes

* [Configurar alertas de saúde de recursos](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide)
* [Tutorial: Ingerir e consultar dados de monitorização no Azure Data Explorer](ingest-data-no-code.md)
* [Monitor Azure Data Explorer desempenho, saúde e uso com métricas](using-metrics.md)