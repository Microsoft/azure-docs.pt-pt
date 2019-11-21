---
title: Understanding the IP address of your IoT hub | Microsoft Docs
description: Understand how to query your IoT hub IP address and its properties. The IP address of your IoT hub can change during certain scenarios such as disaster recovery or regional failover.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 3370442b29bc04ac2ba68d7346ac1c0a881cc080
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215450"
---
# <a name="understanding-the-ip-address-of-your-iot-hub"></a>Understanding the IP address of your IoT hub

The IP address of your IoT hub is a load-balanced public endpoint for your hub and NOT a dedicated IP address. The address is determined by the network address range of the Azure region where it is deployed. This IP address is subject to change without notice. Datacenter network updates, datacenter disaster recovery, or regional failover of an IoT hub can change your IoT hub IP address. See [IoT Hub high availability and disaster recovery](iot-hub-ha-dr.md) for more detail on Azure IoT Hub regional failover and availability.

The IP address of your IoT hub changes after a failover to another region. You can test this functionality by following the tutorial [Perform a manual failover of an IoT hub](tutorial-manual-failover.md).

## <a name="discover-your-iot-hub-ip-address"></a>Discover your IoT hub IP address

Your IoT Hub IP address can be discovered by using a DNS lookup on the CNAME ([*iot-hub-name*].azure-devices.net). You can use **nslookup** to verify the IP address of an IoT hub instance:

```cmd/sh
nslookup {YourIoTHubName}.azure-devices.net
```

This IP address can change without notice. In a failover or disaster recovery scenario, you'll have to poll your IoT hub IP address in the secondary region.

## <a name="outbound-firewall-rules-for-iot-hub"></a>Outbound firewall rules for IoT hub

Try to create firewall rules and filtering based on the IoT hub host name or domain. If you can only allow outbound traffic to specific addresses, poll your IoT hub IP address regularly and update your firewall rules.

## <a name="support-for-ipv6"></a>Support for IPv6 

IPv6 is currently not supported on IoT Hub.
