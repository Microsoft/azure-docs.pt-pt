---
title: Matriz de suporte para recuperação de desastre de VMs do Azure entre regiões do Azure com Azure Site Recovery | Microsoft Docs
description: Resume os pré-requisitos e o suporte para a recuperação de desastre de VMs do Azure de uma região para outra com Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 10/26/2019
ms.author: raynew
ms.openlocfilehash: 726b7ebd21dadf0e179f2752a9783d63492cb4c3
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622458"
---
# <a name="support-matrix-for-replicating-azure-vms-from-one-region-to-another"></a>Matriz de suporte para replicar VMs do Azure de uma região para outra

Este artigo resume o suporte e os pré-requisitos para a recuperação de desastre de VMs do Azure de uma região do Azure para outra, usando o serviço de [Azure site Recovery](site-recovery-overview.md) .


## <a name="deployment-method-support"></a>Suporte ao método de implantação

**Implementação** |  **Suporte**
--- | ---
**Portal do Azure** | Suportado.
**PowerShell** | Suportado. [Saber mais](azure-to-azure-powershell.md)
**API REST** | Suportado.
**CLI** | Sem suporte no momento


## <a name="resource-support"></a>Suporte para recursos

**Ação de recurso** | **Detalhes**
--- | --- 
**Mover cofres entre grupos de recursos** | Não suportado
**Mover recursos de computação/armazenamento/rede entre grupos de recursos** | Não suportado.<br/><br/> Se você mover uma VM ou componentes associados, como armazenamento/rede, após a replicação da VM, será necessário desabilitar e reabilitar a replicação para a VM.
**Replicar VMs do Azure de uma assinatura para outra para recuperação de desastre** | Com suporte no mesmo locatário Azure Active Directory.
**Migrar VMs entre regiões dentro de clusters geográficos com suporte (dentro e entre assinaturas)** | Com suporte no mesmo locatário Azure Active Directory.
**Migrar VMs na mesma região** | Não suportado.

## <a name="region-support"></a>Suporte de região

Você pode replicar e recuperar VMs entre duas regiões no mesmo cluster geográfico. Os clusters geográficos são definidos mantendo a latência de dados e a soberania em mente.


**Cluster geográfico** | **Regiões do Azure**
-- | --
Estados | Leste do Canadá, centro-Canadá, Sul EUA Central, Oeste EUA Central, leste dos EUA, leste dos EUA 2, oeste dos EUA, oeste dos EUA 2, EUA Central, norte EUA Central
Europa | Oeste do Reino Unido, Sul do Reino Unido, Europa Setentrional, Europa Ocidental, França central, sul da França, oeste da África do Sul, norte da África do Sul, leste da Noruega, oeste da Noruega
Ásia | Sul da Índia, Índia central, Índia ocidental, Sudeste Asiático, Ásia Oriental, leste do Japão, oeste do Japão, Coreia central, sul da Coreia, EAU Central, Norte dos EAU
Austrália   | Leste da Austrália, sudeste da Austrália, Austrália Central, Austrália Central 2
Azure Government    | GOV-Virgínia, EUA GOV Iowa, US GOV-Arizona, US GOV Texas, US DOD leste, US DOD central 
Alemanha | Centro da Alemanha, nordeste da Alemanha
China | Leste da China, Norte da China, China North2, China 2
Regiões restritas reservadas para recuperação de desastres no país |Norte da Alemanha reservado para Centro-oeste da Alemanha, Oeste da Suíça reservado para Norte da Suíça, o sul da França reservado para clientes da França central 

