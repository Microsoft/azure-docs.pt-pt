---
title: Ligue-se a VMs Azure no local falha com recuperação do site Azure
description: Descreve como ligar-se aos VMs Azure após falha das instalações para Azure usando a Recuperação do Site Azure
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: f222cdd315b79503b1bdea032f495c71df4682b5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281993"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>Ligue-se aos VMs Azure após falha no local 


Este artigo descreve como configurar a conectividade para que possa ligar-se com sucesso aos VMs Azure após a falha.

Quando configura a recuperação de desastres de máquinas virtuais no local (VMs) e servidores físicos para o Azure, a [Azure Site Recovery](site-recovery-overview.md) começa a replicar máquinas para o Azure. Depois, quando ocorrerem interrupções, pode falhar com o Azure a partir do seu local no local. Quando ocorre a falha, a Recuperação do Site cria VMs Azure, utilizando dados replicados no local. Como parte do planeamento de recuperação de desastres, você precisa descobrir como se conectar a aplicações que executam estes VMs Azure após falha.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Prepare as máquinas no local antes de falhar.
> * Prepare os VMs Azure após a falha. 
> * Mantenha endereços IP em VMs Azure após a falha.
> * Atribuir novos endereços IP aos VMs Azure após a falha.

## <a name="prepare-on-premises-machines"></a>Prepare máquinas no local

Para garantir a conectividade com os VMs Azure, prepare as suas máquinas no local antes de falhar.

### <a name="prepare-windows-machines"></a>Preparar máquinas Windows

No local, faça o seguinte:

1. Configure as definições do Windows. Estes incluem remover quaisquer rotas estáticas persistentes ou proxy WinHTTP, e definir a política SAN do disco para **OnlineAll**. [Siga](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure) estas instruções.

2. Certifique-se de que [estes serviços](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) estão em funcionamento.

3. Ativar o ambiente de trabalho remoto (RDP) para permitir ligações remotas à máquina no local. [Aprenda](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) a ativar rdp com powerShell.

4. Para aceder a um Azure VM através da internet após a falha, no Windows Firewall na máquina no local, permitir TCP e UDP no perfil público, e definir RDP como uma aplicação permitida para todos os perfis.

5. Se pretender aceder a um Azure VM através de uma VPN site-to-site após a falha, no Windows Firewall na máquina no local, permita RDP para os perfis de Domínio e Privado. [Saiba](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) como permitir o tráfego de RDP.
6. Certifique-se de que não existem atualizações do Windows pendentes no VM no local quando acionar uma falha. Se houver, as atualizações podem começar a instalar-se no VM Azure após a falha, e não poderá assinar no VM até que as atualizações terminem.

### <a name="prepare-linux-machines"></a>Prepare as máquinas Linux

Nas máquinas Linux no local, faça o seguinte:

1. Verifique se o serviço Secure Shell está programado para iniciar automaticamente a bota do sistema.
2. Verifique se as regras de firewall permitem uma ligação SSH.


## <a name="configure-azure-vms-after-failover"></a>Configure VMs Azure após falha

Depois de failover, faça o seguinte nos VMs Azure que são criados.

1. Para ligar ao VM através da internet, atribua um endereço IP público ao VM. Não pode utilizar o mesmo endereço IP público para o VM Azure que usou para a sua máquina no local. [Saiba mais](../virtual-network/virtual-network-public-ip-address.md)
2. Verifique se as regras do grupo de segurança da rede (NSG) sobre o VM permitem a entrada de ligações à porta RDP ou SSH.
3. Verifique os [diagnósticos da Bota](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) para ver o VM.


> [!NOTE]
> O serviço Azure Bastion oferece acesso privado de RDP e SSH aos VMs Azure. [Saiba mais](../bastion/bastion-overview.md) sobre este serviço.

## <a name="set-a-public-ip-address"></a>Definir um endereço IP público

Como alternativa à atribuição de um endereço IP público manualmente a um VM Azure, pode atribuir o endereço durante a falha utilizando um livro de execução de script ou automação Azure num plano de [recuperação](site-recovery-create-recovery-plans.md)do site, ou pode configurar o encaminhamento ao nível de DNS utilizando o Gestor de Tráfego Azure. [Saiba mais](concepts-public-ip-address-with-site-recovery.md) sobre a criação de um endereço público.


## <a name="assign-an-internal-address"></a>Atribuir um endereço interno

Para definir o endereço IP interno de um VM Azure após a falha, você tem algumas opções:

- **Reter**o mesmo endereço IP : Pode utilizar o mesmo endereço IP no VM Azure que o atribuído à máquina no local.
- **Utilize um endereço IP diferente:** Pode utilizar um endereço IP diferente para o VM Azure.


## <a name="retain-ip-addresses"></a>Reter endereços IP

A Recuperação do Site permite reter os mesmos endereços IP ao falhar com o Azure. A manutenção do mesmo endereço IP evita potenciais problemas de rede após o fracasso, mas introduz alguma complexidade.

