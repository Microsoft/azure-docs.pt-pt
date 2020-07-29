---
title: Configurar diagnósticos
titleSuffix: Azure Digital Twins
description: Veja como ativar o registo com as definições de diagnóstico.
author: baanders
ms.author: baanders
ms.date: 7/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 60ee2102a2b725c68e29afec9a44de03e52e9467
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374569"
---
# <a name="enable-logging-with-diagnostics-settings"></a>Ativar o registo com definições de diagnóstico

Pode optar por enviar os seus dados de métricas para [Log Analytics](../azure-monitor/log-query/get-started-portal.md), um ponto final do [Event Hubs](../event-hubs/event-hubs-about.md) ou [para o Azure Storage,](../storage/blobs/storage-blobs-overview.md) permitindo o registo com definições de diagnóstico para o seu exemplo.

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Ligue as definições de diagnóstico com o portal Azure

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para a sua instância Azure Digital Twins. Pode encontrá-lo digitando o seu nome na barra de pesquisa do portal. 

2. Selecione **as definições** de diagnóstico do menu e, em seguida, **adicione a definição de diagnóstico**.

    :::image type="content" source="media/how-to-view-metrics/diagnostic-settings.png" alt-text="Screenshot mostrando a página e botão de definições de diagnóstico para adicionar":::

3. Na página que se segue, preencha os seguintes valores:
 * **Nome de definição de**diagnóstico : Dê um nome às definições de diagnóstico.
 * **Detalhes da categoria**: Escolha quais as operações que pretende monitorizar e verifique as caixas para permitir diagnósticos para essas operações. As operações que as definições de diagnóstico podem reportar são:
    - DigitalTwinsOperação
    - EventRoutesOperação
    - ModelosOperação
    - Consulta
    - AllMetrics
 * **Detalhes do destino**: Escolha para onde pretende enviar os registos. Pode selecionar qualquer combinação das três opções:
    - Enviar para o Log Analytics
    - Arquivar numa conta de armazenamento
    - Transmitir em fluxo para um hub de eventos

    Pode ser-lhe pedido que preencha detalhes adicionais se forem necessários para a sua seleção de destino.  
    
4. Guarde as novas definições. 

    :::image type="content" source="media/how-to-set-up-diagnostics/diagnostic-settings-details.png" alt-text="Screenshot mostrando a página e botão de definições de diagnóstico para adicionar":::

As novas definições fazem efeito em cerca de 10 minutos. Depois disso, os registos aparecem no alvo configurado de volta na página **de definições de Diagnóstico,** para o seu exemplo. 

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a configuração de diagnósticos, consulte [*recolher e consumir dados de registo dos seus recursos Azure.*](../azure-monitor/platform/platform-logs-overview.md)
* Para obter informações sobre as métricas Azure Digital Twins, consulte [ *Como fazer: Ver métricas com o Azure Monitor*](how-to-view-metrics.md)