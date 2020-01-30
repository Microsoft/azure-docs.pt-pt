---
title: Noções básicas sobre o endereço IP do seu hub IoT | Microsoft Docs
description: Entenda como consultar seu endereço IP do Hub IoT e suas propriedades. O endereço IP do Hub IoT pode ser alterado durante determinados cenários, como recuperação de desastre ou failover regional.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: c5040721705b90a981f1f8a45a3a2eb70eefde05
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772150"
---
# <a name="iot-hub-ip-addresses"></a>Endereços IP do Hub IoT

Os prefixos de endereço IP dos pontos de extremidade públicos do Hub IoT são publicados periodicamente na [marca de serviço](../virtual-network/service-tags-overview.md) _AzureIoTHub_ . Você pode usar esses prefixos de endereço IP para controlar a conectividade entre o Hub IoT e seus dispositivos ou ativos de rede, a fim de implementar uma variedade de metas de isolamento de rede:

| Objetivo | Cenários aplicáveis | Abordagem |
|------|-----------|----------|
| Garantir que seus dispositivos e serviços se comuniquem somente com pontos de extremidade do Hub IoT | Mensagens do [dispositivo para a nuvem](./iot-hub-devguide-messaging.md)e [da nuvem para o dispositivo](./iot-hub-devguide-messages-c2d.md) , [métodos diretos](./iot-hub-devguide-direct-methods.md), [gêmeos de dispositivo e módulo](./iot-hub-devguide-device-twins.md) e [fluxos de dispositivo](./iot-hub-device-streams-overview.md) | Use as marcas de serviço _AzureIoTHub_ e _EventHub_ para descobrir o Hub IOT e prefixos de endereço IP do hub de eventos e configurar regras de permissão na configuração de firewall dos dispositivos e dos serviços para esses prefixos de endereço IP de acordo; Remova o tráfego para outros endereços IP de destino aos quais você não deseja que os dispositivos ou serviços se comuniquem. |
| Verifique se o ponto de extremidade do dispositivo do Hub IoT recebe conexões somente de seus dispositivos e ativos de rede | Mensagens do [dispositivo para a nuvem](./iot-hub-devguide-messaging.md)e [da nuvem para o dispositivo](./iot-hub-devguide-messages-c2d.md) , [métodos diretos](./iot-hub-devguide-direct-methods.md), [gêmeos de dispositivo e módulo](./iot-hub-devguide-device-twins.md) e [fluxos de dispositivo](./iot-hub-device-streams-overview.md) | Use o [recurso filtro de IP](iot-hub-ip-filtering.md) do Hub IOT para permitir conexões de seus dispositivos e endereços IP de ativos de rede (consulte a seção [limitações](#limitations-and-workarounds) ). | 
| Verifique se os recursos de ponto de extremidade personalizados de suas rotas (contas de armazenamento, barramento de serviço e hubs de eventos) estão acessíveis somente dos seus ativos de rede | [Roteamento de mensagens](./iot-hub-devguide-messages-d2c.md) | Siga as diretrizes do recurso para restringir a conectividade (por exemplo, por meio de [regras de firewall](../storage/common/storage-network-security.md), de [links particulares](../private-link/private-endpoint-overview.md)ou de [pontos de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md)); Use marcas de serviço _AzureIoTHub_ para descobrir prefixos de endereço IP do Hub IOT e adicione regras de permissão para esses prefixos de IP na configuração de firewall do seu recurso (consulte a seção [limitações](#limitations-and-workarounds) ). |



## <a name="best-practices"></a>Melhores práticas

* Ao adicionar regras de permissão na configuração de firewall de seus dispositivos, é melhor fornecer [portas específicas usadas por protocolos aplicáveis](./iot-hub-devguide-protocols.md#port-numbers).

* Os prefixos de endereço IP do Hub IoT estão sujeitos a alterações. Essas alterações são publicadas periodicamente por meio de marcas de serviço antes de entrar em vigor. Portanto, é importante que você desenvolva processos para recuperar e usar regularmente as marcas de serviço mais recentes. Esse processo pode ser automatizado por meio da [API de descoberta de marcas de serviço](../virtual-network/service-tags-overview.md#service-tags-on-premises). Note que a descoberta de etiquetas de serviço API ainda está em pré-visualização e em alguns casos pode não produzir a lista completa de tags e endereços IP. Até que a API esteja geralmente disponível, considere utilizar as etiquetas de [serviço no formato JSON descarregado.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) 

* Use o *AzureIoTHub. nome da região]* etiqueta para identificar prefixos IP utilizados por pontos finais do hub IoT numa região específica. Para considerar a recuperação de desastres do Datacenter ou o [failover regional](iot-hub-ha-dr.md) , verifique se a conectividade com os prefixos de IP da região de par geográfico de seu hub IOT também está habilitada.

* A criação de regras de firewall no IoT Hub pode bloquear a conectividade necessária para executar comandos Azure CLI e PowerShell contra o seu Hub IoT. Para evitar isto, pode adicionar regras de ALLOW para os prefixos de endereço IP dos seus clientes para reativar os clientes CLI ou PowerShell para comunicar com o seu Hub IoT.  


## <a name="limitations-and-workarounds"></a>Limitações e soluções alternativas

* O recurso filtro de IP do Hub IoT tem um limite de 10 regras. Esse limite e pode ser gerado por meio de solicitações por meio do atendimento ao cliente do Azure. 

* Suas [regras de filtragem de IP](iot-hub-ip-filtering.md) configuradas são aplicadas somente nos pontos de extremidade de IP do Hub IOT e não no ponto de extremidades interno do hub de eventos do Hub IOT. Se você também exigir que a filtragem de IP seja aplicada no Hub de eventos em que as mensagens estão armazenadas, você pode fazer isso, trazendo seu próprio recurso de Hub de eventos, no qual você pode configurar as regras de filtragem de IP desejadas diretamente. Para fazer isso, você precisa provisionar seu próprio recurso de Hub de eventos e configurar o [Roteamento de mensagens](./iot-hub-devguide-messages-d2c.md) para enviar suas mensagens para esse recurso em vez do hub de eventos interno do Hub IOT. Por fim, conforme discutido na tabela acima, para habilitar a funcionalidade de roteamento de mensagens, você também precisará permitir a conectividade de prefixos de endereço IP do Hub IoT para o recurso de Hub de eventos provisionado.

* Ao rotear para uma conta de armazenamento, permitir o tráfego dos prefixos de endereço IP do Hub IoT só é possível quando a conta de armazenamento está em uma região diferente do Hub IoT.

## <a name="support-for-ipv6"></a>Suporte para IPv6 

No momento, não há suporte para IPv6 no Hub IoT.