>[!NOTE]
>
> - Para o **sul do Brasil**, você pode replicar e fazer failover para essas regiões: Sul EUA Central, Oeste EUA Central, leste dos EUA, leste dos EUA 2, oeste dos EUA, oeste dos EUA 2 e norte EUA Central.
> - O sul do Brasil só pode ser usado como uma região de origem da qual as VMs podem replicar usando Site Recovery. Ele não pode atuar como uma região de destino. Isso ocorre devido a problemas de latência devido a distâncias geográficas. Observe que se você fizer failover do Sul do Brasil como uma região de origem para um destino, o failback para o sul do Brasil da região de destino terá suporte.
> - Você pode trabalhar em regiões para as quais tem acesso apropriado.
> - Se a região na qual você deseja criar um cofre não for mostrada, verifique se sua assinatura tem acesso para criar recursos nessa região.
> - Se você não conseguir ver uma região em um cluster geográfico ao habilitar a replicação, verifique se sua assinatura tem permissões para criar VMs nessa região.



## <a name="cache-storage"></a>Armazenamento em cache

Esta tabela resume o suporte para a conta de armazenamento de cache usada pelo Site Recovery durante a replicação.

**Definição** | **Suporte** | **Detalhes**
--- | --- | ---
Contas de armazenamento v2 de uso geral (camada quente e fria) | Suportado | O uso de GPv2 não é recomendado porque os custos de transação para v2 são consideravelmente maiores que as contas de armazenamento v1.
Armazenamento Premium | Não suportado | As contas de armazenamento standard são usadas para o armazenamento em cache, para ajudar a otimizar os custos.
Firewalls de armazenamento do Azure para redes virtuais  | Suportado | Se você estiver usando a conta de armazenamento de cache habilitada para firewall ou a conta de armazenamento de destino, verifique se você [' permitir serviços confiáveis da Microsoft '](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).


## <a name="replicated-machine-operating-systems"></a>Sistemas operacionais de máquina replicados

Site Recovery dá suporte à replicação de VMs do Azure que executam os sistemas operacionais listados nesta seção.

### <a name="windows"></a>Windows


