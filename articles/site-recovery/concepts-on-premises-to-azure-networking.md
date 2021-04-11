---
title: Ligue-se aos VMs do Azure no local com a Recuperação do Local de Azure
description: Descreve como ligar-se aos VMs do Azure após a falha de passagem de instalações para Azure usando a recuperação do site Azure
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: harshacs
ms.openlocfilehash: 4b88ed44632aa255837d8fb499782e11c716d443
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048192"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>Ligue-se aos VMs Azure após a queda do local 


Este artigo descreve como configurar a conectividade para que possa ligar-se com sucesso aos VMs Azure após o failover.

Quando configurar a recuperação de desastres de máquinas virtuais (VMs) e servidores físicos para a Azure, [a Azure Site Recovery](site-recovery-overview.md) começa a replicar máquinas para o Azure. Em seguida, quando ocorrerem interrupções, pode falhar até Azure a partir do seu local de paragem. Quando ocorre uma falha, a Recuperação do Site cria VMs Azure, utilizando dados replicados no local. Como parte do planeamento de recuperação de desastres, você precisa descobrir como conectar-se a aplicações executando nestes VMs Azure após o failover.

Neste artigo, saiba como:

> [!div class="checklist"]
> * Prepare as máquinas no local antes de falhar.
> * Prepare os VMs Azure após o fracasso. 
> * Reter endereços IP em VMs Azure após falha.
> * Atribua novos endereços IP aos VMs Azure após o failover.

## <a name="prepare-on-premises-machines"></a>Preparar máquinas no local

Para garantir a conectividade com os VMs Azure, prepare as suas máquinas no local antes de falhar.

### <a name="prepare-windows-machines"></a>Preparar máquinas Windows

Nas máquinas Windows no local, faça o seguinte:

1. Configurar as definições do Windows. Estes incluem a remoção de quaisquer rotas estáticas persistentes ou proxy WinHTTP, e definir a política de DISCO SAN para **OnlineAll**. [Siga](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure) estas instruções.

2. Certifique-se de que [estes serviços](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) estão funcionando.

3. Ativar o ambiente de trabalho remoto (PDR) para permitir ligações remotas à máquina no local. [Saiba como](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) ativar o RDP com o PowerShell.

4. Para aceder a um Azure VM através da internet após o failover, no Windows Firewall na máquina de acesso, permita o TCP e a UDP no perfil público, e coloque o RDP como uma aplicação permitida para todos os perfis.

5. Se pretender aceder a um Azure VM sobre uma VPN site-to-site após a falha, no Windows Firewall na máquina de acesso, permita rdp para os perfis de Domínio e Private. [Saiba](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) como permitir o tráfego rdp.
6. Certifique-se de que não existem atualizações do Windows pendentes no VM no local quando desencadear uma falha. Se houver, as atualizações podem começar a ser instaladas no Azure VM após o failover, e não poderá assinar no VM até que as atualizações terminem.

### <a name="prepare-linux-machines"></a>Preparar máquinas Linux

Nas máquinas Linux no local, faça o seguinte:

1. Verifique se o serviço Secure Shell está programado para iniciar automaticamente no arranque do sistema.
2. Verifique se as regras de firewall permitem uma ligação SSH.


## <a name="configure-azure-vms-after-failover"></a>Configure Azure VMs após falha

Depois do failover, faça o seguinte sobre os VMs Azure que são criados.

