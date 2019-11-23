---
title: Understanding the IP address of your IoT hub | Microsoft Docs
description: Understand how to query your IoT hub IP address and its properties. The IP address of your IoT hub can change during certain scenarios such as disaster recovery or regional failover.
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
# <a name="iot-hub-ip-addresses"></a>IoT Hub IP addresses

The IP address prefixes of IoT hub are published periodically under the *AzureIoTHub* [service tag](../virtual-network/service-tags-overview.md). To ensure proper operation, your IoT devices must have outbound connectivity to address prefixes listed under *AzureIoTHub* service tag. Your IoT application services need to additionally have outbound connectivity to address prefixes listed under *EventHub* service tag.


## <a name="best-practices"></a>Melhores práticas

* The IP address prefixes of IoT hub are subject to change. These changes are published periodically via service tags before taking effect. It is therefore important that you develop processes to regularly retrieve and use the latest service tags. This process can be automated via the [service tags discovery API](../virtual-network/service-tags-overview.md#service-tags-in-on-premises).
* Use the *AzureIoTHub.[region name]* tag to identify IP prefixes used by IoT hub endpoints in a specific region. To account for datacenter disaster recovery, or [regional failover](iot-hub-ha-dr.md) ensure connectivity to IP prefixes of your IoT Hub's geo-pair region is also enabled.


## <a name="support-for-ipv6"></a>Support for IPv6 

IPv6 is currently not supported on IoT Hub.