**Sistema operativo** | **Detalhes**
--- | ---
Windows Server 2019 | Com suporte para Server Core, servidor com experiência desktop.
Windows Server 2016  | Server Core com suporte, servidor com experiência desktop.
Windows Server 2012 R2 | Suportado.
Windows Server 2012 | Suportado.
Windows Server 2008 R2 com SP1/SP2 | Suportado.<br/><br/> Da versão 9.30. x. x (liberação esperada a partir de novembro de 2019) da extensão do serviço de mobilidade para VMs do Azure, você precisa instalar uma atualização de [Ssu (atualização da pilha de manutenção](https://support.microsoft.com/help/4490628) do Windows) e [SHA-2](https://support.microsoft.com/help/4474419) em computadores que executam o Windows Server 2008 R2 SP1/SP2.  O SHA-1 não tem suporte de setembro de 2019 e, se a assinatura de código SHA-2 não estiver habilitada, a extensão do agente não será instalada/atualizada conforme o esperado. Obtenha mais informações sobre a [atualização e os requisitos do SHA-2](https://aka.ms/SHA-2KB).
Windows Server 2008 com SP2 | Da versão 9.30. x. x (liberação esperada a partir de novembro de 2019) da extensão do serviço de mobilidade para VMs do Azure, você precisa instalar uma atualização de [Ssu (atualização da pilha de manutenção](https://support.microsoft.com/help/4493730) do Windows) e [SHA-2](https://support.microsoft.com/help/4474419) em computadores que executam o Windows Server 2008 com SP2.  O SHA-1 não tem suporte de setembro de 2019 e, se a assinatura de código SHA-2 não estiver habilitada, a extensão do agente não será instalada/atualizada conforme o esperado. Obtenha mais informações sobre a [atualização e os requisitos do SHA-2](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Suportado.
Windows 8.1 (x64) | Suportado.
Windows 8 (x64) | Suportado.
Windows 7 (x64) com SP1 em diante | Da versão 9.30. x. x (liberação esperada a partir de novembro de 2019) da extensão do serviço de mobilidade para VMs do Azure, você precisa instalar uma atualização de [Ssu (atualização da pilha de manutenção](https://support.microsoft.com/help/4490628) do Windows) e [SHA-2](https://support.microsoft.com/help/4474419) em computadores que executam o Windows 7 com SP1.  O SHA-1 não tem suporte de setembro de 2019 e, se a assinatura de código SHA-2 não estiver habilitada, a extensão do agente não será instalada/atualizada conforme o esperado.. Obtenha mais informações sobre a [atualização e os requisitos do SHA-2](https://aka.ms/SHA-2KB).



#### <a name="linux"></a>Linux

**Sistema operativo** | **Detalhes**
--- | ---
Red Hat Enterprise Linux | 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7, 8,0
CentOS | 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7, 8,0
Servidor Ubuntu 14, 4 LTS | [Versões de kernel com suporte](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Servidor Ubuntu 16, 4 LTS | [Versão do kernel com suporte](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Os servidores Ubuntu que usam a autenticação baseada em senha e a entrada e o pacote Cloud-init para configurar VMs de nuvem podem ter um logon baseado em senha desabilitado no failover (dependendo da configuração do cloudinit). O logon baseado em senha pode ser habilitado novamente na máquina virtual redefinindo a senha no menu suporte > solução de problemas > configurações (da VM com failover no portal do Azure.
Servidor Ubuntu 18, 4 LTS | [Versão do kernel com suporte](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | [Versões de kernel com suporte](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Versões de kernel com suporte](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [(Versões de kernel com suporte)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP<br/><br/> Não há suporte para a atualização de máquinas de replicação do SP3 para o SP4. Se um computador replicado tiver sido atualizado, você precisará desabilitar a replicação e reabilitar a replicação após a atualização.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7<br/><br/> Executando o kernel do Red Hat compatível ou o inquebrable Enterprise kernel versão 3, 4 & 5 (UEK3, UEK4, UEK5) 


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versões de kernel do Ubuntu com suporte para máquinas virtuais do Azure

**Liberar** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
14, 4 LTS | 9,28 | 3.13.0-24-Generic para 3.13.0-170-Generic,<br/>3.16.0-25-Generic para 3.16.0-77-Generic,<br/>3.19.0-18-Generic para 3.19.0-80-Generic,<br/>4.2.0-18-Generic para 4.2.0-42-Generic,<br/>4.4.0-21-Generic para 4.4.0-148-Generic,<br/>4.15.0-1023-Azure para 4.15.0-1045-Azure |
14, 4 LTS | 9,27 | 3.13.0-24-Generic para 3.13.0-170-Generic,<br/>3.16.0-25-Generic para 3.16.0-77-Generic,<br/>3.19.0-18-Generic para 3.19.0-80-Generic,<br/>4.2.0-18-Generic para 4.2.0-42-Generic,<br/>4.4.0-21-Generic para 4.4.0-148-Generic,<br/>4.15.0-1023-Azure para 4.15.0-1045-Azure |
14, 4 LTS | 9,26 | 3.13.0-24-Generic para 3.13.0-170-Generic,<br/>3.16.0-25-Generic para 3.16.0-77-Generic,<br/>3.19.0-18-Generic para 3.19.0-80-Generic,<br/>4.2.0-18-Generic para 4.2.0-42-Generic,<br/>4.4.0-21-Generic para 4.4.0-148-Generic,<br/>4.15.0-1023-Azure para 4.15.0-1045-Azure |
14, 4 LTS | 9,25 | 3.13.0-24-Generic para 3.13.0-169-Generic,<br/>3.16.0-25-Generic para 3.16.0-77-Generic,<br/>3.19.0-18-Generic para 3.19.0-80-Generic,<br/>4.2.0-18-Generic para 4.2.0-42-Generic,<br/>4.4.0-21-Generic para 4.4.0-146-Generic,<br/>4.15.0-1023-Azure para 4.15.0-1042-Azure |
|||
16, 4 LTS | 9,28 | 4.4.0-21-Generic para 4.4.0-159-Generic,<br/>4.8.0-34-Generic a 4.8.0-58-Generic,<br/>4.10.0-14-generic para 4.10.0-42-Generic,<br/>4.11.0-13-Generic para 4.11.0-14-generic,<br/>4.13.0-16-Generic para 4.13.0-45-Generic,<br/>4.15.0-13-Generic para 4.15.0-58-Generic<br/>4.11.0-1009-Azure para 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure para 4.13.0-1018-Azure <br/>4.15.0-1012-Azure para 4.15.0-1055-Azure|
16, 4 LTS | 9,27 | 4.4.0-21-Generic para 4.4.0-154-Generic,<br/>4.8.0-34-Generic a 4.8.0-58-Generic,<br/>4.10.0-14-generic para 4.10.0-42-Generic<br/>4.11.0-13-Generic para 4.11.0-14-generic,<br/>4.13.0-16-Generic para 4.13.0-45-Generic,<br/>4.15.0-13-Generic para 4.15.0-55-Generic<br/>4.11.0-1009-Azure para 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure para 4.13.0-1018-Azure <br/>4.15.0-1012-Azure para 4.15.0-1051-Azure|
16, 4 LTS | 9,26 | 4.4.0-21-Generic para 4.4.0-148-Generic,<br/>4.8.0-34-Generic a 4.8.0-58-Generic,<br/>4.10.0-14-generic para 4.10.0-42-Generic<br/>4.11.0-13-Generic para 4.11.0-14-generic,<br/>4.13.0-16-Generic para 4.13.0-45-Generic,<br/>4.15.0-13-Generic para 4.15.0-50-Generic<br/>4.11.0-1009-Azure para 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure para 4.13.0-1018-Azure <br/>4.15.0-1012-Azure para 4.15.0-1045-Azure|
16, 4 LTS | 9,25 | 4.4.0-21-Generic para 4.4.0-146-Generic,<br/>4.8.0-34-Generic a 4.8.0-58-Generic,<br/>4.10.0-14-generic para 4.10.0-42-Generic,<br/>4.11.0-13-Generic para 4.11.0-14-generic,<br/>4.13.0-16-Generic para 4.13.0-45-Generic,<br/>4.15.0-13-Generic para 4.15.0-48-Generic<br/>4.11.0-1009-Azure para 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure para 4.13.0-1018-Azure <br/>4.15.0-1012-Azure para 4.15.0-1042-Azure|
16, 4 LTS | 9,24 | 4.4.0-21-Generic para 4.4.0-143-Generic,<br/>4.8.0-34-Generic a 4.8.0-58-Generic,<br/>4.10.0-14-generic para 4.10.0-42-Generic,<br/>4.11.0-13-Generic para 4.11.0-14-generic,<br/>4.13.0-16-Generic para 4.13.0-45-Generic,<br/>4.15.0-13-Generic para 4.15.0-46-Generic<br/>4.11.0-1009-Azure para 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure para 4.13.0-1018-Azure <br/>4.15.0-1012-Azure para 4.15.0-1040-Azure|
|||
18, 4 LTS | [9,29](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) | 4.15.0-20-Generic para 4.15.0-64-Generic </br> 4.18.0-13-Generic para 4.18.0-25-Generic </br> 5.0.0-15-Generic para 5.0.0-29-Generic </br> 4.15.0-1009-Azure para 4.15.0-1037-Azure </br> 4.18.0-1006-Azure para 4.18.0-1025-Azure </br> 5.0.0-1012-Azure para 5.0.0-1020-Azure


#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Versões de kernel Debian com suporte para máquinas virtuais do Azure

**Liberar** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
Debian 7 | 9.25,9.26,9.27,9.28 | 3.2.0-4-AMD64 para 3.2.0-6-AMD64, 3.16.0-0. BPO. 4-AMD64 |
|||
Debian 8 | 9,28 | 3.16.0-4-AMD64 para 3.16.0-10-AMD64, 4.9.0-0. BPO. 4-AMD64 para 4.9.0-0. BPO. 9-AMD64 |
Debian 8 | 9,27 | 3.16.0-4-AMD64 para 3.16.0-9-AMD64, 4.9.0-0. BPO. 4-AMD64 para 4.9.0-0. BPO. 9-AMD64 |
Debian 8 | 9,25, 9,26 | 3.16.0-4-AMD64 para 3.16.0-8-AMD64, 4.9.0-0. BPO. 4-AMD64 para 4.9.0-0. BPO. 8-AMD64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Suporte a SUSE Linux Enterprise Server 12 versões de kernel para máquinas virtuais do Azure

**Liberar** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,28 | SP1 3.12.49-11-padrão para 3.12.74-60.64.40-padrão</br></br> SP1 (LTSS) 3.12.74-60.64.45-padrão para 3.12.74-60.64.118-padrão</br></br> SP2 4.4.21-69-padrão para 4.4.120-92.70-padrão</br></br>SP2 (LTSS) 4.4.121-92.73-padrão para 4.4.121-92.117-padrão</br></br>SP3 4.4.73-5-padrão para 4.4.180-94.100-padrão</br></br>SP3 4.4.138-4.7-Azure para 4.4.180-4.31-Azure</br></br>SP4 4.12.14-94.41-default para 4.12.14-95.29-default</br>SP4 4.12.14-6.3-Azure para 4.12.14-6.23-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,27 | SP1 3.12.49-11-padrão para 3.12.74-60.64.40-padrão</br></br> SP1 (LTSS) 3.12.74-60.64.45-padrão para 3.12.74-60.64.115-padrão</br></br> SP2 4.4.21-69-padrão para 4.4.120-92.70-padrão</br></br>SP2 (LTSS) 4.4.121-92.73-padrão para 4.4.121-92.117-padrão</br></br>SP3 4.4.73-5-padrão para 4.4.180-94.97-padrão</br></br>SP3 4.4.138-4.7-Azure para 4.4.180-4.31-Azure</br></br>SP4 4.12.14-94.41-default para 4.12.14-95.24-default</br>SP4 4.12.14-6.3-Azure para 4.12.14-6.18-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,26 | SP1 3.12.49-11-padrão para 3.12.74-60.64.40-padrão</br></br> SP1 (LTSS) 3.12.74-60.64.45-padrão para 3.12.74-60.64.110-padrão</br></br> SP2 4.4.21-69-padrão para 4.4.120-92.70-padrão</br></br>SP2 (LTSS) 4.4.121-92.73-padrão para 4.4.121-92.109-padrão</br></br>SP3 4.4.73-5-padrão para 4.4.178-94.91-padrão</br></br>SP3 4.4.138-4.7-Azure para 4.4.178-tornariam 4,28-Azure</br></br>SP4 4.12.14-94.41-default para 4.12.14-95.16-default</br>SP4 4.12.14-6.3-Azure para 4.12.14-6,9-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,25 | SP1 3.12.49-11-padrão para 3.12.74-60.64.40-padrão</br></br> SP1 (LTSS) 3.12.74-60.64.45-padrão para 3.12.74-60.64.107-padrão</br></br> SP2 4.4.21-69-padrão para 4.4.120-92.70-padrão</br></br>SP2 (LTSS) 4.4.121-92.73-padrão para 4.4.121-92.104-padrão</br></br>SP3 4.4.73-5-padrão para 4.4.176-94.88-padrão</br></br>SP3 4.4.138-4.7-Azure para 4.4.176-4,25-Azure</br></br>SP4 4.12.14-94.41-default para 4.12.14-95.13-default</br>SP4 4.12.14-6.3-Azure para 4.12.14-6,9-Azure |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Máquinas replicadas-sistema de arquivos do Linux/armazenamento de convidado

* Sistemas de arquivos: ext3, ext4, ReiserFS (somente SuSE Linux Enterprise Server), XFS, BTRFS
* Gerenciador de volumes: LVM2
* Software de vários caminhos: mapeador de dispositivos


## <a name="replicated-machines---compute-settings"></a>Máquinas replicadas – configurações de computação

**Definição** | **Suporte** | **Detalhes**
--- | --- | ---
Tamanho | Qualquer tamanho de VM do Azure com pelo menos 2 núcleos de CPU e 1 GB de RAM | Verifique os [tamanhos de máquina virtual do Azure](../virtual-machines/windows/sizes.md).
Conjuntos de disponibilidade | Suportado | Se você habilitar a replicação para uma VM do Azure com as opções padrão, um conjunto de disponibilidade será criado automaticamente, com base nas configurações de região de origem. Você pode modificar essas configurações.
Zonas de disponibilidade | Suportado |
Benefício de uso híbrido (HUB) | Suportado | Se a VM de origem tiver uma licença de HUB habilitada, um failover de teste ou VM que passou por failover também usará a licença de HUB.
Conjuntos de dimensionamento de máquinas virtuais | Não suportado |
Imagens da galeria do Azure-publicado pela Microsoft | Suportado | Com suporte se a VM for executada em um sistema operacional com suporte.
Imagens da galeria do Azure-publicadas por terceiros | Suportado | Com suporte se a VM for executada em um sistema operacional com suporte.
Imagens personalizadas-publicadas por terceiros | Suportado | Com suporte se a VM for executada em um sistema operacional com suporte.
VMs migradas usando Site Recovery | Suportado | Se uma VM ou máquina física VMware foi migrada para o Azure usando Site Recovery, você precisará desinstalar a versão mais antiga do serviço de mobilidade em execução no computador e reiniciar a máquina antes de replicá-la para outra região do Azure.
Políticas de RBAC | Não suportado | As políticas de RBAC (controle de acesso baseado em função) em VMs não são replicadas para a VM de failover na região de destino.
Extensões | Não suportado | As extensões não são replicadas para a VM de failover na região de destino. Ele precisa ser instalado manualmente após o failover.

## <a name="replicated-machines---disk-actions"></a>Máquinas replicadas-ações de disco

**Ação** | **Detalhes**
-- | ---
Redimensionar disco na VM replicada | Suportado
Adicionar um disco a uma VM replicada | Suportado

## <a name="replicated-machines---storage"></a>Máquinas replicadas-armazenamento

Esta tabela resumiu suporte para o disco do sistema operacional de VM do Azure, disco de dados e disco temporário.

- É importante observar os limites e as metas de disco da VM para VMs do [Linux](../virtual-machines/linux/disk-scalability-targets.md) e do [Windows](../virtual-machines/windows/disk-scalability-targets.md) para evitar problemas de desempenho.
- Se você implantar com as configurações padrão, Site Recovery automaticamente criará discos e contas de armazenamento com base nas configurações de origem.
- Se você personalizar o, certifique-se de seguir as diretrizes.

**Componente** | **Suporte** | **Detalhes**
--- | --- | ---
Tamanho máximo do disco do so | 2048 GB | [Saiba mais](../virtual-machines/windows/managed-disks-overview.md) sobre os discos de VM.
Disco temporário | Não suportado | O disco temporário sempre é excluído da replicação.<br/><br/> Não armazene dados persistentes no disco temporário. [Saiba mais](../virtual-machines/windows/managed-disks-overview.md).
Tamanho máximo do disco de dados | 8192 GB para discos gerenciados<br></br>4095 GB para discos não gerenciados|
Tamanho mínimo do disco de dados | Nenhuma restrição para discos não gerenciados. 2 GB para discos gerenciados | 
Número máximo do disco de dados | Até 64, de acordo com o suporte para um tamanho específico de VM do Azure | [Saiba mais](../virtual-machines/windows/sizes.md) sobre tamanhos de VM.
Taxa de alteração do disco de dados | Máximo de 10 MBps por disco para o armazenamento Premium. Máximo de 2 MBps por disco para o armazenamento Standard. | Se a taxa média de alteração de dados no disco for continuamente maior do que o máximo, a replicação não será atualizada.<br/><br/>  No entanto, se o máximo for excedido esporadicamente, a replicação poderá ser atualizada, mas você poderá ver pontos de recuperação ligeiramente atrasados.
Disco de dados-conta de armazenamento Standard | Suportado |
Disco de dados-conta de armazenamento Premium | Suportado | Se uma VM tiver discos distribuídos em contas de armazenamento Premium e Standard, você poderá selecionar uma conta de armazenamento de destino diferente para cada disco, para garantir que você tenha a mesma configuração de armazenamento na região de destino.
Disco gerenciado-padrão | Com suporte em regiões do Azure nas quais Azure Site Recovery tem suporte. |
Disco gerenciado-Premium | Com suporte em regiões do Azure nas quais Azure Site Recovery tem suporte. |
SSD Standard | Suportado |
Redundância | Há suporte para LRS e GRS.<br/><br/> ZRS não tem suporte.
Armazenamento frio e quente | Não suportado | Os discos de VM não têm suporte em armazenamento frio e dinâmico
Espaços de armazenamento | Suportado |
Criptografia em repouso (SSE) | Suportado | SSE é a configuração padrão em contas de armazenamento.   
Azure Disk Encryption (ADE) para o sistema operacional Windows | Com suporte para VMs com discos gerenciados. Não há suporte para VMs que usam discos não gerenciados |
Azure Disk Encryption (ADE) para o SO Linux | Não suportado |
Adição a quente | Suportado | A habilitação da replicação para um disco de dados que você adiciona a uma VM do Azure replicada tem suporte para VMs que usam discos gerenciados.
Disco de remoção quente | Não suportado | Se você remover o disco de dados na VM, será necessário desabilitar a replicação e habilitar a replicação novamente para a VM.
Excluir o disco | Support. Você deve usar o [PowerShell](azure-to-azure-exclude-disks.md) para configurar o. |  Os discos temporários são excluídos por padrão.
Storage Spaces Direct  | Com suporte para pontos de recuperação com consistência de falha. Não há suporte para pontos de recuperação consistentes com o aplicativo. |
Servidor de arquivos de escalabilidade horizontal  | Com suporte para pontos de recuperação com consistência de falha. Não há suporte para pontos de recuperação consistentes com o aplicativo. |
LRS | Suportado |
GRS | Suportado |
RA-GRS | Suportado |
ZRS | Não suportado |
Armazenamento frio e quente | Não suportado | Não há suporte para discos de máquina virtual em armazenamento frio e quente
Firewalls de armazenamento do Azure para redes virtuais  | Suportado | Se o acesso à rede virtual for restrito às contas de armazenamento, habilite [permitir serviços confiáveis da Microsoft](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
Contas de armazenamento de uso geral v2 (camada quente e fria) | Sim | Os custos de transações aumentam substancialmente em comparação com as contas de armazenamento v1 de uso geral

>[!IMPORTANT]
> Para evitar problemas de desempenho, certifique-se de seguir as metas de desempenho e escalabilidade de disco de VM para VMs [Linux](../virtual-machines/linux/disk-scalability-targets.md) ou [Windows](../virtual-machines/windows/disk-scalability-targets.md) . Se você usar as configurações padrão, Site Recovery criará os discos necessários e as contas de armazenamento, com base na configuração de origem. Se você personalizar e selecionar suas próprias configurações, siga os destinos de desempenho e escalabilidade de disco para suas VMs de origem.

## <a name="limits-and-data-change-rates"></a>Limites e taxas de alteração de dados

A tabela a seguir resume os limites de Site Recovery.

- Esses limites se baseiam em nossos testes, mas, obviamente, não cobrem todas as combinações de e/s de aplicativo possíveis.
- Os resultados reais podem variar com base na combinação de e/s do aplicativo.
- Há dois limites a serem considerados, por variação de dados de disco e por variação de dados de máquina virtual.
- Por exemplo, se usarmos um disco P20 Premium, conforme descrito na tabela a seguir, Site Recovery poderá lidar com 5 MBs de variação por disco, com no máximo cinco discos por VM, devido ao limite total de 25 MB/s de rotatividade por VM.

**Destino de armazenamento** | **Média de e/s de disco de origem** |**Média de alterações a dados do disco de origem** | **Total de alterações a dados do disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB  | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |  336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou superior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou superior |20 MB/s | 1684 GB por disco

## <a name="replicated-machines---networking"></a>Computadores replicados-rede
**Definição** | **Suporte** | **Detalhes**
--- | --- | ---
NIC | Número máximo com suporte para um tamanho específico de VM do Azure | As NICs são criadas quando a VM é criada durante o failover.<br/><br/> O número de NICs na VM de failover depende do número de NICs na VM de origem quando a replicação foi habilitada. Se você adicionar ou remover uma NIC depois de habilitar a replicação, ela não afetará o número de NICs na VM replicada após o failover. Observe também que a ordem de NICs após o failover não tem garantia de ser igual à ordem original.
Balanceador de Carga de Externo | Suportado | Associe o balanceador de carga pré-configurado usando um script de automação do Azure em um plano de recuperação.
Balanceador de carga interno | Suportado | Associe o balanceador de carga pré-configurado usando um script de automação do Azure em um plano de recuperação.
Endereço IP público | Suportado | Associe um endereço IP público existente à NIC. Ou crie um endereço IP público e associe-o à NIC usando um script de automação do Azure em um plano de recuperação.
NSG na NIC | Suportado | Associe o NSG à NIC usando um script de automação do Azure em um plano de recuperação.
NSG na sub-rede | Suportado | Associe o NSG à sub-rede usando um script de automação do Azure em um plano de recuperação.
Endereço IP reservado (estático) | Suportado | Se a NIC na VM de origem tiver um endereço IP estático e a sub-rede de destino tiver o mesmo endereço IP disponível, ele será atribuído à VM com failover.<br/><br/> Se a sub-rede de destino não tiver o mesmo endereço IP disponível, um dos endereços IP disponíveis na sub-rede será reservado para a VM.<br/><br/> Você também pode especificar um endereço IP fixo e uma sub-rede em **itens replicados** > **configurações > as** interfaces de **rede** > **de computação e de rede** .
Endereço IP dinâmico | Suportado | Se a NIC na origem tiver um endereçamento IP dinâmico, a NIC na VM com failover também será dinâmica por padrão.<br/><br/> Você pode modificar isso para um endereço IP fixo, se necessário.
Vários endereços IP | Não suportado | Quando você faz failover de uma VM que tem uma NIC com vários endereços IP, somente o endereço IP primário da NIC na região de origem é mantido. Para atribuir vários endereços IP, você pode adicionar VMs a um [plano de recuperação](recovery-plan-overview.md) e anexar um script para atribuir endereços IP adicionais ao plano ou pode fazer a alteração manualmente ou com um script após o failover. 
Gestor de Tráfego     | Suportado | Você pode pré-configurar o Gerenciador de tráfego para que o tráfego seja roteado para o ponto de extremidade na região de origem regularmente e para o ponto de extremidade na região de destino em caso de failover.
DNS do Azure | Suportado |
DNS Personalizado  | Suportado |
Proxy não autenticado | Suportado | [Saber mais](site-recovery-azure-to-azure-networking-guidance.md)    
Proxy autenticado | Não suportado | Se a VM estiver usando um proxy autenticado para a conectividade de saída, ela não poderá ser replicada usando Azure Site Recovery.    
Conexão VPN site a site para local<br/><br/>(com ou sem o ExpressRoute)| Suportado | Verifique se o UDRs e o NSGs estão configurados de forma que o tráfego de Site Recovery não seja roteado para o local. [Saber mais](site-recovery-azure-to-azure-networking-guidance.md)    
Conexão VNET com VNET | Suportado | [Saber mais](site-recovery-azure-to-azure-networking-guidance.md)  
Pontos Finais de Serviço de Rede Virtual | Suportado | Se você estiver restringindo o acesso à rede virtual a contas de armazenamento, verifique se os serviços confiáveis da Microsoft têm permissão de acesso à conta de armazenamento.
Redes aceleradas | Suportado | A rede acelerada deve estar habilitada na VM de origem. [Saiba mais](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>Passos seguintes
- Leia as [diretrizes de rede](site-recovery-azure-to-azure-networking-guidance.md) para replicar VMs do Azure.
- Implante a recuperação de desastres [replicando VMs do Azure](site-recovery-azure-to-azure.md).
