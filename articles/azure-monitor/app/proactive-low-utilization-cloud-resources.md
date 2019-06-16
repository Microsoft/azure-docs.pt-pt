---
title: Deteção - pouca utilização de recursos de cloud detetadas pelo Azure Application Insights inteligente | Documentos da Microsoft
description: Monitorizar aplicações com o Azure Application Insights para pouca utilização de recursos da cloud.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 7cf72068b9cabceb0c5b535986ac4dfb62151b94
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61297519"
---
# <a name="low-cpu-utilization-in-cloud-resources-preview"></a>Baixa utilização da CPU nos recursos de nuvem (pré-visualização)

Application Insights automaticamente analisa o consumo de CPU de cada instância de função na sua aplicação e Deteta instâncias com baixa utilização da CPU. Esta deteção permite-lhe reduzir os recursos do Azure e reduzir os custos, ao reduzir o número de instâncias de função de que cada função utiliza ou ao reduzir o número de funções.

Esta funcionalidade não requer nenhuma configuração especial, que [configurando contadores de desempenho](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) para a sua aplicação. Ele está ativo quando a sua aplicação gerar a telemetria de contador de desempenho suficiente da CPU (% de tempo do processador).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando é que eu teria este tipo de notificação de deteção inteligente?
Uma notificação típica ocorre quando muitas das suas instâncias de função da Web/trabalho apresentam pouca utilização de CPU.

## <a name="does-my-app-definitely-consume-too-many-resources"></a>Meu aplicativo definitivamente consumir demasiados recursos?
Não, uma notificação não significa que seu aplicativo consome definitivamente demasiados recursos. Embora tais padrões de utilização da CPU baixa normalmente indicam que pode diminuir o consumo de recursos, esse comportamento pode ser típico para a sua função específica, ou pode ter uma justificativa comercial natural e pode ser ignorado. Por exemplo, pode ser que várias instâncias são necessárias para outros recursos, como memória/rede e não da CPU.

## <a name="how-do-i-fix-it"></a>Como posso corrigi-lo?
As notificações incluem informações de diagnóstico para suportar o processo de diagnóstico:
1. **Triagem.** A notificação mostra as funções na sua aplicação que apresentam pouca utilização de CPU. Isto pode ajudar a atribuir uma prioridade para o problema.
2. **Âmbito.** Quantas funções apresentados baixa utilização da CPU e quantas instâncias de cada função utilizem CPU baixa? Estas informações podem ser obtidas a partir da notificação.
3. **Diagnosticar.** A deteção contém a percentagem de CPU utilizada, que mostra a utilização da CPU de cada instância ao longo do tempo. Também pode utilizar os relatórios de ligação para informações, tais como percentis da utilização da CPU, de suporte e itens relacionados para o ajudar a diagnosticar melhor o problema.
