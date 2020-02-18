---
title: Prepare máquinas para migração com Azure Migrate
description: Aprenda a preparar máquinas no local para migração com a Azure Migrate.
ms.topic: tutorial
ms.date: 02/17/2020
ms.custom: MVC
ms.openlocfilehash: adbe9e4b30bf57e8a2038b970306c126035abbe1
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77426253"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Prepare máquinas no local para migração para Azure

Este artigo descreve como preparar máquinas no local antes de começar a migrar para Azure com [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool).


Neste artigo, irá:
> [!div class="checklist"]
> * Verifique as limitações de migração.
> * Verifique os requisitos do sistema operativo e limitações de suporte.
> * Reveja o acesso URL/porta para máquinas que pretende migrar.
> * Reveja as alterações que poderá ter de fazer antes de começar a migração.
> * Configure as definições para que as letras de acionamento sejam preservadas após a migração.
> * Prepare as máquinas para que possa ligar-se aos VMs Azure após a migração.


## <a name="verify-migration-limitations"></a>Verificar limitações de migração

- Você pode avaliar até 35.000 VMs VMs/Hyper-V vms em um único projeto Azure Migrate usando migração de servidores migratórios Azure. Um projeto pode combinar vMs vmware e VMs Hiper-V, até os limites para cada um.
- Pode selecionar até 10 VMs ao mesmo tempo para migração. Se precisar de se replicar mais, reproduza-se em grupos de 10.
- Para a migração sem agente VMware, pode executar até 100 replicações simultaneamente.

## <a name="verify-operating-system-requirements"></a>Verificar os requisitos do sistema operativo

- Verifique se os seus [sistemas operativos Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) são suportados no Azure.
- Verifique se as suas [distribuições Linux](../virtual-machines/linux/endorsed-distros.md) são suportadas no Azure.


## <a name="check-whats-supported"></a>Verifique o que é suportado

- Para VMware VMs, a Migração do Servidor suporta [migração sem agente ou baseada em agentes](server-migrate-overview.md). Verifique [os requisitos de migração vmware vM e suporte](migrate-support-matrix-vmware-migration.md).
- Verifique os requisitos de [migração e o suporte](migrate-support-matrix-hyper-v-migration.md) para hyper-V.
- Verifique os requisitos de [migração e suporte](migrate-support-matrix-physical-migration.md) para máquinas físicas no local, ou outros servidores virtualizados. 




## <a name="review-urlport-access"></a>Rever URL/acesso à porta

As máquinas podem precisar de acesso à Internet durante a migração.

