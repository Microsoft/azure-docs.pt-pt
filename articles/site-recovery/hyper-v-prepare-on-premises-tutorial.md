---
title: Prepare-se para a recuperação de hiper-V Vms para Azure com recuperação do local de Azure
description: Saiba como preparar os VMs hiper-V para recuperação de desastres para Azure com a Recuperação do Local de Azure.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6f24a259d2d71aa6599f6dd417d5e9fc99734e99
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86135661"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Preparar servidores Hyper-V no local para a recuperação após desastre para o Azure

Este artigo descreve como preparar a sua infraestrutura hiper-V no local quando pretender configurar a recuperação de desastres de Hyper-VMs para Azure, utilizando [a recuperação do local de Azure.](site-recovery-overview.md)


Este é o segundo tutorial de uma série que mostra como configurar a recuperação de desastres para Azure para os Hiper-VMs no local. No primeiro tutorial, [criamos os componentes Azure necessários](tutorial-prepare-azure.md) para a recuperação de desastres hiper-V.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Reveja os requisitos de Hiper-V e VMM se os seus anfitriões Hiper-V forem geridos pelo System Center VMM.
> * Preparar VMM, se aplicável.
> * Verifique o acesso à Internet a locais do Azure.
> * Prepare VMs para que possa aceder-lhes depois de falhar em Azure.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, reveja o artigo na secção Como Fazer da Tabela de Recuperação do Site de Conteúdos.

## <a name="before-you-start"></a>Antes de começar

Certifique-se de ter preparado O Azure como descrito no [primeiro tutorial desta série](tutorial-prepare-azure.md).

## <a name="review-requirements-and-prerequisites"></a>Requisitos e pré-requisitos de revisão

Certifique-se de que os anfitriões e VMs hiper-V cumprem os requisitos.

1. [Verifique os](hyper-v-azure-support-matrix.md#on-premises-servers) requisitos do servidor no local.
2. [Verifique os requisitos](hyper-v-azure-support-matrix.md#replicated-vms) para Hiper-VMs que pretende replicar para Azure.
3. Verifique [a rede](hyper-v-azure-support-matrix.md#hyper-v-network-configuration)de anfitriões Hyper-V; e suporte de [armazenamento](hyper-v-azure-support-matrix.md#hyper-v-host-storage) de anfitriões e convidados para anfitriões Hiper-V no local.
4. Verifique o que é suportado para [redes do Azure](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [armazenamento](hyper-v-azure-support-matrix.md#azure-storage), e [computação](hyper-v-azure-support-matrix.md#azure-compute-features), após a ativação pós-falha.
5. As suas VM no local que replica para o Azure devem estar em conformidade com os [requisitos de VM do Azure](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>Preparar VMM (opcional)

Se os anfitriões Hiper-V forem geridos por VMM, tem de preparar o servidor VMM no local. 

- Certifique-se de que o servidor VMM tem pelo menos uma nuvem, com um ou mais grupos anfitriões. O hospedeiro Hyper-V em que os VMs estão a funcionar deve estar localizado na nuvem.
- Prepare o servidor VMM para o mapeamento da rede.

### <a name="prepare-vmm-for-network-mapping"></a>Preparar VMM para mapeamento de rede

Se estiver a utilizar o VMM, [mapes de mapeamento](./hyper-v-vmm-network-mapping.md) de rede entre as redes VMM no local e as redes virtuais Azure. O mapeamento garante que os VMs Azure estão ligados à rede certa quando são criados após o failover.

Preparar VMM para mapeamento de rede da seguinte forma:

1. Certifique-se de que tem uma [rede lógica VMM](/system-center/vmm/network-logical) associada à nuvem em que os anfitriões Do Hiper-V estão localizados.
2. Certifique-se de que tem uma [rede VM](/system-center/vmm/network-virtual) ligada à rede lógica.
3. Em VMM, ligue os VM à rede VM.

## <a name="verify-internet-access"></a>Verificar acesso à Internet

1. Para efeitos do tutorial, a configuração mais simples é para que os anfitriões Hiper-V e o servidor VMM tenham acesso direto à internet sem utilizar um proxy. 
2. Certifique-se de que os anfitriões Hyper-V e o servidor VMM, se relevantes, podem aceder aos URLs necessários abaixo.   
3. Se estiver a controlar o acesso através do endereço IP, certifique-se de que:
    - As regras de firewall baseadas em endereços IP podem ligar-se aos intervalos IP do [Azure Datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653)e à porta HTTPS (443).
    - Permita intervalos de endereços IP para a região Azure da sua subscrição.
    
### <a name="required-urls"></a>URLs necessários


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar a ligação para VMs do Azure após a ativação pós-falha

Durante um cenário de falha, poderá querer ligar-se à sua rede replicada no local.

Para ligar aos VM do Windows utilizando RDP após falha, permita o acesso da seguinte forma:

1. Para aceder através da Internet, ative o protocolo RDP na VM local antes da ativação pós-falha. Certifique-se de que as regras TCP e UDP são adicionadas para o perfil **público,** e que o RDP é permitido em   >  **Apps permitidas para** windows firewall para todos os perfis.
2. Para aceder através da rede de VPNs, ative o protocolo RDP na máquina local. O RDP deve ser permitido nas  ->  **aplicações e funcionalidades permitidas** para o Windows Firewall e para redes **de domínio e privado.**
   Verifique se a política de SAN do sistema operativo está definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135). Não devem haver atualizações do Windows pendentes na VM quando aciona uma ativação pós-falha. Se houver, não poderá iniciar sôs na máquina virtual até que a atualização esteja concluída.
3. Na VM do Azure do Windows após a ativação pós-falha, consulte o **Diagnóstico de arranque** para ver uma captura de ecrã da VM. Se não conseguir ligar-se, verifique se a VM está em execução e reveja estas [sugestões de resolução de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Após o failover, pode aceder aos VMs Azure utilizando o mesmo endereço IP que o VM replicado no local, ou um endereço IP diferente. [Saiba mais](concepts-on-premises-to-azure-networking.md) sobre a configuração do endereço IP para failover.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Preparar a recuperação de desastres para Azure para VMs](./hyper-v-azure-tutorial.md) 
>  hiper-V [Configurar a recuperação de desastres para Azure para Hiper-VMs em nuvens VMM](./hyper-v-vmm-azure-tutorial.md)
