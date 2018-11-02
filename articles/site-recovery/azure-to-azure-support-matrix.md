---
title: Matriz de suporte do Azure Site Recovery para recuperação após desastre de VMs de IaaS do Azure entre regiões do Azure com o Azure Site Recovery | Documentos da Microsoft
description: Resume os sistemas operativos suportados e configurações para a replicação do Azure Site Recovery de máquinas virtuais do Azure (VMs) de uma região para outro para as necessidades de (DR recuperação) após desastre.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 10/28/2018
ms.author: raynew
ms.openlocfilehash: ad6bf2db44996684096deb2b351301e450d80249
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2018
ms.locfileid: "50747971"
---
# <a name="support-matrix-for-replicating-from-one-azure-region-to-another"></a>Matriz de suporte para replicar a partir de uma região do Azure para outra

Este artigo resume as configurações suportadas e componentes quando implementar a recuperação após desastre com replicação, ativação pós-falha e recuperação de VMs do Azure a partir de uma região do Azure para outra, com o [do Azure Site Recovery](site-recovery-overview.md) serviço.


## <a name="deployment-method-support"></a>Suporte de método de implementação

**Método de implementação** |  **Suportado / não suportado**
--- | ---
**Portal do Azure** | Suportadas
**PowerShell** | [Replicação do Azure para o Azure com o PowerShell](azure-to-azure-powershell.md)
**API REST** | Não são atualmente suportados
**CLI** | Não são atualmente suportados


## <a name="resource-support"></a>Suporte de recursos

**Ação de recurso** | **Detalhes**
--- | --- | ---
**Mover o Cofre entre grupos de recursos** | Não suportado
**Mover os recursos de armazenamento/computação/rede entre grupos de recursos** | Não suportado.<br/><br/> Se mover uma VM ou componentes associados, como o armazenamento/rede depois da VM está a replicar, terá de desativar e, em seguida, volte a ativar replicação para a VM.
**Replicar VMs do Azure a partir de uma subscrição para outro para recuperação após desastre** | Suportado no mesmo inquilino do Azure Active Directory.
**Migrar VMs em várias regiões nos clusters geográficas suportados (dentro e entre subscrições)** | Suportado no mesmo inquilino do Azure Active Directory.
**Migrar VMs na mesma região** | Não suportado.

# <a name="region-support"></a>Suporte de região

Pode replicar e recuperar VMs entre quaisquer duas regiões dentro do mesmo cluster geográfico.

**Cluster geográfica** | **Regiões do Azure**
-- | --
América | Leste do Canadá, Canadá Central, Centro-Sul dos E.U.A., EUA Centro-Oeste, E.U.A. leste, E.U.A. Leste 2, E.U.A. oeste, E.U.A. oeste 2, E.U.A. Central, Centro-Norte
Europa | Oeste do Reino Unido, sul do Reino Unido, Europa do Norte, Europa Ocidental, França, Sul de França
Ásia | Sul da Índia, Índia Central, Sudeste asiático, leste do Japão, Ásia Oriental, oeste do Japão, Coreia Central, Sul da Coreia
Austrália   | Leste da Austrália, Sudeste da Austrália, Austrália Central, Austrália Central 2
Azure Government    | US GOV Virginia, US GOV Iowa, US GOV Arizona, US GOV Texas, US DOD leste, US DOD Centro
Alemanha | Alemanha Central, Alemanha Nordeste
China | Leste da China, Norte da China

>[!NOTE]
>
> Para a região sul do Brasil, pode replicar e efetuar a ativação pós-falha para um dos seguintes: Centro-Sul, e.u.a. centro-oeste, E.U.A. leste, E.U.A. Leste 2, E.U.A. oeste, E.U.A. oeste 2 e e.u.a. Centro-Norte regiões.

## <a name="cache-storage"></a>Armazenamento de cache

A tabela seguinte resume o suporte para a conta de armazenamento de cache utilizada pelo Site Recovery durante a replicação.

**Definição** | **Detalhes**
--- | ---
Contas de armazenamento para V2 de fins gerais (acesso frequente e escalão acesso esporádico) | Não suportado. | A limitação existe para o armazenamento de cache, porque os custos de transação para o V2 são consideravelmente mais do que as contas de armazenamento V1.
Firewalls de armazenamento do Azure para redes virtuais  | Não | Permitir o acesso a redes virtuais do Azure específicas em contas de armazenamento de cache utilizadas para armazenar dados replicados não é suportada.



## <a name="replicated-machine-operating-systems"></a>Sistemas de operativos máquina replicada

