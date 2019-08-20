---
title: Matriz de suporte para recuperação de desastre de VMs VMware e servidores físicos no Azure com Azure Site Recovery | Microsoft Docs
description: Resume o suporte para recuperação de desastre de VMs VMware e servidor físico para o Azure usando Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: raynew
ms.openlocfilehash: fd24d0d9f05855cf22da547f95b16da0a8d2c788
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617651"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Matriz de suporte para recuperação de desastre de VMs VMware e servidores físicos para o Azure

Este artigo resume os componentes e as configurações com suporte para recuperação de desastres de VMs VMware e servidores físicos no Azure usando [Azure site Recovery](site-recovery-overview.md).

- [Saiba mais](vmware-azure-architecture.md) sobre a arquitetura de recuperação de desastre do VMware VM/servidor físico.
- Siga nossos [tutoriais](tutorial-prepare-azure.md) para experimentar a recuperação de desastres.

## <a name="deployment-scenarios"></a>Cenários de implementação

**Cenário** | **Detalhes**
--- | ---
Recuperação de desastre de VMs VMware | Replicação de VMs VMware locais para o Azure. Você pode implantar esse cenário no portal do Azure ou usando o [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Recuperação de desastre de servidores físicos | Replicação de servidores físicos Windows/Linux locais para o Azure. Você pode implantar esse cenário no portal do Azure.

## <a name="on-premises-virtualization-servers"></a>Servidores de virtualização locais

**servidor** | **Requirement** | **Detalhes**
--- | --- | ---
vCenter Server | Versão 6,7, 6,5, 6,0 ou 5,5 | Recomendamos que você use um servidor vCenter em sua implantação de recuperação de desastre.
hosts vSphere | Versão 6,7, 6,5, 6,0 ou 5,5 | Recomendamos que os hosts vSphere e os servidores vCenter estejam localizados na mesma rede que o servidor de processo. Por padrão, o servidor de processo é executado no servidor de configuração. [Saiba mais](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Servidor de configuração Site Recovery

O servidor de configuração é um computador local que executa Site Recovery componentes, incluindo o servidor de configuração, o servidor de processo e o servidor de destino mestre.

- Para VMs do VMware, você define o servidor de configuração baixando um modelo OVF para criar uma VM VMware.
- Para servidores físicos, você configura manualmente o computador do servidor de configuração.

**Componente** | **Requirement**
--- |---
Núcleos de CPU | 8
RAM | 16 GB
Número de discos | 3 discos<br/><br/> Os discos incluem o disco do sistema operacional, o disco de cache do servidor de processo e a unidade de retenção para failback.
Espaço livre em disco | 600 GB de espaço para o cache do servidor de processo.
Espaço livre em disco | 600 GB de espaço para a unidade de retenção.
Sistema operativo  | Windows Server 2012 R2 ou Windows Server 2016 com experiência desktop |
Região do sistema operativo | Inglês (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Não é necessário para o Configuration Server versão [9,14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) ou posterior. 
Funções do Windows Server | Não habilitar Active Directory Domain Services; Serviços de Informações da Internet (IIS) ou Hyper-V. 
Políticas de grupo| -Impedir o acesso ao prompt de comando. <br/> -Impedir o acesso às ferramentas de edição do registro. <br/> -Lógica de confiança para anexos de arquivo. <br/> -Ative a execução do script. <br/> - [Saiba Mais](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Verifique se você:<br/><br/> -Não tem um site padrão preexistente <br/> -Habilitar [autenticação anônima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> -Habilitar configuração de [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br/> -Não tem site/aplicativo preexistente ouvindo na porta 443<br/>
Tipo de NIC | VMXNET3 (quando implantado como uma VM VMware)
Tipo de endereço IP | Estático
Portas | 443 usado para orquestração de canal de controle<br/>9443 para o transporte de dados

## <a name="replicated-machines"></a>Máquinas replicadas

O Site Recovery dá suporte à replicação de qualquer carga de trabalho em execução em um computador com suporte.

**Componente** | **Detalhes**
--- | ---
Configurações do computador | Os computadores que replicam para o Azure devem atender aos [requisitos do Azure](#azure-vm-requirements).
Carga de trabalho da máquina | O Site Recovery dá suporte à replicação de qualquer carga de trabalho em execução em um computador com suporte. [Saiba mais](https://aka.ms/asr_workload).
Windows | -Windows Server 2019 (com suporte do [pacote cumulativo de atualizações 34](https://support.microsoft.com/help/4490016) (versão 9,22 do serviço de mobilidade) em diante.<br/> -Windows Server 2016 (64-bit Server Core, servidor com experiência desktop)<br/> -Windows Server 2012 R2, Windows Server 2012<br/> -Windows Server 2008 R2 com pelo menos SP1.<br/> -Windows Server 2008, 64 e 32-bit com pelo menos o SP2]. Com suporte somente para migração. [Saiba mais](migrate-tutorial-windows-server-2008.md).<br/> -Windows 10, Windows 8.1, Windows 8, Windows 7 64-bit (com suporte do [pacote cumulativo de atualizações 36](https://support.microsoft.com/help/4503156) (versão 9,22 do serviço de mobilidade em diante). Não há suporte para o Windows 7 RTM. 
Linux | Somente o sistema de 64 bits tem suporte. Não há suporte para o sistema de 32 bits.<br/><br/>Todos os servidores Linux devem ter [componentes de Lis (Linux Integration Services)](https://www.microsoft.com/download/details.aspx?id=55106) instalados. É necessário inicializar o servidor no Azure após failover/failover de teste. Se os componentes de LIS estiverem ausentes, certifique-se de instalar os [componentes](https://www.microsoft.com/download/details.aspx?id=55106) antes de habilitar a replicação para que os computadores inicializem no Azure. <br/><br/> Site Recovery orquestra o failover para executar servidores Linux no Azure. No entanto, os fornecedores do Linux podem limitar o suporte apenas a versões de distribuição que não atingiram o fim da vida útil.<br/><br/> Em distribuições do Linux, somente os kernels de ações que fazem parte do lançamento/atualização da versão secundária de distribuição têm suporte.<br/><br/> Não há suporte para a atualização de computadores protegidos nas principais versões de distribuição do Linux. Para atualizar, desabilite a replicação, atualize o sistema operacional e habilite a replicação novamente.<br/><br/> [Saiba mais](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) sobre o suporte para Linux e tecnologia de código-fonte aberto no Azure.
Linux Red Hat Enterprise | 5,2 a 5,11</b><br/> 6,1 a 6,10</b><br/> 7,0 a 7,6<br/> <br/> Os servidores que executam Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.10 não têm [componentes LIS (Linux Integration Services)](https://www.microsoft.com/download/details.aspx?id=55106) pré-instalados. Certifique-se de instalar os [componentes](https://www.microsoft.com/download/details.aspx?id=55106) antes de habilitar a replicação para que os computadores inicializem no Azure.
Linux: CentOS | 5,2 a 5,11</b><br/> 6,1 a 6,10</b><br/> 7,0 a 7,6<br/> <br/> Os servidores que executam o CentOS 5.2-5.11 & 6.1-6.10 não têm [componentes LIS (Integration Services do Linux)](https://www.microsoft.com/download/details.aspx?id=55106) pré-instalados. Certifique-se de instalar os [componentes](https://www.microsoft.com/download/details.aspx?id=55106) antes de habilitar a replicação para que os computadores inicializem no Azure.
Ubuntu | Ubuntu 14, 4 LTS Server [(examinar as versões de kernel com suporte)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16, 4 LTS Server [(examinar as versões de kernel com suporte)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(examinar as versões de kernel com suporte)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(examine as versões de kernel com suporte)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> Não há suporte para a atualização de máquinas replicadas do SUSE Linux Enterprise Server 11 SP3 para o SP4. Para atualizar, desabilite a replicação e habilite novamente após a atualização.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6<br/><br/> Executando o kernel do Red Hat compatível ou o inquebrable Enterprise kernel versão 3, 4 & 5 (UEK3, UEK4, UEK5) 


### <a name="ubuntu-kernel-versions"></a>Versões de kernel do Ubuntu


**Versão com suporte** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
14, 4 LTS | [9,27][9.27 UR]| 3.13.0-24-Generic para 3.13.0-170-Generic,<br/>3.16.0-25-Generic para 3.16.0-77-Generic,<br/>3.19.0-18-Generic para 3.19.0-80-Generic,<br/>4.2.0-18-Generic para 4.2.0-42-Generic,<br/>4.4.0-21-Generic para 4.4.0-148-Generic,<br/>4.15.0-1023-Azure para 4.15.0-1045-Azure |
14, 4 LTS | [9,26][9.26 UR]| 3.13.0-24-Generic para 3.13.0-170-Generic,<br/>3.16.0-25-Generic para 3.16.0-77-Generic,<br/>3.19.0-18-Generic para 3.19.0-80-Generic,<br/>4.2.0-18-Generic para 4.2.0-42-Generic,<br/>4.4.0-21-Generic para 4.4.0-148-Generic,<br/>4.15.0-1023-Azure para 4.15.0-1045-Azure |
14, 4 LTS | [9,25][9.25 UR]  | 3.13.0-24-Generic para 3.13.0-169-Generic,<br/>3.16.0-25-Generic para 3.16.0-77-Generic,<br/>3.19.0-18-Generic para 3.19.0-80-Generic,<br/>4.2.0-18-Generic para 4.2.0-42-Generic,<br/>4.4.0-21-Generic para 4.4.0-146-Generic,<br/>4.15.0-1023-Azure para 4.15.0-1042-Azure |
14, 4 LTS | [9,24][9.24 UR] | 3.13.0-24-Generic para 3.13.0-167-Generic,<br/>3.16.0-25-Generic para 3.16.0-77-Generic,<br/>3.19.0-18-Generic para 3.19.0-80-Generic,<br/>4.2.0-18-Generic para 4.2.0-42-Generic,<br/>4.4.0-21-Generic para 4.4.0-143-Generic,<br/>4.15.0-1023-Azure para 4.15.0-1040-Azure |
|||
16, 4 LTS | [9,27][9.27 UR] | 4.4.0-21-Generic para 4.4.0-154-Generic,<br/>4.8.0-34-Generic a 4.8.0-58-Generic,<br/>4.10.0-14-generic para 4.10.0-42-Generic,<br/>4.11.0-13-Generic para 4.11.0-14-generic,<br/>4.13.0-16-Generic para 4.13.0-45-Generic,<br/>4.15.0-13-Generic para 4.15.0-54-Generic<br/>4.11.0-1009-Azure para 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure para 4.13.0-1018-Azure <br/>4.15.0-1012-Azure para 4.15.0-1050-Azure|
16, 4 LTS | [9,26][9.26 UR] | 4.4.0-21-Generic para 4.4.0-148-Generic,<br/>4.8.0-34-Generic a 4.8.0-58-Generic,<br/>4.10.0-14-generic para 4.10.0-42-Generic,<br/>4.11.0-13-Generic para 4.11.0-14-generic,<br/>4.13.0-16-Generic para 4.13.0-45-Generic,<br/>4.15.0-13-Generic para 4.15.0-50-Generic<br/>4.11.0-1009-Azure para 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure para 4.13.0-1018-Azure <br/>4.15.0-1012-Azure para 4.15.0-1045-Azure|
16, 4 LTS | [9,25][9.25 UR] | 4.4.0-21-Generic para 4.4.0-146-Generic,<br/>4.8.0-34-Generic a 4.8.0-58-Generic,<br/>4.10.0-14-generic para 4.10.0-42-Generic,<br/>4.11.0-13-Generic para 4.11.0-14-generic,<br/>4.13.0-16-Generic para 4.13.0-45-Generic,<br/>4.15.0-13-Generic para 4.15.0-48-Generic<br/>4.11.0-1009-Azure para 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure para 4.13.0-1018-Azure <br/>4.15.0-1012-Azure para 4.15.0-1042-Azure|
16, 4 LTS | [9,24][9.24 UR] | 4.4.0-21-Generic para 4.4.0-143-Generic,<br/>4.8.0-34-Generic a 4.8.0-58-Generic,<br/>4.10.0-14-generic para 4.10.0-42-Generic,<br/>4.11.0-13-Generic para 4.11.0-14-generic,<br/>4.13.0-16-Generic para 4.13.0-45-Generic,<br/>4.15.0-13-Generic para 4.15.0-46-Generic<br/>4.11.0-1009-Azure para 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure para 4.13.0-1018-Azure <br/>4.15.0-1012-Azure para 4.15.0-1040-Azure|

### <a name="debian-kernel-versions"></a>Versões do kernel Debian


**Versão com suporte** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
Debian 7 | [9,24][9.24 UR], [9,25][9.25 UR],[9,26][9.26 UR], [9,27][9.27 UR]| 3.2.0-4-AMD64 para 3.2.0-6-AMD64, 3.16.0-0. BPO. 4-AMD64 |
|||
Debian 8 | [9,27][9.27 UR] | 3.16.0-4-AMD64 para 3.16.0-9-AMD64, 4.9.0-0. BPO. 4-AMD64 para 4.9.0-0. BPO. 9-AMD64 |
Debian 8 | [9,24][9.24 UR], [9,25][9.25 UR], [9,26][9.26 UR] | 3.16.0-4-AMD64 para 3.16.0-8-AMD64, 4.9.0-0. BPO. 4-AMD64 para 4.9.0-0. BPO. 8-AMD64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 versões de kernel com suporte

**Liberar** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,27][9.27 UR] | SP1 3.12.49-11-padrão para 3.12.74-60.64.40-padrão</br></br> SP1 (LTSS) 3.12.74-60.64.45-padrão para 3.12.74-60.64.115-padrão</br></br> SP2 4.4.21-69-padrão para 4.4.120-92.70-padrão</br></br>SP2 (LTSS) 4.4.121-92.73-padrão para 4.4.121-92.114-padrão</br></br>SP3 4.4.73-5-padrão para 4.4.180-94.97-padrão</br></br>SP3 4.4.138-4.7-Azure para 4.4.180-4.31-Azure</br></br>SP4 4.12.14-94.41-default para 4.12.14-95.19-default</br>SP4 4.12.14-6.3-Azure para 4.12.14-6.15-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,26][9.26 UR] | SP1 3.12.49-11-padrão para 3.12.74-60.64.40-padrão</br></br> SP1 (LTSS) 3.12.74-60.64.45-padrão para 3.12.74-60.64.110-padrão</br></br> SP2 4.4.21-69-padrão para 4.4.120-92.70-padrão</br></br>SP2 (LTSS) 4.4.121-92.73-padrão para 4.4.121-92.109-padrão</br></br>SP3 4.4.73-5-padrão para 4.4.178-94.91-padrão</br></br>SP3 4.4.138-4.7-Azure para 4.4.178-tornariam 4,28-Azure</br></br>SP4 4.12.14-94.41-default para 4.12.14-95.16-default</br>SP4 4.12.14-6.3-Azure para 4.12.14-6,9-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,25][9.25 UR] | SP1 3.12.49-11-padrão para 3.12.74-60.64.40-padrão</br></br> SP1 (LTSS) 3.12.74-60.64.45-padrão para 3.12.74-60.64.107-padrão</br></br> SP2 4.4.21-69-padrão para 4.4.120-92.70-padrão</br></br>SP2 (LTSS) 4.4.121-92.73-padrão para 4.4.121-92.104-padrão</br></br>SP3 4.4.73-5-padrão para 4.4.176-94.88-padrão</br></br>SP3 4.4.138-4.7-Azure para 4.4.176-4,25-Azure</br></br>SP4 4.12.14-94.41-default para 4.12.14-95.13-default</br>SP4 4.12.14-6.3-Azure para 4.12.14-6,9-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,24][9.24 UR] | SP1 3.12.49-11-padrão para 3.12.74-60.64.40-padrão</br></br> SP1 (LTSS) 3.12.74-60.64.45-padrão para 3.12.74-60.64.107-padrão</br></br> SP2 4.4.21-69-padrão para 4.4.120-92.70-padrão</br></br>SP2 (LTSS) 4.4.121-92.73-padrão para 4.4.121-92.101-padrão</br></br>SP3 4.4.73-5-padrão para 4.4.175-94.79-padrão</br></br>SP4 4.12.14-94.41-default para 4.12.14-95.6-default |


## <a name="linux-file-systemsguest-storage"></a>Armazenamento de sistemas de arquivos/convidados do Linux

**Componente** | **Suportado**
--- | ---
Sistemas de arquivos | ext3, ext4, XFS
Gerenciador de volumes | -O LVM tem suporte.<br/> -/boot no LVM tem suporte do [pacote cumulativo de atualizações 31](https://support.microsoft.com/help/4478871/) (versão 9,20 do serviço de mobilidade) em diante. Ele não tem suporte em versões anteriores do serviço de mobilidade.<br/> -Não há suporte para vários discos de sistema operacional.
Dispositivos de armazenamento paravirtualizados | Os dispositivos exportados por controladores paravirtualizados não são suportados.
Dispositivos de e/s de bloco de várias filas | Não suportado.
Servidores físicos com o controlador de armazenamento HP CCISS | Não suportado.
Convenção de nomenclatura de dispositivo/ponto de montagem | O nome do dispositivo ou o nome do ponto de montagem deve ser exclusivo.<br/> Verifique se não há dois dispositivos/pontos de montagem com nomes que diferenciam maiúsculas de minúsculas. Por exemplo, nomes de dispositivos para a mesma VM que *Device1* e *Device1* não têm suporte.
Diretórios | Se você estiver executando uma versão do serviço de mobilidade anterior à versão 9,20 (lançada no [pacote cumulativo de atualizações 31](https://support.microsoft.com/help/4478871/)), essas restrições se aplicarão:<br/><br/> -Esses diretórios (se configurados como partições/sistemas de arquivos separados) devem estar no mesmo disco do sistema operacional no servidor de origem:/(raiz),/boot,/usr,/usr/local,/var,/etc.</br> -O diretório/boot deve estar em uma partição de disco e não ser um volume LVM.<br/><br/> Da versão 9,20 em diante, essas restrições não se aplicam. 
Diretório de inicialização | -Discos de inicialização não deve estar no formato de partição GPT. Essa é uma limitação da arquitetura do Azure. Os discos GPT têm suporte como discos de dados.<br/><br/> Não há suporte para vários discos de inicialização em uma VM<br/><br/> -/boot em um volume LVM em mais de um disco não tem suporte.<br/> -Um computador sem um disco de inicialização não pode ser replicado.
Requisitos de espaço livre| 2 GB na partição/root <br/><br/> 250 MB na pasta de instalação
XFSv5 | Os recursos de XFSv5 em sistemas de arquivos XFS, como a soma de verificação de metadados, têm suporte (serviço de mobilidade versão 9,10 em diante).<br/> Use o utilitário xfs_info para verificar a partição XFS superbloco. Se `ftype` é definido como 1, os recursos do XFSv5 estão em uso.
BTRFS | O BTRFS tem suporte do [pacote cumulativo de atualizações 34](https://support.microsoft.com/help/4490016) (versão 9,22 do serviço de mobilidade) em diante. BTRFS não tem suporte se:<br/><br/> -O subvolume do sistema de arquivos BTRFS é alterado após habilitar a proteção.</br> -O sistema de arquivos BTRFS está espalhado por vários discos.</br> -O sistema de arquivos BTRFS dá suporte a RAID.

## <a name="vmdisk-management"></a>Gerenciamento de VM/disco

**ação** | **Detalhes**
--- | ---
Redimensionar disco na VM replicada | Suportado.
Adicionar disco na VM replicada | Não suportado.<br/> Desabilite a replicação da VM, adicione o disco e, em seguida, habilite novamente a replicação.

## <a name="network"></a>Rede

**Componente** | **Suportado**
--- | ---
Agrupamento NIC de rede do host | Com suporte para VMs VMware. <br/><br/>Sem suporte para replicação de computador físico.
VLAN de rede do host | Sim.
IPv4 de rede de host | Sim.
IPv6 de rede do host | Não.
Agrupamento NIC da rede de convidado/servidor | Não.
IPv4 da rede de convidado/servidor | Sim.
IPv6 de rede de convidado/servidor | Não.
IP estático de rede de convidado/servidor (Windows) | Sim.
IP estático de rede de convidado/servidor (Linux) | Sim. <br/><br/>As VMs são configuradas para usar o DHCP no failback.
Rede de convidado/servidor várias NICs | Sim.


## <a name="azure-vm-network-after-failover"></a>Rede VM do Azure (após o failover)

**Componente** | **Suportado**
--- | ---
Azure ExpressRoute | Sim
ILB | Sim
ELB | Sim
Gestor de Tráfego do Azure | Sim
Várias NICs | Sim
Endereço IP reservado | Sim
IPv4 | Sim
Reter endereço IP de origem | Sim
Pontos de extremidade de serviço de rede virtual do Azure<br/> | Sim
Funcionamento em rede acelerado | Não

## <a name="storage"></a>Armazenamento
**Componente** | **Suportado**
--- | ---
Disco dinâmico | O disco do sistema operacional deve ser um disco básico. <br/><br/>Discos de dados podem ser discos dinâmicos
Configuração de disco do Docker | Não
NFS do host | Sim para VMware<br/><br/> Não para servidores físicos
SAN do host (iSCSI/FC) | Sim
VSAN do host | Sim para VMware<br/><br/> N/A para servidores físicos
Host de vários caminhos (MPIO) | Sim, testado com o Microsoft DSM, o EMC PowerPath 5,7 SP4, o EMC PowerPath DSM para CLARiiON
VVols (volumes virtuais do host) | Sim para VMware<br/><br/> N/A para servidores físicos
VMDK do convidado/servidor | Sim
Disco de cluster compartilhado do convidado/servidor | Não
Disco criptografado do convidado/servidor | Não
NFS de convidado/servidor | Não
ISCSI de convidado/servidor | Para migração-Sim<br/>Para recuperação de desastre-não, o iSCSI fará o failback como um disco anexado à VM
Convidado/servidor SMB 3,0 | Não
Servidor RDM/convidado | Sim<br/><br/> N/A para servidores físicos
Disco de convidado/servidor > 1 TB | Sim, o disco deve ter mais de 1024 MB<br/><br/>Até 8.192 GB ao replicar para discos gerenciados (versão 9,26 em diante)<br></br> Até 4.095 GB ao replicar para contas de armazenamento
Disco de convidado/servidor com tamanho de setor físico e 4K lógicos de 4K | Não
Disco de convidado/servidor com tamanho de setor físico de 512 bytes e lógicos de 4K | Não
Volume de convidado/servidor com disco distribuído > 4 TB <br/><br/>Gerenciamento de volumes lógicos (LVM)| Sim
Convidado/servidor-espaços de armazenamento | Não
Adicionar/remover disco do convidado/servidor | Não
Convidado/servidor-excluir disco | Sim
MPIO (vários caminhos) de convidado/servidor | Não
Partições do convidado/servidor GPT | Há suporte para cinco partições do [pacote cumulativo de atualizações 37](https://support.microsoft.com/help/4508614/) (versão 9,25 do serviço de mobilidade) em diante. Anteriormente, eram suportadas quatro.
Inicialização de EFI/servidor do convidado/UEFI | -Com suporte quando você está executando o serviço de mobilidade versão 9,13 ou posterior.<br/> -Com suporte ao migrar VMs VMware ou servidores físicos que executam o Windows Server 2012 ou posterior para o Azure.<br/> -Você só pode replicar VMs para migração. Não há suporte para failback para local.<br/> -Somente NTFS tem suporte <br/> -Não há suporte para o tipo de inicialização UEFI segura. <br/> -O tamanho do setor do disco deve ser de 512 bytes por setor físico.

## <a name="replication-channels"></a>Canais de replicação

|**Tipo de replicação**   |**Suportado**  |
|---------|---------|
|Transferências de dados descarregadas (ODX)    |       Não  |
|Seeding Offline        |   Não      |
| Azure Data Box | Não

## <a name="azure-storage"></a>Storage do Azure

**Componente** | **Suportado**
--- | ---
Armazenamento localmente redundante | Sim
Armazenamento georredundante | Sim
Armazenamento com redundância geográfica com acesso de leitura | Sim
Armazenamento frio | Não
Armazenamento dinâmico| Não
Blobs de bloco | Não
Criptografia em repouso (SSE)| Sim
Armazenamento Premium | Sim
Serviço de importação/exportação | Não
Firewalls do armazenamento do Azure para VNets | Sim.<br/> Configurado na conta de armazenamento de armazenamento/cache de destino (usada para armazenar dados de replicação).
Contas de armazenamento v2 de uso geral (camadas quentes e frias) | Sim (os custos de transações são consideravelmente mais altos para v2 em comparação com v1)

## <a name="azure-compute"></a>Computação do Azure

**Funcionalidade** | **Suportado**
--- | ---
Conjuntos de disponibilidade | Sim
Zonas de disponibilidade | Não
82801ER | Sim
Managed disks | Sim

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

As VMs locais replicadas para o Azure devem atender aos requisitos de VM do Azure resumidos nesta tabela. Quando Site Recovery executar uma verificação de pré-requisitos para replicação, a verificação falhará se alguns dos requisitos não forem atendidos.

**Componente** | **Requirement** | **Detalhes**
--- | --- | ---
Sistema operacional convidado | Verifique os [sistemas operacionais com suporte](#replicated-machines) para computadores replicados. | A verificação falhará se não houver suporte.
Arquitetura do sistema operacional convidado | 64 bits. | A verificação falhará se não houver suporte.
Tamanho do disco do sistema operacional | Até 2.048 GB. | A verificação falhará se não houver suporte.
Contagem de disco do sistema operacional | 1 | A verificação falhará se não houver suporte.
Contagem de disco de dados | 64 ou menos. | A verificação falhará se não houver suporte.
Tamanho do disco de dados | Até 8.192 GB ao replicar para o disco gerenciado (versão 9,26 em diante)<br></br>Até 4.095 GB ao replicar para a conta de armazenamento| A verificação falhará se não houver suporte.
Placas de rede | Há suporte para vários adaptadores. |
VHD compartilhado | Não suportado. | A verificação falhará se não houver suporte.
Disco FC | Não suportado. | A verificação falhará se não houver suporte.
BitLocker | Não suportado. | O BitLocker deve ser desabilitado antes de habilitar a replicação para um computador. |
o nome da VM | De 1 a 63 caracteres.<br/><br/> Limitado a letras, números e hífenes.<br/><br/> O nome do computador deve começar e terminar com uma letra ou número. |  Atualize o valor nas propriedades da máquina em Site Recovery.

## <a name="churn-limits"></a>Limites de rotatividade

A tabela seguinte fornece os limites do Azure Site Recovery. 
- Esses limites se baseiam em nossos testes, mas não cobrem todas as combinações de e/s de aplicativo possíveis.
- Os resultados reais podem variar consoante a combinação de E/S da sua aplicação.
- Para obter melhores resultados, é altamente recomendável que você execute a [ferramenta de planejador de implantações](site-recovery-deployment-planner.md)e execute testes de aplicativo extensivos usando failovers de teste para obter a imagem de desempenho real para seu aplicativo.

**Destino de replicação** | **Tamanho médio de E/S do disco de origem** |**Média de alterações a dados do disco de origem** | **Total de alterações a dados do disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB  | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |  336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou superior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou superior |20 MB/s | 1684 GB por disco


**Alterações a dados de origem** | **Limite Máximo**
---|---
Média de alterações a dados por VM| 25 MB/s
Alterações a dados de pico em todos os discos numa VM | 54 MB/s
Máximo de alterações a dados por dia suportadas por um Servidor de Processos | 2 TB

- Estes são números médios, que pressupõem uma sobreposição de 30 por cento de E/S.
- O Site Recovery é capaz de processar um débito superior com base no rácio de sobreposição, em tamanhos de escrita maiores e no comportamento real de E/S da carga de trabalho.
- Esses números pressupõem uma pendência típica de aproximadamente cinco minutos. Ou seja, depois de os dados serem carregados, são processados e é criado um ponto de recuperação ao fim de cinco minutos.

## <a name="vault-tasks"></a>Tarefas do cofre

**ação** | **Suportado**
--- | ---
Mover o cofre entre grupos de recursos | Não
Mover o cofre dentro e entre assinaturas | Não
Mover armazenamento, rede, VMs do Azure entre grupos de recursos | Não
Mova armazenamento, rede, VMs do Azure dentro e entre assinaturas. | Não


## <a name="obtain-latest-components"></a>Obter os componentes mais recentes

**Name** | **Descrição** | **Detalhes**
--- | --- | ---
Servidor de configuração | Instalado no local.<br/> Coordena as comunicações entre servidores VMware locais ou computadores físicos e o Azure. | - [Saiba mais sobre](vmware-physical-azure-config-process-server-overview.md) o servidor de configuração.<br/> - [Saiba mais sobre como](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) atualizar para a versão mais recente.<br/> - [Saiba mais sobre como](vmware-azure-deploy-configuration-server.md) configurar o servidor de configuração. 
Servidor de processos | Instalado por predefinição no servidor de configuração.<br/> Recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Azure.<br/> À medida que sua implantação cresce, você pode adicionar servidores de processo adicionais para lidar com volumes maiores de tráfego de replicação. | - [Saiba mais sobre](vmware-physical-azure-config-process-server-overview.md) o servidor de processo.<br/> - [Saiba mais sobre como](vmware-azure-manage-process-server.md#upgrade-a-process-server) atualizar para a versão mais recente.<br/> - [Saiba mais sobre como](vmware-physical-large-deployment.md#set-up-a-process-server) configurar servidores de processo de expansão.
Serviço de mobilidade | Instalado na VM VMware ou servidores físicos que você deseja replicar.<br/> Coordena a replicação entre servidores VMware/servidores físicos locais e o Azure.| - [Saiba mais sobre](vmware-physical-mobility-service-overview.md) o serviço de mobilidade.<br/> - [Saiba mais sobre como](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) atualizar para a versão mais recente.<br/> 



## <a name="next-steps"></a>Passos seguintes
[Saiba como preparar o](tutorial-prepare-azure.md) Azure para recuperação de desastres de VMs VMware.

[9.27 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
