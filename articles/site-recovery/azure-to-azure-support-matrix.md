---
title: Matriz de suporte para recuperação de desastres azure VM com recuperação do site Azure
description: Resume o apoio à recuperação de desastres dos VMs Azure para uma região secundária com a Recuperação do Sítio Azure.
ms.topic: article
ms.date: 01/10/2020
ms.author: raynew
ms.openlocfilehash: f61f32ddc0a1cc6575907bc72522228b77552947
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673794"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Matriz de apoio à recuperação de desastres do Azure VM entre as regiões de Azure

Este artigo resume o apoio e os pré-requisitos para a recuperação de desastres de VMs Azure de uma região de Azure para outra, utilizando o serviço [azure de recuperação](site-recovery-overview.md) do site.


## <a name="deployment-method-support"></a>Suporte ao método de implantação

**Implementação** |  **Suporte**
--- | ---
**Portal do Azure** | Suportado.
**PowerShell** | Suportado. [Mais informações](azure-to-azure-powershell.md)
**API REST** | Suportado.
**CLI** | Atualmente, não é suportado


## <a name="resource-support"></a>Suporte para recursos

**Ação de recursos** | **Detalhes**
--- | ---
**Mover cofres através de grupos de recursos** | Não suportado
**Mover recursos de computação/armazenamento/rede através de grupos de recursos** | Não suportado.<br/><br/> Se mover um VM ou componentes associados, como o armazenamento/rede após a replicação do VM, é necessário desativar e, em seguida, voltar a ativar a replicação para o VM.
**Replicar VMs Azure de uma subscrição para outra para recuperação de desastres** | Apoiado dentro do mesmo inquilino azure Ative Directory.
**Migram VMs através de regiões dentro de aglomerados geográficos apoiados (dentro e em todas as assinaturas)** | Apoiado dentro do mesmo inquilino azure Ative Directory.
**VMs migrados dentro da mesma região** | Não suportado.

## <a name="region-support"></a>Suporte de região

Pode replicar e recuperar VMs entre duas regiões dentro do mesmo aglomerado geográfico. Os clusters geográficos são definidos mantendo em mente a latência e a soberania dos dados.


**Aglomerado geográfico** | **Regiões de Azure**
-- | --
América | Canadá Leste, Canadá Central, Centro-Sul dos EUA, Centro-Oeste dos EUA, Leste dos EUA, Leste dos EUA 2, Oeste dos EUA, Oeste DOS 2, Centro dos EUA
Europa | Reino Unido Oeste, Reino Unido Sul, Norte da Europa, Europa Ocidental, África do Sul Oeste, África do Sul Norte, Noruega Leste, Noruega Oeste
Ásia | Índia do Sul, Índia Central, Índia Ocidental, Sudeste Asiático, Ásia Oriental, Japão Leste, Japão Oeste, Coreia Central, Coreia do Sul
Austrália    | Austrália Leste, Austrália Sudeste, Austrália Central, Austrália Central 2
Azure Government    | US GOV Virginia, US GOV Iowa, US GOV Arizona, US GOV Texas, US DOD East, US DOD Central
Alemanha    | Alemanha Central, Alemanha Nordeste
China | China Leste, China Norte, China Norte2, China East2
Regiões restritas reservadas para recuperação de desastres no país |Alemanha Norte reservada para alemanha West Central, Suíça West reservada para suíça Norte, França Sul reservada para France Central, Eau Central restrita para clientes uAE Norte

>[!NOTE]
>
> - Para o **Brasil Sul,** você pode replicar e falhar sobre estas regiões: Centro-Sul dos EUA, Oeste Dos EUA, Leste dos EUA, US 2, Oeste dos EUA, Oeste DOS 2 e Norte Dos EUA.
> - O Brasil Sul só pode ser usado como uma região de origem a partir da qual os VMs podem replicar usando a Recuperação do Site. Não pode funcionar como uma região alvo. Isto deve-se a problemas de latência devido a distâncias geográficas. Note-se que se você falhar do Brasil Sul como uma região de origem para um alvo, o regresso ao Brasil Sul da região alvo é apoiado.
> - Pode trabalhar em regiões para as quais tem acesso adequado.
> - Se a região em que pretende criar um cofre não aparecer, certifique-se de que a sua subscrição tem acesso para criar recursos naquela região.
> - Se não consegue ver uma região dentro de um cluster geográfico quando ativa a replicação, certifique-se de que a sua subscrição tem permissões para criar VMs nessa região.



