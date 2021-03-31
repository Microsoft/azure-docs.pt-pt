---
title: Compreender o endereço IP do seu hub IoT | Microsoft Docs
description: Compreenda como consultar o seu endereço IP do hub IoT e as suas propriedades. O endereço IP do seu hub IoT pode mudar em certos cenários, tais como recuperação de desastres ou falha regional.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 1bfae8c7afbfdc6e73dd8bb17b94e6543361e9ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "83848233"
---
# <a name="iot-hub-ip-addresses"></a>Endereços IP do IoT Hub

Os prefixos de endereço IP dos pontos finais públicos do IoT Hub são publicados periodicamente sob a etiqueta de [serviço](../virtual-network/service-tags-overview.md) _AzureIoTHub_ .

> [!NOTE]
> Para dispositivos que são implantados dentro de redes no local, o Azure IoT Hub suporta a integração de conectividade VNET com pontos finais privados. Consulte [o suporte do IoT Hub para o VNet](./virtual-network-support.md) para obter mais informações.


Pode utilizar estes prefixos de endereço IP para controlar a conectividade entre o IoT Hub e os seus dispositivos ou ativos de rede, de forma a implementar uma variedade de objetivos de isolamento de rede:

| Objetivo | Cenários aplicáveis | Abordagem |
|------|-----------|----------|
| Certifique-se de que os seus dispositivos e serviços comunicam apenas com pontos finais do IoT Hub | [Mensagens dispositivo-nuvem](./iot-hub-devguide-messaging.md), e [mensagens nuvem-para-dispositivo,](./iot-hub-devguide-messages-c2d.md) [métodos diretos, gémeos](./iot-hub-devguide-direct-methods.md) [de dispositivos e módulos](./iot-hub-devguide-device-twins.md) e [streams de dispositivos](./iot-hub-device-streams-overview.md) | Utilize tags de serviço _AzureIoTHub_ e _EventHub_ para descobrir prefixos de endereço IoT Hub e Event Hub IP e configurar regras de permitir a definição de firewall dos seus dispositivos e serviços para esses prefixos de endereço IP em conformidade; deixar cair o tráfego para outros endereços IP de destino com os quais não pretende que os dispositivos ou serviços se comuniquem. |
| Certifique-se de que o seu ponto final do dispositivo IoT Hub recebe ligações apenas a partir dos seus dispositivos e ativos de rede | [Mensagens dispositivo-nuvem](./iot-hub-devguide-messaging.md), e [mensagens nuvem-para-dispositivo,](./iot-hub-devguide-messages-c2d.md) [métodos diretos, gémeos](./iot-hub-devguide-direct-methods.md) [de dispositivos e módulos](./iot-hub-devguide-device-twins.md) e [streams de dispositivos](./iot-hub-device-streams-overview.md) | Utilize [a função de filtro IP](iot-hub-ip-filtering.md) IoT Hub para permitir ligações dos seus dispositivos e endereços IP de ativos de rede (ver secção de [limitações).](#limitations-and-workarounds) | 
| Certifique-se de que os recursos personalizados das suas rotas (contas de armazenamento, autocarros de serviço e centros de eventos) são alcançáveis apenas a partir dos seus ativos de rede | [Encaminhamento de mensagens](./iot-hub-devguide-messages-d2c.md) | Siga as orientações do seu recurso sobre restringir a conectividade (por exemplo, através de [regras de firewall,](../storage/common/storage-network-security.md) [links privados](../private-link/private-endpoint-overview.md)ou [pontos finais de serviço);](../virtual-network/virtual-network-service-endpoints-overview.md) utilize tags de serviço _AzureIoTHub_ para descobrir prefixos de endereço IP do IoT Hub e adicione regras ALLOW para esses prefixos IP na configuração de firewall do seu recurso (ver secção [de limitações).](#limitations-and-workarounds) |



## <a name="best-practices"></a>Melhores práticas

* Ao adicionar regras ALLOW na configuração de firewall dos seus dispositivos, o melhor é fornecer [portas específicas utilizadas pelos protocolos aplicáveis](./iot-hub-devguide-protocols.md#port-numbers).

* Os prefixos de endereço IP do hub IoT estão sujeitos a alterações. Estas alterações são publicadas periodicamente através de etiquetas de serviço antes de fazer efeito. Por isso, é importante que desenvolva processos para recuperar e utilizar regularmente as mais recentes etiquetas de serviço. Este processo pode ser automatizado através da descoberta de etiquetas de [serviço API.](../virtual-network/service-tags-overview.md#service-tags-on-premises) Note que a API de marcação de serviço ainda está em pré-visualização e em alguns casos pode não produzir a lista completa de tags e endereços IP. Até que a API de descoberta esteja geralmente disponível, considere usar as [tags de serviço no formato JSON transferível.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) 

* Use o *AzureIoTHub.[ nome da região]* etiqueta para identificar prefixos IP usados por pontos finais do hub IoT numa região específica. Para ter em conta a recuperação de desastres do datacenter, ou [a falha regional](iot-hub-ha-dr.md) garante a conectividade com os prefixos IP da região de geo-par do seu IoT Hub também está ativada.

* A definição de regras de firewall no IoT Hub pode bloquear a conectividade necessária para executar comandos Azure CLI e PowerShell contra o seu Hub IoT. Para evitar isto, pode adicionar regras ALLOW para os prefixos de endereço IP dos seus clientes para reequipar os clientes CLI ou PowerShell para comunicarem com o seu Hub IoT.  


## <a name="limitations-and-workarounds"></a>Limitações e soluções alternativas

* A função de filtro IP IoT Hub tem um limite de 10 regras. Este limite pode ser levantado através de pedidos através do Apoio ao Cliente da Azure. 

* As suas regras de [filtragem IP](iot-hub-ip-filtering.md) configuradas são aplicadas apenas nos seus pontos finais IP do Hub IoT e não no ponto final do hub de eventos incorporado do seu hub IoT. Se também necessitar de filtragem IP no Centro de Eventos onde as suas mensagens estão armazenadas, poderá fazê-lo trazendo o seu próprio recurso Event Hub onde pode configurar diretamente as suas regras de filtragem IP desejadas. Para tal, precisa de providenciar o seu próprio recurso Event Hub e [configurar o encaminhamento de mensagens](./iot-hub-devguide-messages-d2c.md) para enviar as suas mensagens para esse recurso em vez do Hub de Eventos incorporado do seu IoT Hub. Finalmente, tal como discutido na tabela acima, para ativar a funcionalidade de encaminhamento de mensagens, também precisa de permitir a conectividade desde os prefixos de endereço IP do IoT Hub ao recurso do Event Hub.

* Quando o encaminhamento para uma conta de armazenamento, permitir o tráfego a partir dos prefixos ip do IoT Hub só é possível quando a conta de armazenamento está em uma região diferente do seu IoT Hub.

## <a name="support-for-ipv6"></a>Apoio ao IPv6 

O IPv6 não é atualmente suportado no IoT Hub.
