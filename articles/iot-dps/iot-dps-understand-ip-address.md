---
title: Compreender o endereço IP do seu serviço de fornecimento de dispositivos IoT (DPS) | Microsoft Docs
description: Compreenda como consultar o seu endereço IoT Device Provisioning Service (DPS) e as suas propriedades. O endereço IP da sua instância DPS pode ser alterado em certos cenários, tais como recuperação de desastres ou falha regional.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "79284931"
---
# <a name="iot-hub-dps-ip-addresses"></a>Endereços IP DPS do Hub IoT

Os prefixos de endereço IP para os pontos finais públicos de um Serviço de Provisionamento de Dispositivos IoT Hub (DPS) são publicados periodicamente sob a [etiqueta de serviço](../virtual-network/service-tags-overview.md) _AzureIoTHub_ . Pode utilizar estes prefixos de endereço IP para controlar a conectividade entre uma instância DPS IoT e dispositivos ou ativos de rede, de modo a implementar uma variedade de objetivos de isolamento de rede:

| Objetivo | Abordagem |
|------|----------|
| Certifique-se de que os seus dispositivos e serviços comunicam apenas com os pontos finais do IoT Hub DPS | Utilize a etiqueta de serviço _AzureIoTHub_ para descobrir instâncias DPS do IoT Hub. Configurar regras DECONSE NA definição de firewall dos seus dispositivos e serviços para esses prefixos de endereço IP em conformidade. Configure regras para deixar cair o tráfego para outros endereços IP de destino com os quais não pretende que os dispositivos ou serviços se comuniquem. |
| Certifique-se de que o seu ponto final DPS do IoT Hub recebe ligações apenas a partir dos seus dispositivos e ativos de rede | Utilize [a função de filtro IP](iot-dps-ip-filtering.md) IoT DPS para criar regras de filtragem para o dispositivo e APIs de serviço de serviço DPS. Estas regras de filtro podem ser utilizadas para permitir ligações apenas a partir dos seus dispositivos e endereços IP de ativos de rede (ver secção [de limitações).](#limitations-and-workarounds) | 




## <a name="best-practices"></a>Melhores práticas

* Ao adicionar regras ALLOW na configuração de firewall dos seus dispositivos, o melhor é fornecer [portas específicas utilizadas pelos protocolos aplicáveis](../iot-hub/iot-hub-devguide-protocols.md#port-numbers).

* Os prefixos de endereço IP de instâncias DPS IoT estão sujeitos a alterações. Estas alterações são publicadas periodicamente através de etiquetas de serviço antes de fazer efeito. Por isso, é importante que desenvolva processos para recuperar e utilizar regularmente as mais recentes etiquetas de serviço. Este processo pode ser automatizado através da descoberta de etiquetas de [serviço API.](../virtual-network/service-tags-overview.md#service-tags-on-premises) A API de deteção de etiquetas de serviço ainda está em pré-visualização e em alguns casos pode não produzir a lista completa de tags e endereços IP. Até que a API de descoberta esteja geralmente disponível, considere usar as [tags de serviço no formato JSON transferível.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) 

* Use o *AzureIoTHub.[ nome da região]* etiqueta para identificar prefixos IP utilizados por pontos finais DPS numa região específica. Para ter em conta a recuperação de desastres do datacenter, ou [o failover regional,](../iot-hub/iot-hub-ha-dr.md)garantir a conectividade com os prefixos IP da região de geo-par do seu DPS também está ativado.

* A criação de regras de firewall para uma instância DPS pode bloquear a conectividade necessária para executar os comandos Azure CLI e PowerShell contra ele. Para evitar estes problemas de conectividade, pode adicionar regras ALLOW para os prefixos de endereço IP dos seus clientes para reecamar os clientes CLI ou PowerShell para comunicarem com a sua instância DPS.  


## <a name="limitations-and-workarounds"></a>Limitações e soluções alternativas

* A função de filtro IP DPS tem um limite de 100 regras. Este limite pode ser levantado através de pedidos através do Apoio ao Cliente da Azure. 

* As suas regras de [filtragem IP](iot-dps-ip-filtering.md) configuradas são aplicadas apenas nos seus pontos finais DPS e não nos pontos finais do Hub IoT ligados. A filtragem IP para os hubs IoT ligados deve ser configurada separadamente. Para obter mais informações, consulte [as regras de filtragem IP do IoT Hub.](../iot-hub/iot-hub-ip-filtering.md)

## <a name="support-for-ipv6"></a>Apoio ao IPv6 

O IPv6 não é atualmente suportado no IoT Hub ou DPS.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre configurações de endereços IP com DPS, consulte:

* [Configurar a filtragem de IPs](iot-dps-ip-filtering.md)
