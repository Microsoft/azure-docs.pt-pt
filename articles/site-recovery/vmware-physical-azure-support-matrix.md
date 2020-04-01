---
title: Matriz de suporte para recuperação de vmware/desastre físico na recuperação do site azure
description: Resume o suporte para a recuperação de desastres de VMware VMs e servidor físico para O Azure usando a Recuperação do Site Azure.
ms.topic: conceptual
ms.date: 2/24/2020
ms.openlocfilehash: b4cf19f4f74ba24951efb806a9f2e3d88fcad7bc
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478424"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Matriz de suporte para recuperação de desastres de VMware VMs e servidores físicos para O Azure

Este artigo resume componentes e configurações suportadas para recuperação de desastres de VMware VMs e servidores físicos para Azure usando a Recuperação do [Site Azure](site-recovery-overview.md).

- [Saiba mais](vmware-azure-architecture.md) sobre a arquitetura de recuperação de desastres vMware VM/servidor físico.
- Siga [os nossos tutoriais](tutorial-prepare-azure.md) para experimentar a recuperação de desastres.

## <a name="deployment-scenarios"></a>Cenários de implementação

**Cenário** | **Detalhes**
--- | ---
Recuperação de desastres de VMware VMs | Replicação de VMware vMware no local para Azure. Pode implementar este cenário no portal Azure ou utilizando o [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Recuperação de desastres de servidores físicos | Replicação de servidores físicos Windows/Linux no local para o Azure. Pode implementar este cenário no portal Azure.

## <a name="on-premises-virtualization-servers"></a>Servidores de virtualização no local

**Servidor** | **Requisitos** | **Detalhes**
--- | --- | ---
vCenter Server | Versão 6.7, 6.5, 6.0 ou 5.5 | Recomendamos que utilize um servidor vCenter na sua implementação de recuperação de desastres.
vSphere anfitriões | Versão 6.7, 6.5, 6.0 ou 5.5 | Recomendamos que os anfitriões vSphere e servidores vCenter estejam localizados na mesma rede que o servidor de processo. Por padrão, o servidor de processo saem no servidor de configuração. [Saiba mais](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Servidor de configuração de recuperação do site

O servidor de configuração é uma máquina no local que executa os componentes de Recuperação do Site, incluindo o servidor de configuração, servidor de processos e servidor de alvo principal.

- Para VMware VMs, você definiu o servidor de configuração descarregando um modelo OVF para criar um VMware VM.
- Para servidores físicos, configurar manualmente a máquina do servidor de configuração.

**Componente** | **Requisitos**
--- |---
Núcleos de CPU | 8
RAM | 16 GB
Número de discos | 3 discos<br/><br/> Os discos incluem o disco OS, o disco de cache do servidor de processo e a unidade de retenção para o failback.
Espaço livre de disco | 600 GB de espaço para a cache do servidor de processo.
Espaço livre de disco | 600 GB de espaço para a unidade de retenção.
Sistema operativo  | Windows Server 2012 R2, ou Windows Server 2016 com experiência no ambiente de trabalho <br/><br> Se pretender utilizar o Master Target incorporado deste aparelho para o backback, certifique-se de que a versão S é igual ou superior aos itens replicados.|
Região do sistema operativo | Inglês (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Não é necessário para a versão [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) do servidor de configuração.
Funções do Windows Server | Não ative serviços de domínio de diretório ativo; Serviços de Informação de Internet (IIS) ou Hyper-V.
Políticas de grupo| - Impedir o acesso ao pedido de comando. <br/> - Impedir o acesso às ferramentas de edição de registo. <br/> - Lógica de confiança para anexos de ficheiros. <br/> - Ligue a execução do guião. <br/> - [Saiba mais](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Certifique-se de que:<br/><br/> - Não tenha um website preexistente <br/> - Ativar [a autenticação anónima](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br/> - Ativar a definição [de FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10))  <br/> - Não tenha website/app pré-existente a ouvir na porta 443<br/>
Tipo NIC | VMXNET3 (quando implantado como VMware VM)
Tipo de endereço IP | Estático
Portas | 443 utilizados para a orquestração de canais de controlo<br/>9443 para transporte de dados

## <a name="replicated-machines"></a>Máquinas replicadas

A Recuperação do Site suporta a replicação de qualquer carga de trabalho em funcionamento numa máquina suportada.

> [!Note]
> A tabela seguinte lista o suporte para máquinas com bota BIOS. Consulte a secção [de armazenamento](#storage) para suporte em máquinas baseadas na UEFI.

**Componente** | **Detalhes**
--- | ---
Definições da máquina | As máquinas que se replicam ao Azure devem satisfazer os [requisitos do Azure.](#azure-vm-requirements)
Carga de trabalho da máquina | A Recuperação do Site suporta a replicação de qualquer carga de trabalho em funcionamento numa máquina suportada. [Saiba mais](site-recovery-workload.md).
Windows Server 2019 | Suportado a partir do [rollup update 34](https://support.microsoft.com/help/4490016) (versão 9.22 do serviço mobility) em diante.
Windows Server 2016 64-bit | Suportado para Server Core, Server com Experiência de Ambiente de Trabalho.
Windows Server 2012 R2 / Windows Server 2012 | Suportado.
Windows Server 2008 R2 com SP1 em diante. | Suportado.<br/><br/> A partir da versão [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) do agente de serviço de Mobilidade, é necessário [serreparado (SSU)](https://support.microsoft.com/help/4490628) e [atualização SHA-2](https://support.microsoft.com/help/4474419) instalada em máquinas que executam o Windows 2008 R2 com SP1 ou mais tarde. A SHA-1 não é suportada a partir de setembro de 2019 e se a assinatura de código SHA-2 não estiver ativada, a extensão do agente não instala/atualização como esperado. Saiba mais sobre a [atualização e requisitos sha-2.](https://aka.ms/SHA-2KB)
Windows Server 2008 com SP2 ou mais tarde (64-bit/32-bit) |  Apoiado apenas para a migração. [Saiba mais](migrate-tutorial-windows-server-2008.md).<br/><br/> A partir da versão [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) do agente de serviço smomobility, necessita de atualização de pilhas de [manutenção (SSU)](https://support.microsoft.com/help/4493730) e [de atualização SHA-2](https://support.microsoft.com/help/4474419) instalada nas máquinas SP2 do Windows 2008. A ISHA-1 não é suportada a partir de setembro de 2019, e se a assinatura de código SHA-2 não estiver ativada, a extensão do agente não vai instalar/atualizar como esperado. Saiba mais sobre a [atualização e requisitos sha-2.](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
Windows 10, Windows 8.1, Windows 8 | Suportado.
Windows 7 com SP1 64-bit | Suportado a partir do [rollup update 36](https://support.microsoft.com/help/4503156) (versão 9.22 do serviço mobility) em diante. </br></br> A partir das [9h30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) do agente de serviço de Mobilidade, necessita de atualização de pilhas de [manutenção (SSU)](https://support.microsoft.com/help/4490628) e [de atualização SHA-2](https://support.microsoft.com/help/4474419) instalada nas máquinas SP1 do Windows 7.  A SHA-1 não é suportada a partir de setembro de 2019 e se a assinatura de código SHA-2 não estiver ativada, a extensão do agente não instala/atualização como esperado. Saiba mais sobre a [atualização e requisitos sha-2.](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
Linux | Apenas o sistema de 64 bits é suportado. O sistema de 32 bits não é suportado.<br/><br/>Todos os servidores Linux devem ter componentes do Serviço de [Integração Linux (LIS) instalados.](https://www.microsoft.com/download/details.aspx?id=55106) É necessário iniciar o servidor em Azure após o teste failover/failover. Se faltarem componentes LIS, certifique-se de instalar os [componentes](https://www.microsoft.com/download/details.aspx?id=55106) antes de permitir a replicação das máquinas em Azure. <br/><br/> O Site Recovery orquestra falhas na execução de servidores Linux em Azure. No entanto, os fornecedores do Linux podem limitar o suporte apenas a versões de distribuição que não tenham atingido o fim da vida.<br/><br/> On Linux distributions, only the stock kernels that are part of the distribution minor version release/update are supported.<br/><br/> A atualização das máquinas protegidas através das principais versões de distribuição do Linux não é suportada. Para atualizar, desative a replicação, atualize o sistema operativo e, em seguida, ative novamente a replicação.<br/><br/> [Saiba mais](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) sobre o suporte ao Linux e à tecnologia de código aberto no Azure.
Empresa de Chapéu Vermelho Linux | 5.2 a 5.11</b><br/> 6.1 a 6.10</b> </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1 <br/> Os servidores que executam a Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.10 não têm componentes de [Integração Linux (LIS) pré-instalados.](https://www.microsoft.com/download/details.aspx?id=55106) Certifique-se de instalar os [componentes](https://www.microsoft.com/download/details.aspx?id=55106) antes de permitir a replicação das máquinas para arrancar em Azure.
Linux: CentOS | 5.2 a 5.11</b><br/> 6.1 a 6.10</b><br/> 7.0 a 7.6<br/> <br/> 8.0 a 8.1<br/><br/> Os servidores que executam o CentOS 5.2-5.11 & 6.1-6.10 não têm componentes dos Serviços de [Integração linux (LIS) pré-instalados.](https://www.microsoft.com/download/details.aspx?id=55106) Certifique-se de instalar os [componentes](https://www.microsoft.com/download/details.aspx?id=55106) antes de permitir a replicação das máquinas para arrancar em Azure.
Ubuntu | Servidor Ubuntu 14.04 LTS [(versões de kernel suportadas por revisão)](#ubuntu-kernel-versions)<br/><br/>Servidor Ubuntu 16.04 LTS [(versões de kernel suportadas por revisão)](#ubuntu-kernel-versions) </br> Servidor Ubuntu 18.04 LTS [(versões de kernel suportadas por revisão)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(versões de kernel suportadas](#debian-kernel-versions) por revisão)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(versões de kernel suportadas por revisão)](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 15, 15 SP1 [(versões de kernel suportadas por revisão)](#suse-linux-enterprise-server-15-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> A atualização das máquinas replicadas do SUSE Linux Enterprise Server 11 SP3 para SP4 não é suportada. Para atualizar, desative a replicação e reativa-a após a atualização.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)<br/><br/> Executar o kernel compatível com o Chapéu Vermelho ou Unbreakable Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5)

> [!Note]
> Para cada uma das versões do Windows, a Azure Site Recovery apenas suporta a construção do Canal de Manutenção a [Longo Prazo (LTSC).](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc)  Os lançamentos [semestral do Canal](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) não são suportados neste momento.

### <a name="ubuntu-kernel-versions"></a>Versões de kernel Ubuntu

**Libertação apoiada** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
14.04 LTS | [9.32][9.32 UR] | 3.13.0-24-genérico a 3.13.0-170-genérico,<br/>3.16.0-25-genérico a 3.16.0-77-genérico,<br/>3.19.0-18-genérico a 3.19.0-80 genéricos,<br/>4.2.0-18-genérico a 4.2.0-42-genérico,<br/>4.4.0-21-genérico a 4.4.0-148-genérico,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
14.04 LTS | [9.31][9.31 UR] | 3.13.0-24-genérico a 3.13.0-170-genérico,<br/>3.16.0-25-genérico a 3.16.0-77-genérico,<br/>3.19.0-18-genérico a 3.19.0-80 genéricos,<br/>4.2.0-18-genérico a 4.2.0-42-genérico,<br/>4.4.0-21-genérico a 4.4.0-148-genérico,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
14.04 LTS | [9.30][9.30 UR] | 3.13.0-24-genérico a 3.13.0-170-genérico,<br/>3.16.0-25-genérico a 3.16.0-77-genérico,<br/>3.19.0-18-genérico a 3.19.0-80 genéricos,<br/>4.2.0-18-genérico a 4.2.0-42-genérico,<br/>4.4.0-21-genérico a 4.4.0-148-genérico,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
14.04 LTS | [9.29][9.29 UR]| 3.13.0-24-genérico a 3.13.0-170-genérico,<br/>3.16.0-25-genérico a 3.16.0-77-genérico,<br/>3.19.0-18-genérico a 3.19.0-80 genéricos,<br/>4.2.0-18-genérico a 4.2.0-42-genérico,<br/>4.4.0-21-genérico a 4.4.0-148-genérico,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
|||
16.04 LTS | [9.32][9.32 UR] | 4.4.0-21-genérico a 4.4.0-171-genérico,<br/>4.8.0-34-genérico a 4.8.0-58-genérico,<br/>4.10.0-14-genérico a 4.10.0-42-genérico,<br/>4.11.0-13-genérico a 4.11.0-14-genérico,<br/>4.13.0-16-genérico a 4.13.0-45-genérico,<br/>4.15.0-13-genérico a 4.15.0-74-genérico<br/>4.11.0-1009-azure a 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure a 4.15.0-1066-azure|
16.04 LTS | [9.31][9.31 UR] | 4.4.0-21-genérico a 4.4.0-170-genérico,<br/>4.8.0-34-genérico a 4.8.0-58-genérico,<br/>4.10.0-14-genérico a 4.10.0-42-genérico,<br/>4.11.0-13-genérico a 4.11.0-14-genérico,<br/>4.13.0-16-genérico a 4.13.0-45-genérico,<br/>4.15.0-13-genérico a 4.15.0-72-genérico<br/>4.11.0-1009-azure a 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure a 4.15.0-1063-azure|
16.04 LTS | [9.30][9.30 UR] | 4.4.0-21-genérico a 4.4.0-166-genérico,<br/>4.8.0-34-genérico a 4.8.0-58-genérico,<br/>4.10.0-14-genérico a 4.10.0-42-genérico,<br/>4.11.0-13-genérico a 4.11.0-14-genérico,<br/>4.13.0-16-genérico a 4.13.0-45-genérico,<br/>4.15.0-13-genérico a 4.15.0-66-genérico<br/>4.11.0-1009-azure a 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure a 4.15.0-1061-azure|
16.04 LTS | [9.29][9.29 UR] | 4.4.0-21-genérico a 4.4.0-164-genérico,<br/>4.8.0-34-genérico a 4.8.0-58-genérico,<br/>4.10.0-14-genérico a 4.10.0-42-genérico,<br/>4.11.0-13-genérico a 4.11.0-14-genérico,<br/>4.13.0-16-genérico a 4.13.0-45-genérico,<br/>4.15.0-13-genérico a 4.15.0-64-genérico<br/>4.11.0-1009-azure a 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure a 4.15.0-1059-azure|
|||
18.04 LTS | [9.32][9.32 UR]| 4.15.0-20-genérico a 4.15.0-74-genérico </br> 4.18.0-13-genérico a 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-37-genérico </br> 5.3.0-19-genérico a 5.3.0-24-genérico </br> 4.15.0-1009-azure a 4.15.0-1037-azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-azure a 5.0.0-1028-azure </br> 5.3.0-1007-azure a 5.3.0-1009-azure|
18.04 LTS | [9.31][9.31 UR]| 4.15.0-20-genérico a 4.15.0-72-genérico </br> 4.18.0-13-genérico a 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-37-genérico </br> 5.3.0-19-genérico a 5.3.0-24-genérico </br> 4.15.0-1009-azure a 4.15.0-1037-azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-azure a 5.0.0-1025-azure </br> 5.3.0-1007-azure|
18.04 LTS | [9.30][9.30 UR] | 4.15.0-20-genérico a 4.15.0-66-genérico </br> 4.18.0-13-genérico a 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-32-genérico </br> 4.15.0-1009-azure a 4.15.0-1037-azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-azure a 5.0.0-1023-azure|
18.04 LTS | [9.29][9.29 UR] | 4.15.0-20-genérico a 4.15.0-62-genérico </br> 4.18.0-13-genérico a 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-27-genérico </br> 4.15.0-1009-azure a 4.15.0-1037-azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-azure a 5.0.0-1018-azure|

### <a name="debian-kernel-versions"></a>Versões de kernel debian


**Libertação apoiada** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
Debiano 7 | [9.29,][9.29 UR] [9.30,][9.30 UR] [9.31,][9.31 UR] [9.32][9.32 UR]| 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debiano 8 | [9.30,][9.30 UR] [9.31,][9.31 UR] [9.32][9.32 UR] | 3.16.0-4-amd64 a 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.11-amd64 |
Debiano 8 | [9.29][9.29 UR] | 3.16.0-4-amd64 a 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.9-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 versões de kernel suportadas

**Libertar** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.32][9.32 UR] | Todos os [núcleos SUSE 12 SP1,SP2,SP3,SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) são suportados.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.34-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.31][9.31 UR] | Todos os [núcleos SUSE 12 SP1,SP2,SP3,SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) são suportados.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.30][9.30 UR] | Todos os [núcleos SUSE 12 SP1,SP2,SP3,SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) são suportados.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.26-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.29][9.29 UR] | Todos os [núcleos SUSE 12 SP1,SP2,SP3,SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) são suportados.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.23-azure  |

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>SUSE Linux Enterprise Server 15 versões de kernel suportadas

**Libertar** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 e 15 SP1 | 9.32 | Todos os [núcleos SUSE 15 e 15 são](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_15) suportados.</br></br> 4.12.14-5.5-azure a 4.12.14-8.22-azure |

## <a name="linux-file-systemsguest-storage"></a>Sistemas de ficheiros Linux/armazenamento de hóspedes

**Componente** | **Apoiado**
--- | ---
Sistemas de ficheiros | ext3, ext4, XFS, BTRFS (condições aplicáveis nos termos deste quadro)
Gestor de volume | - A LVM é apoiada.<br/> - /boot on LVM é suportado a partir do [Rollup atualizado 31](https://support.microsoft.com/help/4478871/) (versão 9.20 do serviço mobility) em diante. Não é suportado em versões anteriores do serviço de Mobilidade.<br/> - Vários discos de SO não são suportados.
Dispositivos de armazenamento paravirtualizados | Os dispositivos exportados por controladores paravirtualizados não são suportados.
Dispositivos IO de bloco multi-fila | Não suportado.
Servidores físicos com o controlador de armazenamento HP CCISS | Não suportado.
Convenção de nomeação de ponto de montagem/ponto de montagem | O nome do dispositivo ou o nome do ponto de montagem devem ser únicos.<br/> Certifique-se de que não existem dois dispositivos/pontos de montagem com nomes sensíveis à caixa. Por exemplo, os dispositivos de nomeação para o mesmo VM que *o dispositivo1* e *o Dispositivo1* não são suportados.
Diretórios | Se estiver a executar uma versão do serviço mobility mais cedo do que a versão 9.20 (lançada no [Update Rollup 31](https://support.microsoft.com/help/4478871/)), então estas restrições aplicam-se:<br/><br/> - Estes diretórios (se configurados como divisórias/sistemas de ficheiros separados) devem estar no mesmo disco OS no servidor de origem: /(raiz), /boot, /usr, /usr/local, /var, /etc.</br> - O diretório /boot deve estar numa divisória de disco e não ser um volume DEVM.<br/><br/> A partir da versão 9.20, estas restrições não se aplicam.
Diretório de botas | - Os discos de arranque não devem estar em formato de partição GPT. Esta é uma limitação da arquitetura Azure. Os discos GPT são suportados como discos de dados.<br/><br/> Vários discos de arranque num VM não são suportados<br/><br/> - /arranque num volume de LVM em mais de um disco não é suportado.<br/> - Uma máquina sem um disco de arranque não pode ser replicada.
Requisitos de espaço gratuito| 2 GB na partição /raiz <br/><br/> 250 MB na pasta de instalação
XFSv5 | As funcionalidades xFSv5 nos sistemas de ficheiros XFS, tais como verificação de metadados, são suportadas (versão de serviço de mobilidade 9.10 em diante).<br/> Utilize o utilitário xfs_info para verificar o superbloco XFS para a partição. Se `ftype` estiver definido para 1, então as funcionalidades XFSv5 estão a ser utilizadas.
BTRFS | O BTRFS é suportado a partir do [Rollup atualizado 34](https://support.microsoft.com/help/4490016) (versão 9.22 do serviço mobility) em diante. A BTRFS não é apoiada se:<br/><br/> - O subvolume do sistema de ficheiros BTRFS é alterado após a proteção de ativação.</br> - O sistema de ficheiros BTRFS está distribuído por vários discos.</br> - O sistema de ficheiros BTRFS suporta raid.

## <a name="vmdisk-management"></a>Gestão VM/Disco

**Ação** | **Detalhes**
--- | ---
Redimensionar o disco em VM replicado | Suportado na fonte VM antes da falha, diretamente nas propriedades VM. Não é necessário desativar/reativar a replicação.<br/><br/> Se alterar a fonte de VM após a falha, as alterações não são capturadas.<br/><br/> Se alterar o tamanho do disco no VM Azure após a falha, quando falhar, a Recuperação do Site cria um novo VM com as atualizações.
Adicione o disco em VM replicado | Não suportado.<br/> Desative a replicação para o VM, adicione o disco e, em seguida, reative a replicação.

## <a name="network"></a>Rede

**Componente** | **Apoiado**
--- | ---
Rede de anfitriões NIC Teaming | Suportado para VMware VMs. <br/><br/>Não suportado para a replicação da máquina física.
Rede de anfitriões VLAN | Sim.
Rede de anfitriões IPv4 | Sim.
Rede de anfitriões IPv6 | Não.
Equipa de rede de hóspedes/servidorES NIC Teaming | Não.
Rede de hóspedes/servidorIPv4 | Sim.
Rede de hóspedes/servidorIPv6 | Não.
IP estático de rede de hóspedes/servidor (Windows) | Sim.
IP estático de rede de hóspedes/servidor (Linux) | Sim. <br/><br/>Os VMs são configurados para usar DHCP no backback.
Rede de hóspedes/servidor múltiplos NICs | Sim.


## <a name="azure-vm-network-after-failover"></a>Rede Azure VM (após falha)

**Componente** | **Apoiado**
--- | ---
Azure ExpressRoute | Sim
ILB | Sim
ELB | Sim
Traffic Manager do Azure | Sim
Multi-NIC | Sim
Endereço IP reservado | Sim
IPv4 | Sim
Reter endereço IP de origem | Sim
Pontos finais do serviço de rede virtual Azure<br/> | Sim
Redes aceleradas | Não

## <a name="storage"></a>Armazenamento
**Componente** | **Apoiado**
--- | ---
Disco dinâmico | O disco osso deve ser um disco básico. <br/><br/>Os discos de dados podem ser discos dinâmicos
Configuração do disco docker | Não
NFS hospedeiro | Sim para VMware<br/><br/> Não para servidores físicos
Anfitrião SAN (iSCSI/FC) | Sim
Anfitrião vSAN | Sim para VMware<br/><br/> N/A para servidores físicos
Multipath anfitrião (MPIO) | Sim, testado com Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM para CLARiiON
Volumes Virtuais de Hospedar (VVols) | Sim para VMware<br/><br/> N/A para servidores físicos
VMDK convidado/servidor | Sim
Disco de cluster partilhado convidado/servidor | Não
Disco encriptado de hóspede/servidor | Não
NFS convidado/servidor | Não
Convidado/servidor iSCSI | Para a migração - Sim<br/>Para recuperação de desastres - Não, o iSCSI falhará como um disco ligado ao VM
Hóspede/servidor SMB 3.0 | Não
RDM convidado/servidor | Sim<br/><br/> N/A para servidores físicos
Disco convidado/servidor > 1 TB | Sim, o disco deve ser maior que 1024 MB<br/><br/>Até 8.192 GB ao replicar discos geridos (versão 9.26 em diante)<br></br> Até 4.095 GB ao replicar contas de armazenamento
Disco de hóspede/servidor com tamanho de setor físico lógico e 4k | Não
Disco de hóspede/servidor com 4K lógico e 512 bytes tamanho do setor físico | Não
Volume de hóspedes/servidor com disco listrado >4 TB <br/><br/>Gestão lógica do volume (LVM)| Sim
Hóspede/servidor - Espaços de Armazenamento | Não
Disco de adição/remoção de hot/servidor/hóspede/servidor | Não
Hóspede/servidor - excluir o disco | Sim
Multipata convidado/servidor (MPIO) | Não
Partições GPT convidado/servidor | Cinco partições são suportadas a partir do [Rollup update 37](https://support.microsoft.com/help/4508614/) (versão 9.25 do serviço mobility) em diante. Anteriormente, quatro foram apoiados.
ReFS | Sistema de Ficheiros Resiliente é suportado com versão de serviço de Mobilidade 9.23 ou superior
Bota EFI/UEFI de hóspede/servidor | - Suportado para Windows Server 2012 ou posteriormente, SLES 12 SP4 e RHEL 8.0 com versão 9.30 do agente de mobilidade<br/> - Não é suportado o tipo de boot UEFI seguro.

## <a name="replication-channels"></a>Canais de replicação

|**Tipo de replicação**   |**Apoiado**  |
|---------|---------|
|Transferências de dados descarregadas (ODX)    |       Não  |
|Sementeing offline        |   Não      |
| Azure Data Box | Não

## <a name="azure-storage"></a>Storage do Azure

**Componente** | **Apoiado**
--- | ---
Armazenamento localmente redundante | Sim
Armazenamento georredundante | Sim
Armazenamento georredundante com acesso de leitura | Sim
Armazenamento fresco | Não
Armazenamento quente| Não
Blobs de blocos | Não
Encriptação em repouso (SSE)| Sim
Encriptação em repouso (CMK)| Sim (via Powershell Az 3.3.0 módulo em diante)
Armazenamento Premium | Sim
Serviço de importação/exportação | Não
Firewalls de armazenamento azure para VNets | Sim.<br/> Configurado na conta de armazenamento/cache alvo (utilizada para armazenar dados de replicação).
Contas de armazenamento v2 de uso geral (níveis quentes e frescos) | Sim (os custos de transação são substancialmente mais elevados para o V2 em comparação com o V1)

## <a name="azure-compute"></a>Computação Azure

**Funcionalidade** | **Apoiado**
--- | ---
Conjuntos de disponibilidade | Sim
Zonas de disponibilidade | Não
HUB | Sim
Managed disks | Sim

## <a name="azure-vm-requirements"></a>Requisitos da VM do Azure

As VMs no local replicadas ao Azure devem satisfazer os requisitos de VM Azure resumidos nesta tabela. Quando a Recuperação do Site executa um pré-requisito para verificar a replicação, a verificação falhará se alguns dos requisitos não forem cumpridos.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operativo convidado | Verifique [os sistemas operativos suportados](#replicated-machines) para máquinas replicadas. | A verificação falha se não for suportada.
Arquitetura do sistema operativo convidado | 64-bit. | A verificação falha se não for suportada.
Tamanho do disco do sistema operativo | Até 2.048 GB. | A verificação falha se não for suportada.
Contagem de discos do sistema operativo | 1 | A verificação falha se não for suportada.
Contagem de discos de dados | 64 ou menos. | A verificação falha se não for suportada.
Tamanho do disco de dados | Até 8.192 GB ao replicar-se para o disco gerido (versão 9.26 em diante)<br></br>Até 4.095 GB ao replicar para a conta de armazenamento| A verificação falha se não for suportada.
Placas de rede | São suportados múltiplos adaptadores. |
VHD partilhado | Não suportado. | A verificação falha se não for suportada.
Disco FC | Não suportado. | A verificação falha se não for suportada.
BitLocker | Não suportado. | O BitLocker deve ser desativado antes de ativar a replicação de uma máquina. |
o nome da VM | De 1 a 63 caracteres.<br/><br/> Limitado a letras, números e hífenes.<br/><br/> O nome da máquina deve começar e terminar com uma letra ou número. |  Atualize o valor nas propriedades da máquina na Recuperação do Site.

## <a name="resource-group-limits"></a>Limites de grupo de recursos

Para compreender o número de máquinas virtuais que podem ser protegidas num único grupo de recursos, consulte o artigo sobre [limites de subscrição e quotas.](/azure/azure-resource-manager/management/azure-subscription-service-limits#resource-group-limits)

## <a name="churn-limits"></a>Limites de churn

A tabela seguinte fornece os limites do Azure Site Recovery.
- Estes limites são baseados nos nossos testes, mas não cobrem todas as combinações possíveis de I/O da aplicação.
- Os resultados reais podem variar consoante a combinação de E/S da sua aplicação.
- Para obter os melhores resultados, recomendamos vivamente que execute a [ferramenta Deimplantação planner](site-recovery-deployment-planner.md), e realize testes extensivos de aplicação usando falhas de teste para obter a verdadeira imagem de desempenho para a sua aplicação.

**Alvo de replicação** | **Tamanho médio de E/S do disco de origem** |**Média de alterações a dados do disco de origem** | **Total de dados de disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |    336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou superior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou superior |20 MB/s | 1684 GB por disco


**Alterações a dados de origem** | **Limite máximo**
---|---
Alterações a dados de pico em todos os discos numa VM | 54 MB/s
Máximo de alterações a dados por dia suportadas por um Servidor de Processos | 2 TB

- Estes são números médios, que pressupõem uma sobreposição de 30 por cento de E/S.
- O Site Recovery é capaz de processar um débito superior com base no rácio de sobreposição, em tamanhos de escrita maiores e no comportamento real de E/S da carga de trabalho.
- Estes números assumem um atraso típico de aproximadamente cinco minutos. Ou seja, depois de os dados serem carregados, são processados e é criado um ponto de recuperação ao fim de cinco minutos.

## <a name="vault-tasks"></a>Tarefas de cofre

**Ação** | **Apoiado**
--- | ---
Mova o cofre através de grupos de recursos | Não
Mover o cofre dentro e através de assinaturas | Não
Movimentar armazenamento, rede, VMs Azure em grupos de recursos | Não
Mover armazenamento, rede, VMs Azure dentro e através de subscrições. | Não


## <a name="obtain-latest-components"></a>Obter componentes mais recentes

**Nome** | **Descrição** | **Detalhes**
--- | --- | ---
Servidor de configuração | Instalado no local.<br/> Coordena as comunicações entre servidores VMware no local ou máquinas físicas, e Azure. | - [Conheça](vmware-physical-azure-config-process-server-overview.md) o servidor de configuração.<br/> - [Saiba mais sobre](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) o upgrade para a versão mais recente.<br/> - [Saiba a](vmware-azure-deploy-configuration-server.md) configurar o servidor de configuração.
Servidor de processos | Instalado por predefinição no servidor de configuração.<br/> Recebe dados de replicação, otimiza-os com cache, compressão e encriptação, e envia-os para o Azure.<br/> À medida que a sua implementação cresce, pode adicionar servidores de processo adicionais para lidar com volumes maiores de tráfego de replicação. | - [Conheça](vmware-physical-azure-config-process-server-overview.md) o servidor de processos.<br/> - [Saiba mais sobre](vmware-azure-manage-process-server.md#upgrade-a-process-server) o upgrade para a versão mais recente.<br/> - [Aprenda sobre](vmware-physical-large-deployment.md#set-up-a-process-server) a configuração de servidores de processos de escala.
Serviço de Mobilidade | Instalado em VMware VM ou servidores físicos que pretende replicar.<br/> Coordena a replicação entre servidores/servidores físicos VMware no local e Azure.| - [Conheça](vmware-physical-mobility-service-overview.md) o serviço de Mobilidade.<br/> - [Saiba mais sobre](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) o upgrade para a versão mais recente.<br/>



## <a name="next-steps"></a>Passos seguintes
[Saiba como](tutorial-prepare-azure.md) preparar o Azure para a recuperação de desastres de VMware VMs.

[9.32 UR]: https://support.microsoft.com/en-in/help/4538187/update-rollup-44-for-azure-site-recovery
[9.31 UR]: https://support.microsoft.com/en-in/help/4537047/update-rollup-43-for-azure-site-recovery
[9.30 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.29 UR]: https://support.microsoft.com/en-in/help/4528026/update-rollup-41-for-azure-site-recovery
[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
