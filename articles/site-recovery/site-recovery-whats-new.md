---
title: Novidades na Recuperação do Site Azure
description: Fornece um resumo de novas funcionalidades e as mais recentes atualizações no serviço de Recuperação de Sítios Do Azure.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: abb1592bcacf025e9a052d7a9222f6fb3d2b72d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79257436"
---
# <a name="whats-new-in-site-recovery"></a>Novidades no Site Recovery

O serviço de recuperação do [site Azure](site-recovery-overview.md) é atualizado e melhorado de forma contínua. Para o ajudar a manter-se atualizado, este artigo fornece-lhe informações sobre os mais recentes lançamentos, novas funcionalidades e novos conteúdos. Esta página é atualizada regularmente.

Pode seguir e subscrever notificações de atualização do Site Recovery no canal [de atualizações do Azure.](https://azure.microsoft.com/updates/?product=site-recovery)

## <a name="supported-updates"></a>Atualizações suportadas

Para componentes de Recuperação do Site, suportamos versões N-4, onde N é a versão mais recente lançada. Estes estão resumidos na tabela seguinte.

**Atualizar** |  **Configuração unificada** | **O óvulo do servidor de configuração** | **Agente de serviço de mobilidade** | **Fornecedor de recuperação de site** | **Agente de Serviços de Recuperação**
--- | --- | --- | --- | --- | ---
[Rollup 45](https://support.microsoft.com/help/4550047/) | 9.32.5487.1 | 5.1.5400.0 | 9.32.5487.1 | 5.1.5400.0 | 2.0.9165.0
[Rollup 43](https://support.microsoft.com/help/4537047/) | 9.31.5449.1 | 5.1.5300.0 | 9.31.5449.1 | 5.1.5300.0 | 2.0.9165.0
[Rollup 42](https://support.microsoft.com/help/4531426/) | 9.30.5407.1 | 5.1.5200.0 | 9.30.5407.1 | 5.1.5200.0 | 2.0.9165.0
[Rollup 41](https://support.microsoft.com/help/4528026/) | 9.29.5367.1 | 5.1.5000.0 | 9.29.5367.1 | 5.1.5000.0 | 2.0.9165.0
[Rollup 40](https://support.microsoft.com/help/4521530/) | 9.28.5345.1 | 5.1.4800.0 | 9.28.5345.1 | 5.1.4800.0 | 2.0.9165.0

[Saiba mais](service-updates-how-to.md) sobre a instalação e suporte da atualização.

> [!NOTE]
> A atualização do rollup 44 não está apresentada na tabela porque não incluiu atualizações para os fornecedores e agentes de Recuperação do Site.

## <a name="updates-march-2020"></a>Atualizações (março de 2020)

### <a name="update-rollup-45"></a>Atualizar rollup 45

[A atualização do rollup 45](https://support.microsoft.com/help/4550047/update-rollup-45-for-azure-site-recovery) fornece as seguintes atualizações:

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações aos agentes e fornecedores de recuperação do site conforme detalhado no rollup.
**Correções/melhorias de problemas** | Uma série de correções e melhorias conforme detalhado no rollup.

## <a name="updates-january-2020"></a>Atualizações (janeiro de 2020)

### <a name="update-rollup-44"></a>Atualizar rollup 44

[A atualização do rollup 44](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Não houve atualizações para os fornecedores e agentes de Recuperação do Site.
**Correções/melhorias de problemas** | Uma série de correções e melhorias conforme detalhado no rollup.

### <a name="azure-vmware-disaster-recovery"></a>Recuperação de desastres azure VMware

As máquinas virtuais Azure suportam agora vMs ativados para encriptação em repouso com chaves geridas pelo cliente. [Saiba mais](azure-to-azure-how-to-enable-replication-cmk-disks.md).


### <a name="update-rollup-43"></a>Atualizar rollup 43

[A atualização do rollup 43](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações aos agentes e fornecedores de recuperação do site (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)


## <a name="updates-november-2019"></a>Atualizações (novembro 2019)

### <a name="update-rollup-42"></a>Atualizar rollup 42

[A atualização do rollup 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações aos agentes e fornecedores de recuperação do site (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)


### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres da VM Azure

As novas funcionalidades para a recuperação de desastres da VM Azure estão resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**UEFI** | A Recovery do Site apoia agora a recuperação de desastres para Os VMs Azure com a arquitetura de boot baseada na UEFI.
**Linux** | A Recovery do Site suporta agora os VMs Azure que executam o Linux com encriptação de disco azure (ADE).
**Geração 2** | Todos os VMs azure de geração 2 são agora apoiados para a recuperação de desastres.
**Regiões** | Agora pode permitir a recuperação de desastres para os VMs Azure no geo norueguês.

### <a name="vmware-to-azure-disaster-recovery"></a>VMware para recuperação após desastre do Azure

As novas funcionalidades para a Recuperação de Desastres de VMware para azure são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**UEFI** | A Recovery do Site suporta agora a recuperação de desastres para VMware VMs com arquitetura de boot baseada em UEFI.<br/><br/> Os sistemas operativos suportados incluem o Windows Server 2019, o Windows Server 2016, o Windows Server 2012 R2, o Windows Server 2012, o SLES 12 SP4, o RHEL 8.

## <a name="update-to-servicing-stack-updatesha-2"></a>Atualização para a atualização da pilha de manutenção/SHA-2

Para a recuperação de desastres de VMs Azure para uma região secundária, ou VMs no local ou servidores físicos para o Azure, note o seguinte:

- A partir da versão 9.30.5407.1 da extensão do serviço de mobilidade (para VMs Azure) e agente de serviço de Mobilidade (para VMware/máquinas físicas), alguns sistemas operativos de máquinas devem estar a executar a atualização da pilha de manutenção e o SHA-2. Os detalhes são mostrados na tabela abaixo.
- Instale a atualização e SHA-2 de acordo com o KB ligado. A SHA-1 não é suportada a partir de setembro de 2019 e se a assinatura de código SHA-2 não estiver ativada, a extensão do agente não instala/atualização como esperado.
- Saiba mais sobre a [atualização e requisitos sha-2.](https://aka.ms/SHA-2KB)

**Sistema Operativo** | **VM do Azure** | **VMware VM/máquina física**
--- | --- | ---
**Windows 2008 R2 SP1** | [Atualização da pilha de manutenção](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Atualização da pilha de manutenção](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 2008 SP2** | [Atualização da pilha de manutenção](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Atualização da pilha de manutenção](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 7 SP1** | [Atualização da pilha de manutenção](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Atualização da pilha de manutenção](https://support.microsoft.com/help/4490628)<br/> [SHA-2.](https://support.microsoft.com/help/4474419)



## <a name="updates-october-2019"></a>Atualizações (outubro 2019)

### <a name="update-rollup-41"></a>Atualizar rollup 41

[A atualização do rollup 41](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações aos agentes e fornecedores de recuperação do site (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)



### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres da VM Azure

As novas funcionalidades para a recuperação de desastres da VM Azure estão resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Definições de failover de teste** | Ao configurar uma falha de teste, pode agora configurar as definições para o teste failover VM e rede, incluindo endereço IP, NSG, equilíbrio de carga interna e o endereço IP público para cada nic máquina. Estas configurações são opcionais e não alteram o comportamento atual. Se não configurar estas definições, pode escolher um Azure VNet no momento da falha do teste. [Saiba mais](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/).
**Planos de recuperação** | Os planos de recuperação estão agora limitados a 100 VMs, para garantir a fiabilidade do failover.

### <a name="vmware-to-azure-disaster-recovery"></a>VMware para recuperação após desastre do Azure

As novas funcionalidades para a Recuperação de Desastres de VMware para azure são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Planos de recuperação** | Os planos de recuperação estão agora limitados a 100 VMs, para garantir a fiabilidade do failover.


## <a name="updates-september-2019"></a>Atualizações (setembro 2019)

### <a name="update-rollup-40"></a>Atualizar rollup 40

[A atualização do rollup 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações aos agentes e fornecedores de recuperação do site (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)




### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres da VM Azure

As novas funcionalidades para a recuperação de desastres da VM Azure estão resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Limpeza após falha de volta** | Depois de ter falhado com o Azure secundário, e depois de ter falhado na região primária, a Recuperação do Local limpa automaticamente as máquinas na região secundária. Não há necessidade de apagar manualmente VMs e NICs.
**Falha no teste mantém endereço IP** | Agora pode reter o endereço IP da fonte VM durante um exercício de recuperação de desastres e escolher um endereço IP estático para uma falha de teste.

### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastres vMware/servidor físico

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
Novos alertas de servidor de processos | Adicionámos novos alertas de servidores de processos. [Saiba mais](vmware-physical-azure-monitor-process-server.md).

### <a name="hyper-v-disaster-recovery"></a>Recuperação de desastres hiper-V

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
Conta de armazenamento | A Recuperação do Site suporta agora o uso de contas de armazenamento com firewall habilitada para a recuperação de desastres Hyper-V a Azure.  Pode selecionar contas de armazenamento ativadas por firewall como uma conta-alvo ou para armazenamento de cache. Se utilizar uma conta ativada por firewall, certifique-se de que permite a opção de permitir serviços microsoft fidedignos.<br/><br/> Isto é suportado para VMs Hiper-V com ou sem VMM do Centro de Sistema.


## <a name="updates-august-2019"></a>Atualizações (agosto de 2019)

### <a name="update-rollup-39"></a>Atualizar rollup 39

[O rollup atualizado 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações aos agentes e fornecedores de recuperação do site (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)


### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres da VM Azure

As novas funcionalidades para a recuperação de desastres da VM Azure estão resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Encriptação sem Anúncio Azure** | A encriptação sem uma aplicação Azure AD é agora suportada para a replicação do Azure VM para gerir discos que executam o Windows.
**Recursos de rede para falhas** | Ao falhar noutra região, pode agora anexar as definições de recursos de rede (NSGs, equilíbrio de carga, endereço IP público) a um VM.

## <a name="updates-july-2019"></a>Atualizações (julho 2019)

### <a name="update-rollup-38"></a>Atualizar rollup 38

[O rollup atualizado 38](https://support.microsoft.com/help/4513507/) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações aos agentes e fornecedores de recuperação do site (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)


### <a name="general"></a>Geral

A Recuperação do Site suporta agora a utilização de contas de armazenamento v2 de finalidade geral para armazenamento de cache ou armazenamento de alvo. Anteriormente, apenas a v1 era apoiada.

### <a name="vmware-to-azure-disaster-recovery"></a>VMware para recuperação após desastre do Azure

Pode agora replicar discos até 8 TB, quando se replica a um VM Azure com discos geridos.


## <a name="updates-june-2019"></a>Atualizações (junho de 2019)

### <a name="update-rollup-37"></a>Atualizar rollup 37

[O rollup atualizado 37](https://support.microsoft.com/help/4508614/) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações aos agentes e fornecedores de recuperação do site (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)


### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastres vMware/servidor físico

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Divisórias GPT** | A partir do Update Rollup 37 (versão do serviço de mobilidade 9.25.5241.1), até cinco partições GPT são suportadas na UEFI. Antes desta atualização, quatro foram apoiados.



## <a name="updates-may-2019"></a>Atualizações (maio de 2019)

### <a name="update-rollup-36"></a>Atualizar rollup 36

[O rollup atualizado 36](https://support.microsoft.com/help/4503156) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização aos agentes e fornecedores de recuperação do site (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres da VM Azure

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Replicar discos adicionados** | Ativar a replicação para discos de dados adicionados a um VM Azure que já está habilitado para a recuperação de desastres. [Saiba mais](azure-to-azure-enable-replication-added-disk.md).
**Atualizações automáticas** | Ao configurar atualizações automáticas para a extensão do serviço mobility que funciona em VMs Azure habilitados para a recuperação de desastres, pode agora selecionar uma conta de automação existente para usar, em vez de utilizar a conta predefinida criada pela Recovery do Site. [Saiba mais](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastres vMware/servidor físico

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Monitorização do servidor de processos** | Para a recuperação de desastres de VMware VMs e servidores físicos, monitorize e problemas de servidor estoirar problemas com relatórios e alertas de saúde do servidor melhorados. [Saiba mais](vmware-physical-azure-monitor-process-server.md).





## <a name="updates-march-2019"></a>Atualizações (março de 2019)

### <a name="update-rollup-35"></a>Atualizar rollup 35

[A atualização do rollup 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização aos agentes e fornecedores de recuperação do site (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)

### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastres vMware/servidor físico

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Discos geridos** | A replicação de VMware VMs e servidores físicos no local está agora diretamente a gerir discos em Azure. Os dados no local são enviados para uma conta de armazenamento de cache em Azure, e os pontos de recuperação são criados em discos geridos na localização alvo. Isto garante que não precisa de gerir várias contas de armazenamento de alvos.
**Servidor de configuração** | A Recuperação do Site suporta agora servidores de configuração com vários NICs. Adicione adaptadores adicionais ao VM do servidor de configuração antes de registar o servidor de configuração no cofre. Se adicionar mais tarde, precisa de voltar a registar o servidor no cofre.


## <a name="updates-february-2019"></a>Atualizações (fevereiro de 2019)

### <a name="update-rollup-34"></a>Atualizar rollup 34

[O rollup atualizado 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização aos agentes e fornecedores de recuperação do site (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).


### <a name="update-rollup-33"></a>Atualizar rollup 33

[A atualização do rollup 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização aos agentes e fornecedores de recuperação do site (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).


### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres da VM Azure

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Mapeamento da rede** | Para a recuperação de desastres do Azure VM, pode agora utilizar qualquer rede de destino disponível quando ativa a replicação.
**SSD Standard** | Agora pode configurar a recuperação de desastres para os VMs Azure utilizando [discos Standard SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Direto de Espaços de Armazenamento** | Pode configurar a recuperação de desastres para apps que executam aplicações Do Azure VM utilizando espaços de [armazenamento direto](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) para alta disponibilidade.  A utilização de espaços de armazenamento direto (S2D) juntamente com a Recuperação do Local proporciona uma proteção abrangente das cargas de trabalho do Azure VM. O S2D permite-lhe acolher um aglomerado de hóspedes em Azure. Isto é especialmente útil quando um VM acolhe uma aplicação crítica, como camada SAP ASCS, SQL Server ou servidor de ficheiros scale-out.


### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastres vMware/servidor físico

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Sistema de ficheiros Linux BRTFS** | A Recuperação do Site suporta agora a replicação de VMware VMs com o sistema de ficheiros BRTFS. A replicação não é suportada se:<br/><br/>- O subvolume do sistema de ficheiros BTRFS é alterado após a replicação.<br/><br/>- O sistema de ficheiros está espalhado por vários discos.<br/><br/>- O sistema de ficheiros BTRFS suporta raid.
**Windows Server 2019** | Suporte adicionado para máquinas que executam o Windows Server 2019.


## <a name="updates-january-2019"></a>Atualizações (janeiro de 2019)


### <a name="accelerated-networking-azure-vms"></a>Networking acelerado (VMs Azure)

A ligação acelerada em rede permite a virtualização de I/O de raiz única (SR-IOV) a um VM, melhorando o desempenho em rede. Quando ativa a replicação de um VM Azure, a Recuperação do Site deteta se a rede acelerada está ativada. Se for, após a failover Site Recovery configura automaticamente a rede acelerada na réplica-alvo Azure VM, tanto para [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) como [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

[Saiba mais](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>Atualizar rollup 32

[O rollup atualizado 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização aos agentes e fornecedores de recuperação do site (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres da VM Azure

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Apoio Técnico para Linux** | O suporte foi adicionado para redHat Workstation 6/7, e novas versões kernel para Ubuntu, Debian e SUSE.
**Direto de Espaços de Armazenamento** | A Recuperação do Site suporta os VMs Azure utilizando espaços de armazenamento direto (S2D).

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>VMware VMs/recuperação de desastres de servidores físicos

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Apoio Técnico para Linux** | O suporte foi adicionado para Redhat Enterprise Linux 7.6, RedHat Workstation 6/7, Oracle Linux 6.10 e Oracle Linux 7.6, e novas versões kernel para Ubuntu, Debian e SUSE.


### <a name="update-rollup-31"></a>Atualizar rollup 31

[A atualização do rollup 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização aos agentes e fornecedores de recuperação do site (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).

### <a name="vmware-vmsphysical-servers-replication"></a>VMware VMs/replicação de servidores físicos

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Apoio Técnico para Linux** |  O apoio foi adicionado para Oracle Linux 6.8, Oracle Linux 6.9 e Oracle Linux 7.0 com o Kernel compatível com o chapéu vermelho, e para o Unbreakable Enterprise Kernel (UEK) Release 5.
**LVM** | Suporte adicionado para volumes LVM e LVM2.<br/><br/> O diretório /boot numa partição de discos e em volumes LVM é agora suportado.
**Diretórios** | Foi adicionado suporte para estes diretórios criados como divisórias separadas, ou sistemas de ficheiros que não estão no mesmo disco do sistema:<br/><br/> /(raiz), /boot, /usr, /usr/local, /var, /etc.
**Windows Server 2008** | Suporte adicionado para discos dinâmicos.
**Ativação pós-falha** | Tempo de failover melhorado para VMware VMs onde storvsc e vsbus não são boot drivers.
**Apoio uefi** | Os VMs azure não suportam o tipo de boot UEFI. Agora pode migrar no local servidores físicos com UEFI para Azure com Recuperação do Site. A Recuperação do Site migra o servidor convertendo o tipo de boot para BIOS antes da migração. A Recovery do Site apoiou anteriormente esta conversão apenas para VMs. O suporte está disponível para servidores físicos que executam o Windows Server 2012 ou mais tarde.

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres da VM Azure

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Apoio Técnico para Linux** | O apoio foi adicionado para Oracle Linux 6.8, Oracle Linux 6.9 e Oracle Linux 7.0 com o Kernel compatível com o chapéu vermelho, e para o Unbreakable Enterprise Kernel (UEK) Release 5.
**Sistema de ficheiros Linux BRTFS** | Apoiado para VMs Azure.
**VMs azure em zonas de disponibilidade** | Pode permitir a replicação para outra região para VMs Azure implantados em zonas de disponibilidade. Agora pode ativar a replicação de um VM Azure, e definir o alvo para falhar para uma única instância VM, um VM em um conjunto de disponibilidade, ou um VM em uma zona de disponibilidade. A configuração não afeta a replicação. [Leia](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) o anúncio.
**Armazenamento ativado por firewall (portal/PowerShell)** | Suporte adicionado para [contas de armazenamento ativadas por firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Pode replicar VMs Azure com discos não geridos em contas de armazenamento ativadas por firewall para outra região do Azure para recuperação de desastres.<br/><br/> Pode utilizar contas de armazenamento ativadas por firewall como contas de armazenamento de alvos para discos não geridos.<br/><br/> Suportado no portal e usando powerShell.

## <a name="updates-december-2018"></a>Atualizações (dezembro de 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Atualizações automáticas para o serviço de Mobilidade (VMs Azure)

A Recuperação do Site adicionou uma opção para atualizações automáticas à extensão do serviço mobility. A extensão do serviço mobility é instalada em cada VM Azure replicado pela Recuperação do Site. Quando ativa a replicação, seleciona se permite a recuperação do site para gerir as atualizações da extensão.

As atualizações não requerem um reinício vm, e não afetam a replicação. [Saiba mais](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Calculadora de preços para recuperação de desastres de VM Azure

A recuperação de desastres de VMs Azure incorre nos custos de licenciamento da VM e nos custos de rede e armazenamento. O Azure fornece uma [calculadora](https://aka.ms/a2a-cost-estimator) de preços para o ajudar a calcular estes custos. A Recovery do Site fornece agora uma estimativa de [preços exemplo](https://aka.ms/a2a-cost-estimator) que os preços de uma implementação de amostras com base numa aplicação de três camadas usando seis VMs com 12 discos HDD standard e 6 discos SSD Premium.

- A amostra pressupõe uma alteração de dados de 10 GB por dia para a norma e 20 GB para prémio.
- Para a sua implementação em particular, pode alterar as variáveis para estimar os custos.
- Pode especificar o número de VMs, o número e o tipo de discos geridos e a taxa de variação total de dados esperada em todos os VMs.
- Além disso, pode aplicar um fator de compressão para estimar os custos de largura de banda.

[Leia](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) o anúncio.


## <a name="updates-october-2018"></a>Atualizações (outubro de 2018)

### <a name="update-rollup-30"></a>Atualizar rollup 30

[A atualização do rollup 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização aos agentes e fornecedores de recuperação do site (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres da VM Azure
As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Suporte de região** | Apoio de recuperação do site adicionado para Austrália Central 1 e Austrália Central 2.
**Suporte para encriptação de disco** | Suporte adicionado para recuperação de desastres de VMs Azure encriptados com Encriptação de Disco Azure (ADE) com a aplicação Azure AD. [Saiba mais](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Exclusão do disco** | Os discos não inicializados são agora automaticamente excluídos durante a replicação do VM Azure.
**Armazenamento ativado por firewall (PowerShell)** | Suporte adicionado para [contas de armazenamento ativadas por firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Pode replicar VMs Azure com discos não geridos em contas de armazenamento ativadas por firewall para outra região do Azure para recuperação de desastres.<br/><br/> Pode utilizar contas de armazenamento ativadas por firewall como contas de armazenamento de alvos para discos não geridos.<br/><br/> Suportado apenas usando powerShell.


### <a name="update-rollup-29"></a>Atualizar rollup 29

[O rollup atualizado 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização aos agentes e fornecedores de recuperação do site (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).


## <a name="updates-august-2018"></a>Atualizações (agosto de 2018)

### <a name="update-rollup-28"></a>Atualizar rollup 28

[A atualização do rollup 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização aos agentes e fornecedores de recuperação do site (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres da VM Azure
As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Apoio Técnico para Linux** | Adicionado suportado para RedHat Enterprise Linux 6.10; Centos 6,10.<br/><br/>
**Suporte em nuvem** | Apoiou a recuperação de desastres para os VMs Azure na nuvem alemã.
**Recuperação de desastres por assinatura cruzada** | Apoio à replicação de VMs Azure numa região para outra região numa subscrição diferente, dentro do mesmo inquilino azure Ative Directory. [Saiba mais](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/recuperação de desastre saqueado do servidor físico
As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Apoio Técnico para Linux** | Suporte adicionado para RedHat Enterprise Linux 6.10, CentOS 6.10.<br/><br/> VMs baseados em Linux que usam o estilo de partição GUID (GPT) em modo de compatibilidade BIOS legado são agora suportados. Reveja as [FAQ Azure VM](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) para mais informações.
**Recuperação de desastres para VMs após migração** | Apoio para permitir a recuperação de desastres numa região secundária para um VMware VM imigrar para O Azure, sem precisar de desinstalar o serviço de Mobilidade no VM antes de permitir a replicação.
**Windows Server 2008** | Suporte para máquinas migratórias que executam o Windows Server 2008 R2/2008 64-bit e 32-bit.<br/><br/> Apenas migração (replicação e failover). O failback não é suportado.

## <a name="updates-july-2018"></a>Atualizações (julho de 2018)

### <a name="update-rollup-27-july-2018"></a>Atualização rollup 27 (julho 2018)

[A atualização do rollup 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização aos agentes e fornecedores de recuperação do site (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres da VM Azure

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Apoio Técnico para Linux** | Suporte adicionado para Red Hat Enterprise Linux 7.5.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/recuperação de desastre saqueado do servidor físico

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Apoio Técnico para Linux** | Suporte adicionado para Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Passos seguintes

Mantenha-se atualizado com as nossas atualizações na página [De atualizações do Azure.](https://azure.microsoft.com/updates/?product=site-recovery)
