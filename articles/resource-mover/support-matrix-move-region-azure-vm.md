---
title: Matriz de suporte para mover VMs Azure para outra região com Azure Resource Mover
description: Reveja o suporte para mover VMs Azure entre regiões com Azure Resource Mover.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: how-to
ms.date: 10/11/2020
ms.author: raynew
ms.openlocfilehash: 4ee442d1983e4f7c1825690e1c780454272971aa
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521310"
---
# <a name="support-for-moving-azure-vms-between-azure-regions"></a>Apoio à deslocação de VMs Azure entre regiões de Azure

Este artigo resume o suporte e os pré-requisitos quando move máquinas virtuais e recursos de rede relacionados em regiões de Azure usando o Resource Mover.

> [!IMPORTANT]
> A Azure Resource Mover está atualmente em pré-visualização.


## <a name="windows-vm-support"></a>Suporte ao Windows VM

A Resource Mover suporta VMs Azure que executam estes sistemas operativos Windows.

**Sistema operativo** | **Detalhes**
--- | ---
Windows Server 2019 | Suportado para Server Core, Servidor com Experiência de Ambiente de Trabalho.
Windows Server 2016  | Núcleo de Servidor Suportado, Servidor com Experiência de Ambiente de Trabalho.
Windows Server 2012 R2 | Suportado.
Windows Server 2012 | Suportado.
Windows Server 2008 R2 com SP1/SP2 | Suportado.<br/><br/> Para as máquinas que executam o Windows Server 2008 R2 com SP1/SP2, é necessário instalar uma atualização de pilha de manutenção do Windows [(SSU)](https://support.microsoft.com/help/4490628) e [SHA-2](https://support.microsoft.com/help/4474419).  O SHA-1 não é suportado a partir de setembro de 2019, e se a assinatura de código SHA-2 não estiver ativada, a extensão do agente não instalará/atualizará como esperado. Saiba mais sobre [a atualização e requisitos SHA-2](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Suportado.
Windows 8.1 (x64) | Suportado.
Windows 8 (x64) | Suportado.
Windows 7 (x64) com SP1 em diante | Instale uma atualização da pilha de manutenção do Windows [(SSU)](https://support.microsoft.com/help/4490628) e [SHA-2](https://support.microsoft.com/help/4474419) nas máquinas que executam o Windows 7 com SP1.  Sha-1 não é suportado a partir de setembro de 2019, e se a assinatura de código SHA-2 não estiver habilitada o passo de 'preparar' não será bem sucedido. Saiba mais sobre [a atualização e requisitos SHA-2](https://aka.ms/SHA-2KB).


## <a name="linux-vm-support"></a>Suporte Linux VM

O Resource Move suporta os VMs Azure que executam estes sistemas operativos Linux.

**Sistema operativo** | **Detalhes**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7,](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 8.0, 8.1
Ubuntu 14.04 LTS Server | [Versões de kernel suportadas](#supported-ubuntu-kernel-versions)
Ubuntu 16.04 LTS Server | [Versão suportada do núcleo](#supported-ubuntu-kernel-versions)<br/><br/> Os servidores Ubuntu que utilizam a autenticação e o sent-in baseados em palavras-passe, e o pacote de init em nuvem para configurar VMs em nuvem, podem ter inserções baseadas em palavras-passe desativadas na falha (dependendo da configuração de inição de nuvem). O sessão de sessão baseado em palavra-passe pode ser reensitado na máquina virtual, repondo a palavra-passe a partir do menu 'Resolução de problemas' > de resolução de problemas > definições (do que falhou em VM no portal Azure).
Ubuntu 18.04 LTS Server | [Versão suportada do núcleo.](#supported-ubuntu-kernel-versions)
Debian 7 | [Versões de kernel suportadas](#supported-debian-kernel-versions).
Debian 8 | [Versões de kernel suportadas](#supported-debian-kernel-versions).
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [Versões de kernel suportadas](#supported-suse-linux-enterprise-server-12-kernel-versions)
SUSE Linux Enterprise Server 15 | 15 e 15 SP1. [(Versões de núcleo suportado)](#supported-suse-linux-enterprise-server-15-kernel-versions)
SUSE Linux Enterprise Server 11 | SP3
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> Executando o núcleo compatível com chapéu vermelho ou desbloqueio de kernel da empresa inquebrável 3, 4 & 5 (UEK3, UEK4, UEK5)


### <a name="supported-ubuntu-kernel-versions"></a>Versões suportadas do kernel Ubuntu

**Libertar** | **Versão de kernel** 
--- | --- 
14.04 LTS |  3.13.0-24-genérico a 3.13.0-170-genérico,<br/>3.16.0-25-genérico a 3.16.0-77-genéricos,<br/>3.19.0-18-genérico a 3.19.0-80-genérico,<br/>4.2.0-18-genérico a 4.2.0-42-genérico,<br/>4.4.0-21-genérico a 4.4.0-148-genérico,<br/>4.15.0-1023-azure para 4.15.0-1045-azure 
16.04 LTS |  4.4.0-21-genérico a 4.4.0-171-genérico,<br/>4.8.0-34-genérico a 4.8.0-58-genérico,<br/>4.10.0-14-genérico a 4.10.0-42-genérico,<br/>4.11.0-13-genérico a 4.11.0-14-genérico,<br/>4.13.0-16-genérico a 4.13.0-45-genéricos,<br/>4.15.0-13-genérico a 4.15.0-74-genérico<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure para 4.13.0-1018-azure <br/>4.15.0-1012-azure para 4.15.0-1066-azure
18.04 LTS | 4.15.0-20-genérico a 4.15.0-74-genérico </br> 4.18.0-13-genérico a 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-37-genérico </br> 5.3.0-19 genérico a 5.3.0-24-genérico </br> 4.15.0-1009-azure para 4.15.0-1037-azure </br> 4.18.0-1006-azure para 4.18.0-1025-azure </br> 5.0.0-1012-azure para 5.0.0-1028-azure </br> 5.3.0-1007-azure para 5.3.0-1009-azure


### <a name="supported-debian-kernel-versions"></a>Versões suportadas do kernel de Debian 

**Libertar** |  **Versão de kernel** 
--- |  --- 
Debian 7 |  3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 
Debian 8 |  3.16.0-4-amd64 a 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.11-amd64 
Debian 8 |  3.16.0-4-amd64 a 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.9-amd64

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions"></a>SUSE Linux Enterprise Server 12 versões kernel 

**Libertar** | **Versão de kernel** 
--- |  --- 
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) |  Todas as [ações SUSE 12 SP1,SP2,SP3,SP4 são suportadas.](https://www.suse.com/support/kb/doc/?id=000019587)</br></br> 4.4.138-4.7-azure para 4.4.180-4.31-azure,</br>4.12.14-6.3-azure para 4.12.14-6.34-azure  


### <a name="supported-suse-linux-enterprise-server-15-kernel-versions"></a>SUSE Linux Enterprise Server 15 versões kernel

**Libertar** | **Versão de kernel** |
--- |  --- |
SUSE Linux Enterprise Server 15 e 15 SP1 |  Todas as ações SUSE 15 e 15 núcleos são suportados.</br></br> 4.12.14-5.5-azure para 4.12.14-8.22-azure |

## <a name="supported-linux-file-systemguest-storage"></a>Sistema de ficheiros Linux suportado/armazenamento de hóspedes

* Sistemas de ficheiros: ext3, ext4, XFS, BTRFS
* Gestor de volume: LVM2
* Software multipata: Device Mapper


## <a name="supported-vm-compute-settings"></a>Definições de computação VM suportadas

**Definição** | **Suporte** | **Detalhes**
--- | --- | ---
Tamanho | Qualquer tamanho Azure VM com pelo menos dois núcleos cpu e 1 GB de RAM | Verifique os [tamanhos da máquina virtual Azure](../virtual-machines/sizes-general.md).
Conjuntos de disponibilidade | Atualmente, não é suportado | Se adicionar um VM Azure com um conjunto de disponibilidade para a coleção de movimento com as opções predefinidas, o processo de Preparação falha. Pode optar por mover o VM para uma zona de disponibilidade ou movê-lo como um único exemplo VM. Pode modificar estas definições na página de propriedades alvo de edição.
Zonas de disponibilidade | Suportado | Apoiado, dependendo do apoio da região alvo.
Imagens da galeria Azure (publicadas pela Microsoft) | Suportado | Suportado se o VM funcionar num sistema operativo suportado.
Imagens da Galeria Azure (publicadas por terceiros)  | Suportado | Suportado se o VM funcionar num sistema operativo suportado.
Imagens personalizadas (publicadas por terceiros)| Suportado | Suportado se o VM funcionar num sistema operativo suportado.
VMs usando recuperação de site | Não suportado | Mover recursos através de regiões para VMs, usando a Recuperação do Local no backend. Se já estiver a utilizar a Recuperação do Local, desative a replicação e, em seguida, inicie o processo de Preparação.
Políticas de Azure RBAC | Não suportado | As políticas de controlo de acesso baseado em funções (Azure RBAC) em VM não são copiadas para o VM na região-alvo.
Extensões | Não suportado | As extensões não são copiadas para o VM na região alvo. Instale-as manualmente depois de concluída a deslocação.


## <a name="supported-vm-storage-settings"></a>Definições de armazenamento de VM suportadas

Esta tabela resumiu o suporte para o disco Azure VM OS, disco de dados e disco temporário. É importante observar os limites e alvos do disco VM para Os VMs [do Linux](../virtual-machines/linux/disk-scalability-targets.md) e [do Windows](../virtual-machines/windows/disk-scalability-targets.md) para evitar quaisquer problemas de desempenho.

> [!NOTE]
> O tamanho do VM alvo deve ser igual ou maior do que o VM de origem. Os parâmetros utilizados para validação são: Contagem de Discos de Dados, contagem de NICs, CPUs disponíveis, Memória em GB. Se não for um erro, é emitido um erro.


**Componente** | **Suporte** | **Detalhes**
--- | --- | ---
Tamanho máximo do disco de SO | 2048 GB | [Saiba mais](../virtual-machines/windows/managed-disks-overview.md) sobre discos VM.
Disco temporário | Não suportado | O disco temporário está sempre excluído do processo de preparação.<br/><br/> Não guarde dados persistentes no disco temporário. [Saiba mais](../virtual-machines/windows/managed-disks-overview.md#temporary-disk).
Tamanho máximo do disco de dados | 8192 GB para discos geridos
Tamanho mínimo do disco de dados |  2 GB para discos geridos |
Número máximo do disco de dados | Até 64, de acordo com o suporte para um tamanho Azure VM específico | [Saiba mais](../virtual-machines/windows/sizes.md) sobre os tamanhos de VM.
Taxa de alteração do disco de dados | Máximo de 10 MBps por disco para armazenamento premium. Máximo de 2 MBps por disco para armazenamento standard. | Se a taxa média de alteração de dados no disco for continuamente superior ao máximo, a preparação não se alcança.<br/><br/>  No entanto, se o máximo for ultrapassado esporadicamente, a preparação pode recuperar, mas poderá ver pontos de recuperação ligeiramente atrasados.
Disco de dados (conta de armazenamento padrão) | Não suportado. | Mude o tipo de armazenamento para o disco gerido e tente mover o VM.
Disco de dados (conta de armazenamento premium) | Não suportado | Mude o tipo de armazenamento para o disco gerido e tente mover o VM.
Disco gerido (Standard) | Suportado  |
Disco gerido (Premium) | Suportado |
SSD Standard | Suportado |
Geração 2 (bota UEFI) | Suportado
Conta de armazenamento de diagnóstico de arranque | Não suportado | Reenvendá-lo depois de mover o VM para a região alvo.

### <a name="limits-and-data-change-rates"></a>Limites e taxas de alteração de dados

A tabela seguinte resume limites baseados nos nossos testes. Estes não cobrem todas as combinações de I/O possíveis. Os resultados reais variam em função da mistura de I/O da sua aplicação. Existem dois limites a considerar, por conta de dados de disco, e por vm data churn.

**Alvo de armazenamento** | **Disco de origem médio I/O** |**Média de alterações a dados do disco de origem** | **Total de dados de disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |    336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou superior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou superior |20 MB/s | 1684 GB por disco

## <a name="supported-vm-networking-settings"></a>Definições de rede VM suportadas

**Definição** | **Suporte** | **Detalhes**
--- | --- | ---
NIC | Suportado | Especifique um recurso existente na região alvo ou crie um novo recurso durante o processo de Preparação. 
Balanceador de carga interno | Suportado | Especifique um recurso existente na região alvo ou crie um novo recurso durante o processo de Preparação.  
Balanceador de carga público | Atualmente, não é suportado | Especifique um recurso existente na região alvo ou crie um novo recurso durante o processo de Preparação.  
Endereço IP público | Suportado | Especifique um recurso existente na região alvo ou crie um novo recurso durante o processo de Preparação.<br/><br/> O endereço IP público é específico da região, e não será mantido na região alvo após a mudança. Tenha isto em mente quando modificar as definições de rede (incluindo regras de equilíbrio de carga) no local alvo.
Grupo de segurança de rede | Suportado | Especifique um recurso existente na região alvo ou crie um novo recurso durante o processo de Preparação.  
Endereço IP reservado (estático) | Suportado | Não pode configurar isto. O valor é desresposição do valor da fonte. <br/><br/> Se o NIC na fonte VM tiver um endereço IP estático, e a sub-rede alvo tiver o mesmo endereço IP disponível, é atribuído ao VM alvo.<br/><br/> Se a sub-rede-alvo não tiver o mesmo endereço IP disponível, o movimento iniciado para o VM falhará.
Endereço IP dinâmico | Suportado | Não pode configurar isto. O valor é desresposição do valor da fonte.<br/><br/> Se o NIC na fonte tiver um endereço IP dinâmico, o NIC no VM alvo também é dinâmico por padrão.
Configurações IP | Suportado | Não pode configurar isto. O valor é desresposição do valor da fonte.

## <a name="outbound-access-requirements"></a>Requisitos de acesso de saída

VMs Azure que você quer mover precisam de acesso de saída.


### <a name="url-access"></a>Acesso a URL

 Se estiver a usar um proxy de firewall baseado em URL para controlar a conectividade de saída, permita o acesso a estes URLs:

**Nome** | **Nuvem pública azul** | **Detalhes** 
--- | --- | --- 
Armazenamento | `*.blob.core.windows.net`  | Permite que os dados sejam escritos da VM para a conta de armazenamento em cache na região de origem. 
Azure Active Directory | `login.microsoftonline.com`  | Fornece autorização e autenticação para os URLs do serviço Site Recovery. 
Replicação | `*.hypervrecoverymanager.windowsazure.com` | Permite que a VM comunique com o serviço Site Recovery. 
Service Bus | `*.servicebus.windows.net` | Permite que a VM escreva dados de monitorização e diagnóstico do Site Recovery. 

## <a name="nsg-rules"></a>Regras do NSG
Se estiver a utilizar regras de um grupo de segurança de rede (NSG) para controlar a conectividade de saída, crie estas regras [de marcação de serviço.](../virtual-network/service-tags-overview.md) Cada regra deve permitir o acesso de saída em HTTPS (443).
- Crie uma regra de etiqueta de armazenamento para a região de origem.
- Crie uma regra de marca *AzureSiteRecovery,* para permitir o acesso ao serviço de Recuperação do Local em qualquer região. Esta etiqueta tem dependências destas outras tags, por isso é necessário criar regras para que:
    - *AzureActiveDirectory*
    - **EventHub*
    - *AzureKeyVault*
    - *GuestAndHybridManagement*
- Recomendamos que teste regras num ambiente de não produção. [Reveja alguns exemplos.](../site-recovery/azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) 


## <a name="next-steps"></a>Passos seguintes

Tente [mover um Azure VM](tutorial-move-region-virtual-machines.md) para outra região com o Resource Mover.
