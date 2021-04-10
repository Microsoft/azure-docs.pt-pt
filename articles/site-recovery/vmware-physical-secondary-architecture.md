---
title: Arquitetura-VMware/recuperação de desastres físicos para um local secundário com recuperação do local de Azure
description: Este artigo fornece uma visão geral dos componentes e arquitetura utilizados durante a recuperação de desastres de VMware VMware no local ou servidores físicos do Windows/Linux para um site secundário de VMware com recuperação do site Azure.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 032f8619a1f8d4299af1eb069fdccba1307542d4
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579514"
---
# <a name="architecture-for-vmwarephysical-server-replication-to-a-secondary-on-premises-site"></a>Arquitetura para replicação VMware/servidor físico para um site secundário no local

Este artigo descreve a arquitetura e os processos utilizados quando configuram a replicação de recuperação de desastres, failover e recuperação de máquinas virtuais VMware (VMware) ou servidores físicos do Windows/Linux para um site secundário de VMware utilizando [a recuperação do site Azure.](site-recovery-overview.md)


## <a name="architectural-components"></a>Componentes da arquitetura

**Área** | **Componente** | **Detalhes**
--- | --- | ---
**Azure** | Este cenário é implementado através de InMage Scout. | Para obter o InMage Scout, precisa de uma subscrição do Azure.<br/><br/> Depois de criar um cofre dos Serviços de Recuperação, transfere o InMage Scout e instala as atualizações mais recentes para configurar a implementação.
**Servidor de processo** | Localizado no site primário | O servidor de processos é implementado para lidar com a colocação em cache, a compressão e a otimização de dados.<br/><br/> Ele também faz a instalação por push do Agente Unified nas máquinas que pretende proteger.
**Servidor de configuração** | Localizado no site secundário | O servidor de configuração gere, configura e monitoriza a implementação, utilizando o site de gestão ou a consola do vContinuum.
**Servidor de vContinuum** | Opcional. Instalado na mesma localização que o servidor de configuração. | Fornece uma consola de gestão e monitorização do seu ambiente protegido.
**Servidor de destino mestre** | Localizado no site secundário | O servidor de destino mestre contém dados replicados. Recebe dados do servidor de processos, cria uma máquina de réplica no site secundário e detém os pontos de retenção de dados.<br/><br/> O número de servidores de destino principal de que precisa, depende do número de máquinas que estiver a proteger.<br/><br/> Se pretender que haja reativação para o site primário, precisa também que aí exista um servidor de destino principal. O Agente Unified é instalado neste servidor.
**VMware ESX/ESXi e servidor vCenter** |  As VMs são alojadas em anfitriões ESX/ESXi. Os anfitriões são geridos com um servidor vCenter | Precisa de uma infraestrutura do VMware para replicar VMs de VMware.
**VMs/servidores físicos** |  Agente Unificado instalado nas VMs VMware e servidores físicos que pretende replicar. | O agente atua como um fornecedor de comunicação entre todos os componentes.

## <a name="set-up-outbound-network-connectivity"></a>Configurar a conectividade da rede de saída

Para que a Recuperação do Site funcione como esperado, é necessário modificar a conectividade da rede de saída para permitir que o seu ambiente se reproduza.

> [!NOTE]
> A Recuperação do Site não suporta a utilização de um representante de autenticação para controlar a conectividade da rede.

### <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Se estiver a usar um proxy de firewall baseado em URL para controlar a conectividade de saída, permita o acesso a estes URLs:

| **Nome**                  | **Comercial**                               | **Administração Pública**                                 | **Descrição** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Armazenamento                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Permite que os dados sejam escritos da VM para a conta de armazenamento em cache na região de origem. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Fornece autorização e autenticação para os URLs do serviço Site Recovery. |
| Replicação               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Permite que a VM comunique com o serviço Site Recovery. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Permite que a VM escreva dados de monitorização e diagnóstico do Site Recovery. |

## <a name="replication-process"></a>Processo de replicação

1. Configure servidores de componentes em cada site (configuração, processo, destino principal) e instale o agente Unified nas máquinas que pretende replicar.
2. Após a replicação inicial, o agente em cada máquina envia alterações de replicação delta para o servidor de processos.
3. O servidor de processos otimiza os dados e transfere-o para o servidor de destino principal no site secundário. O servidor de configuração gere o processo de replicação.

![Diagrama mostrando replicação de VMware VMs e servidores físicos a um centro de dados secundário](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="next-steps"></a>Passos seguintes

[Configurar a](vmware-physical-secondary-disaster-recovery.md) recuperação de desastres de VMware VMs e servidores físicos para um site secundário.