Recuperação de sites suporta a replicação de VMs do Azure com os sistemas operativos indicados nesta secção.

### <a name="windows"></a>Windows

**Sistema operativo** | **Detalhes**
--- | ---
Windows Server 2016  | Server Core, servidor com experiência de área de trabalho
Windows Server 2012 R2 |
Windows Server 2012 |
Windows Server 2008 R2 | Com SP1 ou posterior

#### <a name="linux"></a>Linux

**Sistema operativo** | **Detalhes**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5   
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1 e 7.2, 7.3,7.4, 7.5
Ubuntu 14.04 LTS Server | [Versões de kernel suportada](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16.04 LTS Server | [Versão de kernel suportada](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Servidores de Ubuntu usando a autenticação baseada em palavra-passe e início de sessão e o pacote de cloud-init para configurar VMs, na cloud podem ter início de sessão baseada em palavra-passe desativado na ativação pós-falha (dependendo da configuração de cloudinit). Início de sessão baseada em palavra-passe pode ser novamente ativado na máquina virtual ao repor a palavra-passe do suporte da > Resolução de problemas > menu de definições (da VM no portal do Azure.
Debian 7 | [Versões de kernel suportada](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Versões de kernel suportada)](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3. [Versões de kernel suportada](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> Não é suportada a atualização de replicar máquinas a partir do SP3 para SP4. Se uma máquina replicada tiver sido atualizada, terá de desativar a replicação e volte a ativar a replicação após a atualização.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7<br/><br/> A executar o kernel compatível do Red Hat ou Indestrutíveis Enterprise Kernel versão 3 (UEK3).


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versões suportadas do kernel de Ubuntu para máquinas virtuais do Azure

**Versão** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
14.04 LTS | 9.19 | 3.13.0-24-Generic para 3.13.0-153-generic,<br/>3.16.0-25-Generic para 3.16.0-77-generic,<br/>3.19.0-18-Generic para 3.19.0-80-generic,<br/>4.2.0-18-Generic para 4.2.0-42-generic,<br/>4.4.0-21-Generic para 4.4.0-131-generic |
14.04 LTS | 9.18 | 3.13.0-24-Generic para 3.13.0-151-generic,<br/>3.16.0-25-Generic para 3.16.0-77-generic,<br/>3.19.0-18-Generic para 3.19.0-80-generic,<br/>4.2.0-18-Generic para 4.2.0-42-generic,<br/>4.4.0-21-Generic para 4.4.0-128-generic |
14.04 LTS | 9.17 | 3.13.0-24-Generic para 3.13.0-147-generic,<br/>3.16.0-25-Generic para 3.16.0-77-generic,<br/>3.19.0-18-Generic para 3.19.0-80-generic,<br/>4.2.0-18-Generic para 4.2.0-42-generic,<br/>4.4.0-21-Generic para 4.4.0-124-generic |
14.04 LTS | 9.16 | 3.13.0-24-Generic para 3.13.0-144-generic,<br/>3.16.0-25-Generic para 3.16.0-77-generic,<br/>3.19.0-18-Generic para 3.19.0-80-generic,<br/>4.2.0-18-Generic para 4.2.0-42-generic,<br/>4.4.0-21-Generic para 4.4.0-119-generic |
|||
16.04 LTS | 9.19 | 4.4.0-21-Generic para 4.4.0-131-generic,<br/>4.8.0-34-Generic para 4.8.0-58-generic,<br/>4.10.0-14-Generic para 4.10.0-42-generic,<br/>4.11.0-13-Generic para 4.11.0-14-generic,<br/>4.13.0-16-Generic para 4.13.0-45-generic,<br/>4.15.0-13-Generic para 4.15.0-30-generic<br/>4.11.0-1009-Azure para 4.11.0-1016-azure,<br/>4.13.0-1005-Azure para 4.13.0-1018-azure <br/>4.15.0-1012-Azure para 4.15.0-1019-azure|
16.04 LTS | 9.18 | 4.4.0-21-Generic para 4.4.0-128-generic,<br/>4.8.0-34-Generic para 4.8.0-58-generic,<br/>4.10.0-14-Generic para 4.10.0-42-generic,<br/>4.11.0-13-Generic para 4.11.0-14-generic,<br/>4.13.0-16-Generic para 4.13.0-45-generic,<br/>4.11.0-1009-Azure para 4.11.0-1016-azure,<br/>4.13.0-1005-Azure para 4.13.0-1018-azure |
16.04 LTS | 9.17 | 4.4.0-21-Generic para 4.4.0-124-generic,<br/>4.8.0-34-Generic para 4.8.0-58-generic,<br/>4.10.0-14-Generic para 4.10.0-42-generic,<br/>4.11.0-13-Generic para 4.11.0-14-generic,<br/>4.13.0-16-Generic para 4.13.0-41-generic,<br/>4.11.0-1009-Azure para 4.11.0-1016-azure,<br/>4.13.0-1005-Azure para 4.13.0-1016-azure |
16.04 LTS | 9.16 | 4.4.0-21-Generic para 4.4.0-119-generic,<br/>4.8.0-34-Generic para 4.8.0-58-generic,<br/>4.10.0-14-Generic para 4.10.0-42-generic,<br/>4.11.0-13-Generic para 4.11.0-14-generic,<br/>4.13.0-16-Generic para 4.13.0-38-generic,<br/>4.11.0-1009-Azure para 4.11.0-1016-azure,<br/>4.13.0-1005-Azure para 4.13.0-1012-azure |


#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Versões do Debian kernel com suporte para máquinas virtuais do Azure

**Versão** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
Debian 7 | 9.17,9.18,9.19 | 3.2.0-4-AMD64 para 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 7 | 9.16 | 3.2.0-4-AMD64 para 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.19 | 3.16.0-4-AMD64 para 3.16.0-6-amd64, 4.9.0-0.bpo.4-amd64 para 4.9.0-0.bpo.7-amd64 |
Debian 8 | 9.17, 9.18 | 3.16.0-4-AMD64 para 3.16.0-6-amd64, 4.9.0-0.bpo.4-amd64 para 4.9.0-0.bpo.6-amd64 |
Debian 8 | 9.16 | 3.16.0-4-AMD64 para 3.16.0-5-amd64, 4.9.0-0.bpo.4-amd64 para 4.9.0-0.bpo.5-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Versões suportadas do kernel de 12 do SUSE Linux Enterprise Server para máquinas virtuais do Azure

**Versão** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | 9.19 | SP1 3.12.49-11-default para 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default para 3.12.74-60.64.93-default</br></br> SP2 4.4.21-69-default para 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default para 4.4.121-92.80-default</br></br>4.4.73-5-default SP3 para 4.4.140-94.42-default |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | 9.18 | SP1 3.12.49-11-default para 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default para 3.12.74-60.64.93-default</br></br> SP2 4.4.21-69-default para 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default para 4.4.121-92.80-default</br></br>4.4.73-5-default SP3 para 4.4.138-94.39-default |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | 9.17 | SP1 3.12.49-11-default para 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default para 3.12.74-60.64.88-default</br></br> SP2 4.4.21-69-default para 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default</br></br>4.4.73-5-default SP3 para 4.4.126-94.22-default |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Replicar máquinas - armazenamento de convidado/sistema de ficheiros do Linux

* Sistemas de ficheiros: ext3 ext4, ReiserFS (Suse Linux Enterprise Server apenas), XFS
* Gestor de volumes: LVM2
* O software MultiPath i: mapeador de dispositivo


## <a name="replicated-machines---compute-settings"></a>Máquinas replicadas - definições de computação

**Definição** | **Suporte** | **Detalhes**
--- | --- | ---
Tamanho | Qualquer tamanho de VM do Azure com, pelo menos, 2 núcleos de CPU e 1 GB de RAM | Certifique-se [tamanhos de máquina virtual do Azure](../virtual-machines/windows/sizes.md).
Conjuntos de disponibilidade | Suportadas | Se ativar a replicação para uma VM do Azure com as opções padrão, um conjunto de disponibilidade é criado automaticamente com base nas definições de região de origem. Pode modificar estas definições.
Zonas de disponibilidade | Não suportado | Atualmente, não é possível replicar as VMs implementadas em zonas de disponibilidade.
Benefício de utilização híbrida (HUB) | Suportadas | Se a VM de origem tem uma licença HUB ativada, uma ativação pós-falha de teste ou efetuar a ativação pós-falha VM utiliza também a licença HUB.
Conjuntos de dimensionamento de VMs | Não suportado |
Imagens da galeria do Azure - Microsoft publicado | Suportadas | Suportada se a VM é executado num sistema operativo suportado.
Imagens da galeria do Azure - publicado de terceiros | Suportadas | Suportada se a VM é executado num sistema operativo suportado.
Imagens personalizadas - publicado de terceiros | Suportadas | Suportada se a VM é executado num sistema operativo suportado.
VMs migradas com o Site Recovery | Suportadas | Se uma VM de VMware ou máquina física foi migrada para o Azure com o Site Recovery, terá de desinstalar a versão mais antiga do serviço de mobilidade em execução na máquina e reinicie a máquina antes de replicá-los para outra região do Azure.

## <a name="replicated-machines---disk-actions"></a>Replicar máquinas - ações de disco

**Ação** | **Detalhes**
-- | ---
Redimensionar disco numa VM replicada | Suportadas
Adicionar um disco a uma VM replicada | Não suportado.<br/><br/> Precisa para desativar a replicação para a VM, adicione o disco e, em seguida, ative novamente a replicação.

## <a name="replicated-machines---storage"></a>Máquinas replicadas - armazenamento

Esta tabela resumidos suporte para o disco de SO de VM do Azure, disco de dados e disco temporário.

- É importante observar os limites de disco VM e os destinos para [Linux](../virtual-machines/linux/disk-scalability-targets.md) e [Windows](../virtual-machines/windows/disk-scalability-targets.md) VMs para evitar problemas de desempenho.
- Se implementar com as configurações padrão, o Site Recovery cria automaticamente os discos e contas de armazenamento com base nas definições de origem.
- Se personalizar, certifique-se de que seguir as diretrizes.

**Componente** | **Suporte** | **Detalhes**
--- | --- | ---
Tamanho máximo do disco de SO | 2048 GB | [Saiba mais](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms) acerca dos discos VM.
Disco temporário | Não suportado | O disco temporário é sempre excluído da replicação.<br/><br/> Não quaisquer dados persistentes no disco temporário. [Saiba mais](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk).
Tamanho máximo do disco de dados | 4095 GB |
Número máximo de disco de dados | Acordo em até 64, com suporte para um tamanho de VM do Azure específico | [Saiba mais](../virtual-machines/windows/sizes.md) sobre tamanhos de VM.
Taxa de alteração de disco de dados | Máximo de 10 MBps por disco para armazenamento premium. Máximo de 2 MBps por disco de armazenamento Standard. | Se a taxa de alteração os dados média no disco é continuamente maior do que o máximo, replicação não acompanhar.<br/><br/>  No entanto, se o número máximo for excedido esporadicamente, pode acompanhar a replicação, mas pode ver pontos de recuperação um pouco atrasado.
Disco de dados - conta de armazenamento standard | Suportadas |
Disco de dados - conta de armazenamento premium | Suportadas | Se uma VM tem discos espalhadas em contas de armazenamento standard e premium, pode selecionar uma conta de armazenamento de destino diferente para cada disco, para garantir que tem a mesma configuração de armazenamento na região de destino.
Disco gerido - standard | Suportada nas regiões do Azure em que o Azure Site Recovery é suportado. |  
Disco gerido - premium | Suportada nas regiões do Azure em que o Azure Site Recovery é suportado. |
Redundância | LRS e GRS são suportadas.<br/><br/> O ZRS não é suportado.
Armazenamento de acesso esporádico e frequente | Não suportado | Discos de VM não são suportados no armazenamento de acesso esporádico e frequente
Espaços de armazenamento | Suportadas |         
Encriptação em repouso (SSE) | Suportadas | O SSE é a predefinição nas contas de armazenamento.   
Azure Disk Encryption (ADE) para o sistema operacional do Windows | As VMs ativadas para [encryption com a aplicação do Azure AD](https://aka.ms/ade-aad-app) são suportados |
Azure Disk Encryption (ADE) para o SO Linux | Não suportado |
Disco de acesso frequente Adicionar/remover | Não suportado | Se adicionar ou remover o disco de dados na VM, terá de desativar a replicação e ative a replicação novamente para a VM.
Excluir o disco | Não suportado|   Disco temporário é excluído por predefinição.
Espaços de armazenamento direto  | Não suportado|
Servidor de ficheiros de escalamento horizontal  | Não suportado|
LRS | Suportadas |
GRS | Suportadas |
RA-GRS | Suportadas |
ZRS | Não suportado |  
Armazenamento de acesso esporádico e frequente | Não suportado | Discos da máquina virtual não são suportados no armazenamento de acesso esporádico e frequente
Firewalls de armazenamento do Azure para redes virtuais  | Sim | Se estão a restringir o acesso de rede virtual para contas de armazenamento, certifique-se de que os serviços Microsoft fidedignos tenham permissão para aceder à conta de armazenamento.
Contas de armazenamento para fins gerais V2 (camada de acesso tanto frequente ou esporádico) | Não | Aumento dos custos de transação substancialmente em comparação com fins gerais V1 contas de armazenamento

>[!IMPORTANT]
> Certifique-se de que observar que os destinos de escalabilidade e desempenho disco VM para [Linux](../virtual-machines/linux/disk-scalability-targets.md) ou [Windows](../virtual-machines/windows/disk-scalability-targets.md) máquinas virtuais para evitar quaisquer problemas de desempenho. Se seguir as configurações padrão, o Site Recovery irá criar os discos necessários e as contas de armazenamento com base na configuração da origem. Se personaliza e selecionar suas próprias definições, certifique-se de que segue os destinos de escalabilidade e desempenho de disco para as VMs de origem.

## <a name="replicated-machines---networking"></a>Máquinas replicadas - rede
**Configuração** | **Suporte** | **Detalhes**
--- | --- | ---
NIC | Número máximo suportado para um tamanho de VM do Azure pecíficas | NICs são criados quando a VM é criada durante a ativação pós-falha.<br/><br/> O número de NICs na VM de ativação pós-falha depende do número de NICs na VM de origem quando a replicação foi ativada. Se adicionar ou remover um NIC depois de ativar a replicação, não afetar o número de NICs na VM replicada após a ativação pós-falha.
Balanceador de Carga de Externo | Suportadas | Associe o Balanceador de carga pré-configurada com um script de automatização do Azure num plano de recuperação.
Balanceador de carga interno | Suportadas | Associe o Balanceador de carga pré-configurada com um script de automatização do Azure num plano de recuperação.
Endereço IP público | Suportadas | Associar um endereço IP público existente com a NIC. Em alternativa, crie um endereço IP público e associe-ao NIC com usando um script de automatização do Azure num plano de recuperação.
NSG em NIC | Suportadas | Associe o NSG ao NIC com usando um script de automatização do Azure num plano de recuperação.  
NSG na sub-rede | Suportadas | Associe o NSG da sub-rede a utilizar um script de automatização do Azure num plano de recuperação.
Endereço IP reservado (estático) | Suportadas | Se a NIC na VM de origem tem um endereço IP estático e a sub-rede de destino tiver o mesmo endereço IP disponível, é atribuído para a VM ativação pós-falha.<br/><br/> Se a sub-rede de destino não tiver o mesmo endereço IP disponível, um dos endereços IP disponíveis na sub-rede está reservado para a VM.<br/><br/> Também pode especificar um endereço IP fixo e uma sub-rede numa **itens replicados** > **definições** > **computação e rede**  >  **Interfaces de rede**.
Endereço IP dinâmico | Suportadas | Se a NIC de origem tiver de endereçamento de IP dinâmico, a NIC na ativação pós-falha VM também é dinâmica por padrão.<br/><br/> Pode modificar esta para um endereço IP fixo se necessário.
Gestor de Tráfego     | Suportadas | Pode pré-configurar o Gestor de tráfego, de modo a que o tráfego é encaminhado para o ponto final na região de origem em intervalos regulares e para o ponto final na região de destino em caso de ativação pós-falha.
DNS do Azure | Suportadas |
DNS Personalizado  | Suportadas |    
Proxy não autenticados | Suportadas | Consulte [documento de orientação para funcionamento em rede.](site-recovery-azure-to-azure-networking-guidance.md)    
Proxy autenticado | Não suportado | Se a VM estiver a utilizar um proxy autenticado para conectividade de saída, não podem ser replicada com o Azure Site Recovery.    
VPN site a Site no local (com ou sem o ExpressRoute)| Suportadas | Certifique-se de que as UDRs e NSGs estão configurados de forma que o tráfego de recuperação de Site não é encaminhado para o local. Consulte [documento de orientação para funcionamento em rede.](site-recovery-azure-to-azure-networking-guidance.md)  
Ligação VNET a VNET | Suportadas | Consulte [documento de orientação para funcionamento em rede.](site-recovery-azure-to-azure-networking-guidance.md)  
Pontos Finais de Serviço de Rede Virtual | Suportadas | Se estão a restringir o acesso de rede virtual para contas de armazenamento, certifique-se de que os serviços Microsoft fidedignos tenham permissão para aceder à conta de armazenamento. 
Redes Aceleradas | Suportadas | Funcionamento em rede acelerado tem de estar ativado na VM de origem. [Saiba mais](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>Passos Seguintes
- Leia [documentação de orientação para replicar VMs do Azure de rede](site-recovery-azure-to-azure-networking-guidance.md).
- Implementar a recuperação de desastres por [replicar VMs do Azure](site-recovery-azure-to-azure.md).
