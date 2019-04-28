---
title: Preparar servidores de Hyper-V no local para a recuperação após desastre de VMs de Hyper-V para o Azure | Documentos da Microsoft
description: Saiba como preparar as VMs de Hyper-V no local para a recuperação após desastre para o Azure com o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6e57b629a0007b06af6e37f96e1466e35afafccc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996542"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Preparar servidores de Hyper-V no local para a recuperação após desastre para o Azure

Este artigo descreve como preparar a infraestrutura de Hyper-V no local quando desejar configurar a recuperação após desastre de VMs de Hyper-para o Azure, utilizando [do Azure Site Recovery](site-recovery-overview.md).


Este é o segundo tutorial de uma série que mostra-lhe como configurar a recuperação após desastre para o Azure para VMs de Hyper-V no local. No primeiro tutorial, iremos [configurados os componentes do Azure](tutorial-prepare-azure.md) necessária para a recuperação após desastre de Hyper-V.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Reveja os requisitos de Hyper-V e os requisitos do VMM, se os seus anfitriões de Hyper-V forem geridos pelo System Center VMM.
> * Prepare o VMM, se aplicável.
> * Verifique se o acesso à internet para localizações do Azure.
> * Prepare VMs, para que pode acessá-los após a ativação pós-falha para o Azure.

> [!NOTE]
> Tutoriais mostram-lhe o caminho de implantação mais simples para um cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, consulte o artigo na seção como a do Site Recovery sumário.

## <a name="before-you-start"></a>Antes de começar

Certifique-se de que já preparou a Azure conforme descrito no [primeiro tutorial nesta série](tutorial-prepare-azure.md).

## <a name="review-requirements-and-prerequisites"></a>Rever requisitos e pré-requisitos

Certifique-se de que anfitriões de Hyper-V e as VM cumprem os requisitos.

1. [Certifique-se de](hyper-v-azure-support-matrix.md#on-premises-servers) requisitos de servidor no local.
2. [Verifique os requisitos](hyper-v-azure-support-matrix.md#replicated-vms) para VMs de Hyper-V que pretende replicar para o Azure.
3. Verifique o anfitrião Hyper-V [funcionamento em rede](hyper-v-azure-support-matrix.md#hyper-v-network-configuration); e host e convidado [armazenamento](hyper-v-azure-support-matrix.md#hyper-v-host-storage) suporte para anfitriões de Hyper-V no local.
4. Verifique o que é suportado para [redes do Azure](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [armazenamento](hyper-v-azure-support-matrix.md#azure-storage), e [computação](hyper-v-azure-support-matrix.md#azure-compute-features), após a ativação pós-falha.
5. As suas VM no local que replica para o Azure devem estar em conformidade com os [requisitos de VM do Azure](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>Preparar o VMM (opcional)

Se os anfitriões Hyper-V forem geridos pelo VMM, terá de preparar o servidor do VMM no local. 

- Certifique-se de que o servidor VMM tem uma nuvem de uma menor, com um ou mais grupos de anfitriões. O anfitrião de Hyper-V no qual as VMs estão em execução deve estar localizado na cloud.
- Prepare o servidor do VMM para o mapeamento de rede.

### <a name="prepare-vmm-for-network-mapping"></a>Preparar o VMM para o mapeamento de rede

Se estiver a utilizar o VMM, [mapeamento da rede](site-recovery-network-mapping.md) mapeia entre as redes VM do VMM no local e redes virtuais do Azure. Mapeamento garante que as VMs do Azure estão ligadas à rede certa quando forem criadas após a ativação pós-falha.

Prepare o VMM para o mapeamento da rede da seguinte forma:

1. Certifique-se de que tem um [rede lógica de VMM](https://docs.microsoft.com/system-center/vmm/network-logical) que está associada à nuvem em que estão localizados os anfitriões de Hyper-V.
2. Certifique-se de que tem um [rede VM](https://docs.microsoft.com/system-center/vmm/network-virtual) ligado à rede lógica.
3. No VMM, ligar as VMs à rede VM.

## <a name="verify-internet-access"></a>Verifique se o acesso à internet

1. Para efeitos do tutorial, a configuração mais simples é para os anfitriões Hyper-V e o servidor do VMM ter acesso direto à internet sem utilizar um proxy. 
2. Certifique-se de que anfitriões de Hyper-V e o servidor do VMM, se forem relevantes, podem aceder aos URLs necessários abaixo.   
3. Se estiver a controlar o acesso por endereço IP, certifique-se de que:
    - Regras de firewall baseadas no endereço IP podem ligar à [intervalos de IP de Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)e a porta HTTPS (443).
    - Permitir que os intervalos de endereços IP para a região do Azure da sua subscrição.
    
### <a name="required-urls"></a>URLs necessários


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar a ligação para VMs do Azure após a ativação pós-falha

Durante um cenário de ativação pós-falha poderá ligar à sua rede replicados no local.

Para ligar às VMs do Windows via RDP após a ativação pós-falha, permitir o acesso da seguinte forma:

1. Para aceder através da Internet, ative o protocolo RDP na VM local antes da ativação pós-falha. Certifique-se de que são adicionadas regras de TCP e UDP para o perfil **Público** e que o protocolo RDP é permitido em **Firewall do Windows** > **Aplicações Permitidas** para todos os perfis.
2. Para aceder através da rede de VPNs, ative o protocolo RDP na máquina local. O protocolo RDP deve estar permitido em **Firewall do Windows** -> **Aplicações e funcionalidades permitidas** para redes de **Domínio e Privadas**.
   Verifique se a política de SAN do sistema operativo está definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135). Não devem haver atualizações do Windows pendentes na VM quando aciona uma ativação pós-falha. Se existirem, não será possível iniciar sessão na máquina virtual enquanto a atualização for concluída.
3. Na VM do Azure do Windows após a ativação pós-falha, consulte o **Diagnóstico de arranque**  para ver uma captura de ecrã da VM. Se não conseguir ligar-se, verifique se a VM está em execução e reveja estas [sugestões de resolução de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Após a ativação pós-falha, pode aceder a VMs do Azure com o mesmo endereço IP, como a VM replicada no local ou um endereço IP diferente. [Saiba mais](concepts-on-premises-to-azure-networking.md) sobre como configurar o endereçamento IP para ativação pós-falha.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Configurar a recuperação após desastre para o Azure das VMs de Hyper-V](tutorial-hyper-v-to-azure.md)
> [configurar a recuperação após desastre para o Azure das VMs de Hyper-V em clouds do VMM](tutorial-hyper-v-vmm-to-azure.md)
