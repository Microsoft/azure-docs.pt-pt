---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 482241deb1081ac8a5265a076eabbdc3fb6d659e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66170878"
---
Embora a utilização das rotas de sistema facilita o tráfego automaticamente para a implementação, há casos em que pretende controlar o encaminhamento de pacotes através de uma aplicação virtual. Pode fazê-lo ao criar rotas definidas pelo utilizador que especificam o salto seguinte para os pacotes que fluem para uma sub-rede específica para aceder à sua aplicação virtual disso e ao ativar o reencaminhamento IP para a VM que funciona como aplicação virtual.

Alguns dos casos em que podem ser utilizadas aplicações virtuais incluem:

* Monitorizar o tráfego com um sistema de detecção de intrusões (IDS)
* Controlar o tráfego com uma firewall

Para obter mais informações sobre o reencaminhamento de IP e a UDR, visite [rotas definidas pelo utilizador e reencaminhamento IP](../articles/virtual-network/virtual-networks-udr-overview.md).

