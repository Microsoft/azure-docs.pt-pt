---
title: Conceitos - Monitorizar e reparar a Azure VMware Solution nuvens privadas
description: Saiba como a Azure VMware Solution monitoriza e repara servidores VMware ESXi numa nuvem privada Azure VMware Solution.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 11a3c53bff7ce7b67b677977eddb9829f336672d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684700"
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

## <a name="next-steps"></a>Próximos passos

Aqui estão alguns tópicos sobre os quais talvez queira saber mais sobre:

- [Atualizações de nuvem privada Azure VMware Solution](concepts-upgrades.md)
- [Gestão do ciclo de vida da Azure VMware Solution VMs](lifecycle-management-of-azure-vmware-solution-vms.md)
- [Proteja o seu VMS de Solução VMware Azure com integração do Centro de Segurança Azure](azure-security-integration.md)
- [Backup VMs de Solução VMware Azure com Servidor de Backup Azure](backup-azure-vmware-solution-virtual-machines.md)
- [Recuperação completa de desastres de máquinas virtuais usando Azure VMware Solution](disaster-recovery-for-virtual-machines.md)
