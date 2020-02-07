---
title: O que há de novo no Azure Site Recovery
description: Fornece um resumo dos novos recursos e das atualizações mais recentes no serviço de Azure Site Recovery.
ms.topic: conceptual
ms.date: 01/31/2020
ms.openlocfilehash: cae653c9e51e2a497d6fa3781415a1f836053593
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064534"
---
# <a name="whats-new-in-site-recovery"></a>Novidades no Site Recovery

O serviço de recuperação do [site Azure](site-recovery-overview.md) é atualizado e melhorado de forma contínua. Para ajudá-lo a se manter atualizado, este artigo fornece informações sobre as versões mais recentes, novos recursos e novos conteúdos. Esta página é atualizada regularmente.

Pode seguir e subscrever notificações de atualização do Site Recovery no canal [de atualizações do Azure.](https://azure.microsoft.com/updates/?product=site-recovery)

## <a name="supported-updates"></a>Atualizações com suporte

Para componentes de Site Recovery, damos suporte a versões N-4, em que N é a versão mais recente lançada. Estes são resumidos na tabela seguinte.

**Atualizar** |  **Configuração unificada** | **O óvulo do servidor de configuração** | **Agente de serviço de mobilidade** | **Fornecedor de recuperação de site** | **Agente de Serviços de Recuperação**
--- | --- | --- | --- | --- | ---
[Rollup 43](https://support.microsoft.com/help/4537047/) | 9.31.5449.1 | 5.1.5300.0 | 9.31.5449.1 | 5.1.5300.0 | 2.0.9165.0
[Rollup 42](https://support.microsoft.com/help/4531426/) | 9.30.5407.1 | 5.1.5200.0 | 9.30.5407.1 | 5.1.5200.0 | 2.0.9165.0
[Rollup 41](https://support.microsoft.com/help/4528026/) | 9.29.5367.1 | 5.1.5000.0 | 9.29.5367.1 | 5.1.5000.0 | 2.0.9165.0
[Rollup 40](https://support.microsoft.com/help/4521530/) | 9.28.5345.1 | 5.1.4800.0 | 9.28.5345.1 | 5.1.4800.0 | 2.0.9165.0
[Rollup 39](https://support.microsoft.com/help/4517283/) | 9.27.5308.1 | 5.1.4600.0 | 9.27.5308.1 | 5.1.4600.0 | 2.0.9165.0

[Saiba mais](service-updates-how-to.md) sobre a instalação e suporte da atualização.

## <a name="updates-january-2020"></a>Atualizações (janeiro de 2020)

### <a name="update-rollup-44"></a>Atualizar rollup 44

[A atualização do rollup 44](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Não houve atualizações para os fornecedores e agentes de Recuperação do Site.
**Correções/melhorias de problemas** | Uma série de correções e melhorias conforme detalhado no rollup.

### <a name="azure-vmware-disaster-recovery"></a>Recuperação de desastres azure VMware

As máquinas virtuais Azure suportam agora vMs ativados para encriptação em repouso com chaves geridas pelo cliente. [Saiba mais](azure-to-azure-how-to-enable-replication-cmk-disks.md).


### <a name="update-rollup-43"></a>Pacote cumulativo de atualizações 43

[A atualização do rollup 43](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações para Site Recovery agentes e provedores (conforme detalhado no acúmulo)
**Correções/melhorias de problemas** | Várias correções e aprimoramentos (conforme detalhado no acúmulo)


## <a name="updates-november-2019"></a>Atualizações (novembro de 2019)

### <a name="update-rollup-42"></a>Pacote cumulativo de atualizações 42

[A atualização do rollup 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações para Site Recovery agentes e provedores (conforme detalhado no acúmulo)
**Correções/melhorias de problemas** | Várias correções e aprimoramentos (conforme detalhado no acúmulo)


### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastre da VM do Azure

Os novos recursos para a recuperação de desastres de VM do Azure são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**UEFI** | O Site Recovery agora dá suporte à recuperação de desastres para VMs do Azure com arquitetura de inicialização baseada em UEFI.
**Linux** | O Site Recovery agora dá suporte a VMs do Azure executando o Linux com Azure Disk Encryption (ADE).
**Geração 2** | Todas as VMs do Azure de geração 2 agora têm suporte para recuperação de desastre.
**Regiões** | Agora, você pode habilitar a recuperação de desastres para VMs do Azure na região da Noruega.

### <a name="vmware-to-azure-disaster-recovery"></a>VMware para recuperação após desastre do Azure

Novos recursos para a recuperação de desastre do VMware para o Azure são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**UEFI** | O Site Recovery agora dá suporte à recuperação de desastres para VMs VMware com arquitetura de inicialização baseada em UEFI.<br/><br/> Os sistemas operacionais com suporte incluem o Windows Server 2019, o Windows Server 2016, o Windows Server 2012 R2, o Windows Server 2012, o SLES 12 SP4, o RHEL 8.

## <a name="update-to-servicing-stack-updatesha-2"></a>Atualizar para atualização da pilha de manutenção/SHA-2

Para a recuperação de desastre de VMs do Azure para uma região secundária ou VMs VMware locais ou servidores físicos para o Azure, observe o seguinte:

- Da versão 9.30.5407.1 da extensão do serviço de mobilidade (para VMs do Azure) e do agente de serviço de mobilidade (para VMware/máquinas físicas), alguns sistemas operacionais de máquina devem estar executando a atualização da pilha de manutenção e o SHA-2. Os detalhes são mostrados na tabela a seguir.
- Instale a atualização e o SHA-2 de acordo com a base de conhecimento vinculada. O SHA-1 não tem suporte de setembro de 2019 e, se a assinatura de código SHA-2 não estiver habilitada, a extensão do agente não será instalada/atualizada conforme o esperado.
- Saiba mais sobre a [atualização e requisitos sha-2.](https://aka.ms/SHA-2KB)

**Sistema operativo** | **VM do Azure** | **VMware VM/máquina física**
--- | --- | ---
**Windows 2008 R2 SP1** | [Atualização da pilha de manutenção](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Atualização da pilha de manutenção](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 2008 SP2** | [Atualização da pilha de manutenção](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Atualização da pilha de manutenção](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 7 SP1** | [Atualização da pilha de manutenção](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Atualização da pilha de manutenção](https://support.microsoft.com/help/4490628)<br/> [SHA-2.](https://support.microsoft.com/help/4474419)



## <a name="updates-october-2019"></a>Atualizações (outubro de 2019)

### <a name="update-rollup-41"></a>Pacote cumulativo de atualizações 41

[A atualização do rollup 41](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações para Site Recovery agentes e provedores (conforme detalhado no acúmulo)
**Correções/melhorias de problemas** | Várias correções e aprimoramentos (conforme detalhado no acúmulo)



### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastre da VM do Azure

Os novos recursos para a recuperação de desastres de VM do Azure são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Definições de failover de teste** | Ao configurar um failover de teste, agora você pode definir as configurações para a VM e a rede de failover de teste, incluindo o endereço IP, NSG, balanceamento de carga interno e o endereço IP público para cada NIC de máquina. Essas configurações são opcionais e não alteram o comportamento atual. Se você não definir essas configurações, poderá escolher uma VNet do Azure no momento do failover de teste. [Saiba mais](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/).
**Planos de recuperação** | Os planos de recuperação agora são limitados a 100 VMs, para garantir a confiabilidade do failover.

### <a name="vmware-to-azure-disaster-recovery"></a>VMware para recuperação após desastre do Azure

Novos recursos para a recuperação de desastre do VMware para o Azure são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Planos de recuperação** | Os planos de recuperação agora são limitados a 100 VMs, para garantir a confiabilidade do failover.


## <a name="updates-september-2019"></a>Atualizações (setembro de 2019)

### <a name="update-rollup-40"></a>Pacote cumulativo de atualizações 40

[A atualização do rollup 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações para Site Recovery agentes e provedores (conforme detalhado no acúmulo)
**Correções/melhorias de problemas** | Várias correções e aprimoramentos (conforme detalhado no acúmulo)




### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastre da VM do Azure

Os novos recursos para a recuperação de desastres de VM do Azure são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Limpeza após falha de volta** | Após o failover para o Azure secundário e, em seguida, o failback para a região primária, Site Recovery limpa automaticamente os computadores na região secundária. Não há necessidade de excluir manualmente as VMs e NICs.
**Falha no teste mantém endereço IP** | Agora você pode reter o endereço IP da VM de origem durante uma análise de recuperação de desastre e escolher um endereço IP estático para um failover de teste.

### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastre do VMware/servidor físico

Os recursos adicionados neste mês são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
Novos alertas do servidor de processo | Adicionamos novos alertas do servidor de processo. [Saiba mais](vmware-physical-azure-monitor-process-server.md).

### <a name="hyper-v-disaster-recovery"></a>Recuperação de desastres do Hyper-V

Os recursos adicionados neste mês são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
Conta de armazenamento | O Site Recovery agora dá suporte ao uso de contas de armazenamento com o firewall habilitado para a recuperação de desastre do Hyper-V para o Azure.  Você pode selecionar contas de armazenamento habilitadas para firewall como uma conta de destino ou para armazenamento em cache. Se você usar a conta habilitada para firewall, certifique-se de habilitar a opção para permitir serviços confiáveis da Microsoft.<br/><br/> Isso tem suporte para VMs do Hyper-V com ou sem o System Center VMM.


## <a name="updates-august-2019"></a>Atualizações (agosto de 2019)

### <a name="update-rollup-39"></a>Pacote cumulativo de atualizações 39

[O rollup atualizado 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações para Site Recovery agentes e provedores (conforme detalhado no acúmulo)
**Correções/melhorias de problemas** | Várias correções e aprimoramentos (conforme detalhado no acúmulo)


### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastre da VM do Azure

Os novos recursos para a recuperação de desastres de VM do Azure são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Encriptação sem Anúncio Azure** | A criptografia sem um aplicativo do Azure AD agora tem suporte para replicação de VM do Azure para discos gerenciados que executam o Windows.
**Recursos de rede para falhas** | Ao fazer failover para outra região, agora você pode anexar as configurações de recurso de rede (NSGs, balanceamento de carga, endereço IP público) a uma VM.

## <a name="updates-july-2019"></a>Atualizações (julho de 2019)

### <a name="update-rollup-38"></a>Atualizar rollup 38

[O rollup atualizado 38](https://support.microsoft.com/help/4513507/) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações para Site Recovery agentes e provedores (conforme detalhado no acúmulo)
**Correções/melhorias de problemas** | Várias correções e aprimoramentos (conforme detalhado no acúmulo)


### <a name="general"></a>Geral

O Site Recovery agora dá suporte ao uso de contas de armazenamento v2 de uso geral para armazenamento em cache ou armazenamento de destino. Anteriormente, apenas v1 tinha suporte.

### <a name="vmware-to-azure-disaster-recovery"></a>VMware para recuperação após desastre do Azure

Agora você pode replicar discos de até 8 TB ao replicar para uma VM do Azure com discos gerenciados.


## <a name="updates-june-2019"></a>Atualizações (junho de 2019)

### <a name="update-rollup-37"></a>Pacote cumulativo de atualizações 37

[O rollup atualizado 37](https://support.microsoft.com/help/4508614/) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações para Site Recovery agentes e provedores (conforme detalhado no acúmulo)
**Correções/melhorias de problemas** | Várias correções e aprimoramentos (conforme detalhado no acúmulo)


### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastre do VMware/servidor físico

Os recursos adicionados neste mês são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Divisórias GPT** | Do pacote cumulativo de atualizações 37 em diante (versão do serviço de mobilidade 9.25.5241.1), há suporte para até cinco partições GPT em UEFI. Antes desta atualização, quatro eram suportadas.



## <a name="updates-may-2019"></a>Atualizações (maio de 2019)

### <a name="update-rollup-36"></a>Atualizar rollup 36

[O rollup atualizado 36](https://support.microsoft.com/help/4503156) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para Site Recovery agentes e provedores (conforme detalhado no acúmulo)
**Correções/melhorias de problemas** | Várias correções e aprimoramentos (conforme detalhado no acúmulo)

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastre da VM do Azure

Os recursos adicionados neste mês são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Replicar discos adicionados** | Habilite a replicação para discos de dados adicionados a uma VM do Azure que já está habilitada para recuperação de desastre. [Saiba mais](azure-to-azure-enable-replication-added-disk.md).
**Atualizações automáticas** | Ao configurar atualizações automáticas para a extensão de serviço de mobilidade que é executada em VMs do Azure habilitadas para recuperação de desastre, agora você pode selecionar uma conta de automação existente a ser usada, em vez de usar a conta padrão criada por Site Recovery. [Saiba mais](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastre do VMware/servidor físico

Os recursos adicionados neste mês são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Monitorização do servidor de processos** | Para a recuperação de desastre de VMs VMware locais e servidores físicos, monitore e solucione problemas de servidor de processo com relatórios e alertas de integridade do servidor aprimorados. [Saiba mais](vmware-physical-azure-monitor-process-server.md).





## <a name="updates-march-2019"></a>Atualizações (março de 2019)

### <a name="update-rollup-35"></a>Pacote cumulativo de atualizações 35

[A atualização do rollup 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para Site Recovery agentes e provedores (conforme detalhado no acúmulo)
**Correções/melhorias de problemas** | Várias correções e aprimoramentos (conforme detalhado no acúmulo)

### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastre do VMware/servidor físico

Os recursos adicionados neste mês são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Discos geridos** | A replicação de VMs VMware locais e servidores físicos agora é diretamente para discos gerenciados no Azure. Os dados locais são enviados para uma conta de armazenamento de cache no Azure, e os pontos de recuperação são criados em discos gerenciados no local de destino. Isso garante que você não precisa gerenciar várias contas de armazenamento de destino.
**Servidor de configuração** | O Site Recovery agora dá suporte a servidores de configuração com várias NICs. Adicione outros adaptadores à VM do servidor de configuração antes de registrar o servidor de configuração no cofre. Se você adicionar posteriormente, precisará registrar novamente o servidor no cofre.


## <a name="updates-february-2019"></a>Atualizações (fevereiro de 2019)

### <a name="update-rollup-34"></a>Pacote cumulativo de atualizações 34

[O rollup atualizado 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para Site Recovery agentes e provedores (conforme detalhado no acúmulo).
**Correções/melhorias de problemas** | Várias correções e aprimoramentos (conforme detalhado no acúmulo).


### <a name="update-rollup-33"></a>Pacote cumulativo de atualizações 33

[A atualização do rollup 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para Site Recovery agentes e provedores (conforme detalhado no acúmulo).
**Correções/melhorias de problemas** | Várias correções e aprimoramentos (conforme detalhado no acúmulo).


### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastre da VM do Azure

Os recursos adicionados neste mês são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Mapeamento de rede** | Para a recuperação de desastre de VM do Azure, agora você pode usar qualquer rede de destino disponível quando habilitar a replicação.
**SSD padrão** | Agora pode configurar a recuperação de desastres para os VMs Azure utilizando [discos Standard SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Espaços de Armazenamento Direto** | Pode configurar a recuperação de desastres para apps que executam aplicações Do Azure VM utilizando espaços de [armazenamento direto](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) para alta disponibilidade.  Usar Espaços de Armazenamento Diretos (S2D) junto com Site Recovery fornece proteção abrangente de cargas de trabalho de VM do Azure. O S2D permite que você hospede um cluster de convidado no Azure. Isso é especialmente útil quando uma VM hospeda um aplicativo crítico, como o SAP ASCS Layer, o SQL Server ou o servidor de arquivos de escalabilidade horizontal.


### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastre do VMware/servidor físico

Os recursos adicionados neste mês são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Sistema de ficheiros Linux BRTFS** | O Site Recovery agora dá suporte à replicação de VMs VMware com o sistema de arquivos BRTFS. A replicação não terá suporte se:<br/><br/>-O subvolume do sistema de arquivos BTRFS é alterado após habilitar a replicação.<br/><br/>-O sistema de arquivos está espalhado por vários discos.<br/><br/>-O sistema de arquivos BTRFS dá suporte a RAID.
**Windows Server 2019** | Suporte adicionado para computadores que executam o Windows Server 2019.


## <a name="updates-january-2019"></a>Atualizações (janeiro de 2019)


### <a name="accelerated-networking-azure-vms"></a>Rede acelerada (VMs do Azure)

A rede acelerada habilita o SR-IOV (virtualização de e/s de raiz única) para uma VM, melhorando o desempenho da rede. Quando você habilita a replicação para uma VM do Azure, Site Recovery detecta se a rede acelerada está habilitada. Se for, após a failover Site Recovery configura automaticamente a rede acelerada na réplica-alvo Azure VM, tanto para [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) como [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

[Saiba mais](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>Pacote cumulativo de atualizações 32

[O rollup atualizado 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para Site Recovery agentes e provedores (conforme detalhado no acúmulo).
**Correções/melhorias de problemas** | Várias correções e aprimoramentos (conforme detalhado no acúmulo).

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastre da VM do Azure

Os recursos adicionados neste mês são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Suporte linux** | Foi adicionado suporte para o RedHat Workstation 6/7 e novas versões de kernel para Ubuntu, Debian e SUSE.
**Espaços de Armazenamento Direto** | O Site Recovery dá suporte a VMs do Azure usando Espaços de Armazenamento Diretos (S2D).

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>Recuperação de desastre de VMs VMware/servidores físicos

Os recursos adicionados neste mês são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Suporte linux** | Foi adicionado suporte para RedHat Enterprise Linux 7,6, RedHat Workstation 6/7, Oracle Linux 6,10 e Oracle Linux 7,6, e novas versões de kernel para Ubuntu, Debian e SUSE.


### <a name="update-rollup-31"></a>Pacote cumulativo de atualizações 31

[A atualização do rollup 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para Site Recovery agentes e provedores (conforme detalhado no acúmulo).
**Correções/melhorias de problemas** | Várias correções e aprimoramentos (conforme detalhado no acúmulo).

### <a name="vmware-vmsphysical-servers-replication"></a>Replicação de servidores físicos/VMs VMware

Os recursos adicionados neste mês são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Suporte linux** |  Foi adicionado suporte para Oracle Linux 6,8, Oracle Linux 6,9 e Oracle Linux 7,0 com kernel compatível com Red Hat e para o UEK (inbreakable Enterprise kernel) versão 5.
**LVM** | Suporte adicionado para volumes LVM e LVM2.<br/><br/> Agora há suporte para o diretório/boot em uma partição de disco e em volumes LVM.
**Diretórios** | O suporte foi adicionado para esses diretórios configurados como partições separadas ou sistemas de arquivos que não estão no mesmo disco do sistema:<br/><br/> /(root), /boot, /usr, /usr/local, /var, /etc.
**Windows Server 2008** | Suporte adicionado para discos dinâmicos.
**Ativação pós-falha** | Tempo de failover melhorado para VMs VMware em que storvsc e vsbus não são drivers de inicialização.
**Apoio uefi** | As VMs do Azure não dão suporte ao tipo de inicialização UEFI. Agora você pode migrar servidores físicos locais com UEFI para o Azure com o Site Recovery. Site Recovery migra o servidor convertendo o tipo de inicialização para BIOS antes da migração. Site Recovery anteriormente suportava essa conversão somente para VMs. O suporte está disponível para servidores físicos que executam o Windows Server 2012 ou posterior.

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastre da VM do Azure

Os recursos adicionados neste mês são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Suporte linux** | Foi adicionado suporte para Oracle Linux 6,8, Oracle Linux 6,9 e Oracle Linux 7,0 com kernel compatível com Red Hat e para o UEK (inbreakable Enterprise kernel) versão 5.
**Sistema de ficheiros Linux BRTFS** | Com suporte para VMs do Azure.
**VMs azure em zonas de disponibilidade** | Você pode habilitar a replicação para outra região para VMs do Azure implantadas em zonas de disponibilidade. Agora você pode habilitar a replicação para uma VM do Azure e definir o destino para failover para uma única instância de VM, uma VM em um conjunto de disponibilidade ou uma VM em uma zona de disponibilidade. A configuração não afeta a replicação. [Leia](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) o anúncio.
**Armazenamento ativado por firewall (portal/PowerShell)** | Suporte adicionado para [contas de armazenamento ativadas por firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Você pode replicar VMs do Azure com discos não gerenciados em contas de armazenamento habilitadas para firewall para outra região do Azure para recuperação de desastre.<br/><br/> Você pode usar contas de armazenamento habilitadas para firewall como contas de armazenamento de destino para discos não gerenciados.<br/><br/> Com suporte no portal e usando o PowerShell.

## <a name="updates-december-2018"></a>Atualizações (dezembro de 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Atualizações automáticas para o serviço de mobilidade (VMs do Azure)

Site Recovery adicionou uma opção para atualizações automáticas para a extensão do serviço de mobilidade. A extensão do serviço de mobilidade é instalada em cada VM do Azure replicada por Site Recovery. Ao habilitar a replicação, selecione se deseja permitir que Site Recovery gerenciem atualizações para a extensão.

As atualizações não exigem uma reinicialização da VM e não afetam a replicação. [Saiba mais](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Calculadora de preços para a recuperação de desastre de VM do Azure

A recuperação de desastres de VMs do Azure incorre em custos de licenciamento de VM e custos de rede e armazenamento. O Azure fornece uma [calculadora](https://aka.ms/a2a-cost-estimator) de preços para o ajudar a calcular estes custos. A Recovery do Site fornece agora uma estimativa de [preços exemplo](https://aka.ms/a2a-cost-estimator) que os preços de uma implementação de amostras com base numa aplicação de três camadas usando seis VMs com 12 discos HDD standard e 6 discos SSD Premium.

- O exemplo supõe uma alteração de dados de 10 GB por dia para Standard e 20 GB para Premium.
- Para sua implantação específica, você pode alterar as variáveis para estimar os custos.
- Você pode especificar o número de VMs, o número e o tipo de discos gerenciados e a taxa de alteração total de dados esperada em todas as VMs.
- Além disso, você pode aplicar um fator de compactação para estimar os custos de largura de banda.

[Leia](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) o anúncio.


## <a name="updates-october-2018"></a>Atualizações (outubro de 2018)

### <a name="update-rollup-30"></a>Pacote cumulativo de atualizações 30

[A atualização do rollup 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para Site Recovery agentes e provedores (conforme detalhado no acúmulo).
**Correções/melhorias de problemas** | Várias correções e aprimoramentos (conforme detalhado no acúmulo).

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastre da VM do Azure
Os recursos adicionados neste mês são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Apoio da região** | Suporte Site Recovery adicionado para a Austrália Central 1 e Austrália Central 2.
**Suporte para encriptação de disco** | Suporte adicionado para recuperação de desastre de VMs do Azure criptografadas com Azure Disk Encryption (ADE) com o aplicativo do Azure AD. [Saiba mais](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Exclusão do disco** | Os discos não inicializados agora são automaticamente excluídos durante a replicação de VM do Azure.
**Armazenamento ativado por firewall (PowerShell)** | Suporte adicionado para [contas de armazenamento ativadas por firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Você pode replicar VMs do Azure com discos não gerenciados em contas de armazenamento habilitadas para firewall para outra região do Azure para recuperação de desastre.<br/><br/> Você pode usar contas de armazenamento habilitadas para firewall como contas de armazenamento de destino para discos não gerenciados.<br/><br/> Com suporte usando apenas o PowerShell.


### <a name="update-rollup-29"></a>Pacote cumulativo de atualizações 29

[O rollup atualizado 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para Site Recovery agentes e provedores (conforme detalhado no acúmulo).
**Correções/melhorias de problemas** | Várias correções e aprimoramentos (conforme detalhado no acúmulo).


## <a name="updates-august-2018"></a>Atualizações (agosto de 2018)

### <a name="update-rollup-28"></a>Pacote cumulativo de atualizações 28

[A atualização do rollup 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para Site Recovery agentes e provedores (conforme detalhado no acúmulo).
**Correções/melhorias de problemas** | Várias correções e aprimoramentos (conforme detalhado no acúmulo).

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastre da VM do Azure
Os recursos adicionados neste mês são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Suporte linux** | Adicionado suporte para RedHat Enterprise Linux 6,10; CentOS 6,10.<br/><br/>
**Suporte em nuvem** | Recuperação de desastres com suporte para VMs do Azure na nuvem da Alemanha.
**Recuperação de desastres por assinatura cruzada** | Suporte para replicar VMs do Azure em uma região para outra região em uma assinatura diferente, dentro do mesmo locatário de Azure Active Directory. [Saiba mais](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Recuperação de desastre do servidor físico/VM VMware
Os recursos adicionados neste mês são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Suporte linux** | Suporte adicionado para RedHat Enterprise Linux 6,10, CentOS 6,10.<br/><br/> As VMs baseadas em Linux que usam o estilo de partição GPT (tabela de partição GUID) no modo de compatibilidade de BIOS herdado agora têm suporte. Reveja as [FAQ Azure VM](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) para mais informações.
**Recuperação de desastres para VMs após migração** | Suporte para habilitar a recuperação de desastre em uma região secundária para uma VM VMware local migrada para o Azure, sem a necessidade de desinstalar o serviço de mobilidade na VM antes de habilitar a replicação.
**Windows Server 2008** | Suporte para migrar computadores que executam o Windows Server 2008 R2/2008 64-bit e 32-bit.<br/><br/> Somente migração (replicação e failover). Não há suporte para o failback.

## <a name="updates-july-2018"></a>Atualizações (julho de 2018)

### <a name="update-rollup-27-july-2018"></a>Pacote cumulativo de atualizações 27 (julho de 2018)

[A atualização do rollup 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização para Site Recovery agentes e provedores (conforme detalhado no acúmulo).
**Correções/melhorias de problemas** | Várias correções e aprimoramentos (conforme detalhado no acúmulo).

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastre da VM do Azure

Os recursos adicionados neste mês são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Suporte linux** | Adicionado suporte para Red Hat Enterprise Linux 7,5.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Recuperação de desastre do servidor físico/VM VMware

Os recursos adicionados neste mês são resumidos na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Suporte linux** | Suporte adicionado para Red Hat Enterprise Linux 7,5, SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Passos seguintes

Mantenha-se atualizado com as nossas atualizações na página [De atualizações do Azure.](https://azure.microsoft.com/updates/?product=site-recovery)
