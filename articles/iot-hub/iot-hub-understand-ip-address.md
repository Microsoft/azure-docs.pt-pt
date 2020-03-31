---
title: Compreender o endereço IP do seu hub IoT [ Microsoft Docs
description: Entenda como consultar o seu endereço IP do hub IoT e as suas propriedades. O endereço IP do seu hub IoT pode mudar durante certos cenários, tais como recuperação de desastres ou falha regional.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: c609f2a3843481442e97061739a806de60a680b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367572"
---
# <a name="iot-hub-ip-addresses"></a>Endereços IP do IoT Hub

Os prefixos de endereço IP dos pontos finais públicos do IoT Hub são publicados periodicamente sob a etiqueta de [serviço](../virtual-network/service-tags-overview.md) _AzureIoTHub_ .

> [!NOTE]
> Para dispositivos que são implantados dentro das redes no local, o Azure IoT Hub suporta a integração da conectividade VNET com pontos finais privados. Consulte o suporte do IoT Hub para obter mais informações sobre o [VNET's.](./virtual-network-support.md#ingress-connectivity-to-iot-hub-using-private-endpoints)


Pode utilizar estes prefixos de endereço IP para controlar a conectividade entre o IoT Hub e os seus dispositivos ou ativos de rede, a fim de implementar uma variedade de objetivos de isolamento da rede:

| Objetivo | Cenários aplicáveis | Abordagem |
|------|-----------|----------|
| Certifique-se de que os seus dispositivos e serviços comunicam apenas com pontos finais do Hub IoT | [Dispositivo-a-nuvem](./iot-hub-devguide-messaging.md), e mensagens [cloud-to-device,](./iot-hub-devguide-messages-c2d.md) [métodos diretos,](./iot-hub-devguide-direct-methods.md) [gémeos de dispositivo e módulo](./iot-hub-devguide-device-twins.md) e fluxos de [dispositivos](./iot-hub-device-streams-overview.md) | Utilize etiquetas de serviço _AzureIoTHub_ e _EventHub_ para descobrir os prefixos de endereço Ip do IoT Hub e do Event Hub IP e configurar as regras de PERMIT na definição de firewall dos seus dispositivos e serviços para esses prefixos de endereço IP em conformidade; deixe cair o tráfego para outros endereços IP de destino com os quais não deseja que os dispositivos ou serviços se comuniquem. |
| Certifique-se de que o seu ponto final do dispositivo IoT Hub recebe ligações apenas a partir dos seus dispositivos e ativos de rede | [Dispositivo-a-nuvem](./iot-hub-devguide-messaging.md), e mensagens [cloud-to-device,](./iot-hub-devguide-messages-c2d.md) [métodos diretos,](./iot-hub-devguide-direct-methods.md) [gémeos de dispositivo e módulo](./iot-hub-devguide-device-twins.md) e fluxos de [dispositivos](./iot-hub-device-streams-overview.md) | Utilize a [função](iot-hub-ip-filtering.md) de filtro IP IoT Hub para permitir ligações dos seus dispositivos e endereços IP do ativo da rede (ver secção [de limitações).](#limitations-and-workarounds) | 
| Certifique-se de que os recursos de ponto final personalizados das suas rotas (contas de armazenamento, autocarros de serviço e centros de eventos) são acessíveis apenas a partir dos seus ativos de rede | [Encaminhamento de mensagens](./iot-hub-devguide-messages-d2c.md) | Siga a orientação do seu recurso sobre restringir a conectividade (por exemplo, através de regras de [firewall,](../storage/common/storage-network-security.md) [links privados](../private-link/private-endpoint-overview.md)ou [pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md)de serviço); utilize etiquetas de serviço _AzureIoTHub_ para descobrir prefixos de endereço IP do IoT Hub e adicionar regras DE ALLOW para os prefixos IP na configuração de firewall do seu recurso (ver secção [de limitações).](#limitations-and-workarounds) |



## <a name="best-practices"></a>Melhores práticas

* Ao adicionar regras DE ALLOW na configuração de firewall dos seus dispositivos, o melhor é fornecer [portas específicas utilizadas pelos protocolos aplicáveis](./iot-hub-devguide-protocols.md#port-numbers).

* Os prefixos de endereço IP do centro IoT estão sujeitos a alterações. Estas alterações são publicadas periodicamente através de etiquetas de serviço antes de produzir em vigor. Por isso, é importante que desenvolva processos para recuperar e utilizar regularmente as mais recentes etiquetas de serviço. Este processo pode ser automatizado através da descoberta de etiquetas de [serviço API](../virtual-network/service-tags-overview.md#service-tags-on-premises). Note que a descoberta de etiquetas de serviço API ainda está em pré-visualização e em alguns casos pode não produzir a lista completa de tags e endereços IP. Até que a API esteja geralmente disponível, considere utilizar as etiquetas de [serviço no formato JSON descarregado.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) 

* Use o *AzureIoTHub.. nome da região]* etiqueta para identificar prefixos IP utilizados por pontos finais do hub IoT numa região específica. Para responder à recuperação de desastres do datacenter, ou [falha regional](iot-hub-ha-dr.md) garantir conectividade com prefixos IP da região geo-par do seu IoT Hub também está ativado.

* A criação de regras de firewall no IoT Hub pode bloquear a conectividade necessária para executar comandos Azure CLI e PowerShell contra o seu Hub IoT. Para evitar isto, pode adicionar regras de ALLOW para os prefixos de endereço IP dos seus clientes para reativar os clientes CLI ou PowerShell para comunicar com o seu Hub IoT.  


## <a name="limitations-and-workarounds"></a>Limitações e suposições

* A função de filtro Ip IoT Hub tem um limite de 10 regras. Este limite pode ser aumentado através de pedidos através do Apoio ao Cliente azure. 

* As suas regras de [filtragem IP](iot-hub-ip-filtering.md) configuradas só são aplicadas nos pontos finais ip do ioT Hub e não no ponto final do Hub de Eventos integrado do seu hub IoT. Se também necessitar de filtragem IP no Centro de Eventos onde as suas mensagens são armazenadas, poderá fazê-lo trazendo o seu próprio recurso Do Event Hub, onde pode configurar diretamente as regras de filtragem IP desejadas. Para tal, precisa de fornecer o seu próprio recurso Do Event Hub e configurar [o encaminhamento](./iot-hub-devguide-messages-d2c.md) de mensagens para enviar as suas mensagens para esse recurso em vez do Hub de Eventos integrado do seu IoT Hub. Finalmente, conforme discutido na tabela acima, para permitir a funcionalidade de encaminhamento de mensagens, também precisa de permitir a conectividade dos prefixos ip do IoT Hub ao seu recurso De evento hub.

* Ao encaminhar para uma conta de armazenamento, permitir o tráfego dos prefixos de endereço IP do IoT Hub só é possível quando a conta de armazenamento estiver numa região diferente do seu Hub IoT.

## <a name="support-for-ipv6"></a>Suporte para IPv6 

O IPv6 não é atualmente suportado no IoT Hub.
