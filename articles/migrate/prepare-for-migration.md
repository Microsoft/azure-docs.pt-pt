---
title: Prepare máquinas para migração com Azure Migrate
description: Aprenda a preparar máquinas no local para migração com a Azure Migrate.
ms.topic: tutorial
ms.date: 02/17/2020
ms.custom: MVC
ms.openlocfilehash: eba177a254606bb847e0866ae48281a889c53f9b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78927476"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Prepare máquinas no local para migração para Azure

Este artigo descreve como preparar máquinas no local antes de começar a migrar para Azure usando [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool).

Neste artigo, irá:
> [!div class="checklist"]
> * Verifique as limitações de migração.
> * Verifique os requisitos do sistema operativo e limitações de suporte.
> * Reveja o URL e o acesso portuário às máquinas que pretende migrar.
> * Reveja as alterações que poderá ter de fazer antes de começar a migração.
> * Configure as definições para que as letras de unidade sejam preservadas após a migração.
> * Prepare as máquinas para que possa ligar-se aos VMs Azure após a migração.

## <a name="verify-migration-limitations"></a>Verificar limitações de migração

- Você pode avaliar até 35.000 VMs VMs VMs/Hyper-V num único projeto Azure Migrate utilizando a Migração do Servidor Migratório Migratório Azure Migrate. Um projeto pode combinar VMs VMware e VMs Hiper-V, até os limites para cada um.
- Pode selecionar até 10 VMs de cada vez para migração. Se precisar de se replicar mais, reproduza-se em grupos de 10.
- Para a migração sem agente VMware, pode executar até 100 replicações simultaneamente.

## <a name="verify-operating-system-requirements"></a>Verificar os requisitos do sistema operativo

- Verifique se os seus [sistemas operativos Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) são suportados no Azure.
- Verifique se as suas [distribuições Linux](../virtual-machines/linux/endorsed-distros.md) são suportadas no Azure.

## <a name="see-whats-supported"></a>Veja o que é apoiado

Para VMware VMs, a Migração do Servidor suporta [migração sem agente ou baseada em agentes](server-migrate-overview.md).

- **VMware VMs**: Verifique [os requisitos de migração e suporte](migrate-support-matrix-vmware-migration.md) para VMware VMs.
- **Hiper-V VMs**: Verifique [os requisitos de migração e suporte](migrate-support-matrix-hyper-v-migration.md) para VMs hiper-V.
- **Máquinas físicas**: Verifique os requisitos de [migração e suporte](migrate-support-matrix-physical-migration.md) para máquinas físicas no local e outros servidores virtualizados. 

## <a name="review-url-and-port-access"></a>Rever URL e acesso à porta

As máquinas podem precisar de acesso à Internet durante a migração.

