---
title: Configurar diagnósticos
titleSuffix: Azure Digital Twins
description: Veja como ativar o registo com as definições de diagnóstico.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 0376a57e3f2c1158e9da97fb291a28c99ce2463c
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87903969"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Resolução de problemas Azure Digital Twins: Registo de diagnósticos

A Azure Digital Twins recolhe [métricas](troubleshoot-metrics.md) para a sua instância de serviço que dão informações sobre o estado dos seus recursos. Você pode usar estas métricas para avaliar a saúde geral do serviço Azure Digital Twins e os recursos ligados a ele. Estas estatísticas viradas para o utilizador ajudam-no a ver o que se passa com as suas Gémeas Digitais Azure e ajudam a realizar análises de causa-raiz em problemas sem necessidade de contactar o suporte do Azure.

Este artigo mostra-lhe como ligar **o registo de diagnósticos** para os seus dados de métricas a partir da sua instância Azure Digital Twins. Pode utilizar estes registos para ajudá-lo a resolver problemas de serviço e configurar definições de diagnóstico para enviar métricas Azure Digital Twins para diferentes destinos. Pode ler mais sobre estas definições na [*Criar definições de diagnóstico para enviar registos e métricas da plataforma para diferentes destinos.*](../azure-monitor/platform/diagnostic-settings.md)

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Ligue as definições de diagnóstico com o portal Azure

Eis como ativar as definições de diagnóstico para a sua instância Azure Digital Twins:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para a sua instância Azure Digital Twins. Pode encontrá-lo digitando o seu nome na barra de pesquisa do portal. 

2. Selecione **as definições** de diagnóstico do menu e, em seguida, **adicione a definição de diagnóstico**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Screenshot mostrando a página e botão de definições de diagnóstico para adicionar":::

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

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Screenshot mostrando a página e botão de definições de diagnóstico para adicionar":::

As novas definições fazem efeito em cerca de 10 minutos. Depois disso, os registos aparecem no alvo configurado de volta na página **de definições de Diagnóstico,** para o seu exemplo. 

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a configuração de diagnósticos, consulte [*recolher e consumir dados de registo dos seus recursos Azure.*](../azure-monitor/platform/platform-logs-overview.md)
* Para obter informações sobre as métricas Azure Digital Twins, consulte [*resolução de problemas: Ver métricas com O Monitor Azure*](troubleshoot-metrics.md).
* Para ver como ativar alertas para as suas métricas, consulte [*Resolução de Problemas: Configurar alertas*](troubleshoot-alerts.md).
