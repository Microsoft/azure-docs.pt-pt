---
title: Conceitos - Monitorizar e reparar a Azure VMware Solution nuvens privadas
description: Saiba como a Azure VMware Solution monitoriza e repara servidores VMware ESXi numa nuvem privada Azure VMware Solution.
ms.topic: conceptual
ms.custom: contperf-fy21q2
ms.date: 02/16/2021
ms.openlocfilehash: 59319b5598be9770e82b9676a28444648230a019
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633143"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Monitore e repare Azure VMware Solução nuvens privadas

A Azure VMware Solution monitoriza continuamente os servidores VMware ESXi numa nuvem privada Azure VMware Solution. 

## <a name="what-azure-vmware-solution-monitors"></a>O que a Azure VMware Solution monitoriza

A Azure VMware Solution monitoriza as seguintes condições no anfitrião:  

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

Quando a Azure VMware Solution deteta uma degradação ou falha num nó de Solução VMware Azure, ativa o processo de remediação do hospedeiro. A reparação do hospedeiro envolve a substituição do nó defeituoso por um novo nó saudável.  

A remediação do hospedeiro começa por adicionar um novo nó saudável no cluster. Em seguida, quando possível, o hospedeiro defeituoso é colocado no modo de manutenção VMware vSphere. VMware vMotion move os VMs do hospedeiro defeituoso para outros servidores disponíveis no cluster, potencialmente permitindo zero tempo de inatividade para a migração ao vivo de cargas de trabalho. Se o hospedeiro defeituoso não puder ser colocado em modo de manutenção, o hospedeiro é removido do cluster.

## <a name="next-steps"></a>Passos seguintes

Agora que cobriu a forma como a Azure VMware Solution monitoriza e repara nuvens privadas, talvez queira saber:

- [Upgrades de nuvem privada Azure VMware Solution](concepts-upgrades.md).
- [Como ativar o recurso Azure VMware Solution](enable-azure-vmware-solution.md).