## <a name="cache-storage"></a>Armazenamento em cache

Esta tabela resume o suporte para a conta de armazenamento de cache utilizada pela Recuperação do Local durante a replicação.

**Definição** | **Suporte** | **Detalhes**
--- | --- | ---
Contas de armazenamento V2 de propósito geral (nível quente e fresco) | Suportado | O uso do GPv2 não é recomendado porque os custos de transação para V2 são substancialmente superiores às contas de armazenamento V1.
Armazenamento Premium | Não suportado | As contas de armazenamento padrão são usadas para armazenamento de cache, para ajudar a otimizar os custos.
Firewalls de armazenamento azure para redes virtuais  | Suportado | Se estiver a utilizar uma conta de armazenamento de cache ativada por firewall ou uma conta de armazenamento de alvos, certifique-se de [que "permite serviços microsoft fidedignos".](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)<br></br>Além disso, certifique-se de que permite o acesso a pelo menos uma sub-rede de fonte Vnet.


## <a name="replicated-machine-operating-systems"></a>Sistemas operativos de máquinas replicados

A Recuperação do Site suporta a replicação de VMs Azure que executam os sistemas operativos listados nesta secção. Por favor, note que se uma máquina de replicação já for posteriormente atualizada (ou desclassificada) para um núcleo principal diferente, precisa de desativar a replicação e de reativar a replicação após a atualização.

### <a name="windows"></a>Windows


