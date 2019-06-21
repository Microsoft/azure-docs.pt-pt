---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184682"
---
As seguintes tarefas serão efetuadas neste cenário:

* Criar um balanceador de carga que recebe o tráfego de rede na porta 80 e enviar o tráfego com balanceamento de carga para as máquinas virtuais "web1" e "web2"
* Criar regras NAT para acesso/SSH de ambiente de trabalho remoto para máquinas virtuais por trás do balanceador de carga
* Criar sondas do estado de funcionamento

![Cenário do Balanceador de carga](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)
