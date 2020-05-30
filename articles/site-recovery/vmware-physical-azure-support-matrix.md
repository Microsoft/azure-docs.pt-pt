---
title: Matriz de suporte para vMware/recuperação de desastres físicos na recuperação do local de Azure
description: Resume o suporte para a recuperação de desastres de VMware VMs e servidor físico para Azure usando a Recuperação do Site Azure.
ms.topic: conceptual
ms.date: 2/24/2020
ms.openlocfilehash: 52c5b20a4411d865f88a5010ed1b0b3b309345ac
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84190636"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Matriz de suporte para recuperação de desastres de VMware VMs e servidores físicos para Azure

Este artigo resume os componentes e configurações suportados para a recuperação de desastres de VMware VMs e servidores físicos para a Azure usando [a recuperação do site Azure](site-recovery-overview.md).

- [Saiba mais](vmware-azure-architecture.md) sobre a arquitetura de recuperação de desastres vMware VM/servidor físico.
- Siga os [nossos tutoriais](tutorial-prepare-azure.md) para experimentar a recuperação de desastres.

## <a name="deployment-scenarios"></a>Cenários de implementação

**Cenário** | **Detalhes**
--- | ---
Recuperação de desastres de VMware VMs | Replicação de VMware VMs no local para Azure. Pode implementar este cenário no portal Azure ou utilizando [o PowerShell](vmware-azure-disaster-recovery-powershell.md).
Recuperação de desastres de servidores físicos | Replicação dos servidores físicos Windows/Linux no local para o Azure. Pode implementar este cenário no portal Azure.

## <a name="on-premises-virtualization-servers"></a>Servidores de virtualização no local

