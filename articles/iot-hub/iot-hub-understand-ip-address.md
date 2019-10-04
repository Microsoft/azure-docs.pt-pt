---
title: Noções básicas sobre o endereço IP do seu hub IoT | Microsoft Docs
description: Entenda como consultar seu endereço IP do Hub IoT e suas propriedades. O endereço IP do Hub IoT pode ser alterado durante determinados cenários, como recuperação de desastre ou failover regional.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 7af40404550fb78af891563d8256f23620781b24
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841536"
---
# <a name="understanding-the-ip-address-of-your-iot-hub"></a>Noções básicas sobre o endereço IP do Hub IoT

O endereço IP do Hub IoT é um ponto de extremidade público com balanceamento de carga para o Hub e não para um endereço IP dedicado. O endereço é determinado pelo intervalo de endereços de rede da região do Azure onde ele é implantado. Este endereço IP está sujeito a alterações sem aviso prévio. Atualizações de rede do datacenter, recuperação de desastres do Datacenter ou failover regional de um hub IoT podem alterar o endereço IP do Hub IoT. Consulte [alta disponibilidade e recuperação de desastre do Hub IOT](iot-hub-ha-dr.md) para obter mais detalhes sobre o failover regional e a disponibilidade do Hub IOT do Azure.

O endereço IP do Hub IoT é alterado após um failover para outra região. Você pode testar essa funcionalidade seguindo o tutorial [executar um failover manual de um hub IOT](tutorial-manual-failover.md).

## <a name="discover-your-iot-hub-ip-address"></a>Descobrir o endereço IP do Hub IoT

O endereço IP do Hub IoT pode ser descoberto usando uma pesquisa reversa de DNS no CNAME ([*IOT-Hub-Name*]. Azure-Devices.net). Você pode usar o **nslookup** para verificar o endereço IP de uma instância do Hub IOT:

```cmd/sh
nslookup {YourIoTHubName}.azure-devices.net
```

Esse endereço IP pode ser alterado sem aviso prévio. Em um cenário de failover ou de recuperação de desastre, você precisará sondar o endereço IP do Hub IoT na região secundária.

## <a name="outbound-firewall-rules-for-iot-hub"></a>Regras de firewall de saída para o Hub IoT

Tente criar regras de firewall e filtragem com base no nome ou domínio do host do Hub IoT. Se você só pode permitir o tráfego de saída para endereços específicos, pesquise o endereço IP do Hub IoT regularmente e atualize suas regras de firewall.

## <a name="support-for-ipv6"></a>Suporte para IPv6 

No momento, não há suporte para IPv6 no Hub IoT.