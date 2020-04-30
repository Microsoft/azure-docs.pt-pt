---
title: Prepare-se para a recuperação de desastres de VMs Hiper-V para Azure com recuperação do site Azure
description: Saiba como preparar as Hiper-V VMs no local para a recuperação de desastres em Azure com a Recuperação do Site Azure.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b36e4688ebd6e929a56869a6bb191d98b2f1f432
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79239841"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Preparar servidores Hyper-V no local para a recuperação após desastre para o Azure

Este artigo descreve como preparar a sua infraestrutura Hyper-V no local quando pretende configurar a recuperação de desastres de Hiper-VMs para Azure, utilizando a Recuperação do [Site Azure.](site-recovery-overview.md)


Este é o segundo tutorial de uma série que mostra como configurar a recuperação de desastres para Azure para os Hiper-V V. no local. No primeiro tutorial, [instalamos os componentes Azure necessários](tutorial-prepare-azure.md) para a recuperação de desastres hiper-V.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Reveja os requisitos de Hiper-V e os requisitos de VMM se os seus anfitriões Hyper-V forem geridos pelo System Center VMM.
> * Prepare vMM, se aplicável.
> * Verifique o acesso à Internet às localizações do Azure.
> * Prepare VMs para que possa acessá-los após a falha no Azure.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, reveja o artigo na secção Como Fazer da Tabela de Recuperação do Site.

## <a name="before-you-start"></a>Antes de começar

Certifique-se de ter preparado Azure como descrito no [primeiro tutorial desta série.](tutorial-prepare-azure.md)

## <a name="review-requirements-and-prerequisites"></a>Requisitos e pré-requisitos de revisão

Certifique-se de que os anfitriões e VMs hiper-V cumprem os requisitos.

1. [Verifique](hyper-v-azure-support-matrix.md#on-premises-servers) os requisitos do servidor no local.
2. [Verifique os requisitos](hyper-v-azure-support-matrix.md#replicated-vms) para os VMs hiper-V que pretende replicar para o Azure.
3. Verifique [a rede](hyper-v-azure-support-matrix.md#hyper-v-network-configuration)de anfitriões Hyper-V; e suporte de armazenamento de [anfitrião](hyper-v-azure-support-matrix.md#hyper-v-host-storage) e hóspede para anfitriões hiper-V no local.
4. Verifique o que é suportado para [redes do Azure](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [armazenamento](hyper-v-azure-support-matrix.md#azure-storage), e [computação](hyper-v-azure-support-matrix.md#azure-compute-features), após a ativação pós-falha.
5. As suas VM no local que replica para o Azure devem estar em conformidade com os [requisitos de VM do Azure](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>Preparar VMM (opcional)

Se os anfitriões Hyper-V forem geridos por VMM, você precisa preparar o servidor VMM no local. 

- Certifique-se de que o servidor VMM tem pelo menos uma nuvem, com um ou mais grupos de anfitriões. O hospedeiro Hyper-V em que os VMs estão a funcionar deve estar localizado na nuvem.
- Prepare o servidor VMM para mapeamento de rede.

### <a name="prepare-vmm-for-network-mapping"></a>Prepare vMM para mapeamento de rede

Se estiver a utilizar vmm, mapas de [mapeamento](site-recovery-network-mapping.md) de rede entre redes VMM VM no local e redes virtuais Azure. O mapeamento garante que os VMs Azure estão ligados à rede certa quando são criados após a falha.

Prepare o VMM para o mapeamento da rede da seguinte forma:

1. Certifique-se de que tem uma [rede lógica VMM](https://docs.microsoft.com/system-center/vmm/network-logical) que está associada à nuvem em que os anfitriões hyper-V estão localizados.
2. Certifique-se de que dispõe de uma [rede VM](https://docs.microsoft.com/system-center/vmm/network-virtual) ligada à rede lógica.
3. Em VMM, ligue os VMs à rede VM.

## <a name="verify-internet-access"></a>Verificar o acesso à Internet

1. Para efeitos do tutorial, a configuração mais simples é que os anfitriões Hyper-V e o servidor VMM tenham acesso direto à internet sem utilizar um proxy. 
2. Certifique-se de que os anfitriões hyper-V e o servidor VMM, se for caso disso, podem aceder aos URLs necessários abaixo.   
3. Se estiver a controlar o acesso por endereço IP, certifique-se de que:
    - As regras de firewall baseadas em endereçoIP podem ligar-se às gamas IP do Centro de [Dados Do Azure,](https://www.microsoft.com/download/confirmation.aspx?id=41653)e à porta HTTPS (443).
    - Permita intervalos de endereçoIP para a região Azure da sua subscrição.
    
### <a name="required-urls"></a>URLs necessários


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar a ligação para VMs do Azure após a ativação pós-falha

Durante um cenário de failover, poderá querer ligar-se à sua rede replicada no local.

Para ligar aos VMs do Windows utilizando RDP após a falha, permita o acesso da seguinte forma:

1. Para aceder através da Internet, ative o protocolo RDP na VM local antes da ativação pós-falha. Certifique-se de que as regras de TCP e UDP são adicionadas para o perfil **público,** e que o RDP é permitido em**Aplicações permitidas** pelo **Windows Firewall** > para todos os perfis.
2. Para aceder através da rede de VPNs, ative o protocolo RDP na máquina local. O RDP deve ser permitido nas**aplicações e funcionalidades permitidas** pelo **Windows Firewall** -> para redes **De domínio e privadas.**
   Verifique se a política de SAN do sistema operativo está definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135). Não devem haver atualizações do Windows pendentes na VM quando aciona uma ativação pós-falha. Se houver, não poderá entrar na máquina virtual até que a atualização esteja concluída.
3. Na VM do Azure do Windows após a ativação pós-falha, consulte o **Diagnóstico de arranque ** para ver uma captura de ecrã da VM. Se não conseguir ligar-se, verifique se a VM está em execução e reveja estas [sugestões de resolução de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Após a falha, pode aceder a VMs Azure utilizando o mesmo endereço IP que o VM replicado no local, ou um endereço IP diferente. [Saiba mais](concepts-on-premises-to-azure-networking.md) sobre a configuração de endereços IP para falha.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Instale recuperação de desastres para Azure para VMs](tutorial-hyper-v-to-azure.md)
> hiper-V[Configurar recuperação de desastres para Azure para VMs hiper-V em nuvens VMM](tutorial-hyper-v-vmm-to-azure.md)