**Servidor** | **Requisitos** | **Detalhes**
--- | --- | ---
vCenter Server | Versão 6.7, 6.5, 6.0 ou 5.5 | Recomendamos que utilize um servidor vCenter na sua implementação de recuperação de desastres.
vSphere anfitriões | Versão 6.7, 6.5, 6.0 ou 5.5 | Recomendamos que os anfitriões vSphere e servidores vCenter estejam localizados na mesma rede que o servidor de processo. Por predefinição, o servidor de processo funciona no servidor de configuração. [Saiba mais](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Servidor de configuração de recuperação de site

O servidor de configuração é uma máquina no local que executa os componentes de Recuperação do Site, incluindo o servidor de configuração, o servidor de processo e o servidor alvo principal.

- Para VMware VMs, você define o servidor de configuração descarregando um modelo OVF para criar um VMware VM.
- Para servidores físicos, configura manualmente a máquina do servidor de configuração.

**Componente** | **Requisitos**
--- |---
Núcleos de CPU | 8
RAM | 16 GB
Número de discos | 3 discos<br/><br/> Os discos incluem o disco de so, o disco de cache do servidor de processo e a unidade de retenção para falha.
Espaço livre de disco | 600 GB de espaço para a cache do servidor de processo.
Espaço livre de disco | 600 GB de espaço para a unidade de retenção.
Sistema operativo  | Windows Server 2012 R2 ou Windows Server 2016 com experiência no Desktop <br/><br> Se pretender utilizar o Master Target incorporado deste aparelho para falhar, certifique-se de que a versão OS é igual ou superior aos itens replicados.|
Região do sistema operativo | Inglês (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Não é necessário para a versão [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) ou posterior do servidor de configuração.
Funções do Windows Server | Não ative os serviços de domínio do diretório ativo; Serviços de Informação da Internet (IIS) ou Hiper-V.
Políticas de grupo| - Impedir o acesso à ordem de comando. <br/> - Impedir o acesso a ferramentas de edição de registo. <br/> - Lógica de confiança para anexos de ficheiros. <br/> - Ligue a execução do guião. <br/> - [Saiba mais](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Certifique-se de:<br/><br/> - Não tem um website pre-existente <br/> - Ativar a [autenticação anónima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - Ativar a definição [fastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br/> - Não tem site/app pré-existente a ouvir na porta 443<br/>
Tipo NIC | VMXNET3 (quando implementado como VMware VM)
Tipo de endereço IP | Estático
Portas | 443 usado para orquestração de canais de controlo<br/>9443 para transporte de dados

## <a name="replicated-machines"></a>Máquinas replicadas

A Recuperação do Local suporta a replicação de qualquer carga de trabalho em execução numa máquina suportada.

> [!Note]
> A tabela que se segue lista o suporte para máquinas com arranque BIOS. Consulte a secção [de armazenamento](#storage) para obter suporte em máquinas baseadas na UEFI.

**Componente** | **Detalhes**
--- | ---
Configurações da máquina | As máquinas que se replicam em Azure devem satisfazer os [requisitos do Azure.](#azure-vm-requirements)
Carga de trabalho da máquina | A Recuperação do Local suporta a replicação de qualquer carga de trabalho em execução numa máquina suportada. [Saiba mais](https://aka.ms/asr_workload).
Nome da máquina | Certifique-se de que o nome de exibição da máquina não cai em [nomes de recursos reservados do Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/error-reserved-resource-name)<br/><br/> Os nomes de volume lógico não são sensíveis a casos. Certifique-se de que não há dois volumes num dispositivo com o mesmo nome. Ex: Volumes com nomes "voLUME1", "volume1" não podem ser protegidos através da Recuperação do Local de Azure.
Windows Server 2019 | Suportado a partir do [rollup 34](https://support.microsoft.com/help/4490016) da Atualização (versão 9.22 do serviço mobility) em diante.
Windows Server 2016 64-bit | Suportado para Server Core, Servidor com Experiência de Ambiente de Trabalho.
Windows Server 2012 R2 / Windows Server 2012 | Suportado.
Windows Server 2008 R2 com SP1 em diante. | Suportado.<br/><br/> A partir da versão [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) do agente de serviço mobility, precisa de atualização de [stack de manutenção (SSU)](https://support.microsoft.com/help/4490628) e [atualização SHA-2](https://support.microsoft.com/help/4474419) instalada em máquinas que executam o Windows 2008 R2 com SP1 ou mais tarde. O SHA-1 não é suportado a partir de setembro de 2019, e se a assinatura de código SHA-2 não estiver ativada, a extensão do agente não instalará/atualizará como esperado. Saiba mais sobre [a atualização e requisitos SHA-2](https://aka.ms/SHA-2KB).
Windows Server 2008 com SP2 ou mais tarde (64-bit/32-bit) |  Apoiado apenas para a migração. [Saiba mais](migrate-tutorial-windows-server-2008.md).<br/><br/> A partir da versão [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) do agente de serviço mobility, precisa de [atualização de stack de manutenção (SSU)](https://support.microsoft.com/help/4493730) e [atualização SHA-2](https://support.microsoft.com/help/4474419) instalada nas máquinas SP2 do Windows 2008. O ISHA-1 não é suportado a partir de setembro de 2019, e se a assinatura de código SHA-2 não estiver ativada, a extensão do agente não instalará/atualizará como esperado. Saiba mais sobre [a atualização e requisitos SHA-2](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus).
Windows 10, Windows 8.1, Windows 8 | Suportado.
Windows 7 com SP1 64-bit | Suportado a partir do [rollup 36](https://support.microsoft.com/help/4503156) da Atualização (versão 9.22 do serviço mobility) em diante. </br></br> A partir das [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) do agente de serviço de Mobilidade, precisa de [atualização de stack de manutenção (SSU)](https://support.microsoft.com/help/4490628) e [atualização SHA-2](https://support.microsoft.com/help/4474419) instalada nas máquinas SP1 do Windows 7.  O SHA-1 não é suportado a partir de setembro de 2019, e se a assinatura de código SHA-2 não estiver ativada, a extensão do agente não instalará/atualizará como esperado. Saiba mais sobre [a atualização e requisitos SHA-2](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus).
Linux | Apenas um sistema de 64 bits é suportado. Sistema de 32 bits não é suportado.<br/><br/>Todos os servidores Linux devem ter [componentes do Linux Integration Services (LIS) instalados.](https://www.microsoft.com/download/details.aspx?id=55106) É necessário iniciar o servidor em Azure após o teste failover/failover. Se faltarem componentes LIS incorporados, certifique-se de que instala os [componentes](https://www.microsoft.com/download/details.aspx?id=55106) antes de permitir a replicação para as máquinas arrancarem em Azure. <br/><br/> As orquestras de recuperação do site falham em executar servidores Linux em Azure. No entanto, os fornecedores do Linux podem limitar o suporte apenas a versões de distribuição que não tenham atingido o fim de vida.<br/><br/> Nas distribuições linux, apenas os núcleos de stock que fazem parte da versão/atualização da versão menor de distribuição são suportados.<br/><br/> A atualização de máquinas protegidas nas principais versões de distribuição do Linux não é suportada. Para atualizar, desativar a replicação, atualizar o sistema operativo e, em seguida, voltar a ativar a replicação.<br/><br/> [Saiba mais](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) sobre o suporte ao Linux e à tecnologia open-source em Azure.
Linux Red Hat Enterprise | 5.2 a 5.11</b><br/> 6.1 a 6.10</b> </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1 <br/> Poucos núcleos mais antigos em servidores com red hat Enterprise Linux 5.2-5.11 & 6.1-6.10 não têm [componentes linux de integração (LIS) pré-instalados.](https://www.microsoft.com/download/details.aspx?id=55106) Se faltarem componentes LIS incorporados, certifique-se de que instala os [componentes](https://www.microsoft.com/download/details.aspx?id=55106) antes de permitir a replicação para as máquinas arrancarem em Azure.
Linux: CentOS | 5.2 a 5.11</b><br/> 6.1 a 6.10</b><br/> 7.0 a 7.6<br/> <br/> 8.0 a 8.1<br/><br/> Poucos núcleos mais antigos em servidores que executam CentOS 5.2-5.11 & 6.1-6.10 não possuem [componentes do Linux Integration Services (LIS) pré-instalados.](https://www.microsoft.com/download/details.aspx?id=55106) Se faltarem componentes LIS incorporados, certifique-se de que instala os [componentes](https://www.microsoft.com/download/details.aspx?id=55106) antes de permitir a replicação para as máquinas arrancarem em Azure.
Ubuntu | Ubuntu 14.04 servidor LTS [(versões de kernel suportados por revisão)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 servidor LTS [(versões de kernel suportados por revisão)](#ubuntu-kernel-versions) </br> Ubuntu 18.04 servidor LTS [(versões de kernel suportados por revisão)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(versões de kernel suportadas pela revisão)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(versões de kernel suportadas pela revisão)](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 15, 15 SP1 [(versões de kernel suportadas pela revisão)](#suse-linux-enterprise-server-15-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> O upgrade de máquinas replicadas do SUSE Linux Enterprise Server 11 SP3 para SP4 não é suportado. Para atualizar, desative a replicação e reativa após a atualização.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)<br/><br/> Executando o núcleo compatível com chapéu vermelho ou desbloqueio de kernel da empresa inquebrável 3, 4 & 5 (UEK3, UEK4, UEK5)

> [!Note]
> Para cada uma das versões do Windows, a Azure Site Recovery apenas suporta [construções de Canais de Manutenção a Longo Prazo (LTSC).](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc)  Os lançamentos [semi-anuais do Canal](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) não são atualmente suportados neste momento.

### <a name="ubuntu-kernel-versions"></a>Versões de kernel Ubuntu

**Lançamento apoiado** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
14.04 LTS | [9.32][9.32 UR] | 3.13.0-24-genérico a 3.13.0-170-genérico,<br/>3.16.0-25-genérico a 3.16.0-77-genéricos,<br/>3.19.0-18-genérico a 3.19.0-80-genérico,<br/>4.2.0-18-genérico a 4.2.0-42-genérico,<br/>4.4.0-21-genérico a 4.4.0-148-genérico,<br/>4.15.0-1023-azure para 4.15.0-1045-azure |
14.04 LTS | [9.31][9.31 UR] | 3.13.0-24-genérico a 3.13.0-170-genérico,<br/>3.16.0-25-genérico a 3.16.0-77-genéricos,<br/>3.19.0-18-genérico a 3.19.0-80-genérico,<br/>4.2.0-18-genérico a 4.2.0-42-genérico,<br/>4.4.0-21-genérico a 4.4.0-148-genérico,<br/>4.15.0-1023-azure para 4.15.0-1045-azure |
14.04 LTS | [9.30][9.30 UR] | 3.13.0-24-genérico a 3.13.0-170-genérico,<br/>3.16.0-25-genérico a 3.16.0-77-genéricos,<br/>3.19.0-18-genérico a 3.19.0-80-genérico,<br/>4.2.0-18-genérico a 4.2.0-42-genérico,<br/>4.4.0-21-genérico a 4.4.0-148-genérico,<br/>4.15.0-1023-azure para 4.15.0-1045-azure |
14.04 LTS | [9.29][9.29 UR]| 3.13.0-24-genérico a 3.13.0-170-genérico,<br/>3.16.0-25-genérico a 3.16.0-77-genéricos,<br/>3.19.0-18-genérico a 3.19.0-80-genérico,<br/>4.2.0-18-genérico a 4.2.0-42-genérico,<br/>4.4.0-21-genérico a 4.4.0-148-genérico,<br/>4.15.0-1023-azure para 4.15.0-1045-azure |
|||
16.04 LTS | [9.32][9.32 UR] | 4.4.0-21-genérico a 4.4.0-171-genérico,<br/>4.8.0-34-genérico a 4.8.0-58-genérico,<br/>4.10.0-14-genérico a 4.10.0-42-genérico,<br/>4.11.0-13-genérico a 4.11.0-14-genérico,<br/>4.13.0-16-genérico a 4.13.0-45-genéricos,<br/>4.15.0-13-genérico a 4.15.0-74-genérico<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure para 4.13.0-1018-azure <br/>4.15.0-1012-azure para 4.15.0-1066-azure|
16.04 LTS | [9.31][9.31 UR] | 4.4.0-21-genérico a 4.4.0-170-genérico,<br/>4.8.0-34-genérico a 4.8.0-58-genérico,<br/>4.10.0-14-genérico a 4.10.0-42-genérico,<br/>4.11.0-13-genérico a 4.11.0-14-genérico,<br/>4.13.0-16-genérico a 4.13.0-45-genéricos,<br/>4.15.0-13-genérico a 4.15.0-72-genérico<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure para 4.13.0-1018-azure <br/>4.15.0-1012-azure para 4.15.0-1063-azure|
16.04 LTS | [9.30][9.30 UR] | 4.4.0-21-genérico a 4.4.0-166-genérico,<br/>4.8.0-34-genérico a 4.8.0-58-genérico,<br/>4.10.0-14-genérico a 4.10.0-42-genérico,<br/>4.11.0-13-genérico a 4.11.0-14-genérico,<br/>4.13.0-16-genérico a 4.13.0-45-genéricos,<br/>4.15.0-13-genérico a 4.15.0-66-genérico<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure para 4.13.0-1018-azure <br/>4.15.0-1012-azure para 4.15.0-1061-azure|
16.04 LTS | [9.29][9.29 UR] | 4.4.0-21-genérico a 4.4.0-164-genérico,<br/>4.8.0-34-genérico a 4.8.0-58-genérico,<br/>4.10.0-14-genérico a 4.10.0-42-genérico,<br/>4.11.0-13-genérico a 4.11.0-14-genérico,<br/>4.13.0-16-genérico a 4.13.0-45-genéricos,<br/>4.15.0-13-genérico a 4.15.0-64-genérico<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure para 4.13.0-1018-azure <br/>4.15.0-1012-azure para 4.15.0-1059-azure|
|||
18.04 LTS | [9.32][9.32 UR]| 4.15.0-20-genérico a 4.15.0-74-genérico </br> 4.18.0-13-genérico a 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-37-genérico </br> 5.3.0-19 genérico a 5.3.0-24-genérico </br> 4.15.0-1009-azure para 4.15.0-1037-azure </br> 4.18.0-1006-azure para 4.18.0-1025-azure </br> 5.0.0-1012-azure para 5.0.0-1028-azure </br> 5.3.0-1007-azure para 5.3.0-1009-azure|
18.04 LTS | [9.31][9.31 UR]| 4.15.0-20-genérico a 4.15.0-72-genérico </br> 4.18.0-13-genérico a 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-37-genérico </br> 5.3.0-19 genérico a 5.3.0-24-genérico </br> 4.15.0-1009-azure para 4.15.0-1037-azure </br> 4.18.0-1006-azure para 4.18.0-1025-azure </br> 5.0.0-1012-azure para 5.0.0-1025-azure </br> 5.3.0-1007-azure|
18.04 LTS | [9.30][9.30 UR] | 4.15.0-20-genérico a 4.15.0-66-genérico </br> 4.18.0-13-genérico a 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-32-genérico </br> 4.15.0-1009-azure para 4.15.0-1037-azure </br> 4.18.0-1006-azure para 4.18.0-1025-azure </br> 5.0.0-1012-azure para 5.0.0-1023-azure|
18.04 LTS | [9.29][9.29 UR] | 4.15.0-20-genérico a 4.15.0-62-genérico </br> 4.18.0-13-genérico a 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-27-genérico </br> 4.15.0-1009-azure para 4.15.0-1037-azure </br> 4.18.0-1006-azure para 4.18.0-1025-azure </br> 5.0.0-1012-azure para 5.0.0-1018-azure|

### <a name="debian-kernel-versions"></a>Versões de kernel debian


**Lançamento apoiado** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
Debian 7 | [9.29][9.29 UR], [9.30][9.30 UR], [9.31][9.31 UR], [9.32][9.32 UR]| 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.30][9.30 UR], [9.31][9.31 UR], [9.32][9.32 UR] | 3.16.0-4-amd64 a 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.11-amd64 |
Debian 8 | [9.29][9.29 UR] | 3.16.0-4-amd64 a 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.9-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 versões de kernel suportadas

**Libertar** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.28][9.28 UR] | SP1 3.12.49-11-padrão para 3.12.74-60.64.40-padrão</br></br> SP1 (LTSS) 3.12.74-60.64.45-default para 3.12.74-60.64.118-default</br></br> SP2 4.4.21-69-padrão para 4.4.120-92.70-padrão</br></br>SP2 (LTSS) 4.4.121-92.73-default para 4.4.121-92.117-padrão</br></br>SP3 4.4.73-5-default para 4.4.180-94.100-padrão</br></br>SP3 4.4.138-4.7-azure para 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-padrão para 4.12.14-95.29-padrão</br>SP4 4.12.14-6.3-azure para 4.12.14-6.23-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.27][9.27 UR] | SP1 3.12.49-11-padrão para 3.12.74-60.64.40-padrão</br></br> SP1 (LTSS) 3.12.74-60.64.45-default para 3.12.74-60.64.115-default</br></br> SP2 4.4.21-69-padrão para 4.4.120-92.70-padrão</br></br>SP2 (LTSS) 4.4.121-92.73-default para 4.4.121-92.114-padrão</br></br>SP3 4.4.73-5-default para 4.4.180-94.97-padrão</br></br>SP3 4.4.138-4.7-azure para 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-padrão para 4.12.14-95.19-padrão</br>SP4 4.12.14-6.3-azure para 4.12.14-6.15-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.26][9.26 UR] | SP1 3.12.49-11-padrão para 3.12.74-60.64.40-padrão</br></br> SP1 (LTSS) 3.12.74-60.64.45-default para 3.12.74-60.64.110-default</br></br> SP2 4.4.21-69-padrão para 4.4.120-92.70-padrão</br></br>SP2 (LTSS) 4.4.121-92.73-default para 4.4.121-92.109-padrão</br></br>SP3 4.4.73-5-default para 4.4.178-94.91-padrão</br></br>SP3 4.4.138-4.7-azure para 4.4.178-4.28-azure</br></br>SP4 4.12.14-94.41-padrão para 4.12.14-95.16-padrão</br>SP4 4.12.14-6.3-azure para 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.25][9.25 UR] | SP1 3.12.49-11-padrão para 3.12.74-60.64.40-padrão</br></br> SP1 (LTSS) 3.12.74-60.64.45-default para 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-padrão para 4.4.120-92.70-padrão</br></br>SP2 (LTSS) 4.4.121-92.73-default para 4.4.121-92.104-padrão</br></br>SP3 4.4.73-5-default para 4.4.176-94.88-padrão</br></br>SP3 4.4.138-4.7-azure para 4.4.176-4.25-azure</br></br>SP4 4.12.14-94.41-padrão para 4.12.14-95.13-padrão</br>SP4 4.12.14-6.3-azure para 4.12.14-6.9-azure |

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>SUSE Linux Enterprise Server 15 versões de kernel suportado

**Libertar** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 e 15 SP1 | [9.32](https://support.microsoft.com/help/4550047/) | Por padrão, todas as [ações SUSE 15 e 15 núcleos são suportados.](https://www.suse.com/support/kb/doc/?id=000019587) </br></br> 4.12.14-5.5-azure para 4.12.14-8.22-azure



## <a name="linux-file-systemsguest-storage"></a>Sistemas de ficheiros Linux/armazenamento de hóspedes

**Componente** | **Suportado**
--- | ---
Sistemas de ficheiros | ext3, ext4, XFS, BTRFS (condições aplicáveis por esta tabela)
Provisão para gestão lógica do volume (LVM)| Provisão grossa - Sim <br></br> Provisão fina - Não
Gestor de volume | - A LVM é apoiada.<br/> - /boot on LVM é suportado a partir de [Update Rollup 31](https://support.microsoft.com/help/4478871/) (versão 9.20 do serviço mobility) em diante. Não é suportado em versões anteriores do serviço de mobilidade.<br/> - Vários discos de SO não são suportados.
Dispositivos de armazenamento paravirtualizados | Os dispositivos exportados por controladores paravirtualizados não são suportados.
Dispositivos IO de bloco multi-fila | Não suportado.
Servidores físicos com o controlador de armazenamento HP CCISS | Não suportado.
Convenção de nomeação de pontos de dispositivo/montagem | O nome do dispositivo ou o nome do ponto de montagem devem ser únicos.<br/> Certifique-se de que não há dois dispositivos/pontos de montagem com nomes sensíveis a casos. Por exemplo, o nomeamento de dispositivos para o mesmo VM que o *dispositivo1* e *o Dispositivo1* não são suportados.
Diretórios | Se estiver a executar uma versão do serviço Mobility antes da versão 9.20 (lançada no [Update Rollup 31),](https://support.microsoft.com/help/4478871/)então estas restrições aplicam-se:<br/><br/> - Estes diretórios (se configurados como divisórias/sistemas de ficheiros separados) devem estar no mesmo disco DE no servidor de origem: /(raiz), /boot, /usr, /usr/local, /var, /etc.</br> - O diretório /boot deve estar numa partição de disco e não ser um volume LVM.<br/><br/> A partir da versão 9.20, estas restrições não se aplicam. 
Diretório de botas | - Os discos de arranque não devem estar no formato de partição GPT. Esta é uma limitação da arquitetura Azure. Os discos GPT são suportados como discos de dados.<br/><br/> Vários discos de arranque num VM não são suportados<br/><br/> - /arranque num volume LVM em mais de um disco não é suportado.<br/> - Uma máquina sem disco de arranque não pode ser replicada.
Requisitos de espaço gratuito| 2 GB na partição /raiz <br/><br/> 250 MB na pasta de instalação
XFSv5 | As funcionalidades XFSv5 nos sistemas de ficheiros XFS, como a checkum de metadados, são suportadas (versão de serviço de mobilidade 9.10 em diante).<br/> Utilize o utilitário xfs_info para verificar o superbloco XFS para a partição. Se `ftype` estiver definido para 1, então as funcionalidades XFSv5 estão a ser utilizadas.
BTRFS | O BTRFS é suportado a partir do [Update Rollup 34](https://support.microsoft.com/help/4490016) (versão 9.22 do serviço mobility) em diante. BTRFS não é suportado se:<br/><br/> - O subvolume do sistema de ficheiros BTRFS é alterado após a proteção.</br> - O sistema de ficheiros BTRFS está espalhado por vários discos.</br> - O sistema de ficheiros BTRFS suporta RAID.

## <a name="vmdisk-management"></a>Gestão de VM/Disco

**Ação** | **Detalhes**
--- | ---
Redimensione o disco em VM replicado | Suportado na fonte VM antes do failover, diretamente nas propriedades VM. Não há necessidade de desativar/reativar a replicação.<br/><br/> Se alterar o VM de origem após o failover, as alterações não são capturadas.<br/><br/> Se alterar o tamanho do disco no Azure VM após o failover, quando falha, a Recuperação do Site cria um novo VM com as atualizações.
Adicione o disco em VM replicado | Não suportado.<br/> Desative a replicação para o VM, adicione o disco e, em seguida, reative a replicação.

## <a name="network"></a>Rede

**Componente** | **Suportado**
--- | ---
Equipa NIC de rede de anfitriões | Suportado para VMware VMs. <br/><br/>Não suportado para a replicação da máquina física.
Rede de anfitriões VLAN | Sim.
Rede de anfitriões IPv4 | Sim.
Rede de anfitriões IPv6 | Não.
Rede de hóspedes/servidor NIC Teaming | Não.
Rede de hóspedes/servidores IPv4 | Sim.
Rede de hóspedes/servidores IPv6 | Não.
IP estático de rede de hóspedes/servidor (Windows) | Sim.
IP estático de rede de hóspedes/servidor (Linux) | Sim. <br/><br/>Os VMs estão configurados para usar o DHCP no failback.
Rede de hóspedes/servidores múltiplos NICs | Sim.


## <a name="azure-vm-network-after-failover"></a>Rede Azure VM (após falha)

**Componente** | **Suportado**
--- | ---
Azure ExpressRoute | Yes
ILB | Yes
ELB | Yes
Traffic Manager do Azure | Yes
Multi-NIC | Yes
Endereço IP reservado | Yes
IPv4 | Yes
Reter endereço IP de origem | Yes
Pontos finais de serviço de rede virtual Azure<br/> | Yes
Redes aceleradas | No

## <a name="storage"></a>Armazenamento
**Componente** | **Suportado**
--- | ---
Disco dinâmico | O disco de so deve ser um disco básico. <br/><br/>Os discos de dados podem ser discos dinâmicos
Configuração do disco docker | No
Anfitrião NFS | Sim para VMware<br/><br/> Não para servidores físicos
Host SAN (iSCSI/FC) | Yes
Anfitrião vSAN | Sim para VMware<br/><br/> N/A para servidores físicos
Hospedeiro multipata (MPIO) | Sim, testado com Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM para CLARiiON
Volumes Virtuais de Hospedo (VVols) | Sim para VMware<br/><br/> N/A para servidores físicos
VMDK de hóspedes/servidor | Yes
Disco de cluster compartilhado de hóspede/servidor | No
Disco encriptado de hóspede/servidor | No
NFS de hóspedes/servidores | No
Hóspede/servidor iSCSI | Para a Migração - Sim<br/>Para a recuperação de desastres - Não, o iSCSI falhará como um disco anexado ao VM
Hóspede/servidor SMB 3.0 | No
Hóspede/servidor RDM | Yes<br/><br/> N/A para servidores físicos
Disco de hóspedes/servidor > 1 TB | Sim, o disco deve ser maior que 1024 MB<br/><br/>Até 8.192 GB ao replicar discos geridos (versão 9.26 em diante)<br></br> Até 4.095 GB ao replicar-se em contas de armazenamento
Disco de hóspede/servidor com tamanho lógico lógico e 4k do setor físico | No
Disco de hóspedes/servidor com tamanho lógico lógico de 4K e 512 bytes do setor físico | No
Volume de hóspede/servidor com disco listrado >4 TB | Yes
Gestão lógica do volume (LVM)| Provisão grossa - Sim <br></br> Provisões finas - Não
Hóspede/servidor - Espaços de Armazenamento | No
Adicionar/remover o disco quente do hóspede/servidor | No
Guest/server - excluir o disco | Yes
Multipata de hóspedes/servidores (MPIO) | No
Divisórias GPT de hóspedes/servidor | Cinco divisórias são suportadas a partir do [Update Rollup 37](https://support.microsoft.com/help/4508614/) (versão 9.25 do serviço mobility) em diante. Anteriormente quatro foram apoiados.
ReFS | O Sistema de Ficheiros Resiliente é suportado com a versão 9.23 ou superior do serviço de mobilidade
Bota EFI/UEFI de hóspedes/servidor | - Suportado para o Windows Server 2012 ou mais tarde, SLES 12 SP4 e RHEL 8.0 com a versão 9.30 do agente de mobilidade<br/> - O tipo de arranque UEFI seguro não é suportado. [Saiba mais.](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2#on-premises-vs-azure-generation-2-vms)

## <a name="replication-channels"></a>Canais de replicação

|**Tipo de replicação**   |**Suportado**  |
|---------|---------|
|Transferências de dados descarregadas (ODX)    |       No  |
|Sementeira offline        |   No      |
| Azure Data Box | No

## <a name="azure-storage"></a>Storage do Azure

**Componente** | **Suportado**
--- | ---
Armazenamento localmente redundante | Yes
Armazenamento georredundante | Yes
Armazenamento georredundante com acesso de leitura | Yes
Armazenamento fresco | No
Armazenamento quente| No
Blobs de blocos | No
Encriptação em repouso (SSE)| Yes
Encriptação em repouso (CMK)| Sim (via módulo PowerShell Az 3.3.0 em diante)
Armazenamento Premium | Yes
Serviço de importação/exportação | No
Firewalls de armazenamento Azure para VNets | Sim.<br/> Configurado na conta de armazenamento/cache-alvo (utilizada para armazenar dados de replicação).
Contas de armazenamento v2 para fins gerais (camadas quentes e frescas) | Sim (Os custos de transação são substancialmente mais elevados para o V2 em comparação com v1)

## <a name="azure-compute"></a>Azure compute

**Funcionalidade** | **Suportado**
--- | ---
Conjuntos de disponibilidade | Yes
Zonas de disponibilidade | No
HUB | Yes
Managed disks | Yes

## <a name="azure-vm-requirements"></a>Requisitos da VM do Azure

Os VMs no local replicados ao Azure devem satisfazer os requisitos Azure VM resumidos nesta tabela. Quando a Recuperação do Site executa uma verificação prévia para a replicação, a verificação falhará se alguns dos requisitos não forem cumpridos.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operativo convidado | Verifique os [sistemas operativos suportados](#replicated-machines) para máquinas replicadas. | A verificação falha se não for apoiada.
Arquitetura do sistema operativo convidado | 64-bit. | A verificação falha se não for apoiada.
Tamanho do disco do sistema operativo | Até 2.048 GB. | A verificação falha se não for apoiada.
Contagem de discos do sistema operativo | 1 | A verificação falha se não for apoiada.
Contagem de discos de dados | 64 ou menos. | A verificação falha se não for apoiada.
Tamanho do disco de dados | Até 8.192 GB ao replicar o disco gerido (versão 9.26 em diante)<br></br>Até 4.095 GB ao replicar-se na conta de armazenamento| A verificação falha se não for apoiada.
Placas de rede | Vários adaptadores são suportados. |
VHD partilhado | Não suportado. | A verificação falha se não for apoiada.
Disco FC | Não suportado. | A verificação falha se não for apoiada.
BitLocker | Não suportado. | O BitLocker tem de ser desativado antes de ativar a replicação de uma máquina. |
o nome da VM | De 1 a 63 caracteres.<br/><br/> Limitado a letras, números e hífenes.<br/><br/> O nome da máquina deve começar e terminar com uma letra ou número. |  Atualize o valor nas propriedades da máquina na Recuperação do Local.

## <a name="resource-group-limits"></a>Limites de grupo de recursos

Para compreender o número de máquinas virtuais que podem ser protegidas sob um único grupo de recursos, consulte o artigo sobre [limites de subscrição e quotas](/azure/azure-resource-manager/management/azure-subscription-service-limits#resource-group-limits).

## <a name="churn-limits"></a>Limites de churn

A tabela seguinte fornece os limites do Azure Site Recovery.
- Estes limites são baseados nos nossos testes, mas não cobrem todas as combinações possíveis de I/O da aplicação.
- Os resultados reais podem variar consoante a combinação de E/S da sua aplicação.
- Para obter os melhores resultados, recomendamos vivamente que execute a [ferramenta Deployment Planner](site-recovery-deployment-planner.md)e realize testes extensivos de aplicações utilizando falhas de teste para obter a verdadeira imagem de desempenho da sua aplicação.

**Alvo de replicação** | **Tamanho médio de E/S do disco de origem** |**Média de alterações a dados do disco de origem** | **Total de dados de disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |    336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou superior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou superior |20 MB/s | 1684 GB por disco


**Alterações a dados de origem** | **Limite Máximo**
---|---
Alterações a dados de pico em todos os discos numa VM | 54 MB/s
Máximo de alterações a dados por dia suportadas por um Servidor de Processos | 2 TB

- Estes são números médios, que pressupõem uma sobreposição de 30 por cento de E/S.
- O Site Recovery é capaz de processar um débito superior com base no rácio de sobreposição, em tamanhos de escrita maiores e no comportamento real de E/S da carga de trabalho.
- Estes números pressupõem um atraso típico de aproximadamente cinco minutos. Ou seja, depois de os dados serem carregados, são processados e é criado um ponto de recuperação ao fim de cinco minutos.

## <a name="vault-tasks"></a>Tarefas de abóbada

**Ação** | **Suportado**
--- | ---
Mover cofre através de grupos de recursos | No
Mover o cofre dentro e através de subscrições | No
Mover armazenamento, rede, VMs Azure em grupos de recursos | No
Mover armazenamento, rede, VMs Azure dentro e em todas as subscrições. | No


## <a name="obtain-latest-components"></a>Obtenha componentes mais recentes

**Nome** | **Descrição** | **Detalhes**
--- | --- | ---
Servidor de configuração | Instalado no local.<br/> Coordena comunicações entre servidores VMware ou máquinas físicas no local e Azure. | - [Saiba mais sobre](vmware-physical-azure-config-process-server-overview.md) o servidor de configuração.<br/> - [Saiba mais sobre](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) o upgrade para a versão mais recente.<br/> - [Saiba como](vmware-azure-deploy-configuration-server.md) configurar o servidor de configuração.
Servidor de processo | Instalado por predefinição no servidor de configuração.<br/> Recebe dados de replicação, otimiza-os com caching, compressão e encriptação, e envia-os para o Azure.<br/> À medida que a sua implementação cresce, pode adicionar servidores de processo adicionais para lidar com volumes maiores de tráfego de replicação. | - [Saiba mais sobre](vmware-physical-azure-config-process-server-overview.md) o servidor de processo.<br/> - [Saiba mais sobre](vmware-azure-manage-process-server.md#upgrade-a-process-server) o upgrade para a versão mais recente.<br/> - [Saiba como](vmware-physical-large-deployment.md#set-up-a-process-server) configurar servidores de processos de escala.
Serviço de Mobilidade | Instalado em VMware VM ou servidores físicos que pretende replicar.<br/> Coordena a replicação entre servidores VMware/servidores físicos e Azure.| - [Conheça](vmware-physical-mobility-service-overview.md) o serviço de Mobilidade.<br/> - [Saiba mais sobre](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) o upgrade para a versão mais recente.<br/>



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
