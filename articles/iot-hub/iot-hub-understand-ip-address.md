---
title: Noções básicas sobre o endereço IP do seu hub IoT | Microsoft Docs
description: Entenda como consultar seu endereço IP do Hub IoT e suas propriedades. O endereço IP do Hub IoT pode ser alterado durante determinados cenários, como recuperação de desastre ou failover regional.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 5c77fb30ef60c1ad82d0a87442bc8af186c54321
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383901"
---
# <a name="iot-hub-ip-addresses"></a>Endereços IP do Hub IoT

Os prefixos de endereço IP do Hub IoT são publicados periodicamente na [marca de serviço](../virtual-network/service-tags-overview.md) *AzureIoTHub* . Para garantir a operação correta, seus dispositivos IoT devem ter conectividade de saída para os prefixos de endereço listados em marca de serviço *AzureIoTHub* . Os serviços de aplicativo IoT precisam, adicionalmente, ter conectividade de saída para os prefixos de endereço listados na marca de serviço do *EventHub* .


## <a name="best-practices"></a>Melhores práticas

* Os prefixos de endereço IP do Hub IoT estão sujeitos a alterações. Essas alterações são publicadas periodicamente por meio de marcas de serviço antes de entrar em vigor. Portanto, é importante que você desenvolva processos para recuperar e usar regularmente as marcas de serviço mais recentes. Esse processo pode ser automatizado por meio da [API de descoberta de marcas de serviço](../virtual-network/service-tags-overview.md#service-tags-in-on-premises).
* Use o *AzureIoTHub. [ nome da região]* para identificar os prefixos IP usados pelos pontos de extremidade do Hub IOT em uma região específica. Para considerar a recuperação de desastres do Datacenter ou o [failover regional](iot-hub-ha-dr.md) , verifique se a conectividade com os prefixos de IP da região de par geográfico de seu hub IOT também está habilitada.


## <a name="support-for-ipv6"></a>Suporte para IPv6 

No momento, não há suporte para IPv6 no Hub IoT.