- **Aparelho migratório Azure**: Reveja [os URLs](migrate-appliance.md#url-access) e as [portas](migrate-support-matrix-vmware-migration.md#agentless-ports) que o aparelho Azure Migrate precisa de aceder durante a migração sem agente.
- **Migração baseada em vmware VM**: Reveja [urLs](migrate-replication-appliance.md#url-access) e [portas](migrate-replication-appliance.md#port-access) que o aparelho de replicação utiliza durante a migração baseada em vmware VM. 
- **Anfitriões hiper-V**: Reveja [urLs e portas que](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) os anfitriões hyper-V precisam de aceder durante a migração. 
- **Servidores físicos**: Reveja [os URLs](migrate-replication-appliance.md#url-access) e as [portas](migrate-replication-appliance.md#port-access) que o aparelho de replicação utiliza durante a migração do servidor físico.

## <a name="verify-required-changes-before-migrating"></a>Verifique as alterações necessárias antes da migração

Alguns VMs podem exigir alterações para que possam correr em Azure. A Azure Migrate faz estas alterações automaticamente para os VMs que estão a executar estes sistemas operativos:

- Red Hat Enterprise Linux 7.0+, 6.5+
- Centos 7.0+, 6.5+
- SUSE Linux Enterprise Server 12 SP1+
- Ubuntu 18.04LTS, 16.04LTS, 14.04LTS
- Debiano 8, 7

Para outros sistemas operativos, deve preparar manualmente as máquinas antes da migração. 

### <a name="prepare-windows-machines"></a>Preparar máquinas Windows

Se estiver a migrar uma máquina Windows, faça as seguintes alterações antes da migração. Se migrar o VM antes de fazer as alterações, o VM pode não arrancar em Azure.

1. [Ativar](../virtual-machines/troubleshooting/serial-console-windows.md) a consola em série Azure para o Azure VM. Ativar a consola ajuda-o a resolver problemas. Não precisas de reiniciar o VM. O VM Azure arrancará utilizando a imagem do disco. A bota de imagem do disco é equivalente a um reboot para o novo VM. 
2. Se estiver a migrar máquinas que estão a executar o Windows Server 2003, instale serviços de integração de hóspedes Hiper-V no sistema operativo VM. [Saiba mais](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).

### <a name="prepare-linux-machines"></a>Prepare as máquinas Linux

1. Instale serviços de integração Hyper-V Linux. A maioria das novas versões de distribuições linux incluem Hiper-V Linux Integration Services por padrão.
2. Reconstrua a imagem de init linux para que contenha os necessários condutores hyper-V. A reconstrução da imagem inicial garante que o VM arrancará em Azure (exigido apenas em algumas distribuições).
3. [Ativar o registo da consola em série Azure.](../virtual-machines/troubleshooting/serial-console-linux.md) Permitir o registo de consolas ajuda-o a resolver problemas. Não precisas de reiniciar o VM. O VM Azure arrancará utilizando a imagem do disco. A bota de imagem do disco é equivalente a um reboot para o novo VM.
4. Atualize o ficheiro do mapa do dispositivo com as associações nome-volume do dispositivo, para que utilize identificadores persistentes do dispositivo.
5. Atualizar as entradas no ficheiro fstab para utilizar identificadores de volume persistentes.
6. Remova quaisquer regras udev que reservem nomes de interface com base no endereço MAC, e assim por diante.
7. Atualizar interfaces de rede para receber um endereço IP do DHCP.

Saiba mais sobre os [passos para executar um Linux VM no Azure](../virtual-machines/linux/create-upload-generic.md), e obtenha instruções para algumas das populares distribuições linux.

## <a name="preserve-drive-letters-after-migration"></a>Preservar as cartas de condução após a migração

Quando se migra uma máquina no local para o Microsoft Azure, as letras de acionamento de discos de dados adicionais podem mudar dos seus valores originais. 

Por predefinição, os VMs Azure são designados para a unidade D para usar como armazenamento temporário. Esta atribuição de unidade faz com que todas as outras atribuições de unidade de armazenamento anexas incrementem por uma letra. Por exemplo, se a sua instalação no local utilizar um disco de dados que é atribuído para conduzir D para instalações de aplicação, a atribuição para este incremento de unidade para conduzir E depois de migrar o VM para Azure. Para evitar esta atribuição automática e para garantir que o Azure atribui a próxima carta de acesso gratuito ao seu volume temporário, delineie a política da rede de área de armazenamento (SAN) para **onlineAll:**

1. Na máquina no local (não no servidor de anfitriões), abra um pedido de comando elevado.
2. Introduza **a peça do disco**.
3. **Insira SAN**. Se a letra de unidade do sistema operativo de hóspedes não for mantida, **offline All** ou **Offline Shared** é devolvido.
4. Na solicitação **do DISKPART,** introduza **a SAN Policy=OnlineAll**. Esta definição garante que os discos são trazidos online e garante que pode ler e escrever para ambos os discos.
5. Durante a migração do teste, pode verificar se as letras de unidade estão preservadas.

## <a name="check-azure-vm-requirements"></a>Verifique os requisitos da VM Azure

As máquinas no local que replica para o Azure devem cumprir os requisitos do Azure VM para o sistema operativo e arquitetura, os discos, as definições de rede e o nome vM. Verifique os requisitos para [VMware VMs e servidores físicos](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)e [VMs Hiper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) antes de migrar.

## <a name="prepare-to-connect-after-migration"></a>Prepare-se para ligar após a migração

Os VMs azure são criados durante a migração para Azure. Após a migração, deve ser capaz de se ligar aos novos VMs Azure. São necessários vários passos para se ligarcom sucesso.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Prepare-se para ligar aos VMs do Windows Azure

No local, as máquinas Windows:

1. Configure as definições do Windows. As definições incluem a remoção de quaisquer rotas estáticas persistentes ou proxy WinHTTP.
2. Certifique-se de que [os serviços necessários](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) estão em funcionamento.
3. Ativar o ambiente de trabalho remoto (RDP) para permitir ligações remotas à máquina no local. Aprenda a utilizar o [PowerShell para ativar o RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
4. Para aceder a um Azure VM através da internet após a migração, no Windows Firewall na máquina no local, permitir TCP e UDP no perfil público, e definir rdp como uma aplicação permitida para todos os perfis.
5. Se pretender aceder a um VM Azur e um VPN local após a migração, no Windows Firewall na máquina no local, permita RDP para os perfis de Domínio e Privado. Saiba como permitir o [tráfego de RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules). 
6. Certifique-se de que não existem atualizações do Windows pendentes no VM no local quando migrar. Se houver, as atualizações podem começar a instalar-se no VM Azure após a migração, e não poderá assinar no VM até que as atualizações terminem.

### <a name="prepare-to-connect-with-linux-azure-vms"></a>Prepare-se para se conectar com Os VMs Linux Azure

No local, máquinas Linux:

1. Verifique se o serviço Secure Shell está programado para iniciar automaticamente a bota do sistema.
2. Verifique se as regras de firewall permitem uma ligação SSH.

### <a name="configure-azure-vms-after-migration"></a>Configure VMs Azure após migração

Após a migração, complete estes passos sobre os VMs Azure que são criados:

1. Para ligar ao VM através da internet, atribua um endereço IP público ao VM. Deve utilizar um endereço IP público diferente do Que usou para a sua máquina no local. [Saiba mais](../virtual-network/virtual-network-public-ip-address.md).
2. Verifique se as regras do grupo de segurança da rede (NSG) sobre o VM permitem a entrada de ligações à porta RDP ou SSH.
3. Verifique os [diagnósticos da bota](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) para ver o VM.

> [!NOTE]
> O serviço Azure Bastion oferece acesso privado de RDP e SSH aos VMs Azure. [Saiba mais](../bastion/bastion-overview.md) sobre este serviço.

## <a name="next-steps"></a>Passos seguintes

Decida qual o método que pretende utilizar para [migrar VMware VMs](server-migrate-overview.md) para O Azure, ou começar a migrar [VMs Hiper-V](tutorial-migrate-hyper-v.md) ou [servidores físicos ou VMs virtualizados ou em nuvem](tutorial-migrate-physical-virtual-machines.md).
