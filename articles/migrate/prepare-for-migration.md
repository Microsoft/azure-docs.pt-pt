---
title: Prepare máquinas para migração com Azure Migrate
description: Saiba como preparar máquinas no local para migração com a Azure Migrate.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 06/08/2020
ms.openlocfilehash: d8f9d4e0b002348f286f45c6b45c96531c5d6530
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558232"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Preparar máquinas no local para migração para Azure

Este artigo descreve como preparar máquinas no local antes de as migrar para Azure utilizando a ferramenta [Azure Migrate:Server Migration.](migrate-services-overview.md#azure-migrate-server-migration-tool)

Neste artigo, irá:
> [!div class="checklist"]
> * Reveja as limitações de migração.
> * Selecione um método para migrar VMware VMs
> * Verifique os requisitos do hipervisor e do sistema operativo para as máquinas que pretende migrar.
> * Reveja o URL e o acesso portuário às máquinas que pretende migrar.
> * Reveja as alterações que poderá ter de fazer antes de começar a migração.
> * Verifique os requisitos dos VMs da Azure para máquinas migradas
> * Prepare as máquinas para que possa ligar-se aos VMs Azure após a migração.



## <a name="verify-migration-limitations"></a>Verificar limitações de migração

A tabela resume os limites de descoberta, avaliação e migração para Azure Migrate. Recomendamos que avalie as máquinas antes da migração, mas não precisa.

**Cenário** | **Project** | **Descoberta/Avaliação** | **Migração**
--- | --- | --- | ---
**VMs VMware** | Descubra e avalie até 35.000 VMs num único projeto Azure Migrate. | Descubra até 10.000 VMware VMs com um único [aparelho Azure Migrate](common-questions-appliance.md) para VMware. | **Migração sem agente:** pode simultaneamente replicar um máximo de 500 VMs de cada vCenter Server. **Migração baseada em agente:** pode [escalonar](./agent-based-migration-architecture.md#performance-and-scaling) o [aparelho de replicação](migrate-replication-appliance.md) para replicar um grande número de VMs.<br/><br/> No portal, pode selecionar até 10 máquinas ao mesmo tempo para replicação. Para replicar mais máquinas, adicione em lotes de 10.
**VMs Hyper-V** | Descubra e avalie até 35.000 VMs num único projeto Azure Migrate. | Descubra até 5.000 VMs Hiper-V com um único aparelho Azure Migrate | Um aparelho não é usado para migração de Hiper-V. Em vez disso, o Fornecedor de Replicação Hiper-V funciona em cada anfitrião Hiper-V.<br/><br/> A capacidade de replicação é influenciada por fatores de desempenho como o churn VM e carregar largura de banda para dados de replicação.<br/><br/> No portal, pode selecionar até 10 máquinas ao mesmo tempo para replicação. Para replicar mais máquinas, adicione em lotes de 10.
**Computadores** | Descubra e avalie até 35.000 máquinas num único projeto Azure Migrate. | Descubra até 250 servidores físicos com um único aparelho Azure Migrate para servidores físicos. | Pode [escalonar](./agent-based-migration-architecture.md#performance-and-scaling) o [aparelho de replicação](migrate-replication-appliance.md) para replicar um grande número de servidores.<br/><br/> No portal, pode selecionar até 10 máquinas ao mesmo tempo para replicação. Para replicar mais máquinas, adicione em lotes de 10.

## <a name="select-a-vmware-migration-method"></a>Selecione um método de migração VMware

Se estiver a migrar VMware VMs para Azure, [compare](server-migrate-overview.md#compare-migration-methods) os métodos de migração sem agente e baseados em agentes, para decidir o que funciona para si.

## <a name="verify-hypervisor-requirements"></a>Verificar os requisitos do hipervisor

- Verifique os [requisitos de agente VMware ou](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) [VMware baseados em agentes.](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based)
- Verifique os requisitos [do hospedeiro Hyper-V.](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements)


## <a name="verify-operating-system-requirements"></a>Verificar os requisitos do sistema operativo

Verifique os sistemas operativos suportados para a migração:

- Se estiver a migrar VMware VMs ou VMs hiper-V, verifique os requisitos de VMware VM para migração [sem agente,](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless)e [por agentes,](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) e requisitos para [Hiper-VMs](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms).
- Verifique se os [sistemas operativos Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) são suportados no Azure.
- Verifique as [distribuições do Linux](../virtual-machines/linux/endorsed-distros.md) suportadas no Azure.

## <a name="review-url-and-port-access"></a>Rever URL e acesso à porta

Rever quais URLs e portos são acedidos durante a migração.

**Cenário** | **Detalhes** |  **URLs** | **Portas**
--- | --- | --- | ---
**VMware migração sem agente** | Utiliza o [aparelho Azure Migrate](migrate-appliance-architecture.md) para migração. Nada está instalado em VMware VMs. | Reveja a nuvem pública e [os URLs governamentais necessários](migrate-appliance.md#url-access) para a descoberta, avaliação e migração com o aparelho. | [Reveja](migrate-support-matrix-vmware-migration.md#port-requirements-agentless) os requisitos portuários para a migração sem agentes.
**Migração baseada em agentes VMware** | Utiliza o [aparelho de replicação](migrate-replication-appliance.md) para a migração. O agente de serviço de mobilidade está instalado em VMs. | Reveja a [nuvem pública](migrate-replication-appliance.md#url-access) e os URLs [do Governo Azure](migrate-replication-appliance.md#azure-government-url-access) que o aparelho de replicação precisa de aceder. | [Reveja](migrate-replication-appliance.md#port-access) as portas utilizadas durante a migração baseada em agentes.
**Migração hiper-V** | Utiliza um Fornecedor instalado em anfitriões Hiper-V para migração. Nada está instalado em Hiper-VMs. | Reveja a [nuvem pública](migrate-support-matrix-hyper-v-migration.md#url-access-public-cloud) e os URLs [do Governo Azure](migrate-support-matrix-hyper-v-migration.md#url-access-azure-government) que o Fornecedor de Replicação que funciona nos anfitriões precisa de aceder. | O Fornecedor de Replicação no anfitrião Hiper-V utiliza ligações de saída na porta HTTPS 443 para enviar dados de replicação VM.
**Computadores** | Utiliza o [aparelho de replicação](migrate-replication-appliance.md) para a migração. O agente de serviço de mobilidade está instalado nas máquinas físicas. | Reveja a [nuvem pública](migrate-replication-appliance.md#url-access) e os URLs [do Governo Azure](migrate-replication-appliance.md#azure-government-url-access) que o aparelho de replicação precisa de aceder. | [Reveja](migrate-replication-appliance.md#port-access) os portos utilizados durante a migração física.

## <a name="verify-required-changes-before-migrating"></a>Verifique as alterações necessárias antes de migrar

São necessárias algumas alterações nos VM antes de as migrar para Azure.

- Para alguns sistemas operativos, a Azure Migrate faz alterações automaticamente durante o processo de replicação/migração.
- Para outros sistemas operativos, é necessário configurar as definições manualmente.
- É importante configurar as definições manualmente antes de começar a migração. Se migrar o VM antes de fazer a alteração, o VM pode não arrancar em Azure.

Reveja as tabelas para identificar as alterações que precisa de fazer.

### <a name="windows-machines"></a>Máquinas de windows

As alterações necessárias são resumidas na tabela.

**Ação** | **VMware (migração sem agente)** | **VMware (baseado em agente)/máquinas físicas** | **Janelas em Hiper-V** 
--- | --- | --- | ---
**Configure a política da SAN como Online All**<br/><br/> Isto garante que os volumes do Windows em Azure VM utilizem as mesmas atribuições de letra de acionamento que o VM no local. | Configurar automaticamente para máquinas que executam o Windows Server 2008 R2 ou mais tarde.<br/><br/> Configurar manualmente para sistemas operativos anteriores. | Desa quando se pode definir automaticamente na maioria dos casos. | Configurar manualmente.
**Instalar integração de hóspedes Hiper-V** | [Instale manualmente](prepare-windows-server-2003-migration.md#install-on-vmware-vms) em máquinas que executam o Windows Server 2003. | [Instale manualmente](prepare-windows-server-2003-migration.md#install-on-vmware-vms) em máquinas que executam o Windows Server 2003. | [Instale manualmente](prepare-windows-server-2003-migration.md#install-on-hyper-v-vms) em máquinas que executam o Windows Server 2003.
**Ativar a consola em série Azure**.<br/><br/>[Ative a consola](/troubleshoot/azure/virtual-machines/serial-console-windows) em VMs Azure para ajudar na resolução de problemas. Não precisas de reiniciar o VM. O Azure VM arrancará utilizando a imagem do disco. A bota de imagem do disco equivale a um reboot para o novo VM. | Ativar manualmente | Ativar manualmente | Ativar manualmente
**Conecte-se após a migração**<br/><br/> Para se conectar após a migração, há uma série de passos a tomar antes de migrar. | [Configurar](#prepare-to-connect-to-azure-windows-vms) manualmente. | [Configurar](#prepare-to-connect-to-azure-windows-vms) manualmente. | [Configurar](#prepare-to-connect-to-azure-windows-vms) manualmente.


#### <a name="configure-san-policy"></a>Configure a política SAN

Por predefinição, os VMs Azure são atribuídos drive D para usar como armazenamento temporário.

- Esta atribuição de unidade faz com que todas as outras atribuições de unidade de armazenamento anexadas incrementem por uma letra.
- Por exemplo, se a sua instalação no local utilizar um disco de dados que está designado para conduzir D para instalações de aplicações, a atribuição para este impulso de unidade incrementa para conduzir E depois de migrar o VM para Azure. 
- Para evitar esta atribuição automática, e para garantir que a Azure atribui a próxima carta de condução gratuita ao seu volume temporário, dedibra a política da rede de área de armazenamento (SAN) ao **OnlineAll:**

Configure esta definição manualmente da seguinte forma:

1. Na máquina no local (não no servidor anfitrião), abra um pedido de comando elevado.
2. Introduza **a parte do disco**.
3. Insira **SAN**. Se a carta de condução do sistema operativo do hóspede não for mantida, **offline All** ou **Offline Shared** é devolvida.
4. No pedido **DISKPART,** insira **SAN Policy=OnlineAll**. Esta definição garante que os discos são trazidos on-line, e garante que pode ler e escrever em ambos os discos.
5. Durante a migração do teste, pode verificar se as letras de acionamento estão preservadas.


### <a name="linux-machines"></a>Máquinas Linux

Azure Migrate completa estas ações automaticamente para estas versões

- Red Hat Enterprise Linux 7.8, 7.7, 7.6, 7.5, 7.4, 7.0, 6.x (O agente VM Azure Linux também é instalado automaticamente durante a migração)
- Cent OS 7.7, 7.6, 7.5, 7.4, 6.x (O agente VM Azure Linux também é instalado automaticamente durante a migração)
- SUSE Linux Enterprise Server 12 SP1+
- SUSE Linux Enterprise Server 15 SP1
- Ubuntu 19.04, 19.10, 18.04LTS, 16.04LTS, 14.04LTS (O agente VM Azure Linux também é instalado automaticamente durante a migração)
- Ubuntu 18.04LTS, 16.04LTS
- Debian 9, 8, 7
- Oracle Linux 7.7, 7.7-CI

Para outras versões, prepare as máquinas como resumido na tabela.  


**Ação** | **Detalhes** | **Versão Linux**
--- | --- | ---
**Instalar serviços de integração do Linux Hiper-V** | Reconstrua a imagem init Linux para conter os condutores hiper-V necessários. A reconstrução da imagem init garante que o VM arrancará em Azure. | A maioria das novas versões de distribuição do Linux incluem isto por padrão.<br/><br/> Se não estiver incluído, instale manualmente todas as versões, exceto as chamadas acima.
**Ativar o registo da consola em série do Azure** | Ativar o registo de consolas ajuda-o a resolver problemas. Não precisas de reiniciar o VM. O Azure VM arrancará utilizando a imagem do disco. A bota de imagem do disco equivale a um reboot para o novo VM.<br/><br/> Siga [estas instruções](/troubleshoot/azure/virtual-machines/serial-console-linux) para ativar.
**Atualizar o ficheiro do mapa do dispositivo** | Atualize o ficheiro do mapa do dispositivo com as associações nome-volume do dispositivo, para que utilize identificadores de dispositivos persistentes. | Instale manualmente para todas as versões, exceto as chamadas acima. (Apenas aplicável no cenário VMware baseado em agente)
**Atualizar entradas fstab** |  Atualize as entradas para utilizar identificadores de volume persistentes.    | Atualize manualmente para todas as versões, exceto as chamadas acima.
**Remover a regra udev** | Remova quaisquer regras udev que reservam nomes de interface com base no endereço mac, etc. | Remova manualmente para todas as versões, exceto as chamadas acima.
**Atualizar interfaces de rede** | Atualizar interfaces de rede para receber endereço IP com base em DHCP.nst | Atualize manualmente para todas as versões, exceto as chamadas acima.
**Ativar ssh** | Certifique-se de que o ssh está ativado e que o serviço sshd está programado para iniciar automaticamente no reboot.<br/><br/> Certifique-se de que os pedidos de ligação ssh de entrada não são bloqueados pela firewall do SISTEMA ou regras scriptáveis.| Ativar manualmente todas as versões, exceto as chamadas acima.

O quadro seguinte resume os passos realizados automaticamente para os sistemas operativos acima indicados.


| Ação                                      | Migração \- VMware baseada em agente | Migração de VMware sem agente | Hyper\-V   |
|---------------------------------------------|-------------------------------|----------------------------|------------|
| Instalar serviços de integração Hyper \- V Linux | Yes                           | Yes                        | Não necessário |
| Ativar o registo da consola em série do Azure         | Yes                           | Yes                        | No         |
| Atualizar o ficheiro do mapa do dispositivo                      | Yes                           | No                         | No         |
| Atualizar entradas fstab                        | Yes                           | Yes                        | No         |
| Remover a regra udev                            | Yes                           | Yes                        | No         |
| Atualizar interfaces de rede                   | Yes                           | Yes                        | No         |
| Ativar ssh                                  | No                            | No                         | No         |

Saiba mais sobre os passos para [executar um Linux VM em Azure,](../virtual-machines/linux/create-upload-generic.md)e obtenha instruções para algumas das populares distribuições do Linux.

Reveja a lista de [pacotes necessários](../virtual-machines/extensions/agent-linux.md#requirements) para instalar o agente Linux VM. A Azure Migrate instala automaticamente o agente Linux VM para RHEL6, RHEL7, CentOS7 (6 deve ser suportado similar ao RHEL), Ubuntu 14.04, Ubuntu 16.04, Ubuntu18.04 quando utilizar o método sem agente da migração VMware.

## <a name="check-azure-vm-requirements"></a>Verifique os requisitos do Azure VM

As máquinas no local que replicar para a Azure devem cumprir os requisitos do Azure VM para o sistema operativo e arquitetura, os discos, as definições de rede e o nome de VM.

Antes de migrar, reveja os requisitos de VMs Azure para [VMware,](migrate-support-matrix-vmware-migration.md#azure-vm-requirements) [Hyper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements)e migração [de servidores físicos.](migrate-support-matrix-physical-migration.md#azure-vm-requirements)



## <a name="prepare-to-connect-after-migration"></a>Prepare-se para ligar após a migração

Os VMs Azure são criados durante a migração para Azure. Após a migração, deve ser capaz de se conectar com os novos VMs Azure. São necessários vários passos para se ligar com sucesso.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Prepare-se para ligar ao Azure Windows VMs

Nas máquinas Windows no local:

1. Configurar as definições do Windows. As definições incluem a remoção de quaisquer rotas estáticas persistentes ou procuração winHTTP.
2. Certifique-se de que os [serviços necessários](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) estão a funcionar.
3. Ativar o ambiente de trabalho remoto (PDR) para permitir ligações remotas à máquina no local. Saiba como utilizar o [PowerShell para ativar o RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
4. Para aceder a um Azure VM através da internet após a migração, no Windows Firewall na máquina de acesso, permita a TCP e a UDP no perfil público, e coloque o RDP como uma aplicação permitida para todos os perfis.
5. Se pretender aceder a um Azure VM sobre uma VPN site-to-site após a migração, no Windows Firewall na máquina de acesso, permita rdp para os perfis de Domínio e Privado. Saiba como [permitir o tráfego rdp](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules). 
6. Certifique-se de que não existem atualizações do Windows pendentes no VM no local quando migrar. Se houver, as atualizações podem começar a ser instaladas no Azure VM após a migração, e não poderá assinar no VM até que as atualizações terminem.


### <a name="prepare-to-connect-with-linux-azure-vms"></a>Prepare-se para ligar com os VMs Linux Azure

Nas máquinas Linux no local:

1. Verifique se o serviço Secure Shell está programado para iniciar automaticamente no arranque do sistema.
2. Verifique se as regras de firewall permitem uma ligação SSH.

### <a name="configure-azure-vms-after-migration"></a>Configure Azure VMs após migração

Após a migração, complete estes passos sobre os VMs Azure que são criados:

1. Para ligar ao VM através da internet, atribua um endereço IP público ao VM. Deve utilizar um endereço IP público diferente para o VM Azure do que utilizou para a sua máquina no local. [Saiba mais](../virtual-network/virtual-network-public-ip-address.md).
2. Verifique as regras do grupo de segurança da rede (NSG) no VM, permita a entrada de ligações à porta RDP ou SSH.
3. Verifique [os diagnósticos](/troubleshoot/azure/virtual-machines/boot-diagnostics#enable-boot-diagnostics-on-existing-virtual-machine) de arranque para ver o VM.


## <a name="next-steps"></a>Passos seguintes

Decida qual o método que pretende utilizar para [migrar VMware VMs](server-migrate-overview.md) para Azure, ou começar a migrar [Hiper-VMs](tutorial-migrate-hyper-v.md) ou [servidores físicos ou VMs virtualizados ou em nuvem](tutorial-migrate-physical-virtual-machines.md).

## <a name="see-whats-supported"></a>Veja o que é suportado

Para VMware VMs, a Migração de Servidores suporta [migração sem agentes ou baseada em agentes.](server-migrate-overview.md)

- **VMware VMs**: Verifique os [requisitos de migração e suporte](migrate-support-matrix-vmware-migration.md) para VMware VMs.
- **Hiper-V VMs**: Verifique os [requisitos de migração e suporte](migrate-support-matrix-hyper-v-migration.md) para VMs Hiper-V.
- **Máquinas físicas**: Verifique os requisitos de [migração e suporte](migrate-support-matrix-physical-migration.md) para máquinas físicas no local e outros servidores virtualizados.