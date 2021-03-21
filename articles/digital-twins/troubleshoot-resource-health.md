---
title: Compreender o estado de funcionamento dos recursos
titleSuffix: Azure Digital Twins
description: Veja como usar a Azure Resource Health para verificar a saúde da sua instância Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 10/6/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b64ee4d1538276bbbcab3fe09054f399ad20c162
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94616554"
---
# <a name="troubleshooting-azure-digital-twins-resource-health"></a>Resolução de problemas Azure Digital Twins: Saúde de recursos

[A Azure Resource Health](../service-health/resource-health-overview.md) ajuda-o a diagnosticar e a obter apoio para problemas de serviço que afetam os seus recursos Azure. Relata a saúde atual e passada dos seus recursos.

Este artigo mostra-lhe como obter informações de **saúde** de recursos para as suas instâncias Azure Digital Twins.

## <a name="use-azure-resource-health"></a>Utilizar a Azure Resource Health

A Azure Resource Health pode ajudá-lo a monitorizar se a sua instância Azure Digital Twins está em funcionamento. Também pode usá-lo para saber se uma paralisação regional está a afetar a saúde do seu caso.

Para verificar a saúde do seu caso, siga estes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para a sua instância Azure Digital Twins. Pode encontrá-lo digitando o seu nome na barra de pesquisa do portal. 

2. No menu do seu caso, selecione _**a saúde de recursos**_ em Suporte + *resolução de problemas*. Isto irá levá-lo à página para visualizar o histórico de saúde dos recursos. 

    :::image type="content" source="media/troubleshoot-resource-health/resource-health.png" alt-text="Screenshot mostrando a página 'Resource health'. Há uma secção de &quot;história da saúde&quot; que mostra um relatório diário dos últimos nove dias. Cada dia mostra um estado de 'Disponível'.":::

Na imagem acima, este caso está a mostrar-se *como Disponível*, e tem sido nos últimos nove dias. Para saber mais sobre o estado *disponível* e os outros tipos de estado que possam aparecer, consulte [*a visão geral da saúde dos recursos Azure*](../service-health/resource-health-overview.md).

Também pode aprender mais sobre os diferentes controlos que vão para a saúde dos recursos para diferentes tipos de recursos Azure em tipos de [*recursos e verificações de saúde na saúde dos recursos Azure.*](../service-health/resource-health-checks-resource-types.md)

## <a name="next-steps"></a>Passos seguintes

Leia sobre outras formas de monitorizar a sua instância Azure Digital Twins nos seguintes artigos:
* [*Resolução de problemas: Ver métricas com monitor Azure*](troubleshoot-metrics.md)
* [*Resolução de problemas: Configurar diagnósticos*](troubleshoot-diagnostics.md).
* [*Resolução de problemas: Configurar alertas*](troubleshoot-alerts.md)