- [Reveja os URLs](migrate-appliance.md#url-access) a que o aparelho Azure Migrate precisa de aceder durante a migração sem agente. Rever os requisitos de [acesso à porta.](migrate-support-matrix-vmware-migration.md#agentless-ports)
- Reveja [os URLs](migrate-replication-appliance.md#url-access) e [portas] (migrate-replication-appliance.md#port-access) que o aparelho de replicação utiliza durante a migração baseada em vmware VM. 
- [Revisão](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) UrLs e portas que os anfitriões hyper-V precisam de aceder durante a migração. 
- Reveja [os URLs](migrate-replication-appliance.md#url-access) e [portas] (migrate-replication-appliance.md#port-access) que o aparelho de replicação utiliza durante a migração do servidor físico.



## <a name="verify-required-changes-before-migration"></a>Verificar as alterações necessárias antes da migração

Alguns VMs podem exigir alterações para que possam correr em Azure. A Azure Migrate faz estas alterações automaticamente para os VMs que executam estes sistemas operativos:
- Red Hat Enterprise Linux 6.5+, 7.0+
- Centos 6.5+, 7.0+
- SUSE Linux Enterprise Server 12 SP1+
- Ubuntu 14.04LTS, 16.04LTS, 18.04LTS
- Debian 7, 8

Para outros sistemas operativos, é necessário preparar as máquinas manualmente antes da migração. 

### <a name="prepare-windows-machines"></a>Preparar máquinas Windows

Se estiver a migrar uma máquina do Windows, então faça estas alterações antes da migração. Se migrar o VM antes de fazer as alterações, o VM pode não arrancar em Azure.

1. Ative a consola de [acesso em série Azure](../virtual-machines/troubleshooting/serial-console-windows.md) para o Azure VM. Isto ajuda na resolução de problemas. Não precisas de reiniciar o VM. O Azure VM arrancará utilizando a imagem do disco. Isto equivale a um reboot para o novo VM. 
2. Se estiver a migrar máquinas que executam o Windows Server 2003, instale serviços de integração de hóspedes Hiper-V no sistema operativo VM. [Saiba mais](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).

### <a name="prepare-linux-machines"></a>Prepare as máquinas Linux

1. Instale serviços de integração Hyper-V Linux. A maioria das novas versões de distribuições do Linux incluem isto por padrão.
2. Reconstrua a imagem de init linux para que contenha os necessários condutores hyper-V. Isto garante que o VM irá arrancar em Azure, e só é necessário em algumas distribuições.
3. [Ativar o registo de consolas em série Azure.](../virtual-machines/troubleshooting/serial-console-linux.md) Isto ajuda na resolução de problemas. Não precisas de reiniciar o VM. O Azure VM arrancará utilizando a imagem do disco. Isto equivale a um reboot para o novo VM.
4. Atualize o ficheiro do mapa do dispositivo com o nome do dispositivo para associações de volume, de modo a que utilize identificadores de dispositivo sinuosos.
5. Atualizar as entradas de fstab para utilizar identificadores de volume persistentes.
6. Remova quaisquer regras udev que reservem nomes de interface com base no endereço MAC, etc.
7. Atualizar interfaces de rede para receber um endereço IP do DHCP.
8. [Saiba mais](../virtual-machines/linux/create-upload-generic.md) sobre os passos necessários para executar um Linux VM em Azure, e obtenha instruções para algumas das populares distribuições linux.

## <a name="preserve-drive-letters-after-migration"></a>Preservar as cartas de condução após a migração

Quando se migra uma máquina no local para o Microsoft Azure, as letras de acionamento de discos de dados adicionais podem mudar em função dos valores anteriores. Por predefinição, os VMs Azure são designados de unidade D para utilização como armazenamento temporário. Esta atribuição de unidade faz com que todas as outras atribuições de unidade de armazenamento anexas incrementem por uma letra.

Por exemplo, se a sua instalação no local utilizar um disco de dados que é atribuído para conduzir D para instalações de aplicação, a atribuição para este incremento de unidade para conduzir E depois de migrar o VM para Azure. Para evitar esta atribuição automática, e para garantir que o Azure atribui a próxima carta de unidade gratuita ao seu volume temporário, delineie a política da rede de área de armazenamento (SAN) para onlineAll, da seguinte forma:

1. Na máquina no local (não no servidor de anfitriões) abre um pedido de comando elevado.
2. Digite **peça de disco**.
3. Tipo **SAN**. Se a letra de unidade do sistema operativo de hóspedes não for mantida, **offline All** ou **Offline Shared** é devolvido.
4. No aviso **DISKPART,** digite **SAN Policy=OnlineAll**. Esta definição garante que os discos são trazidos online, e são simultaneamente legíveis e relegáveis.
5. Durante a migração do teste, pode verificar se as letras de unidade estão preservadas.


## <a name="check-azure-vm-requirements"></a>Verifique os requisitos da VM Azure

As máquinas no local que replica para o Azure devem cumprir os requisitos do Azure VM para sistema operativo e arquitetura, discos, definições de rede e nomeação vm. Verifique os requisitos para [VMware VMs/servidores físicos](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)e [VMs Hiper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) antes da migração.


## <a name="prepare-to-connect-after-migration"></a>Prepare-se para ligar após a migração

Os VMs azure são criados durante a migração para Azure. Após a migração, você precisa ser capaz de se conectar com os novos VMs Azure. Há uma série de passos necessários para se conectar com sucesso.

### <a name="prepare-to-connect-to-windows-azure-vms"></a>Prepare-se para ligar aos VMs do Windows Azure

No local, faça o seguinte:

1. Configure as definições do Windows. Estes incluem a remoção de quaisquer rotas estáticas persistentes ou proxy WinHTTP.
2. Certifique-se de que [estes serviços](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) estão em funcionamento.
3. Ativar o ambiente de trabalho remoto (RDP) para permitir ligações remotas à máquina no local. [Aprenda](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) a ativar rdp com powerShell.
4. Para aceder a um Azure VM através da internet após a migração, no Windows Firewall na máquina no local, permitir TCP e UDP no perfil público, e definir rdp como uma aplicação permitida para todos os perfis.
5. Se pretender aceder a um VM Azur e um VPN local após a migração, no Windows Firewall na máquina no local, permita RDP para os perfis de Domínio e Privado. [Saiba](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) como permitir o tráfego de RDP. 
6. Certifique-se de que não existem atualizações do Windows pendentes no VM no local quando migrar. Se houver, as atualizações podem começar a instalar-se no VM Azure após a migração, e não poderá assinar no VM até que as atualizações terminem.


### <a name="prepare-to-connect-with-linux-azure-vms"></a>Prepare-se para se conectar com Os VMs Linux Azure

Nas máquinas Linux no local, faça o seguinte:

1. Verifique se o serviço Secure Shell está programado para iniciar automaticamente a bota do sistema.
2. Verifique se as regras de firewall permitem uma ligação SSH.

### <a name="configure-azure-vms-after-migration"></a>Configure VMs Azure após migração

Após a migração, faça o seguinte sobre os VMs Azure que são criados.

1. Para ligar ao VM através da internet, atribua um endereço IP público ao VM. Não pode utilizar o mesmo endereço IP público para o VM Azure que usou para a sua máquina no local. [Saiba mais](../virtual-network/virtual-network-public-ip-address.md).
2. Verifique se as regras do grupo de segurança da rede (NSG) sobre o VM permitem a entrada de ligações à porta RDP ou SSH.
3. Verifique os [diagnósticos da Bota](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) para ver o VM.

> [!NOTE]
> O serviço Azure Bastion oferece acesso privado de RDP e SSH aos VMs Azure. [Saiba mais](../bastion/bastion-overview.md) sobre este serviço.



## <a name="next-steps"></a>Passos seguintes

Decida qual o método que pretende utilizar para [migrar VMware VMs](server-migrate-overview.md) para O Azure, ou comece a migrar [VMs Hiper-V](tutorial-migrate-hyper-v.md) ou [servidores físicos ou VMs virtualizados/em nuvem](tutorial-migrate-physical-virtual-machines.md).