1. Para ligar ao VM através da internet, atribua um endereço IP público ao VM. Não pode utilizar o mesmo endereço IP público para o VM Azure que usou para a sua máquina no local. [Saiba mais](../virtual-network/virtual-network-public-ip-address.md)
2. Verifique as regras do grupo de segurança da rede (NSG) no VM, permita a entrada de ligações à porta RDP ou SSH.
3. Verifique [os diagnósticos da Boot](/troubleshoot/azure/virtual-machines/boot-diagnostics#enable-boot-diagnostics-on-existing-virtual-machine) para ver o VM.


> [!NOTE]
> O serviço Azure Bastion oferece acesso privado de RDP e SSH a VMs Azure. [Saiba mais](../bastion/bastion-overview.md) sobre este serviço.

## <a name="set-a-public-ip-address"></a>Definir um endereço IP público

Como alternativa à atribuição manual de um endereço IP público a um VM Azure, pode atribuir o endereço durante o failover utilizando um script ou um runbook de automação Azure num plano de [recuperação](site-recovery-create-recovery-plans.md)do site , ou pode configurar o encaminhamento ao nível DNS utilizando o Azure Traffic Manager. [Saiba mais](concepts-public-ip-address-with-site-recovery.md) sobre a criação de um endereço público.


## <a name="assign-an-internal-address"></a>Atribuir um endereço interno

Para definir o endereço IP interno de um VM Azure após o failover, você tem algumas opções:

- **Reter** o mesmo endereço IP : Pode utilizar o mesmo endereço IP no Azure VM que o atribuído à máquina no local.
- **Utilize diferentes endereços IP**: Pode utilizar um endereço IP diferente para o VM Azure.


## <a name="retain-ip-addresses"></a>Reter endereços IP

A Recuperação do Site permite-lhe reter os mesmos endereços IP ao falhar no Azure. A manutenção do mesmo endereço IP evita potenciais problemas de rede após o failover, mas introduz alguma complexidade.

- Se o Target Azure VM utilizar o mesmo endereço IP/sub-rede que o seu site no local, não pode ligar-se entre eles utilizando uma ligação VPN site-to-site ou ExpressRoute, devido à sobreposição do endereço. As sub-redes devem ser únicas.
- Você precisa de uma ligação de in-in-ins para Azure após o failover, para que as aplicações estejam disponíveis em VMs Azure. O Azure não suporta VLANs esticados, por isso, se pretender reter endereços IP, precisa de levar o espaço IP para a Azure, falhando em toda a sub-rede, além da máquina no local.
- O failover da subnet garante que uma sub-rede específica não está disponível simultaneamente no local e no Azure.

A retenção de endereços IP requer as seguintes etapas:

- No Compute & as propriedades da Rede do item replicado, da rede definida e do endereço IP para o target Azure VM para espelhar a definição no local.
- As sub-redes devem ser geridas como parte do processo de recuperação de desastres. Você precisa de um Azure VNet para combinar com a rede no local, e depois de rotas de rede falhadas deve ser modificado para refletir que a sub-rede mudou-se para Azure, e novas localizações de endereço IP.  

### <a name="failover-example"></a>Exemplo de falha

Vejamos um exemplo.

- A empresa fictícia Woodgrove Bank acolhe as suas aplicações empresariais no local, acolhendo as suas aplicações móveis em Azure.
- Eles ligam-se do local ao Azure sobre a VPN local-a-local. 
- A Woodgrove está a usar a Recuperação do Local para replicar máquinas no local para o Azure.
- As suas aplicações no local utilizam endereços IP codificados por código rígido, pelo que pretendem manter os mesmos endereços IP no Azure.
- No local, as máquinas que executam as aplicações estão a funcionar em três sub-redes:
    - 192.168.1.0/24
    - 192.168.2.0/24
    - 192.168.3.0/24
- As suas aplicações em execução em Azure estão localizadas na Rede Azure **Azure Azure** em duas sub-redes:
    - 172.16.1.0/24
    - 172.16.2.0/24

Para reter os endereços, eis o que fazem.

1. Quando ativam a replicação, especificam que as máquinas devem replicar-se na **Rede Azure**.
2. Criam **a Rede de Recuperação** em Azure. Este VNet espelha a sub-rede 192.168.1.0/24 na sua rede no local.
3. A Woodgrove estabelece uma [ligação VNet-vNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) entre as duas redes. 

    > [!NOTE]
    > Dependendo dos requisitos de aplicação, uma ligação VNet-vNet poderia ser configurada antes do failover, como um runbook de automação passo/Azure manual num plano de [recuperação](site-recovery-create-recovery-plans.md)do site , ou após a falha estar concluída.

4. Antes de falhar, nas propriedades da máquina na Recuperação do Local, definem o endereço IP alvo para o endereço da máquina no local, conforme descrito no procedimento seguinte.
5. Após o failover, os VMs Azure são criados com o mesmo endereço IP. A Woodgrove liga-se da **Rede Azure** à **Rede de Recuperação** VNet utilizando o espremiamento VNet (com conectividade de trânsito ativada).
6. No local, a Woodgrove precisa de fazer alterações na rede, incluindo a modificação das rotas para refletir que 192.168.1.0/24 se mudou para Azure.  

**Infraestrutura antes do failover**

![Antes do failover da sub-rede](./media/site-recovery-network-design/network-design7.png)


**Infraestrutura após falha**

![Após falha da sub-rede](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>Definir definições de rede alvo

Antes de falhar, especifique as definições de rede e o endereço IP para o target Azure VM.

1.  No cofre dos Serviços de Recuperação -> **itens replicados,** selecione a máquina no local.
2. Na página **Compute and Network** para a máquina, clique em **Editar,** para configurar as definições de rede e adaptador para o target Azure VM.
3. Nas **propriedades da Rede**, selecione a rede alvo na qual o VM Azure será localizado quando for criado após a falha.
4. Nas **interfaces de rede,** configurar os adaptadores de rede na rede alvo. Por predefinição, a Recuperação do Local mostra todos os NICs detetados na máquina no local.
    - No **tipo de interface de rede Target** pode definir cada NIC como **Primário,** **Secundário** ou **Não criar** se não precisar desse NIC específico na rede alvo. Um adaptador de rede deve ser definido como primário para o failover. Note que modificar a rede alvo afeta todos os NICs para o VM Azure.
    - Clique no nome NIC para especificar a sub-rede na qual o VM Azure será implantado.
    - Overwrite **Dynamic** com o endereço IP privado que pretende atribuir ao alvo Azure VM. Se um endereço IP não for especificado, a Recuperação do Site atribuirá o próximo endereço IP disponível na sub-rede ao NIC no failover.
    - [Saiba mais](site-recovery-manage-network-interfaces-on-premises-to-azure.md) sobre a gestão de NICs para falhas no local para Azure.


## <a name="get-new-ip-addresses"></a>Obtenha novos endereços IP

Neste cenário, o Azure VM obtém um novo endereço IP após o failover. Para configurar um novo Endereço IP para a máquina virtual criada após o failover, podem ser encaminhados os seguintes passos -

1. Ir para **itens replicados.**
2. Selecione a máquina virtual Azure desejada.
3. Selecione **Compute and Network** e selecione **Editar.**

     ![Personalize as configurações de rede de failover](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Para atualizar as definições de rede Failover, **selecione Editar** para o NIC que pretende configurar. Na página seguinte que abrir, forneça o endereço IP pré-criado correspondente na localização de failover e failover do teste.

    ![Editar a configuração NIC](media/azure-to-azure-customize-networking/nic-drilldown.png)

5. Selecione **OK**.

A Recuperação do Site irá agora honrar estas definições e garantir que a máquina virtual em falha está ligada ao recurso selecionado através do endereço IP correspondente, se estiver disponível na gama IP alvo. Neste cenário, não há necessidade de falhar toda a sub-rede. Será necessária uma atualização DNS para atualizar registos de máquinas falhadas para apontar para o novo endereço IP da máquina virtual.

## <a name="next-steps"></a>Passos seguintes
[Saiba como](site-recovery-active-directory.md) replicar no local o Ative Directory e o DNS para Azure.