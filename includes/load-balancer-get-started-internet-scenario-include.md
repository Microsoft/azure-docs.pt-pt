---
author: WenJason
ms.service: load-balancer
ms.topic: include
origin.date: 11/09/2018
ms.date: 12/31/2018
ms.author: v-jay
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60516960"
---
As seguintes tarefas serão efetuadas neste cenário:

* Criar um balanceador de carga que recebe o tráfego de rede na porta 80 e enviar o tráfego com balanceamento de carga para as máquinas virtuais "web1" e "web2"
* Criar regras NAT para acesso/SSH de ambiente de trabalho remoto para máquinas virtuais por trás do balanceador de carga
* Criar sondas do estado de funcionamento

![Cenário do Balanceador de carga](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)