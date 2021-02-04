---
title: Conceitos - Monitorizar e reparar a Azure VMware Solution nuvens privadas
description: Saiba como a Azure VMware Solution monitoriza e repara servidores VMware ESXi numa nuvem privada Azure VMware Solution.
ms.topic: conceptual
ms.custom: contperf-fy21q2
ms.date: 02/03/2021
ms.openlocfilehash: 6174df429fd9b21c7f685c8ba14e6d5c0bba4c83
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538963"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Monitore e repare Azure VMware Solução nuvens privadas

A Azure VMware Solution monitoriza continuamente os servidores VMware ESXi numa nuvem privada Azure VMware Solution. 

## <a name="what-azure-vmware-solution-monitors"></a>O que a Azure VMware Solution monitoriza

A Azure VMware Solution monitoriza o seguinte para as condições de falha no hospedeiro:  

- Estado do processador 
- Estado da memória 
- Estado de ligação e energia 
- Estado do ventilador de hardware 
- Perda de conectividade de rede 
- Estado do quadro do sistema de hardware 
- Ocorreram erros no ou dos discos de um hospedeiro vSAN 
- Tensão de hardware 
- Estado da temperatura do hardware 
- Estado de potência de hardware 
- Estado de armazenamento 
- Falha de ligação 

> [!NOTE]
> Os administradores inquilinos da Azure VMware Solution não devem editar ou eliminar os alarmes VMware vCenter acima definidos, uma vez que estes são geridos pelo plano de controlo Azure VMware Solution em vCenter. Estes alarmes são utilizados pela monitorização da Azure VMware Solution para acionar o processo de remediação do anfitrião Azure VMware Solution.

## <a name="azure-vmware-solution-host-remediation"></a>Remediar o anfitrião da Solução VMware Azure  

Quando a Azure VMware Solution deteta uma degradação ou falha num nó Azure VMware Solution na nuvem privada de um inquilino, desencadeia o processo de remediação do hospedeiro. A reparação do hospedeiro envolve a substituição do nó defeituoso por um novo nó saudável.  

O processo de remediação do hospedeiro começa adicionando um novo nó saudável no cluster. Em seguida, quando possível, o hospedeiro defeituoso é colocado no modo de manutenção VMware vSphere. VMware vMotion é usado para mover os VMs do hospedeiro defeituoso para outros servidores disponíveis no cluster, potencialmente permitindo a migração ao vivo zero de cargas de trabalho. Em cenários em que o hospedeiro defeituoso não pode ser colocado em modo de manutenção, o hospedeiro é removido do cluster.

## <a name="next-steps"></a>Passos seguintes

Agora que cobriu a forma como a Azure VMware Solution monitoriza e repara nuvens privadas, talvez queira saber:

- [Upgrades de nuvem privada Azure VMware Solution](concepts-upgrades.md).
- [Como ativar o recurso Azure VMware Solution](enable-azure-vmware-solution.md).