- Se o target Azure VM utilizar o mesmo endereço IP/subnet que o seu site no local, não pode ligar-se entre eles utilizando uma ligação VPN site-para-site ou ExpressRoute, devido à sobreposição do endereço. As subredes devem ser únicas.
- Você precisa de uma ligação do local para Azure após o failover, para que as aplicações estejam disponíveis em VMs Azure. O Azure não suporta VLANs esticados, por isso, se quiser reter endereços IP, tem de levar o espaço IP para O Azure falhando em toda a subnet, além da máquina no local.
- A falha da subnet garante que uma subnet específica não está disponível simultaneamente no local e em Azure.

A conservação dos endereços IP requer os seguintes passos:

- Nas propriedades computacionais e de rede do item replicado, a rede definida e o endereço IP para o target Azure VM para espelhar a definição no local.
- As subredes devem ser geridas como parte do processo de recuperação de desastres. É necessário um Azure VNet para combinar com a rede no local, e depois de as rotas da rede failover devem ser modificadas para refletir que a subnet se mudou para Azure, e novas localizações de endereços IP.  

### <a name="failover-example"></a>Exemplo de failover

Vejamos um exemplo.

- A empresa fictícia Woodgrove Bank acolhe as suas aplicações de negócio no local, alojando as suas aplicações móveis em Azure.
- Ligam-se desde as instalações até ao Azure através da VPN local-a-local. 
- A Woodgrove está a usar a Recuperação do Local para replicar máquinas no local para o Azure.
- As suas aplicações no local utilizam endereços IP codificados, pelo que pretendem reter os mesmos endereços IP em Azure.
- No local, as máquinas que executam as aplicações estão a funcionar em três subredes:
    - 192.168.1.0/24.
    - 192.168.2.0/24
    - 192.168.3.0/24
- As suas aplicações em funcionamento em Azure estão localizadas na Rede Azure VNet **Azure** em duas subredes:
- 172.16.1.0/24
- 172.16.2.0/24.

Para reter os endereços, eis o que fazem.

1. Quando ativam a replicação, especificam que as máquinas devem replicar-se na **rede Azure**.
2. Criam rede de **recuperação** em Azure. Este VNet espelha a sub-rede 192.168.1.0/24 na sua rede no local.
3. Woodgrove estabelece uma [ligação VNet-to-VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) entre as duas redes. 

    > [!NOTE]
    > Dependendo dos requisitos de aplicação, uma ligação VNet-to-VNet poderia ser configurada antes da falha, como um manual de passo/scripted step/Azure automation runbook em um plano de recuperação de [recuperação](site-recovery-create-recovery-plans.md)do site, ou após a falha estar completa.

4. Antes do failover, nas propriedades da máquina na Recuperação do Local, eles definiram o endereço IP alvo para o endereço da máquina no local, conforme descrito no procedimento seguinte.
5. Após a falha, os VMs Azure são criados com o mesmo endereço IP. A Woodgrove liga-se da **Rede Azure** à Rede de **Recuperação** VNet utilizando o peering VNet (com conectividade de trânsito ativada).
6. No local, a Woodgrove precisa de fazer alterações de rede, incluindo modificar rotas para refletir que 192.168.1.0.0/24 mudou-se para Ota.  

**Infraestrutura antes do fracasso**

![Antes da falha da sub-rede](./media/site-recovery-network-design/network-design7.png)


**Infraestrutura após falha**

![Após falha na rede](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>Definir definições de rede de destino

Antes da falha, especifique as definições de rede e o endereço IP para o Target Azure VM.

1.  No cofre dos Serviços de Recuperação -> **Itens replicados,** selecione a máquina no local.
2. Na página **Compute e Network** para a máquina, clique em **Editar,** para configurar definições de rede e adaptador para o target Azure VM.
3. Nas **propriedades da Rede,** selecione a rede de destino na qual o Azure VM estará localizado quando for criado após a falha.
4. Nas **interfaces da Rede,** configure os adaptadores de rede na rede alvo. Por predefinição, a Recuperação do Site mostra todos os NICs detetados na máquina no local.
    - No tipo de interface de **rede Target** pode definir cada NIC como **Primário,** **Secundário,** ou **não criar** se não precisar desse NIC específico na rede de destino. Um adaptador de rede deve ser definido como primário para a falha. Note que modificar a rede alvo afeta todos os NICs para o VM Azure.
    - Clique no nome NIC para especificar a subrede em que o VM Azure será implantado.
    - Revê a **Dynamic** com o endereço IP privado que pretende atribuir ao Azure VM. Se um endereço IP não for especificado, a Recuperação do Site atribuirá o próximo endereço IP disponível na subnet ao NIC em failover.
    - [Saiba mais](site-recovery-manage-network-interfaces-on-premises-to-azure.md) sobre a gestão de NICs para falhas no local para o Azure.


## <a name="get-new-ip-addresses"></a>Obtenha novos endereços IP

Neste cenário, o Azure VM recebe um novo endereço IP após a falha. Uma atualização DNS para atualizar os registos de máquinas falhadas para apontar para o endereço IP do VM Azure.



## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre](site-recovery-active-directory.md) a replicação no local Ative Directory e DNS para Azure.


