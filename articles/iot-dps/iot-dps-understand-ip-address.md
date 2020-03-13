---
title: Compreender o endereço IP do seu serviço de fornecimento de dispositivos IoT (DPS) Microsoft Docs
description: Compreenda como consultar o seu endereço ioT Device Provisioning Service (DPS) e as suas propriedades. O endereço IP da sua instância dPS pode mudar durante certos cenários, tais como recuperação de desastres ou falha regional.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284931"
---
# <a name="iot-hub-dps-ip-addresses"></a>Endereços IP do IoT Hub DPS

Os prefixos de endereço IP para os pontos finais públicos de um Serviço de Provisionamento de Dispositivos IoT Hub (DPS) são publicados periodicamente sob a etiqueta de [serviço](../virtual-network/service-tags-overview.md) _AzureIoTHub_ . Pode utilizar estes prefixos de endereço IP para controlar a conectividade entre uma instância de DPS IoT e dispositivos ou ativos de rede, a fim de implementar uma variedade de objetivos de isolamento da rede:

| Objetivo | Abordagem |
|------|----------|
| Certifique-se de que os seus dispositivos e serviços comunicam apenas com pontos finais Do IoT Hub DPS | Utilize a etiqueta de serviço _AzureIoTHub_ para descobrir as instâncias de DPS do Hub IoT. Configure as regras de ALLOW na definição de firewall dos seus dispositivos e serviços para esses prefixos de endereço IP em conformidade. Configure as regras para deixar o tráfego para outros endereços IP de destino com os quais não deseja que dispositivos ou serviços se comuniquem. |
| Certifique-se de que o seu ponto final Do IoT Hub DPS recebe ligações apenas a partir dos seus dispositivos e ativos de rede | Utilize a [função](iot-dps-ip-filtering.md) de filtro IP IoT DPS para criar regras de filtro para o dispositivo e apis de serviço DPS. Estas regras de filtro podem ser utilizadas para permitir ligações apenas a partir dos seus dispositivos e endereços IP do ativo da rede (ver secção [de limitações).](#limitations-and-workarounds) | 




## <a name="best-practices"></a>Melhores práticas

* Ao adicionar regras DE ALLOW na configuração de firewall dos seus dispositivos, o melhor é fornecer [portas específicas utilizadas pelos protocolos aplicáveis](../iot-hub/iot-hub-devguide-protocols.md#port-numbers).

* Os prefixos do endereço IP dos casos de DPS ioT estão sujeitos a alterações. Estas alterações são publicadas periodicamente através de etiquetas de serviço antes de produzir em vigor. Por isso, é importante que desenvolva processos para recuperar e utilizar regularmente as mais recentes etiquetas de serviço. Este processo pode ser automatizado através da descoberta de etiquetas de [serviço API](../virtual-network/service-tags-overview.md#service-tags-on-premises). A Descoberta de Etiquetas de Serviço API ainda está em pré-visualização e em alguns casos pode não produzir a lista completa de tags e endereços IP. Até que a API esteja geralmente disponível, considere utilizar as etiquetas de [serviço no formato JSON descarregado.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) 

* Use o *AzureIoTHub. nome da região]* etiqueta para identificar prefixos IP utilizados por pontos finais DPS numa região específica. Para ter em conta a recuperação de desastres do datacenter, ou [falha regional,](../iot-hub/iot-hub-ha-dr.md)garantir a conectividade com os prefixos IP da região geo-par da sua instância DPS também está ativada.

* A criação de regras de firewall para uma instância de DPS pode bloquear a conectividade necessária para executar comandos Azure CLI e PowerShell contra ele. Para evitar estes problemas de conectividade, pode adicionar regras de ALLOW para os prefixos de endereço IP dos seus clientes para reativar os clientes CLI ou PowerShell para comunicar com a sua instância DPS.  


## <a name="limitations-and-workarounds"></a>Limitações e suposições

* A função de filtro DPS IP tem um limite de 100 regras. Este limite pode ser aumentado através de pedidos através do Apoio ao Cliente azure. 

* As suas regras de [filtragem IP](iot-dps-ip-filtering.md) configuradas só são aplicadas nos pontos finais do DPS e não nos pontos finais do IoT Hub ligados. A filtragem IP para centros IoT ligados deve ser configurada separadamente. Para mais informações, consulte, regras de [filtragem IP Do IoT Hub](../iot-hub/iot-hub-ip-filtering.md).

## <a name="support-for-ipv6"></a>Suporte para IPv6 

O IPv6 não é atualmente suportado no IoT Hub ou DPS.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as configurações de endereçoIP com DPS, consulte:

* [Configurar a filtragem de IPs](iot-dps-ip-filtering.md)
