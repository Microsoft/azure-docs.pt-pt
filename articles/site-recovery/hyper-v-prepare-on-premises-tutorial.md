---
title: Preparar servidores Hyper-V locais para recuperação de desastres de VMs do Hyper-V para o Azure
description: Saiba como preparar VMs do Hyper-V locais para recuperação de desastres no Azure com o serviço Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b17e42378daf51543a2664315f2971d15c288611
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813720"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Preparar servidores Hyper-V locais para recuperação de desastres no Azure

Este artigo descreve como preparar sua infraestrutura do Hyper-V local quando você deseja configurar a recuperação de desastre de Hyper-VMs no Azure, usando [Azure site Recovery](site-recovery-overview.md).


Este é o segundo tutorial de uma série que mostra como configurar a recuperação de desastres para o Azure para VMs do Hyper-V locais. No primeiro tutorial, [configuramos os componentes do Azure](tutorial-prepare-azure.md) necessários para a recuperação de desastres do Hyper-V.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Examine os requisitos do Hyper-V e os requisitos do VMM se os hosts do Hyper-V forem gerenciados pelo System Center VMM.
> * Prepare o VMM, se aplicável.
> * Verifique o acesso à Internet para locais do Azure.
> * Prepare as VMs para que você possa acessá-las após o failover para o Azure.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, examine o artigo na seção como do Sumário de Site Recovery.

## <a name="before-you-start"></a>Antes de começar

Verifique se você preparou o Azure conforme descrito no [primeiro tutorial desta série](tutorial-prepare-azure.md).

## <a name="review-requirements-and-prerequisites"></a>Revisar requisitos e pré-requisitos

Verifique se os hosts e as VMs do Hyper-V estão em conformidade com os requisitos.

1. [Verifique](hyper-v-azure-support-matrix.md#on-premises-servers) os requisitos do servidor local.
2. [Verifique os requisitos](hyper-v-azure-support-matrix.md#replicated-vms) das VMs do Hyper-V que você deseja replicar para o Azure.
3. Verificar a [rede](hyper-v-azure-support-matrix.md#hyper-v-network-configuration)do host Hyper-V; e suporte de [armazenamento](hyper-v-azure-support-matrix.md#hyper-v-host-storage) de host e convidado para hosts Hyper-V locais.
4. Verifique o que é suportado para [redes do Azure](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [armazenamento](hyper-v-azure-support-matrix.md#azure-storage), e [computação](hyper-v-azure-support-matrix.md#azure-compute-features), após a ativação pós-falha.
5. As suas VM no local que replica para o Azure devem estar em conformidade com os [requisitos de VM do Azure](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>Preparar o VMM (opcional)

Se os hosts Hyper-V forem gerenciados pelo VMM, você precisará preparar o servidor VMM local. 

- Verifique se o servidor do VMM tem pelo menos uma nuvem, com um ou mais grupos de hosts. O host Hyper-V no qual as VMs estão em execução deve estar localizado na nuvem.
- Prepare o servidor do VMM para o mapeamento de rede.

### <a name="prepare-vmm-for-network-mapping"></a>Preparar o VMM para o mapeamento de rede

Se você estiver usando o VMM, o [mapeamento de rede](site-recovery-network-mapping.md) mapeia entre redes de VM do VMM local e redes virtuais do Azure. O mapeamento garante que as VMs do Azure estejam conectadas à rede correta quando elas forem criadas após o failover.

Prepare o VMM para o mapeamento de rede da seguinte maneira:

1. Verifique se você tem uma [rede lógica do VMM](https://docs.microsoft.com/system-center/vmm/network-logical) associada à nuvem na qual os hosts do Hyper-V estão localizados.
2. Verifique se você tem uma [rede VM](https://docs.microsoft.com/system-center/vmm/network-virtual) vinculada à rede lógica.
3. No VMM, conecte as VMs à rede VM.

## <a name="verify-internet-access"></a>Verificar acesso à Internet

1. Para os fins do tutorial, a configuração mais simples é que os hosts Hyper-V e o servidor do VMM tenham acesso direto à Internet sem usar um proxy. 
2. Verifique se os hosts Hyper-V e o servidor do VMM, se relevante, podem acessar as URLs necessárias abaixo.   
3. Se você estiver controlando o acesso por endereço IP, verifique se:
    - As regras de firewall baseadas em endereço IP podem se conectar aos [intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)e a porta HTTPS (443).
    - Permitir intervalos de endereços IP para a região do Azure da sua assinatura.
    
### <a name="required-urls"></a>URLs necessárias


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar a ligação para VMs do Azure após a ativação pós-falha

Durante um cenário de failover, talvez você queira se conectar à sua rede local replicada.

Para se conectar a VMs do Windows usando o RDP após o failover, permita o acesso da seguinte maneira:

1. Para aceder através da Internet, ative o protocolo RDP na VM local antes da ativação pós-falha. Certifique-se de que são adicionadas regras de TCP e UDP para o perfil **Público** e que o protocolo RDP é permitido em **Firewall do Windows** > **Aplicações Permitidas** para todos os perfis.
2. Para aceder através da rede de VPNs, ative o protocolo RDP na máquina local. O protocolo RDP deve estar permitido em **Firewall do Windows** -> **Aplicações e funcionalidades permitidas** para redes de **Domínio e Privadas**.
   Verifique se a política de SAN do sistema operativo está definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135). Não devem haver atualizações do Windows pendentes na VM quando aciona uma ativação pós-falha. Se houver, você não poderá entrar na máquina virtual até que a atualização seja concluída.
3. Na VM do Azure do Windows após a ativação pós-falha, consulte o **Diagnóstico de arranque**  para ver uma captura de ecrã da VM. Se não conseguir ligar-se, verifique se a VM está em execução e reveja estas [sugestões de resolução de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Após o failover, você pode acessar as VMs do Azure usando o mesmo endereço IP da VM local replicada ou um endereço IP diferente. [Saiba mais](concepts-on-premises-to-azure-networking.md) sobre como configurar o endereçamento IP para failover.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Configurar a recuperação de desastre para o Azure para VMs](tutorial-hyper-v-to-azure.md)
> do Hyper-v[Configurar a recuperação de desastres para o Azure para VMs do Hyper-v em nuvens do VMM](tutorial-hyper-v-vmm-to-azure.md)
