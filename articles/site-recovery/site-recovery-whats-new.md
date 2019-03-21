---
title: O que há de novo no Azure Site Recovery | Documentos da Microsoft
description: Fornece um resumo dos novos recursos introduzidos no Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: raynew
ms.openlocfilehash: fece1adc9ad2f5844a7d6cc1e0e9cc92d44de6d8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57892936"
---
# <a name="whats-new-in-site-recovery"></a>Novidades no Site Recovery

O [do Azure Site Recovery](site-recovery-overview.md) serviço é atualizado e melhorado de forma contínua. Para ajudar a manter-se atualizado, este artigo fornece informações sobre as versões mais recentes, novos recursos e novo conteúdo. Esta página é atualizada regularmente.

Se tiver sugestões para recursos de recuperação de Site, Adoraríamos [ouvir os seus comentários](https://feedback.azure.com/forums/256299-site-recovery).

## <a name="q1-2019"></a>2019 P1 

### <a name="update-rollup-34-february-2019"></a>O Update rollup 34 (Fevereiro de 2019)

[Coletânea de 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para os agentes de recuperação de Site e fornecedores (conforme detalhado no rollup)
**Correções de problema** | Diversas correções e melhorias (conforme detalhado no rollup)



### <a name="update-rollup-33-february-2019"></a>O Update rollup 33 (Fevereiro de 2019)

[Coletânea de 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para os agentes de recuperação de Site e fornecedores (conforme detalhado no rollup)
**Correções de problema** | Diversas correções e melhorias (conforme detalhado no rollup)
**Mapeamento da rede** | Para recuperação após desastre de VM do Azure, agora, pode utilizar qualquer rede de destino disponíveis quando ativa a replicação. 
**Standard SSD** | Agora pode configurar a recuperação após desastre para VMs do Azure utilizando [discos Standard SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Espaços de armazenamento direto** | Pode configurar a recuperação após desastre para aplicações em execução em aplicações de VM do Azure, utilizando [espaços de armazenamento direto](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) para elevada disponibilidade.
**Sistema de ficheiros BRTFS** | Suportada para VMs de VMware, além de VMs do Azure.<br/><br/> Não é suportada se: O volume de frações de sistema de ficheiros BTRFS é alterado depois de ativar a replicação, o sistema de ficheiros está distribuído por vários discos ou se o sistema de ficheiros as BTRFS suporta RAID.



### <a name="update-rollup-32-january-2019"></a>O Update rollup 32 (Janeiro de 2019)

[Update rollup 31](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para os agentes de recuperação de Site e fornecedores (conforme detalhado no rollup)
**Correções de problema** | Diversas correções e melhorias (conforme detalhado no rollup)
**Recuperação após desastre para Linux** | **VMs do Azure**: Estação de trabalho do Red Hat 6/7; suporte para novas versões de kernel para Ubuntu, Debian e SUSE.<br/><br/> **VMware VMs/servidores físicos**: Red Hat Enterprise Linux a 7,6; Estação de trabalho do Red Hat 6/7; Oracle Linux 6.10/7,6; spport para novas versões de kernel para Ubuntu, Debian e SUSE.


### <a name="update-rollup-31-january-2019"></a>O Update rollup 31 (Janeiro de 2019)

[Update rollup 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para os agentes de recuperação de Site e fornecedores (conforme detalhado no rollup)
**Correções de problema** | Diversas correções e melhorias (conforme detalhado no rollup)
**Recuperação após desastre para Linux** | **VMs do Azure**: Oracle Linux 6.8 e 6.9/7.0; suporte para UEK5 kernels.<br/><br/> **VMware VMs/servidores físicos**: Oracle Linux 6.8 e 6.9/7.0; suporte para UEK5 kernel.
**Sistema de ficheiros BRTFS** | Suportada para VMs do Azure.
**LVM** | Suporte adicionado para volumes LVM e LVM2.<br/><br/> O diretório de /boot numa partição de disco e em volumes LVM é suportado.
**Diretórios** | Suporte adicionado para esses seet diretórios cópia de segurança, como partições separadas ou sistemas de ficheiros que não estão no mesmo disco de sistema: / (raiz), /boot, /usr, /usr/local., /var, /etc.
**Windows Server 2008** | Suporte adicionado para discos dinâmicos.
**Ativação pós-falha da VM de VMware** | Tempo de ativação pós-falha melhorada para onde storvsc e vsbus não são controladores de arranque de VMs de VMware.
**Suporte UEFI** | VMs do Azure não suportam o tipo de arranque UEFI. Agora, pode migrar servidores físicos no local com a UEFI para o Azure com o Site Recovery. Recuperação de site efetua a migração do servidor ao converter o tipo de arranque para BIOS antes da migração. Recuperação de site anteriormente suportada essa conversão para VMs apenas. O suporte está disponível para os servidores físicos com o Windows Server 2012 ou posterior.
**VMs do Azure em zonas de disponibilidade** | Pode ativar a replicação para outra região para VMs do Azure implementadas em zonas de disponibilidade. UO pode agora ativar a replicação para uma VM do Azure e definir o destino para a ativação pós-falha para uma única instância VM, uma VM num conjunto de disponibilidade ou uma VM numa zona de disponibilidade. A definição não tem impacto na replicação. [Leitura](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) o anúncio.


## <a name="q4-2018"></a>2018 P4

### <a name="update-rollup-30-october-2018"></a>O Update rollup 30 (Outubro de 2018)

[Update rollup 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para os agentes de recuperação de Site e fornecedores (conforme detalhado no rollup)
**Correções de problema** | Diversas correções e melhorias (conforme detalhado no rollup)
**Suporte de região** | Adicionado para a Austrália Central 1 e da Austrália Central 2 de suporte de recuperação de sites.
**Suporte para encriptação de disco** | Suporte adicionado para recuperação após desastre de VMs do Azure encriptado com encriptação de disco do Azure (ADE) com a aplicação do Azure AD. [Saiba mais](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Exclusão de disco** | Discos unitialized agora são automaticamente excluídos durante a replicação de VM do Azure.
**Armazenamento de firewall ativada** | Suporte adicionado para [contas de armazenamento de firewall ativada](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Pode replicar VMs do Azure com discos não geridos nas contas de armazenamento ativada de firewall para outra região do Azure para recuperação após desastre.<br/><br/> Pode utilizar contas de armazenamento com a firewall habilitada como contas de armazenamento de destino para discos não geridos.<br/><br/> Suportado apenas com o PowerShell.


### <a name="update-rollup-29-october-2018"></a>O Update rollup 29 (Outubro de 2018)

[Update rollup 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para os agentes de recuperação de Site e fornecedores (conforme detalhado no rollup)
**Correções de problema** | Diversas correções e melhorias (conforme detalhado no rollup)

### <a name="automatic-updates-for-the-mobility-service-extension"></a>Atualizações automáticas para a extensão de serviço de mobilidade

Recuperação de site adicionado uma opção para as atualizações automáticas para a extensão de serviço de mobilidade. A extensão de serviço de mobilidade está instalada em cada VM do Azure replicadas pelo Site Recovery. Quando ativa a replicação, selecione se pretende permitir a recuperação de sites gerir atualizações para a extensão. As atualizações não requerem um reinício VM e não afetam os replicação. [Saiba mais](azure-to-azure-autoupdate.md).

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>Recuperação após desastre para VMs com o accelerated networking

Funcionamento em rede acelerado ativar Virtualização de e/s de raiz única (SR-IOV) para uma VM, melhorando o desempenho do sistema de rede. Quando ativa a replicação para uma VM do Azure, o Site Recovery Deteta se o funcionamento em rede acelerado está ativado. Se é, depois de ativação pós-falha configura automaticamente o Site Recovery accelerated networking da VM do Azure, de réplica de destino para ambos [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) e [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms). [Saiba mais](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Calculadora de preços para recuperação após desastre de VM do Azure

Recuperação após desastre para VMs do Azure incorre em custos de licenciamento de VM e os custos de armazenamento e rede. O Azure fornece um [Calculadora de preços](https://aka.ms/a2a-cost-estimator) para ajudar a descobrir esses custos. Recuperação de sites fornece agora uma [exemplos de preços estimativa](https://aka.ms/a2a-cost-estimator) que os preços de uma implementação de exemplo com base numa aplicação de três camadas com seis VMs com discos de HDD padrão 12 e 6 discos de Premium SSD. O exemplo presumem uma alteração de dados de 10 GB por dia para standard e 20 GB para premium. Para a sua implementação específica, pode alterar as variáveis de estimar os custos. Pode especificar o número de VMs, o número e tipo de discos geridos e o total de dados esperado altera a frequência esperada entre as VMs. Além disso, pode aplicar um fator de compactação para estimar os custos de largura de banda. [Leitura](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) o anúncio.



## <a name="q3-2018"></a>2018 P3 


### <a name="update-rollup-28-august-2018"></a>O Update rollup 28 (Agosto de 2018)

[Coletânea de 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para os agentes de recuperação de Site e fornecedores (conforme detalhado no rollup)
**Recuperação após desastre para Linux** | **VMs do Azure**: Adicionado suporte para Red Hat Enterprise Linux 6.10; CentOS 6.10.<br/><br/> **VMs de VMware**: RedHat Enterprise Linux 6.10; CentOS 6.10.<br/><br/> VMs baseadas em Linux utilizar o estilo de partição de tabela (GPT) do GUID partição no modo de compatibilidade de BIOS legado são agora suportados.
**Suporte de cloud** | Suporte a recuperação após desastre para VMs do Azure na cloud da Alemanha.
**Recuperação de desastres entre subscrições** | Suporte para replicar VMs do Azure numa única região para outra região de uma subscrição diferente, no mesmo inquilino do Azure Active Directory. [Saiba mais](https://aka.ms/cross-sub-blog).
**Windows Server 2008** | Suporte para a migração das máquinas Windows Server 2008 R2/2008 de 64 bits em execução e de 32 bits.<br/><br/> Migração apenas (replicação e ativação pós-falha). Não é suportada a reativação pós-falha.

### <a name="update-rollup-27-july-2018"></a>O Update rollup 27 (Julho de 2018)

[Update rollup 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para os agentes de recuperação de Site e fornecedores (conforme detalhado no rollup)
**Recuperação após desastre para Linux** | **VMs do Azure**: Red Hat Enterprise Linux 7.5<br/><br/> **VMware VMs/servidores físicos**: Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12




## <a name="next-steps"></a>Passos Seguintes

Mantenha-se atualizado com nossas atualizações sobre o [Azure atualiza as](https://azure.microsoft.com/updates/?product=site-recovery) página.




 









