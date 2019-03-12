---
title: Matriz de suporte para recuperação após desastre de VMs de VMware e servidores físicos para o Azure com o Azure Site Recovery | Documentos da Microsoft
description: Resume os sistemas operativos suportados e componentes para recuperação após desastre de VMs de VMware e de servidor físico para o Azure com o Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: raynew
ms.openlocfilehash: 637a8e91ba03240cd4c2c530ae2c982d2115c81d
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569829"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Matriz de suporte para recuperação após desastre de VMs de VMware e servidores físicos para o Azure

Este artigo resume os componentes suportados e configurações para recuperação após desastre de VMs de VMware para o Azure, utilizando [do Azure Site Recovery](site-recovery-overview.md).

Para começar a utilizar o Azure Site Recovery com o cenário de implementação mais simples, visite nosso [tutoriais](tutorial-prepare-azure.md). Pode saber mais sobre a arquitetura do Azure Site Recovery [aqui](vmware-azure-architecture.md).

## <a name="replication-scenario"></a>Cenário de replicação

**Cenário** | **Detalhes**
--- | ---
VMs VMware | Replicação de VMs de VMware no local para o Azure. Pode implementar este cenário no portal do Azure ou utilizando [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Servidores físicos | Replicação de servidores físicos de Windows/Linux no local para o Azure. Pode implementar este cenário no portal do Azure.

## <a name="on-premises-virtualization-servers"></a>Servidores de virtualização no local

**servidor** | **Requisitos** | **Detalhes**
--- | --- | ---
VMware | o vCenter Server 6.7, 6.5, 6.0, ou 5.5 ou vSphere 6.7, 6.5, 6.0 ou 5.5 | Recomendamos que utilize um servidor vCenter.<br/><br/> Recomendamos que os anfitriões vSphere e servidores vCenter estão localizados na mesma rede que o servidor de processos. Por predefinição os componentes de servidor de processo é executado no servidor de configuração,, esta será a rede na qual configurou o servidor de configuração, a menos que configurar um servidor de processos dedicados.
Físico | N/A

## <a name="site-recovery-configuration-server"></a>Servidor de configuração do site Recovery

O servidor de configuração é uma máquina no local que executa os componentes do Site Recovery, incluindo o servidor de configuração, o servidor de processos e o servidor de destino mestre. Para a replicação de VMware é configurar o servidor de configuração com todos os requisitos, através de um modelo do OVF para criar uma VM de VMware. Para a replicação de servidor físico, configure a máquina do servidor de configuração manualmente.

**Componente** | **Requisitos**
--- |---
Núcleos de CPU | 8
RAM | 16 GB
Número de discos | 3 discos<br/><br/> Discos incluem o disco de SO, disco de cache do servidor de processo e unidade de retenção para a reativação pós-falha.
Espaço livre em disco | 600 GB de espaço necessário para a cache do servidor de processo.
Espaço livre em disco | 600 GB de espaço necessário para a unidade de retenção.
Sistema operativo  | Windows Server 2012 R2 ou Windows Server 2016 |
Região do sistema operativo | Inglês (en-us)
PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") deve ser instalado.
Funções do Windows Server | Não ative: <br/> - Active Directory Domain Services <br/>- Serviços de Informação da Internet <br/> - Hyper-V |
Políticas de grupo| Não ative: <br/> -Impedi o acesso ao prompt de comando. <br/> -Impedi o acesso a ferramentas de edição do registo. <br/> -Lógica para anexos de ficheiros de fidedignidade. <br/> – Ative a execução do Script. <br/> [Saiba mais](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Certifique-se de que:<br/><br/> -Não é necessário um Web site predefinido do preexistente <br/> -Permitir [autenticação anónima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> -Ativar [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) definição  <br/> -Não tiverem preexistente de Web site/aplicação em escuta na porta 443<br/>
Tipo NIC | VMXNET3 (quando implementado como uma VM VMware)
Tipo de endereço IP | Estático
Portas | 443 utilizado para a orquestração de canal de controlo)<br/>9443 utilizado para o transporte de dados

## <a name="replicated-machines"></a>Máquinas replicadas

Recuperação de sites suporta a replicação de qualquer carga de trabalho em execução numa máquina.

**Componente** | **Detalhes**
--- | ---
Definições do computador | As máquinas que replicam para o Azure tem de cumprir [requisitos do Azure](#azure-vm-requirements).
Carga de trabalho de máquina | Recuperação de sites suporta a replicação de qualquer carga de trabalho (Digamos que o Active Directory, o SQL server, etc.) em execução numa máquina. Para obter mais informações, clique em [aqui](https://aka.ms/asr_workload)
Sistema de operativo do Windows | 64-bit Windows Server 2016 (Server Core, servidor com experiência de área de trabalho), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 com em, pelo menos, SP1. </br></br>  [Windows Server 2008 com pelo menos o SP2 - 32 bits e 64 bits](migrate-tutorial-windows-server-2008.md) (apenas para migração). </br></br> O servidor Nano do Windows 2016 não é suportado.
Arquitetura de sistema operativo Linux | Sistema de 64 bits apenas é suportado. sistema de 32 bits não é suportado
Sistema operativo Linux | Red Hat Enterprise Linux: 5.2 para 5.11<b>\*\*</b>, 6.1 para 6.10<b>\*\*</b>, 7.0 para 7,6 <br/><br/>CentOS: 5.2 para 5.11<b>\*\*</b>, 6.1 para 6.10<b>\*\*</b>, 7.0 para 7,6 <br/><br/>Servidor de Ubuntu 14.04 LTS[ (versões de kernel de suportadas)](#ubuntu-kernel-versions)<br/><br/>Servidor de Ubuntu 16.04 LTS[ (versões de kernel de suportadas)](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8[ (versões de kernel de suportadas)](#debian-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 12 SP1, SP2, SP3 [ (versões de kernel de suportadas)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 11 SP3<b>\*\*</b>, SUSE Linux Enterprise Server 11 SP4 * </br></br>Oracle Linux 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5 com o kernel compatível do Red Hat ou Indestrutíveis Enterprise Kernel versão 3 (UEK3) <br/><br/></br>-Atualizar máquinas replicadas do SUSE Linux Enterprise Server 11 SP3 para SP4 não é suportada. Para atualizar, desative a replicação e ativá-la novamente após a atualização.</br></br> - [Saiba mais](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) sobre o suporte para Linux e tecnologia de código aberto no Azure. Recuperação de sites orquestra a ativação pós-falha para executar servidores Linux no Azure. No entanto fornecedores do Linux podem limitar o suporte para apenas as versões de distribuição que não atingiram o fim-de-vida.<br/><br/> -Em distribuições do Linux, apenas os kernels das ações que fazem parte da versão/atualização do podverze distribuição são suportados.<br/><br/> -Atualizar máquinas protegidas em Linux principais versões de distribuição não é suportada. Para atualizar, desative a replicação, atualize o sistema operativo e, em seguida, ative novamente a replicação.<br/><br/> -Servidores a executar o Red Hat Enterprise Linux 5.2 5.11 ou CentOS 5.2 5.11 devem ter o [componentes de serviços de integração do Linux (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) instalado para as máquinas efetuar o arranque no Azure.


### <a name="ubuntu-kernel-versions"></a>Versões de kernel do Ubuntu


**Versão suportada** | **Versão do serviço de mobilidade de recuperação de sites do Azure** | **Versão de kernel** |
--- | --- | --- |
14.04 LTS | [9.22][9.22 UR] | 3.13.0-24-Generic para 3.13.0-164-generic,<br/>3.16.0-25-Generic para 3.16.0-77-generic,<br/>3.19.0-18-Generic para 3.19.0-80-generic,<br/>4.2.0-18-Generic para 4.2.0-42-generic,<br/>4.4.0-21-Generic para 4.4.0-140-generic,<br/>4.15.0-1023-Azure para 4.15.0-1036-azure |
14.04 LTS | [9.21][9.21 UR] | 3.13.0-24-Generic para 3.13.0-163-generic,<br/>3.16.0-25-Generic para 3.16.0-77-generic,<br/>3.19.0-18-Generic para 3.19.0-80-generic,<br/>4.2.0-18-Generic para 4.2.0-42-generic,<br/>4.4.0-21-Generic para 4.4.0-140-generic,<br/>4.15.0-1023-Azure para 4.15.0-1035-azure |
14.04 LTS | [9.20][9.20 UR] | 3.13.0-24-Generic para 3.13.0-153-generic,<br/>3.16.0-25-Generic para 3.16.0-77-generic,<br/>3.19.0-18-Generic para 3.19.0-80-generic,<br/>4.2.0-18-Generic para 4.2.0-42-generic,<br/>4.4.0-21-Generic para 4.4.0-138-generic,<br/>4.15.0-1023-Azure para 4.15.0-1025-azure |
14.04 LTS | [9.19][9.19 UR] | 3.13.0-24-Generic para 3.13.0-153-generic,<br/>3.16.0-25-Generic para 3.16.0-77-generic,<br/>3.19.0-18-Generic para 3.19.0-80-generic,<br/>4.2.0-18-Generic para 4.2.0-42-generic,<br/>4.4.0-21-Generic para 4.4.0-131-generic |
|||
16.04 LTS | [9.22][9.22 UR] | 4.4.0-21-Generic para 4.4.0-140-generic,<br/>4.8.0-34-Generic para 4.8.0-58-generic,<br/>4.10.0-14-Generic para 4.10.0-42-generic,<br/>4.11.0-13-Generic para 4.11.0-14-generic,<br/>4.13.0-16-Generic para 4.13.0-45-generic,<br/>4.15.0-13-Generic para 4.15.0-43-generic<br/>4.11.0-1009-Azure para 4.11.0-1016-azure,<br/>4.13.0-1005-Azure para 4.13.0-1018-azure <br/>4.15.0-1012-Azure para 4.15.0-1036-azure|
16.04 LTS | [9.21][9.21 UR] | 4.4.0-21-Generic para 4.4.0-140-generic,<br/>4.8.0-34-Generic para 4.8.0-58-generic,<br/>4.10.0-14-Generic para 4.10.0-42-generic,<br/>4.11.0-13-Generic para 4.11.0-14-generic,<br/>4.13.0-16-Generic para 4.13.0-45-generic,<br/>4.15.0-13-Generic para 4.15.0-42-generic<br/>4.11.0-1009-Azure para 4.11.0-1016-azure,<br/>4.13.0-1005-Azure para 4.13.0-1018-azure <br/>4.15.0-1012-Azure para 4.15.0-1035-azure|
16.04 LTS | [9.20][9.20 UR] | 4.4.0-21-Generic para 4.4.0-138-generic,<br/>4.8.0-34-Generic para 4.8.0-58-generic,<br/>4.10.0-14-Generic para 4.10.0-42-generic,<br/>4.11.0-13-Generic para 4.11.0-14-generic,<br/>4.13.0-16-Generic para 4.13.0-45-generic,<br/>4.15.0-13-Generic para 4.15.0-38-generic<br/>4.11.0-1009-Azure para 4.11.0-1016-azure,<br/>4.13.0-1005-Azure para 4.13.0-1018-azure <br/>4.15.0-1012-Azure para 4.15.0-1025-azure|
16.04 LTS | [9.19][9.19 UR] | 4.4.0-21-Generic para 4.4.0-131-generic,<br/>4.8.0-34-Generic para 4.8.0-58-generic,<br/>4.10.0-14-Generic para 4.10.0-42-generic,<br/>4.11.0-13-Generic para 4.11.0-14-generic,<br/>4.13.0-16-Generic para 4.13.0-45-generic,<br/>4.15.0-13-Generic para 4.15.0-30-generic<br/>4.11.0-1009-Azure para 4.11.0-1016-azure,<br/>4.13.0-1005-Azure para 4.13.0-1018-azure <br/>4.15.0-1012-Azure para 4.15.0-1019-azure|

### <a name="debian-kernel-versions"></a>Versões do debian kernel


**Versão suportada** | **Versão do serviço de mobilidade de recuperação de sites do Azure** | **Versão de kernel** |
--- | --- | --- |
Debian 7 | [9.19][9.19 UR],[9.20][9.20 UR],[9.21][9.21 UR], [9.22][9.22 UR]| 3.2.0-4-amd64 to 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.20][9.20 UR],[9.21][9.21 UR],[9.22][9.22 UR] | 3.16.0-4-amd64 to 3.16.0-7-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.8-amd64 |
Debian 8 | [9.19][9.19 UR] | 3.16.0-4-amd64 to 3.16.0-6-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.7-amd64 |


### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>Versões de kernel de suporte de 12 do SUSE Linux Enterprise Server

**Versão** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | [9.22][9.22 UR] | SP1 3.12.49-11-default para 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default para 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default para 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default para 4.4.121-92.98-default</br></br>4.4.73-5-default SP3 para 4.4.162-94.72-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | [9.21][9.21 UR] | SP1 3.12.49-11-default para 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default para 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default para 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default para 4.4.121-92.98-default</br></br>4.4.73-5-default SP3 para 4.4.156-94.72-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | [9.20][9.20 UR] | SP1 3.12.49-11-default para 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default para 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default para 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default para 4.4.121-92.98-default</br></br>4.4.73-5-default SP3 para 4.4.156-94.64-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | [9.19][9.19 UR] | SP1 3.12.49-11-default para 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default para 3.12.74-60.64.96-default</br></br> SP2 4.4.21-69-default para 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default para 4.4.121-92.85-default</br></br>4.4.73-5-default SP3 para 4.4.140-94.42-default |

## <a name="linux-file-systemsguest-storage"></a>Armazenamento de convidado/sistemas de ficheiros do Linux

**Componente** | **Suportado**
--- | ---
Sistemas de ficheiros | ext3, ext4, XFS
Gestor de volumes | Antes de [versão 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), <br/> 1. LVM2 é suportada. <br/> 2. LVM é suportada para discos de dados apenas. <br/> 3. As VMs do Azure têm apenas um único disco de SO.<br/><br/>Partir [versão 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) e posteriores, LVM e LVM2 são suportados.
Dispositivos de armazenamento Paravirtualizados | Os dispositivos exportados por controladores paravirtualizados não são suportados.
Dispositivos de e/s de fila multi bloco | Não suportado.
Servidores físicos com o controlador de armazenamento de HP CCISS | Não suportado.
Convenção de nomenclatura de ponto de montagem/dispositivo | Nome do dispositivo ou nome do ponto de montagem deve ser exclusivo. Certifique-se de que não existem dois pontos de montagem/dispositivos têm nomes maiúsculas de minúsculas. </br> Exemplo: Dois dispositivos da mesma máquina virtual como de nomenclatura *device1* e *Device1* não é permitida.
Diretórios | Antes de [versão 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), <br/> 1. Os seguintes diretórios (se definido como separados partições /-sistemas de ficheiros) todos têm de estar no mesmo disco de SO no servidor de origem: / (raiz), /boot, /usr, /usr/local., /var, /etc.</br>2. /boot deve estar numa partição de disco e não ser um volume LVM.<br/><br/> Partir [versão 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) e posteriores, acima restrições não se aplicam. Não é suportada a /Boot no volume LVM entre discos mais do que um.
Diretório de arranque | Vários discos de arranque numa máquina virtual não é suportada <br/><br/> Não é possível proteger uma máquina sem disco de arranque

Libertar os requisitos de espaço | 2 GB na partição /root <br/><br/> 250 MB na pasta de instalação XFSv5 | Funcionalidades de XFSv5 em sistemas de ficheiros XFS, por exemplo, soma de verificação de metadados, são suportadas a partir do serviço de mobilidade versão 9.10 ou superior. Utilize o utilitário de xfs_info para verificar o superblock XFS para a partição. Se ftype é definido como 1, em seguida, XFSv5 funcionalidades estão em utilização.

## <a name="vmdisk-management"></a>Gerenciamento de VM/disco

**ação** | **Detalhes**
--- | ---
Redimensionar disco numa VM replicada | Suportado.
Adicionar o disco na VM replicada | Desative a replicação para a VM, adicione o disco e, em seguida, reativar a replicação. Adicionar um disco numa VM de replicação não é atualmente suportada.

## <a name="network"></a>Rede

**Componente** | **Suportado**
--- | ---
Rede de anfitrião, o agrupamento NIC | Suportada para VMs de VMware. <br/><br/>Não é suportada para a replicação da máquina física.
Rede de anfitrião VLAN | Sim.
Rede de anfitrião IPv4 | Sim.
Rede do anfitrião IPv6 | Não.
Rede de convidado/servidor agrupamento NIC | Não.
Rede de convidado/servidor IPv4 | Sim.
Rede de convidado/servidor IPv6 | Não.
IP estático da rede do servidor/convidado (Windows) | Sim.
IP estático da rede do servidor/convidado (Linux) | Sim. <br/><br/>As VMs estão configuradas para utilizar DHCP na reativação pós-falha.
Várias NICs de rede de convidado/servidor | Sim.


## <a name="azure-vm-network-after-failover"></a>Rede VM do Azure (após a ativação pós-falha)

**Componente** | **Suportado**
--- | ---
Azure ExpressRoute | Sim
ILB | Sim
ELB | Sim
Gestor de Tráfego do Azure | Sim
Multi-NIC | Sim
Endereço IP reservado | Sim
IPv4 | Sim
Manter o endereço IP de origem | Sim
Pontos finais de serviço de rede Virtual do Azure<br/> (sem firewalls de armazenamento do Azure) | Sim
Redes Aceleradas | Não

## <a name="storage"></a>Armazenamento
**Componente** | **Suportado**
--- | ---
Disco dinâmico | Disco do sistema de operação tem de ser um disco básico. <br/><br/>Discos de dados podem ter discos dinâmicos
Configuração do disco de docker | Não
Host NFS | Sim para VMware<br/><br/> Não para os servidores físicos
Host SAN (iSCSI/FC) | Sim
Host vSAN | Sim para VMware<br/><br/> N/d para servidores físicos
Multipath i de anfitrião (MPIO) | Sim, testado com o Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM para CLARiiON
Volumes virtuais do anfitrião (VVols) | Sim para VMware<br/><br/> N/d para servidores físicos
VMDK/servidor convidado | Sim
Servidor de convidado/EFI/UEFI| Parcial (migração para o Azure para o Windows Server 2012 e posterior) <br/><br/> Consulte a nota no final da tabela
Disco de cluster partilhado do servidor/convidado | Não
Disco de convidado/servidor encriptado | Não
Convidado/servidor NFS | Não
O SMB 3.0 de servidor/convidado | Não
Convidado/servidor RDM | Sim<br/><br/> N/d para servidores físicos
Servidor/convidado disco > 1 TB | Sim<br/><br/>Até 4095 GB<br/><br/> Disco tem de ser superior a 1024 MB.
Disco de convidado/servidor com o tamanho de setor físico de k lógicos e 4 de 4K | Sim
Disco de convidado/servidor com lógico de 4K e o tamanho de setor físico de 512 bytes | Sim
Volume de convidado/servidor com o disco repartido > 4 TB <br/><br/>Gestão de volumes lógicos (LVM)| Sim
Servidor/convidado - espaços de armazenamento | Não
Disco de acesso frequente Adicionar/Remover/servidor de convidado | Não
Servidor/convidado - excluir o disco | Sim
Multipath i de convidado/servidor (o MPIO) | Não

> [!NOTE]
> UEFI de arranque máquinas de virtuais de VMware com o Windows Server 2012 ou posterior podem ser migrados para o Azure. As seguintes restrições aplicam-se:

> - Apenas uma migração para o Azure é suportada. Não é suportada a reativação pós-falha para o site de VMware no local.
> - O servidor não deve ter mais de quatro partições no disco do SO.
> - Requer a versão de serviço de mobilidade 9.13 ou posterior.

## <a name="azure-storage"></a>Armazenamento do Azure

**Componente** | **Suportado**
--- | ---
Armazenamento localmente redundante | Sim
Armazenamento georredundante | Sim
Armazenamento georredundante com acesso de leitura | Sim
Armazenamento de acesso esporádico | Não
Armazenamento frequente| Não
Blobs de bloco | Não
Encriptação em repouso (encriptação do serviço de armazenamento)| Sim
Armazenamento Premium | Sim
Serviço de importação/exportação | Não
Firewalls de armazenamento do Azure para redes virtuais configuradas na conta de armazenamento de cache/armazenamento de destino (usada para armazenar dados de replicação) | Não
Contas de armazenamento para fins gerais v2 (escalões esporádico e frequentes) | Não

## <a name="azure-compute"></a>Computação do Azure

**Funcionalidade** | **Suportado**
--- | ---
Conjuntos de disponibilidade | Sim
Zonas de disponibilidade | Não
HUB | Sim
Managed Disks | Sim

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

As VMs no local que replica para o Azure tem de cumprir os requisitos de VM do Azure resumidos nesta tabela. Quando a recuperação de Site é executada uma verificação de pré-requisitos, ele falhará se alguns dos requisitos não forem cumpridas.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operativo convidado | Certifique-se [sistemas operativos suportados](#replicated-machines) para replicar máquinas. | Falha na verificação se não suportado.
Arquitetura de sistema operativo convidado | 64 bits. | Falha na verificação se não suportado.
Tamanho de disco do sistema operativo | Até 2048 GB. | Falha na verificação se não suportado.
Número de discos do sistema operativo | 1 | Falha na verificação se não suportado.
Número de discos de dados | 64 ou menos. | Falha na verificação se não suportado.
Tamanho do disco de dados | Até 4095 GB | Falha na verificação se não suportado.
Placas de rede | São suportados vários adaptadores. |
VHD partilhado | Não suportado. | Falha na verificação se não suportado.
Disco FC | Não suportado. | Falha na verificação se não suportado.
BitLocker | Não suportado. | O BitLocker tem de ser desativado antes de ativar a replicação para uma máquina. |
Nome da VM | Entre 1 e 63 carateres.<br/><br/> Limitado a letras, números e hífenes.<br/><br/> O nome do computador deve começar e terminar com uma letra ou número. |  Atualize o valor nas propriedades da máquina no Site Recovery.

## <a name="azure-site-recovery-churn-limits"></a>Limites de alterações a dados do Azure Site Recovery

A tabela seguinte fornece os limites do Azure Site Recovery. Estes limites baseiam-se nos nossos testes, mas não abrangem todas as combinações de E/S de aplicações possíveis. Os resultados reais podem variar consoante a combinação de E/S da sua aplicação. Para obter melhores resultados, recomendamos vivamente a [execute a ferramenta Planeador de implementações](site-recovery-deployment-planner.md) e realizar testes exaustivos às aplicações através da emissão de uma ativação pós-falha de teste para obter a imagem de VERDADEIRO de desempenho do aplicativo.

**Destino do armazenamento da replicação** | **Tamanho médio de E/S do disco de origem** |**Média de alterações a dados do disco de origem** | **Total de alterações a dados do disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB  | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |  336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou superior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou superior |10 MB/s | 842 GB por disco

**Alterações a dados de origem** | **Limite Máximo**
---|---
Média de alterações a dados por VM| 25 MB/s
Alterações a dados de pico em todos os discos numa VM | 54 MB/s
Máximo de alterações a dados por dia suportadas por um Servidor de Processos | 2 TB

Estes são números médios, que pressupõem uma sobreposição de 30 por cento de E/S. O Site Recovery é capaz de processar um débito superior com base no rácio de sobreposição, em tamanhos de escrita maiores e no comportamento real de E/S da carga de trabalho. Os números anteriores pressupõem um atraso típico de aproximadamente cinco minutos. Ou seja, depois de os dados serem carregados, são processados e é criado um ponto de recuperação ao fim de cinco minutos.

## <a name="vault-tasks"></a>Tarefas de cofre

**ação** | **Suportado**
--- | ---
Mover o Cofre entre grupos de recursos<br/><br/> Dentro e entre subscrições | Não
Mover o armazenamento, rede, as VMs do Azure entre grupos de recursos<br/><br/> Dentro e entre subscrições | Não


## <a name="download-latest-azure-site-recovery-components"></a>Transferir componentes mais recentes do Azure Site Recovery

**Nome** | **Descrição** | **Instruções de transferência de versão mais recente**
--- | --- | --- | --- | ---
Servidor de configuração | Coordena as comunicações entre servidores de VMware no local e o Azure <br/><br/> Instalado em servidores de VMware no local | Para instalação de raiz, clique em [aqui](vmware-azure-deploy-configuration-server.md). Para atualizar o componente existente para a versão mais recente, clique em [aqui](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server).
Servidor de processos|Instalado por predefinição no servidor de configuração. Recebe dados de replicação. otimiza-os com colocação em cache, compressão e encriptação; e envia-os para o armazenamento do Azure. À medida que cresça a implementação, pode adicionar servidores de processo adicionais, em separado para processar maiores volumes de tráfego de replicação.| Para instalação de raiz, clique em [aqui](vmware-azure-set-up-process-server-scale.md). Para atualizar o componente existente para a versão mais recente, clique em [aqui](vmware-azure-manage-process-server.md#upgrade-a-process-server).
Serviço de mobilidade | Coordena a replicação entre servidores de servidores/físico de VMware no local e o site do Azure/secundária<br/><br/> Instalado na VM de VMware ou servidores físicos que pretende replicar | Para instalação de raiz, clique em [aqui](vmware-azure-install-mobility-service.md). Para atualizar o componente existente para a versão mais recente, clique em [aqui](vmware-physical-mobility-service-overview.md##update-mobility-service-from-azure-portal).

Para saber mais sobre as funcionalidades e correções mais recentes, clique em [aqui](https://aka.ms/ASR_latest_release_notes).


## <a name="next-steps"></a>Passos Seguintes
[Saiba como](tutorial-prepare-azure.md) para preparar o Azure para recuperação após desastre de VMs de VMware.

[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
