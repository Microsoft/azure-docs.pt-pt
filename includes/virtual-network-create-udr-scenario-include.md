---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-network
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 04/13/2018
ms.date: 06/11/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 40b81904daabfdad7e45571d8ab86cf32cac8964
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743387"
---
## <a name="scenario"></a>Cenário
Para melhor ilustrar como criar as UDRs, este documento usa o seguinte cenário:

![DESCRIÇÃO DA IMAGEM](./media/virtual-network-create-udr-scenario-include/figure1.png)

Neste cenário, vai criar um UDR para o *sub-rede de front-end* e outra UDR para o *sub-rede de Back-end*, da seguinte forma: 

* **UDR-FrontEnd**. O UDR front-end é aplicado para o *front-end* sub-rede e contêm uma rota:    
  * **RouteToBackend**. Esta rota envia todo o tráfego para a sub-rede de back-end para o **FW1** máquina virtual.
* **UDR-BackEnd**. O UDR de back-end é aplicado para o *back-end* sub-rede e contêm uma rota:    
  * **RouteToFrontend**. Esta rota envia todo o tráfego para a sub-rede de front-end para o **FW1** máquina virtual.

A combinação destas rotas garante que todo o tráfego destinado a partir de uma sub-rede para outra é encaminhado para o **FW1** máquina virtual, que está a ser utilizada como uma aplicação virtual. Terá também de ativar o IP de reencaminhamento para o **FW1** VM, para garantir que ele pode receber o tráfego destinado a outras VMs.
