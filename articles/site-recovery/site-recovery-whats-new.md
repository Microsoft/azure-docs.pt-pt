---
title: O que há de novo no Azure Site Recovery | Documentos da Microsoft
description: Fornece um resumo dos novos recursos introduzidos no Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 50e1cb95249f0108430e978ae3ffe23b6edc778d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418394"
---
# <a name="whats-new-in-site-recovery"></a>Novidades no Site Recovery

O [do Azure Site Recovery](site-recovery-overview.md) serviço é atualizado e melhorado de forma contínua. Para ajudar a manter-se atualizado, este artigo fornece informações sobre as versões mais recentes, novos recursos e novo conteúdo. Esta página é atualizada regularmente.

Se tiver sugestões para recursos de recuperação de Site, Adoraríamos [ouvir os seus comentários](https://feedback.azure.com/forums/256299-site-recovery).


## <a name="updates-march-2019"></a>Atualizações (Março de 2019)

### <a name="update-rollup-35"></a>O Update rollup 35

[Coletânea de 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para os agentes de recuperação de Site e fornecedores (conforme detalhado no rollup)
**Correções/melhoramentos de problemas** | Diversas correções e melhorias (conforme detalhado no rollup)

#### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastre do VMware/servidor físico
Novos recursos adicionados na atualização.

**Funcionalidade** | **Detalhes**
--- | ---
**Discos geridos** | Replicação de VMs de VMware no local e servidores físicos está agora diretamente para os managed disks no Azure. No local são enviados dados para uma conta de armazenamento de cache no Azure e pontos de recuperação são criados em discos geridos na localização de destino. Isto garante que não precisa de gerir várias contas de armazenamento de destino.
**Servidor de configuração** | Site Recovery agora suporta servidores de configuração com várias NICs. Tem de adicionar adaptadores adicionais para a VM do servidor de configuração antes de registar o servidor de configuração no cofre. Se adicionar, posteriormente, terá de voltar a registar o servidor no cofre.


## <a name="updates-february-2019"></a>Atualizações (Fevereiro de 2019)

### <a name="update-rollup-34"></a>O Update rollup 34 

[Coletânea de 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para os agentes de recuperação de Site e fornecedores (conforme detalhado no rollup).
**Correções/melhoramentos de problemas** | Um número de correções e melhorias (conforme detalhado no rollup).


### <a name="update-rollup-33"></a>O Update rollup 33 

[Coletânea de 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para os agentes de recuperação de Site e fornecedores (conforme detalhado no rollup).
**Correções/melhoramentos de problemas** | Um número de correções e melhorias (conforme detalhado no rollup).


#### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastre VM do Azure 
Novos recursos adicionados na atualização.

**Funcionalidade** | **Detalhes**
--- | ---
**Mapeamento da rede** | Para recuperação após desastre de VM do Azure, agora, pode utilizar qualquer rede de destino disponíveis quando ativa a replicação. 
**Standard SSD** | Agora pode configurar a recuperação após desastre para VMs do Azure utilizando [discos Standard SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Espaços de armazenamento direto** | Pode configurar a recuperação após desastre para aplicações em execução em aplicações de VM do Azure, utilizando [espaços de armazenamento direto](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) para elevada disponibilidade.  Utilizar espaços de armazenamento direto (S2D), juntamente com o Site Recovery fornece proteção abrangente das cargas de trabalho de VM do Azure. O S2D permite-lhe alojar num cluster de convidados no Azure. Isso é especialmente útil quando uma VM aloja um aplicativo crítico, como a camada de SAP ASCS, o SQL Server ou o servidor de ficheiros de escalamento horizontal.


#### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastre do VMware/servidor físico
Novos recursos adicionados na atualização.

**Funcionalidade** | **Detalhes**
--- | ---
**Sistema de ficheiros de Linux BRTFS** | Recuperação de sites suporta agora a replicação de VMware VMs com o sistema de ficheiros BRTFS. Não é suportada a replicação se:<br/><br/>-O volume de frações de sistema de ficheiros BTRFS é alterado depois de ativar a replicação.<br/><br/>-O sistema de ficheiros está distribuído por vários discos.<br/><br/>-O sistema de ficheiros BTRFS suporta RAID.
**Windows Server 2019** | Suporte adicionado para computadores que executam o Windows Server 2019.


## <a name="updates-january-2019"></a>Atualizações (Janeiro de 2019)

### <a name="accelerated-networking-azure-vms"></a>Funcionamento em rede acelerado (as VMs do Azure)

Funcionamento em rede acelerado ativar Virtualização de e/s de raiz única (SR-IOV) para uma VM, melhorando o desempenho do sistema de rede. Quando ativa a replicação para uma VM do Azure, o Site Recovery Deteta se o funcionamento em rede acelerado está ativado. Se é, depois de ativação pós-falha configura automaticamente o Site Recovery accelerated networking da VM do Azure, de réplica de destino para ambos [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) e [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

[Saiba mais](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>O Update rollup 32 

[Coletânea de 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para os agentes de recuperação de Site e fornecedores (conforme detalhado no rollup).
**Correções/melhoramentos de problemas** | Um número de correções e melhorias (conforme detalhado no rollup).

#### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastre VM do Azure

Novos recursos adicionados na atualização.

**Funcionalidade** | **Detalhes**
--- | ---
**Suporte do Linux** | Foi adicionado suporte para a estação de trabalho do Red Hat 6 horas dia, 7 e novas versões de kernel para Ubuntu, Debian e SUSE.
**Espaços de armazenamento direto** | Site Recovery suporta VMs do Azure com espaços de armazenamento direto (S2D).

#### <a name="vmware-vmsphysical-servers-replication"></a>Replicação de servidores de VMs de VMware/físico 
**Funcionalidade** | **Detalhes**
--- | ---
**Suporte do Linux** | Foi adicionado suporte para a VM de Redhat Enterprise Linux 7,6, estação de trabalho do Red Hat 6 horas dia, 7, Oracle Linux 6.10/7,6 e novas versões de kernel para Ubuntu, Debian e SUSE.


### <a name="update-rollup-31"></a>O Update rollup 31 

[Update rollup 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para os agentes de recuperação de Site e fornecedores (conforme detalhado no rollup).
**Correções/melhoramentos de problemas** | Um número de correções e melhorias (conforme detalhado no rollup).

#### <a name="vmware-vmsphysical-servers-replication"></a>Replicação de servidores de VMs de VMware/físico 
Novos recursos adicionados na atualização.

**Funcionalidade** | **Detalhes**
--- | ---
**Suporte do Linux** | Foi adicionado suporte para Oracle Linux 6.8 e 6.9/7.0 e para o kernel de UEK5.
**LVM** | Suporte adicionado para volumes LVM e LVM2.<br/><br/> O diretório de /boot numa partição de disco e em volumes LVM é agora suportado.
**Diretórios** | Foi adicionado suporte para esses diretórios definido como partições separadas ou sistemas de ficheiros que não estão no mesmo disco de sistema:<br/><br/> /(root), /boot, /usr, /usr/local, /var, /etc.
**Windows Server 2008** | Suporte adicionado para discos dinâmicos.
**Ativação pós-falha** | Tempo de ativação pós-falha melhorada para onde storvsc e vsbus não são controladores de arranque de VMs de VMware.
**Suporte UEFI** | VMs do Azure não suportam o tipo de arranque UEFI. Agora, pode migrar servidores físicos no local com a UEFI para o Azure com o Site Recovery. Recuperação de site efetua a migração do servidor ao converter o tipo de arranque para BIOS antes da migração. Recuperação de site anteriormente suportada essa conversão para VMs apenas. O suporte está disponível para os servidores físicos com o Windows Server 2012 ou posterior.

#### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastre VM do Azure
Novos recursos adicionados na atualização.

**Funcionalidade** | **Detalhes**
--- | ---
**Suporte do Linux** | Suporte foi adicionado para Oracle Linux 6.8 e 6.9/7.0; e para o kernel de UEK5.
**Sistema de ficheiros de Linux BRTFS** | Suportada para VMs do Azure.
**VMs do Azure em zonas de disponibilidade** | Pode ativar a replicação para outra região para VMs do Azure implementadas em zonas de disponibilidade. Agora pode ativar a replicação para uma VM do Azure e definir o destino para a ativação pós-falha para uma única instância VM, uma VM num conjunto de disponibilidade ou uma VM numa zona de disponibilidade. A definição não tem impacto na replicação. [Leitura](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) o anúncio.
**Armazenamento de firewall ativada (portal/PowerShell)** | Suporte adicionado para [contas de armazenamento de firewall ativada](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Pode replicar VMs do Azure com discos não geridos nas contas de armazenamento ativada de firewall para outra região do Azure para recuperação após desastre.<br/><br/> Pode utilizar contas de armazenamento com a firewall habilitada como contas de armazenamento de destino para discos não geridos.<br/><br/> Suportado no portal e com o PowerShell.

## <a name="updates-december-2018"></a>Atualizações (de Dezembro de 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Atualizações automáticas para o serviço de mobilidade (as VMs do Azure)

Recuperação de site adicionado uma opção para as atualizações automáticas para a extensão de serviço de mobilidade. A extensão de serviço de mobilidade está instalada em cada VM do Azure replicadas pelo Site Recovery. Quando ativa a replicação, selecione se pretende permitir a recuperação de sites gerir atualizações para a extensão.

As atualizações não requerem um reinício VM e não afetam os replicação. [Saiba mais](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Calculadora de preços para recuperação após desastre de VM do Azure

Recuperação após desastre de VMs do Azure incorre em custos de licenciamento de VM e os custos de armazenamento e rede. O Azure fornece um [Calculadora de preços](https://aka.ms/a2a-cost-estimator) para ajudar a descobrir esses custos. Recuperação de sites fornece agora uma [exemplos de preços estimativa](https://aka.ms/a2a-cost-estimator) que os preços de uma implementação de exemplo com base numa aplicação de três camadas com seis VMs com discos de HDD padrão 12 e 6 discos de Premium SSD.

- O exemplo pressupõe uma alteração de dados de 10 GB por dia para standard e 20 GB para premium.
- Para a sua implementação específica, pode alterar as variáveis de estimar os custos.
- Pode especificar o número de VMs, o número e tipo de discos geridos e o total de dados esperado altera a frequência esperada entre as VMs.
- Além disso, pode aplicar um fator de compactação para estimar os custos de largura de banda.

[Leitura](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) o anúncio.


## <a name="updates-october-2018"></a>Atualizações (Outubro de 2018)

### <a name="update-rollup-30"></a>O Update rollup 30 

[Update rollup 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para os agentes de recuperação de Site e fornecedores (conforme detalhado no rollup).
**Correções/melhoramentos de problemas** | Um número de correções e melhorias (conforme detalhado no rollup).

#### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastre VM do Azure
Novos recursos adicionados na atualização.

**Funcionalidade** | **Detalhes**
--- | ---
**Suporte de região** | Adicionado para a Austrália Central 1 e da Austrália Central 2 de suporte de recuperação de sites.
**Suporte para encriptação de disco** | Suporte adicionado para recuperação após desastre de VMs do Azure encriptado com encriptação de disco do Azure (ADE) com a aplicação do Azure AD. [Saiba mais](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Exclusão de disco** | Discos não inicializados agora são automaticamente excluídos durante a replicação de VM do Azure.
**Armazenamento de firewall ativada (PowerShell)** | Suporte adicionado para [contas de armazenamento de firewall ativada](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Pode replicar VMs do Azure com discos não geridos nas contas de armazenamento ativada de firewall para outra região do Azure para recuperação após desastre.<br/><br/> Pode utilizar contas de armazenamento com a firewall habilitada como contas de armazenamento de destino para discos não geridos.<br/><br/> Suportado apenas com o PowerShell.


### <a name="update-rollup-29"></a>O Update rollup 29 

[Update rollup 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para os agentes de recuperação de Site e fornecedores (conforme detalhado no rollup).
**Correções/melhoramentos de problemas** | Um número de correções e melhorias (conforme detalhado no rollup).


## <a name="updates-august-2018"></a>Atualizações (Agosto de 2018)

### <a name="update-rollup-28"></a>O Update rollup 28 

[Coletânea de 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para os agentes de recuperação de Site e fornecedores (conforme detalhado no rollup).
**Correções/melhoramentos de problemas** | Um número de correções e melhorias (conforme detalhado no rollup).

#### <a name="azure-vms-disaster-recovery"></a>Recuperação de desastre de VMs do Azure 
Novos recursos adicionados na atualização.

**Funcionalidade** | **Detalhes**
--- | ---
**Suporte do Linux** | Adicionado suporte para Red Hat Enterprise Linux 6.10; CentOS 6.10.<br/><br/>
**Suporte de cloud** | Suporte a recuperação após desastre para VMs do Azure na cloud da Alemanha.
**Recuperação de desastres entre subscrições** | Suporte para replicar VMs do Azure numa única região para outra região de uma subscrição diferente, no mesmo inquilino do Azure Active Directory. [Saiba mais](https://aka.ms/cross-sub-blog).

#### <a name="vmware-vmphysical-server-disaster-recovery"></a>Recuperação após desastre de VM de VMware/servidor físico 
Novos recursos adicionados na atualização.

**Funcionalidade** | **Detalhes**
--- | ---
**Suporte do Linux** | Suporte adicionado para 6.10 no Red Hat Enterprise Linux, CentOS 6.10.<br/><br/> VMs baseadas em Linux utilizar o estilo de partição de tabela (GPT) do GUID partição no modo de compatibilidade de BIOS legado são agora suportados. Reveja os [FAQ de VM do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) para obter mais informações. 
**Recuperação após desastre para VMs após a migração** | Suporte para ativar a recuperação após desastre para uma região secundária para um VM de VMware no local migrado para o Azure, sem precisar de desinstalar o Mobility service na VM antes de ativar a replicação.
**Windows Server 2008** | Suporte para a migração das máquinas Windows Server 2008 R2/2008 de 64 bits em execução e de 32 bits.<br/><br/> Migração apenas (replicação e ativação pós-falha). Não é suportada a reativação pós-falha.

## <a name="updates-july-2018"></a>Atualizações (Julho de 2018)

### <a name="update-rollup-27-july-2018"></a>O Update rollup 27 (Julho de 2018)

[Update rollup 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para os agentes de recuperação de Site e fornecedores (conforme detalhado no rollup).
**Correções/melhoramentos de problemas** | Um número de correções e melhorias (conforme detalhado no rollup).

#### <a name="azure-vms-disaster-recovery"></a>Recuperação de desastre de VMs do Azure 

Novos recursos adicionados na atualização.

**Funcionalidade** | **Detalhes**
--- | ---
**Suporte do Linux** | Suporte adicionado para Red Hat Enterprise Linux 7.5.

#### <a name="vmware-vmphysical-server-disaster-recovery"></a>Recuperação após desastre de VM de VMware/servidor físico 

Novos recursos adicionados na atualização.

**Funcionalidade** | **Detalhes**
--- | ---
**Suporte do Linux** | Suporte adicionado para 7.5 de Linux do Red Hat Enterprise, 12 do SUSE Linux Enterprise Server.



## <a name="next-steps"></a>Passos Seguintes

Mantenha-se atualizado com nossas atualizações sobre o [Azure atualiza as](https://azure.microsoft.com/updates/?product=site-recovery) página.
