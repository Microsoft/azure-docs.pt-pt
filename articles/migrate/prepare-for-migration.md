---
title: Preparar computadores locais para migração para o Azure com migrações para Azure
description: Este artigo descreve como preparar computadores locais para migração para o Azure com as migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 07612d325929468e3919b543d9b4ae0ad268036a
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71938475"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Preparar computadores locais para migração para o Azure

Este artigo descreve como preparar o computador local e baseado em nuvem antes de começar a migrá-los para o Azure com a [migração de servidor de migrações para Azure](migrate-services-overview.md#azure-migrate-server-migration).


Neste artigo, irá:
> [!div class="checklist"]
> * Verifique as limitações de migração.
> * Verifique os requisitos do sistema operacional e as limitações de suporte.
> * Examine o acesso à URL/porta para computadores que você deseja migrar.
> * Examine as alterações que talvez você precise fazer antes de começar a migração.
> * Defina as configurações para que as letras das unidades sejam preservadas após a migração.
> * Prepare as máquinas para que você possa se conectar às VMs do Azure após a migração.


## <a name="verify-migration-limitations"></a>Verificar as limitações de migração

- Você pode avaliar até 35.000 VMs VMware/VMs Hyper-V em um único projeto de migrações para Azure usando a migração de servidor de migrações para Azure. Um projeto pode combinar VMs do VMware e VMs do Hyper-V, até os limites de cada uma.
- Você pode selecionar até 10 VMs de uma só vez para a migração. Se você precisar replicar mais, replique em grupos de 10.
- Para a migração sem agente do VMware, você pode executar até 100 replicações simultaneamente.

## <a name="verify-operating-system-requirements"></a>Verificar os requisitos do sistema operacional

- Verifique se os [sistemas operacionais Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) têm suporte no Azure.
- Verifique se as [distribuições do Linux](../virtual-machines/linux/endorsed-distros.md) têm suporte no Azure.


## <a name="check-whats-supported"></a>Verifique o que tem suporte

- Para VMs VMware, a migração de servidor migrações para Azure dá suporte à [migração baseada em agente ou sem agentes](/server-migrate-overview.md). Verifique os requisitos/suporte de VM do VMware para migrações [baseadas em](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) agente e sem [agente](migrate-support-matrix-vmware.md#migration---limitations) .
- Verifique [os requisitos de migração e o suporte](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) para VMs do Hyper-V.
- Verifique [os requisitos de migração e o suporte](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) para computadores físicos locais ou outros servidores virtualizados. Esses requisitos são semelhantes aos requisitos de VM do VMware.





## <a name="review-urlport-access"></a>Examinar o acesso à URL/porta

Os computadores podem precisar de acesso à Internet durante a migração.

- Examine as URLs que as VMs VMware precisam acessar durante as migrações [baseadas em](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements) agente ou sem [agente](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) .
- Examine as URLs que os hosts Hyper-V precisam acessar durante a migração. As VMs do Hyper-V não precisam de acesso à Internet.
- [Examine as URLs](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements) que as máquinas físicas ou outros servidores virtualizados precisam acessar durante a migração.
- Na migração baseada em agente de VMs VMware/servidores físicos, o serviço de mobilidade em execução nos computadores precisa acessar os componentes de migração do Azure. Para o gerenciamento de replicação, o serviço em execução no computador se comunica com o dispositivo de replicação local migrações do Azure na porta HTTPS 443 de entrada. Os computadores enviam dados de replicação para o servidor de processo de migrações para Azure na porta HTTPS 9443 de entrada. Essa porta pode ser modificada.


## <a name="verify-required-changes-before-migration"></a>Verificar as alterações necessárias antes da migração

Algumas VMs podem exigir alterações para que possam ser executadas no Azure. As migrações para Azure fazem essas alterações automaticamente para VMs que executam estes sistemas operacionais:
- Red Hat Enterprise Linux 6.5 +, 7.0 +
- CentOS 6.5 +, 7.0 +
- SUSE Linux Enterprise Server 12 SP1+
- Ubuntu 14.04LTS, 16.04LTS, 18.04LTS
- Debian 7, 8

Para outros sistemas operacionais, você precisa preparar as máquinas manualmente antes da migração. 

### <a name="prepare-windows-machines"></a>Preparar computadores Windows

Se você estiver migrando um computador Windows, faça essas alterações antes da migração. Se você migrar a VM antes de fazer as alterações, a VM poderá não ser inicializada no Azure.

1. [Habilite o console de acesso serial do Azure](../virtual-machines/troubleshooting/serial-console-windows.md) para a VM do Azure. Isso ajuda na solução de problemas. Você não precisa reinicializar a VM. A VM do Azure será inicializada usando a imagem de disco. Isso é equivalente a uma reinicialização para a nova VM. 
2. Se você estiver migrando computadores que executam o Windows Server 2003, instale o Integration Services convidado do Hyper-V no sistema operacional da VM. [Saiba mais](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).

### <a name="prepare-linux-machines"></a>Preparar computadores Linux

1. Instale o Integration Services Linux do Hyper-V. A maioria das novas versões das distribuições do Linux inclui isso por padrão.
2. Recrie a imagem de inicialização do Linux para que ela contenha os drivers do Hyper-V necessários. Isso garante que a VM será inicializada no Azure e só será necessária em algumas distribuições.
3. [Habilite o log do console serial do Azure](../virtual-machines/troubleshooting/serial-console-linux.md). Isso ajuda na solução de problemas. Você não precisa reinicializar a VM. A VM do Azure será inicializada usando a imagem de disco. Isso é equivalente a uma reinicialização para a nova VM.
4. Atualize o arquivo do mapa do dispositivo com o nome do dispositivo para associações de volume, para que você use identificadores de dispositivo persistentes.
5. Atualize as entradas fstab para usar identificadores de volume persistentes.
6. Remova as regras udev que reservam nomes de interface com base no endereço MAC, etc.
7. Atualize as interfaces de rede para receber um endereço IP do DHCP.
8. [Saiba mais](../virtual-machines/linux/create-upload-generic.md) sobre as etapas necessárias para executar uma VM do Linux no Azure e obtenha instruções para algumas das distribuições populares do Linux.

## <a name="preserve-drive-letters-after-migration"></a>Preservar as letras da unidade após a migração

Quando você migra um computador local para Microsoft Azure, as letras de unidade de discos de dados adicionais podem mudar de seus valores anteriores. Por padrão, as VMs do Azure são atribuídas à unidade D para uso como armazenamento temporário. Essa atribuição de unidade faz com que todas as outras atribuições de unidade de armazenamento anexadas sejam incrementadas por uma letra.

Por exemplo, se sua instalação local usar um disco de dados atribuído à unidade D para instalações de aplicativos, a atribuição dessa unidade será incrementada para a unidade E depois que você migrar a VM para o Azure. Para evitar essa atribuição automática e garantir que o Azure atribua a próxima letra de unidade gratuita ao seu volume temporário, defina a política de SAN (rede de área de armazenamento) como OnlineAll, da seguinte maneira:

1. No computador local (não no servidor host), abra um prompt de comando com privilégios elevados.
2. Digite **DiskPart**.
3. Digite **San**. Se a letra da unidade do sistema operacional convidado não for mantida, **offline todos** ou **compartilhado offline** será retornado.
4. No prompt do **DiskPart** , digite **SANPOLICY = ONLINEALL**. Essa configuração garante que os discos sejam colocados online e sejam legíveis e graváveis.
5. Durante a migração de teste, você pode verificar se as letras da unidade são preservadas.


## <a name="check-azure-vm-requirements"></a>Verificar os requisitos de VM do Azure

Os computadores locais que você replica para o Azure devem estar em conformidade com os requisitos de VM do Azure para sistema operacional e arquitetura, discos, configurações de rede e nomenclatura de VM. Verifique os requisitos para [VMs VMware/servidores físicos](migrate-support-matrix-vmware.md#azure-vm-requirements)e [VMs do Hyper-V](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) antes da migração.


## <a name="prepare-to-connect-after-migration"></a>Preparar para conectar após a migração

As VMs do Azure são criadas durante a migração para o Azure. Após a migração, você precisa ser capaz de se conectar às novas VMs do Azure. Há várias etapas necessárias para se conectar com êxito.

### <a name="prepare-to-connect-to-windows-azure-vms"></a>Preparar para se conectar a VMs do Windows Azure

Em máquinas locais do Windows, faça o seguinte:

1. Defina as configurações do Windows. Isso inclui a remoção de rotas persistentes estáticas ou do WinHTTP proxy.
2. Verifique se [esses serviços](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) estão em execução.
3. Habilite a área de trabalho remota (RDP) para permitir conexões remotas com o computador local. [Saiba como habilitar o](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) RDP com o PowerShell.
4. Para acessar uma VM do Azure pela Internet após a migração, no firewall do Windows no computador local, permita TCP e UDP no perfil público e defina o RDP como um aplicativo permitido para todos os perfis.
5. Se você quiser acessar uma VM do Azure por meio de uma VPN site a site após a migração, no firewall do Windows no computador local, permita o RDP para o domínio e os perfis particulares. [Saiba](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) como permitir o tráfego RDP. 
6. Certifique-se de que não haja nenhuma atualização do Windows pendente na VM local quando você migrar. Se houver, as atualizações poderão iniciar a instalação na VM do Azure após a migração e você não poderá entrar na VM até que as atualizações sejam concluídas.


### <a name="prepare-to-connect-with-linux-azure-vms"></a>Preparar para conectar-se com VMs do Azure do Linux

Em computadores Linux locais, faça o seguinte:

1. Verifique se o serviço de Secure Shell está definido para iniciar automaticamente na inicialização do sistema.
2. Verifique se as regras de firewall permitem uma ligação SSH.

### <a name="configure-azure-vms-after-migration"></a>Configurar VMs do Azure após a migração

Após a migração, faça o seguinte nas VMs do Azure que são criadas.

1. Para se conectar à VM pela Internet, atribua um endereço IP público à VM. Você não pode usar o mesmo endereço IP público para a VM do Azure que você usou para seu computador local. [Saiba mais](../virtual-network/virtual-network-public-ip-address.md).
2. Verifique se as regras do NSG (grupo de segurança de rede) na VM permitem conexões de entrada para a porta RDP ou SSH.
3. Verifique o [diagnóstico de inicialização](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) para exibir a VM.

> [!NOTE]
> O serviço de bastiões do Azure oferece acesso RDP privado e SSH para VMs do Azure. [Saiba mais](../bastion/bastion-overview.md) sobre este serviço.



## <a name="next-steps"></a>Passos seguintes

Decida qual método você deseja usar para [migrar VMs VMware](server-migrate-overview.md) para o Azure ou iniciar a migração de [VMs Hyper-V](tutorial-migrate-hyper-v.md) ou [servidores físicos ou VMS virtualizadas/de nuvem](tutorial-migrate-physical-virtual-machines.md).
