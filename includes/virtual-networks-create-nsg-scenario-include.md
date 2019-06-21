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
ms.openlocfilehash: 588aa260f2ece543445bfd4da7ef4682dab8334c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184180"
---
## <a name="scenario"></a>Cenário
Para ilustrar melhor como criar NSGs, este documento usa o seguinte cenário:

![Cenário de VNet](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

Neste cenário, vai criar um NSG para cada sub-rede na **TestVNet** rede virtual, da seguinte forma: 

* **NSG-FrontEnd**. O NSG de front-end é aplicado para o *front-end* sub-rede e contém duas regras:    
  * **rdp-rule**. Permita o tráfego RDP para o *front-end* sub-rede.
  * **web-rule**. Permita o tráfego HTTP para o *front-end* sub-rede.
* **NSG-BackEnd**. O NSG de back-end é aplicado para o *back-end* sub-rede e contém duas regras:    
  * **sql-rule**. Permite o tráfego SQL apenas a partir da *front-end* sub-rede.
  * **web-rule**. Nega o tráfego de vinculado à internet de todos os *back-end* sub-rede.

A combinação dessas regras criar um cenário do tipo de rede de Perímetro, em que a sub-rede de back-end pode apenas de receber tráfego de entrada para o SQL da sub-rede do front-end e não tem acesso à Internet, enquanto a sub-rede do front-end pode comunicar com a Internet e receber pedidos HTTP recebidos apenas.