**Sistema Operativo** | **Detalhes**
--- | ---
Windows Server 2019 | Suportado para Server Core, Server com Experiência de Ambiente de Trabalho.
Windows Server 2016  | Núcleo de servidor suportado, servidor com experiência de ambiente de trabalho.
Windows Server 2012 R2 | Suportado.
Windows Server 2012 | Suportado.
Windows Server 2008 R2 com SP1/SP2 | Suportado.<br/><br/> A partir da versão [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) da extensão do serviço mobility para VMs Azure, é necessário instalar uma atualização de pilhas de serviços Windows [(SSU)](https://support.microsoft.com/help/4490628) e [atualização SHA-2](https://support.microsoft.com/help/4474419) em máquinas que executam o Windows Server 2008 R2 SP1/SP2.  A SHA-1 não é suportada a partir de setembro de 2019 e se a assinatura de código SHA-2 não estiver ativada, a extensão do agente não instala/atualização como esperado. Saiba mais sobre a [atualização e requisitos sha-2.](https://aka.ms/SHA-2KB)
Windows 10 (x64) | Suportado.
Windows 8.1 (x64) | Suportado.
Windows 8 (x64) | Suportado.
Windows 7 (x64) com SP1 em diante | A partir da versão [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) da extensão do serviço mobility para VMs Azure, é necessário instalar uma atualização de pilha de serviços Windows [(SSU)](https://support.microsoft.com/help/4490628) e [atualização SHA-2](https://support.microsoft.com/help/4474419) em máquinas que executam o Windows 7 com SP1.  A SHA-1 não é suportada a partir de setembro de 2019, e se a assinatura de código SHA-2 não estiver ativada, a extensão do agente não instala/atualização como esperado.. Saiba mais sobre a [atualização e requisitos sha-2.](https://aka.ms/SHA-2KB)



#### <a name="linux"></a>Linux

**Sistema Operativo** | **Detalhes**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0,](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)8.1
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 8.0, 8.1
Ubuntu 14.04 LTS Server | [Versões de kernel suportadas](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16.04 LTS Server | [Versão de kernel suportado](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Os servidores Ubuntu que usam a autenticação e o insessão baseados em palavras-passe, e o pacote cloud-init para configurar VMs em nuvem, podem ter um sinal baseado em palavra-passe em falha (dependendo da configuração cloudinit). O sinal baseado em palavra-passe pode ser reativado na máquina virtual, repondo a palavra-passe do menu de definições de > de resolução de problemas de suporte > (do vM falhado no portal Azure.
Ubuntu 18.04 LTS Server | [Versão de kernel suportado](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debiano 7 | [Versões de kernel suportadas](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debiano 8 | [Versões de kernel suportadas](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [(Versões de kernel suportadas)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15 e 15 SP1. [(Versões de kernel suportadas)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> A atualização das máquinas de replicação do SP3 para o SP4 não é suportada. Se uma máquina replicada tiver sido atualizada, é necessário desativar a replicação e reativar a replicação após a atualização.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> Executar o kernel compatível com o Chapéu Vermelho ou Unbreakable Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5)


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versões de kernel Ubuntu suportadas para máquinas virtuais Azure

**Libertar** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
14.04 LTS | 9.32| 3.13.0-24-genérico a 3.13.0-170-genérico,<br/>3.16.0-25-genérico a 3.16.0-77-genérico,<br/>3.19.0-18-genérico a 3.19.0-80 genéricos,<br/>4.2.0-18-genérico a 4.2.0-42-genérico,<br/>4.4.0-21-genérico a 4.4.0-148-genérico,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
14.04 LTS | 9.31 | 3.13.0-24-genérico a 3.13.0-170-genérico,<br/>3.16.0-25-genérico a 3.16.0-77-genérico,<br/>3.19.0-18-genérico a 3.19.0-80 genéricos,<br/>4.2.0-18-genérico a 4.2.0-42-genérico,<br/>4.4.0-21-genérico a 4.4.0-148-genérico,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
14.04 LTS | 9.30 | 3.13.0-24-genérico a 3.13.0-170-genérico,<br/>3.16.0-25-genérico a 3.16.0-77-genérico,<br/>3.19.0-18-genérico a 3.19.0-80 genéricos,<br/>4.2.0-18-genérico a 4.2.0-42-genérico,<br/>4.4.0-21-genérico a 4.4.0-148-genérico,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
14.04 LTS | 9.29 | 3.13.0-24-genérico a 3.13.0-170-genérico,<br/>3.16.0-25-genérico a 3.16.0-77-genérico,<br/>3.19.0-18-genérico a 3.19.0-80 genéricos,<br/>4.2.0-18-genérico a 4.2.0-42-genérico,<br/>4.4.0-21-genérico a 4.4.0-148-genérico,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
|||
16.04 LTS | 9.32 | 4.4.0-21-genérico a 4.4.0-171-genérico,<br/>4.8.0-34-genérico a 4.8.0-58-genérico,<br/>4.10.0-14-genérico a 4.10.0-42-genérico,<br/>4.11.0-13-genérico a 4.11.0-14-genérico,<br/>4.13.0-16-genérico a 4.13.0-45-genérico,<br/>4.15.0-13-genérico a 4.15.0-74-genérico<br/>4.11.0-1009-azure a 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure a 4.15.0-1066-azure|
16.04 LTS | 9.31 | 4.4.0-21-genérico a 4.4.0-170-genérico,<br/>4.8.0-34-genérico a 4.8.0-58-genérico,<br/>4.10.0-14-genérico a 4.10.0-42-genérico,<br/>4.11.0-13-genérico a 4.11.0-14-genérico,<br/>4.13.0-16-genérico a 4.13.0-45-genérico,<br/>4.15.0-13-genérico a 4.15.0-72-genérico<br/>4.11.0-1009-azure a 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure a 4.15.0-1063-azure|
16.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.4.0-21-genérico a 4.4.0-166-genérico,<br/>4.8.0-34-genérico a 4.8.0-58-genérico,<br/>4.10.0-14-genérico a 4.10.0-42-genérico,<br/>4.11.0-13-genérico a 4.11.0-14-genérico,<br/>4.13.0-16-genérico a 4.13.0-45-genérico,<br/>4.15.0-13-genérico a 4.15.0-66-genérico<br/>4.11.0-1009-azure a 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure a 4.15.0-1061-azure|
16.04 LTS | 9.29 | 4.4.0-21-genérico a 4.4.0-164-genérico,<br/>4.8.0-34-genérico a 4.8.0-58-genérico,<br/>4.10.0-14-genérico a 4.10.0-42-genérico,<br/>4.11.0-13-genérico a 4.11.0-14-genérico,<br/>4.13.0-16-genérico a 4.13.0-45-genérico,<br/>4.15.0-13-genérico a 4.15.0-64-genérico<br/>4.11.0-1009-azure a 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure a 4.15.0-1059-azure|
|||
18.04 LTS | 9.32| 4.15.0-20-genérico a 4.15.0-74-genérico </br> 4.18.0-13-genérico a 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-37-genérico </br> 5.3.0-19-genérico a 5.3.0-24-genérico </br> 4.15.0-1009-azure a 4.15.0-1037-azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-azure a 5.0.0-1028-azure </br> 5.3.0-1007-azure a 5.3.0-1009-azure|
18.04 LTS | 9.31| 4.15.0-20-genérico a 4.15.0-72-genérico </br> 4.18.0-13-genérico a 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-37-genérico </br> 5.3.0-19-genérico a 5.3.0-24-genérico </br> 4.15.0-1009-azure a 4.15.0-1037-azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-azure a 5.0.0-1025-azure </br> 5.3.0-1007-azure|
18.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.15.0-20-genérico a 4.15.0-66-genérico </br> 4.18.0-13-genérico a 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-32-genérico </br> 4.15.0-1009-azure a 4.15.0-1037-azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-azure a 5.0.0-1023-azure|
18.04 LTS | [9.29](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) | 4.15.0-20-genérico a 4.15.0-64-genérico </br> 4.18.0-13-genérico a 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-29-genérico </br> 4.15.0-1009-azure a 4.15.0-1037-azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-azure a 5.0.0-1020-azure|


#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Versões de kernel Debian suportadas para máquinas virtuais Azure

**Libertar** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
Debiano 7 | 9.28,9.29,9.30,9.31 | 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debiano 8 | 9.29,9.30,9.31 | 3.16.0-4-amd64 a 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.11-amd64 |
Debiano 8 | 9.28 | 3.16.0-4-amd64 a 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.9-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>SUSE Linux Enterprise Server 12 versões de kernel para máquinas virtuais Azure

**Libertar** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.32 | Todos os [núcleos SUSE 12 SP1,SP2,SP3,SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) são suportados.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.34-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.31 | Todos os [núcleos SUSE 12 SP1,SP2,SP3,SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) são suportados.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.30 | Todos os [núcleos SUSE 12 SP1,SP2,SP3,SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) são suportados.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.29 | Todos os [núcleos SUSE 12 SP1,SP2,SP3,SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) são suportados.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.23-azure  |

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>SUSE Linux Enterprise Server 15 versões de kernel para máquinas virtuais Azure

**Libertar** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 e 15 SP1 | 9.32 | Todos os [núcleos SUSE 15 e 15 são](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_15) suportados.</br></br> 4.12.14-5.5-azure a 4.12.14-8.22-azure |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Máquinas replicadas - sistema de ficheiros Linux/armazenamento de hóspedes

* Sistemas de ficheiros: ext3, ext4, XFS, BTRFS
* Gestor de volume: LVM2
* Software multipath: Device Mapper


## <a name="replicated-machines---compute-settings"></a>Máquinas replicadas - definições de cálculo

**Definição** | **Suporte** | **Detalhes**
--- | --- | ---
Tamanho | Qualquer tamanho De VM Azure com pelo menos 2 núcleos CPU e RAM de 1-GB | Verifique o [tamanho da máquina virtual Azure.](../virtual-machines/windows/sizes.md)
Conjuntos de disponibilidade | Suportado | Se ativar a replicação de um VM Azure com as opções predefinidas, um conjunto de disponibilidade é criado automaticamente, com base nas definições da região de origem. Pode modificar estas definições.
Zonas de disponibilidade | Suportado |
Benefício de Utilização Híbrida (HUB) | Suportado | Se a VM de origem tiver uma licença HUB ativada, um teste failover ou falhou sobre vM também usa a licença HUB.
Conjuntos de dimensionamento de máquinas virtuais | Não suportado |
Imagens da galeria Azure - Microsoft publicada | Suportado | Suportado se o VM funcionar num sistema operativo suportado.
Imagens da Galeria Azure - Terceiro publicado | Suportado | Suportado se o VM funcionar num sistema operativo suportado.
Imagens personalizadas - Terceiro publicado | Suportado | Suportado se o VM funcionar num sistema operativo suportado.
VMs migraram usando a recuperação do site | Suportado | Se um VMware VM ou uma máquina física foi migrado para O T.A. usando a Recuperação do Site, é necessário desinstalar a versão mais antiga do serviço mobility que funciona na máquina e reiniciar a máquina antes de a replicar para outra região do Azure.
Políticas rbac | Não suportado | As políticas de controlo de acesso (RBAC) baseadas em funções não são replicadas para o VM failover na região-alvo.
Extensões | Não suportado | As extensões não são replicadas ao VM failover na região-alvo. Tem de ser instalado manualmente após a falha.

## <a name="replicated-machines---disk-actions"></a>Máquinas replicadas - ações de disco

**Ação** | **Detalhes**
-- | ---
Redimensionar o disco em VM replicado | Suportado na fonte VM antes da falha. Não é necessário desativar/reativar a replicação.<br/><br/> Se alterar a fonte de VM após a falha, as alterações não são capturadas.<br/><br/> Se alterar o tamanho do disco no VM Azure após a falha, as alterações não são captadas pela Recovery do Site, e o failback será para o tamanho original do VM.
Adicione um disco a um VM replicado | Suportado

## <a name="replicated-machines---storage"></a>Máquinas replicadas - armazenamento

Esta tabela resumiu o suporte para o disco Do SO Azure VM, disco de dados e disco temporário.

- É importante observar os limites e alvos do disco VM para os VMs [linux](../virtual-machines/linux/disk-scalability-targets.md) e [Windows](../virtual-machines/windows/disk-scalability-targets.md) para evitar quaisquer problemas de desempenho.
- Se implementar com as definições predefinidas, a Recuperação do Site cria automaticamente discos e contas de armazenamento com base nas definições de origem.
- Se personalizar, certifique-se de seguir as orientações.

**Componente** | **Suporte** | **Detalhes**
--- | --- | ---
Tamanho máximo do disco osso | 2048 GB | [Saiba mais](../virtual-machines/windows/managed-disks-overview.md) sobre discos VM.
Disco temporário | Não suportado | O disco temporário está sempre excluído da replicação.<br/><br/> Não guarde dados persistentes no disco temporário. [Saiba mais](../virtual-machines/windows/managed-disks-overview.md).
Tamanho máximo do disco de dados | 8192 GB para discos geridos<br></br>4095 GB para discos não geridos|
Tamanho mínimo do disco de dados | Sem restrições para discos não geridos. 2 GB para discos geridos |
Número máximo do disco de dados | Até 64, de acordo com o apoio a um tamanho de VM Azure específico | [Saiba mais](../virtual-machines/windows/sizes.md) sobre tamanhos vm.
Taxa de alteração do disco de dados | Máximo de 10 MBps por disco para armazenamento premium. Máximo de 2 MBps por disco para armazenamento Standard. | Se a taxa média de variação de dados no disco for continuamente superior à máxima, a replicação não alcança.<br/><br/>  No entanto, se o máximo for ultrapassado esporadicamente, a replicação pode recuperar, mas pode ver pontos de recuperação ligeiramente atrasados.
Disco de dados - conta de armazenamento padrão | Suportado |
Disco de dados - conta de armazenamento premium | Suportado | Se um VM tiver discos distribuídos por contas de armazenamento premium e standard, pode selecionar uma conta de armazenamento de destino diferente para cada disco, para garantir que tem a mesma configuração de armazenamento na região alvo.
Disco gerido - padrão | Apoiado nas regiões de Azure em que a Recuperação do Sítio Azure é apoiada. |
Disco gerido - premium | Apoiado nas regiões de Azure em que a Recuperação do Sítio Azure é apoiada. |
SSD Standard | Suportado |
Redundância | LRS e GRS são apoiados.<br/><br/> O ZRS não é apoiado.
Armazenamento fresco e quente | Não suportado | Os discos VM não são suportados em armazenamento fresco e quente
Espaços de Armazenamento | Suportado |
Encriptação em repouso (SSE) | Suportado | SSE é a definição padrão nas contas de armazenamento.
Encriptação em repouso (CMK) | Suportado | Tanto as teclas Software como HSM são suportadas para discos geridos
Encriptação de disco azure (ADE) para Windows OS | Suportado para VMs com discos geridos. | Não são suportados vMs que utilizem discos não geridos. <br/><br/> As chaves protegidas pelo HSM não são suportadas. |
Encriptação de disco azure (ADE) para Linux OS | Suportado para VMs com discos geridos. | Não são suportados vMs que utilizem discos não geridos. <br/><br/> As chaves protegidas pelo HSM não são suportadas. |
Hot add    | Suportado | Permitir a replicação de um disco de dados que adicione a um VM Azure replicado é suportado para VMs que utilizam discos geridos.
Disco de remoção a quente    | Não suportado | Se remover o disco de dados no VM, tem de desativar a replicação e ativar novamente a replicação para o VM.
Excluir o disco | Apoio, apoio. Tem de usar o [PowerShell](azure-to-azure-exclude-disks.md) para configurar. |    Os discos temporários são excluídos por defeito.
Direto de Espaços de Armazenamento  | Apoiado para pontos de recuperação consistentes de acidentes. Não são apoiados pontos de recuperação consistentes de aplicação. |
Servidor de ficheiros scale-out  | Apoiado para pontos de recuperação consistentes de acidentes. Não são apoiados pontos de recuperação consistentes de aplicação. |
DRBD | Os discos que fazem parte de uma configuração DRBD não são suportados. |
LRS | Suportado |
GRS | Suportado |
RA-GRS | Suportado |
ZRS | Não suportado |
Armazenamento fresco e quente | Não suportado | Os discos de máquinas virtuais não são suportados em armazenamento fresco e quente
Firewalls de armazenamento azure para redes virtuais  | Suportado | Se restringir o acesso virtual à rede a contas de armazenamento, ative [permitir serviços microsoft fidedignos](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
Contas de armazenamento V2 de propósito geral (nível quente e fresco) | Suportado | Os custos de transação aumentam substancialmente em comparação com as contas de armazenamento V1 de finalidade geral
Geração 2 (bota UEFI) | Suportado

>[!IMPORTANT]
> Para evitar problemas de desempenho, certifique-se de que segue os alvos de escalabilidade e desempenho do disco VM para Os VMs [linux](../virtual-machines/linux/disk-scalability-targets.md) ou [Windows.](../virtual-machines/windows/disk-scalability-targets.md) Se utilizar as definições predefinidas, a Recuperação do Site cria os discos e contas de armazenamento necessárias, com base na configuração de origem. Se personalizar e selecionar as suas próprias definições, siga os alvos de escalabilidade e desempenho do disco para os seus VMs de origem.

## <a name="limits-and-data-change-rates"></a>Limites e taxas de alteração de dados

A tabela que se segue resume os limites de recuperação do site.

- Estes limites são baseados nos nossos testes, mas obviamente não cobrem todas as combinações possíveis de Aplicação Em/S.
- Os resultados reais podem variar em função da mistura de I/O da aplicação.
- Existem dois limites a ter em conta, por estoque de dados de disco e por cada vez que os dados das máquinas virtuais.

**Alvo de armazenamento** | **Disco de origem média I/O** |**Média de alterações a dados do disco de origem** | **Total de dados de disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |    336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou superior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou superior |20 MB/s | 1684 GB por disco

## <a name="replicated-machines---networking"></a>Máquinas replicadas - networking
**Definição** | **Suporte** | **Detalhes**
--- | --- | ---
NIC | Número máximo suportado para um tamanho de VM Azure específico | Os NICs são criados quando o VM é criado durante a failover.<br/><br/> O número de NICs no VM failover depende do número de NICs na fonte VM quando a replicação foi ativada. Se adicionar ou remover um NIC após a replicação, não afeta o número de NICs no VM replicado após a falha. Note também que a ordem dos NICs após a falha não é garantida como a ordem original.
Balanceador de Carga de Externo | Suportado | Associe o equilibrador de carga reconfigurado utilizando um script de Automação Azure num plano de recuperação.
Equilibrador de Carga Interna | Suportado | Associe o equilibrador de carga reconfigurado utilizando um script de Automação Azure num plano de recuperação.
Endereço IP público | Suportado | Associe um endereço IP público existente com o NIC. Ou criar um endereço IP público e associá-lo ao NIC usando um script de Automação Azure num plano de recuperação.
NSG em NIC | Suportado | Associe o NSG ao NIC utilizando um script de Automação Azure num plano de recuperação.
NSG na sub-rede | Suportado | Associe o NSG à subnet utilizando um script de Automação Azure num plano de recuperação.
Endereço IP reservado (estático) | Suportado | Se o NIC na fonte VM tiver um endereço IP estático, e a sub-rede alvo tiver o mesmo endereço IP disponível, é atribuído ao VM falhado.<br/><br/> Se a sub-rede alvo não tiver o mesmo endereço IP disponível, um dos endereços IP disponíveis na subnet está reservado para o VM.<br/><br/> Também pode especificar um endereço IP fixo e uma sub-rede em **itens replicados Definições** > De**definições** > **Compute e** > **Network Network Network interfaces**.
Endereço IP dinâmico | Suportado | Se o NIC na fonte tiver um endereço IP dinâmico, o NIC no VM falhado também é dinâmico por padrão.<br/><br/> Pode modificá-lo para um endereço IP fixo, se necessário.
Vários endereços IP | Não suportado | Quando falha um VM que tem um NIC com vários endereços IP, apenas o endereço IP primário do NIC na região de origem é mantido. Para atribuir vários endereços IP, pode adicionar VMs a um plano de [recuperação](recovery-plan-overview.md) e anexar um script para atribuir endereços IP adicionais ao plano, ou pode fazer a alteração manualmente ou com um script após a falha.
Gestor de Tráfego     | Suportado | Pode configurar regularmente o Traffic Manager para que o tráfego seja encaminhado para o ponto final da região de origem e para o ponto final na região alvo em caso de falha.
DNS do Azure | Suportado |
DNS Personalizado    | Suportado |
Procuração não autenticada | Suportado | [Mais informações](site-recovery-azure-to-azure-networking-guidance.md)
Procuração Autenticada | Não suportado | Se o VM estiver a utilizar um proxy autenticado para a conectividade de saída, não pode ser replicado utilizando a Recuperação do Site Azure.
VPN ligação site-to-site para as instalações<br/><br/>(com ou sem ExpressRoute)| Suportado | Certifique-se de que os UDRs e NSGs estão configurados de modo a que o tráfego de recuperação do site não seja encaminhado para as instalações. [Mais informações](site-recovery-azure-to-azure-networking-guidance.md)
Ligação VNET a VNET    | Suportado | [Mais informações](site-recovery-azure-to-azure-networking-guidance.md)
Pontos Finais de Serviço de Rede Virtual | Suportado | Se estiver a restringir o acesso à rede virtual às contas de armazenamento, certifique-se de que os serviços confiáveis da Microsoft têm acesso à conta de armazenamento.
Redes aceleradas | Suportado | A ligação acelerada deve ser ativada no VM de origem. [Saiba mais](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>Passos seguintes
- Leia [orientação de networking](site-recovery-azure-to-azure-networking-guidance.md) para replicar VMs Azure.
- Implementar a recuperação de desastres [replicando VMs Azure](site-recovery-azure-to-azure.md).
