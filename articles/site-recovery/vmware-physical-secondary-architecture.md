---
title: Recuperação de arquitetura-VMware/desastre físico para um local secundário com recuperação do site Azure
description: Este artigo fornece uma visão geral dos componentes e arquitetura utilizados durante a recuperação de desastres de VMware VMs no local ou servidores físicos Windows/Linux para um site de VMware secundário com Recuperação do Site Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: b0a46dcf8fe298494a53713f122b1bda8ce07e5e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73954581"
---
# <a name="architecture-for-vmwarephysical-server-replication-to-a-secondary-on-premises-site"></a>Arquitetura para replicação vmware/servidor físico para um site secundário no local

Este artigo descreve a arquitetura e os processos utilizados na configuração da replicação de recuperação de desastres, falha e recuperação de máquinas virtuais VMware (VMs) ou servidores físicos Windows/Linux para um site de VMware secundário utilizando a Recuperação do [Site Azure.](site-recovery-overview.md)


## <a name="architectural-components"></a>Componentes da arquitetura

**Área** | **Componente** | **Detalhes**
--- | --- | ---
**Azure** | Este cenário é implementado através de InMage Scout. | Para obter o InMage Scout, precisa de uma subscrição do Azure.<br/><br/> Depois de criar um cofre dos Serviços de Recuperação, transfere o InMage Scout e instala as atualizações mais recentes para configurar a implementação.
**Servidor de processos** | Localizado no site primário | O servidor de processos é implementado para lidar com a colocação em cache, a compressão e a otimização de dados.<br/><br/> Ele também faz a instalação por push do Agente Unified nas máquinas que pretende proteger.
**Servidor de configuração** | Localizado no site secundário | O servidor de configuração gere, configura e monitoriza a implementação, utilizando o site de gestão ou a consola do vContinuum.
**Servidor de vContinuum** | Opcional. Instalado na mesma localização que o servidor de configuração. | Fornece uma consola de gestão e monitorização do seu ambiente protegido.
**Servidor de destino mestre** | Localizado no site secundário | O servidor de destino mestre contém dados replicados. Recebe dados do servidor de processos, cria uma máquina de réplica no site secundário e detém os pontos de retenção de dados.<br/><br/> O número de servidores de destino principal de que precisa, depende do número de máquinas que estiver a proteger.<br/><br/> Se pretender que haja reativação para o site primário, precisa também que aí exista um servidor de destino principal. O Agente Unified é instalado neste servidor.
**VMware ESX/ESXi e servidor vCenter** |  As VMs são alojadas em anfitriões ESX/ESXi. Os anfitriões são geridos com um servidor vCenter | Precisa de uma infraestrutura do VMware para replicar VMs de VMware.
**VMs/servidores físicos** |  Agente Unificado instalado nas VMs VMware e servidores físicos que pretende replicar. | O agente atua como um fornecedor de comunicação entre todos os componentes.

## <a name="replication-process"></a>Processo de replicação

1. Configure servidores de componentes em cada site (configuração, processo, destino principal) e instale o agente Unified nas máquinas que pretende replicar.
2. Após a replicação inicial, o agente em cada máquina envia alterações de replicação delta para o servidor de processos.
3. O servidor de processos otimiza os dados e transfere-o para o servidor de destino principal no site secundário. O servidor de configuração gere o processo de replicação.

**Figura 6: Replicação de VMware para VMware**

![VMware para VMware](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="next-steps"></a>Passos seguintes

[Configurar](vmware-physical-secondary-disaster-recovery.md) a recuperação de desastres de VMware VMs e servidores físicos para um site secundário.
