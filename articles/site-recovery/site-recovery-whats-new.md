---
title: Novidades na Recuperação do Local de Azure
description: Fornece um resumo de novas funcionalidades e as últimas atualizações no serviço de Recuperação do Site Azure.
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 51d54ca4d46af3b9e0746f36114fadc47edd5b1f
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98805053"
---
# <a name="whats-new-in-site-recovery"></a>Novidades no Site Recovery

O serviço [de Recuperação do Local Azure](site-recovery-overview.md) é atualizado e melhorado de forma contínua. Para o ajudar a manter-se atualizado, este artigo fornece-lhe informações sobre as mais recentes versões, novas funcionalidades e novos conteúdos. Esta página é atualizada regularmente.

Pode seguir e subscrever notificações de atualização da Recuperação do Site no canal [de atualizações do Azure.](https://azure.microsoft.com/updates/?product=site-recovery)

## <a name="supported-updates"></a>Atualizações suportadas

Para os componentes de Recuperação de Sítios, apoiamos as versões N-4, onde N é a versão mais recente lançada. Estes estão resumidos na tabela seguinte.

**Atualizar** |  **Configuração Unificada** | **Óvulo do servidor de configuração** | **Agente de serviço de mobilidade** | **Provedor de Recuperação de Sítios** | **Agente dos Serviços de Recuperação**
--- | --- | --- | --- | --- | ---
[Rollup 53](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | 9.40.5850.1 | 5.1.6537.0 | 9.40.5850.1 | 5.1.6537.0  | 2.0.9202.0
[Rollup 52](https://support.microsoft.com/help/4597409/)  | 9.39.5796.1 | 5.1.6458.0 | 9.39.5796.1 | 5.1.6458.0  | 2.0.9196.0
[Rollup 51](https://support.microsoft.com/help/4590304)  | 9.38.5761.1 | 5.1.6400.0 | 9.38.5761.1 | 5.1.6400.0  | 2.0.9193.0
[Rollup 50](https://support.microsoft.com/help/4582666/) | 9.37.5724.1 | 5.1.6347.0 | 9.37.5724.1 | 5.1.6347.0  | 2.0.9192.0
[Rollup 49](https://support.microsoft.com/help/4578241/) | 9.36.5696.1 | 5.1.6315.0 | 9.36.5696.1 | 5.1.6315.0 | 2.0.9188.0


[Saiba mais](service-updates-how-to.md) sobre a instalação e suporte de atualização.

## <a name="updates-january-2021"></a>Atualizações (janeiro de 2021)

### <a name="update-rollup-53"></a>Atualização rollup 53

[O rollup de atualização 53](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) fornece as seguintes atualizações:

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações para agentes e fornecedores de Recuperação de Locais conforme detalhado no rollup.
**Correções/melhorias de problemas** | Uma série de correções e melhorias conforme detalhado no rollup. 
**Recuperação de desastres em Azure VM** | A replicação das etiquetas está agora suportada. Quaisquer tags adicionadas aos VMs, discos e NICs da região de origem são replicadas em máquinas na região alvo.<br/><br/> Os VMs Azure que executam o Debian 10 estão agora suportados para replicação.
**VMware VM/recuperação de desastres físicos para Azure** | A atualização inclui melhorias de registo para a replicação de VMware VM para Azure e mensagens de erro melhoradas.<br/><br/> VMware VMs e máquinas físicas em execução Debian 10 são agora suportados para replicação.


## <a name="updates-november-2020"></a>Atualizações (novembro 2020)

### <a name="update-rollup-52"></a>Atualização rollup 52

[O rollup de atualização 52](https://support.microsoft.com/help/4597409/update-rollup-52-for-azure-site-recovery) fornece as seguintes atualizações:

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações para agentes e fornecedores de Recuperação de Locais conforme detalhado no rollup.
**Correções/melhorias de problemas** | Uma série de correções e melhorias conforme detalhado no rollup, incluindo o novo suporte Linux para o serviço de Mobilidade.
**Recuperação de desastres em Azure VM** | Agora suportado para VMs executando RHEL 8.3 e Oracle Linux 7.9
**VMware VM/recuperação de desastres físicos para Azure** | Agora suportado para VMs executando RHEL 8.3, Oracle Linux 7.9/8.3.

## <a name="updates-october-2020"></a>Atualizações (outubro 2020)

### <a name="update-rollup-51"></a>Atualização rollup 51

[O rollup de atualização 51](https://support.microsoft.com/help/4590304/update-rollup-51-for-azure-site-recovery) fornece as seguintes atualizações:

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações para agentes e fornecedores de Recuperação de Locais conforme detalhado no rollup.
**Correções/melhorias de problemas** | Uma série de correções e melhorias conforme detalhado no rollup, incluindo o novo suporte Linux para o serviço de Mobilidade.

## <a name="updates-september-2020"></a>Atualizações (setembro 2020)

### <a name="update-rollup-50"></a>Atualização rollup 50

[O rollup de atualização 50](https://support.microsoft.com/help/4582666/update-rollup-50-for-azure-site-recovery) fornece as seguintes atualizações:

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações para agentes e fornecedores de Recuperação de Locais conforme detalhado no rollup.
**Correções/melhorias de problemas** | Uma série de correções e melhorias conforme detalhado no rollup.

## <a name="updates-august-2020"></a>Atualizações (agosto 2020)

### <a name="update-rollup-49"></a>Atualização rollup 49

[O rollup de atualização 49](https://support.microsoft.com/help/4578241/update-rollup-49-for-azure-site-recovery) fornece as seguintes atualizações:

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações para agentes e fornecedores de Recuperação de Locais conforme detalhado no rollup.
**Correções/melhorias de problemas** | Uma série de correções e melhorias conforme detalhado no rollup, incluindo o novo suporte Linux para o serviço de Mobilidade.

## <a name="updates-july-2020"></a>Atualizações (julho 2020)

### <a name="update-rollup-48"></a>Atualização rollup 48

[O rollup de atualização 48](https://support.microsoft.com/help/4573888/update-rollup-48-for-azure-site-recovery) fornece as seguintes atualizações:

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações para agentes e fornecedores de Recuperação de Locais conforme detalhado no rollup.
**Correções/melhorias de problemas** | Uma série de correções e melhorias conforme detalhado no rollup.

> [!NOTE]
> O rollup 48 da atualização tem um problema conhecido com a replicação de máquinas Linux encriptadas através do ADE. [Saiba mais](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137).

### <a name="update-rollup-47"></a>Atualização rollup 47

[O rollup de atualização 47](https://support.microsoft.com/help/4570609/update-rollup-47-for-azure-site-recovery) fornece as seguintes atualizações:

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações para agentes e fornecedores de Recuperação de Locais conforme detalhado no rollup.
**Correções/melhorias de problemas** | Uma série de correções e melhorias conforme detalhado no rollup.

## <a name="updates-june-2020"></a>Atualizações (junho de 2020)

### <a name="update-rollup-46"></a>Atualização rollup 46

[O rollup de atualização 46](https://support.microsoft.com/help/4564347/update-rollup-46-for-azure-site-recovery) fornece as seguintes atualizações:

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações para agentes e fornecedores de Recuperação de Locais conforme detalhado no rollup.
**Correções/melhorias de problemas** | Uma série de correções e melhorias conforme detalhado no rollup.

## <a name="updates-march-2020"></a>Atualizações (março 2020)

### <a name="update-rollup-45"></a>Atualização rollup 45

[O rollup de atualização 45](https://support.microsoft.com/help/4550047/update-rollup-45-for-azure-site-recovery) fornece as seguintes atualizações:

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações para agentes e fornecedores de Recuperação de Locais conforme detalhado no rollup.
**Correções/melhorias de problemas** | Uma série de correções e melhorias conforme detalhado no rollup.

## <a name="updates-january-2020"></a>Atualizações (janeiro de 2020)

### <a name="update-rollup-44"></a>Atualização rollup 44

[O rollup de atualização 44](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Não houve atualizações para os fornecedores e agentes de Recuperação do Site.
**Correções/melhorias de problemas** | Uma série de correções e melhorias conforme detalhado no rollup.

### <a name="azure-vmware-disaster-recovery"></a>Recuperação de desastres Azure VMware

As máquinas virtuais Azure agora suportam VMs que permitem encriptar em repouso com chaves geridas pelo cliente. [Saiba mais](azure-to-azure-how-to-enable-replication-cmk-disks.md).


### <a name="update-rollup-43"></a>Atualização rollup 43

[O rollup de atualização 43](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações aos agentes e fornecedores de recuperação de locais (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)


## <a name="updates-november-2019"></a>Atualizações (novembro 2019)

### <a name="update-rollup-42"></a>Atualização rollup 42

[O rollup de atualização 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações aos agentes e fornecedores de recuperação de locais (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)


### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres em Azure VM

As novas funcionalidades para a recuperação de desastres em Azure VM são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**UEFI** | A Recuperação do Site suporta agora a recuperação de desastres para VMs Azure com arquitetura de boot baseada na UEFI.
**Linux** | A Recuperação do Site suporta agora VMs Azure que executam o Linux com encriptação de disco Azure (ADE).
**Geração 2** | Todos os VMs Azure da geração 2 são agora apoiados para a recuperação de desastres.
**Regiões** | Pode agora permitir a recuperação de desastres para os VMs Azure no geo norueguês.

### <a name="vmware-to-azure-disaster-recovery"></a>VMware para recuperação após desastre do Azure

Novas funcionalidades para vMware para recuperação de desastres Azure são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**UEFI** | A Recuperação do Site suporta agora a recuperação de desastres para VMware VMs com arquitetura de boot baseada na UEFI.<br/><br/> Os sistemas operativos suportados incluem o Windows Server 2019, o Windows Server 2016, o Windows Server 2012 R2, o Windows Server 2012, o SLES 12 SP4, o RHEL 8.

## <a name="update-to-servicing-stack-updatesha-2"></a>Atualização para atualização da pilha de serviço/SHA-2

Para a recuperação de desastres de Azure VMs para uma região secundária, ou no local VMware VMs ou servidores físicos para Azure, note o seguinte:

- A partir da versão 9.30.5407.1 da extensão do serviço mobility (para VMs Azure) e do agente de serviço de mobilidade (para VMware/máquinas físicas), alguns sistemas operativos de máquinas devem estar a executar a atualização da pilha de manutenção e SHA-2. Os detalhes são mostrados na tabela abaixo.
- Instale a atualização e SHA-2 de acordo com o KB ligado. O SHA-1 não é suportado a partir de setembro de 2019, e se a assinatura de código SHA-2 não estiver ativada, a extensão do agente não instalará/atualizará como esperado.
- Saiba mais sobre [a atualização e requisitos SHA-2](https://aka.ms/SHA-2KB).

**Sistema operativo** | **VM do Azure** | **VMware VM/máquina física**
--- | --- | ---
**Windows 2008 R2 SP1** | [Atualização da pilha de manutenção](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Atualização da pilha de manutenção](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 2008 SP2** | [Atualização da pilha de manutenção](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Atualização da pilha de manutenção](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 7 SP1** | [Atualização da pilha de manutenção](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Atualização da pilha de manutenção](https://support.microsoft.com/help/4490628)<br/> [SHA-2.](https://support.microsoft.com/help/4474419)



## <a name="updates-october-2019"></a>Atualizações (outubro 2019)

### <a name="update-rollup-41"></a>Atualização rollup 41

[O rollup de atualização 41](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações aos agentes e fornecedores de recuperação de locais (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)



### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres em Azure VM

As novas funcionalidades para a recuperação de desastres em Azure VM são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Testar definições de failover** | Ao configurar uma falha de teste, pode agora configurar as definições para o teste de falha de VM e rede, incluindo endereço IP, NSG, saldo de carga interna e o endereço IP público para cada máquina NIC. Estas definições são opcionais e não alteram o comportamento atual. Se não configurar estas definições, pode escolher um Azure VNet no momento do teste. [Saiba mais](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/).
**Planos de recuperação** | Os planos de recuperação estão agora limitados a 100 VMs, para garantir a fiabilidade de falhas.

### <a name="vmware-to-azure-disaster-recovery"></a>VMware para recuperação após desastre do Azure

Novas funcionalidades para vMware para recuperação de desastres Azure são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Planos de recuperação** | Os planos de recuperação estão agora limitados a 100 VMs, para garantir a fiabilidade de falhas.


## <a name="updates-september-2019"></a>Atualizações (setembro 2019)

### <a name="update-rollup-40"></a>Atualização rollup 40

[O rollup de atualização 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações aos agentes e fornecedores de recuperação de locais (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)




### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres em Azure VM

As novas funcionalidades para a recuperação de desastres em Azure VM são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Limpeza após falha** | Depois de ter falhado no Azure secundário e de ter falhado na região primária, a Recuperação do Local limpa automaticamente as máquinas na região secundária. Não há necessidade de apagar manualmente VMs e NICs.
**Falha no teste mantém endereço IP** | Pode agora reter o endereço IP da fonte VM durante um exercício de recuperação de desastres e escolher um endereço IP estático para uma falha de teste.

### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastres VMware/servidor físico

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
Novos alertas de servidor de processo | Adicionámos novos alertas de servidores de processos. [Saiba mais](vmware-physical-azure-monitor-process-server.md).

### <a name="hyper-v-disaster-recovery"></a>Recuperação de desastres hiper-V

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
Conta de armazenamento | A Recuperação do Site suporta agora a utilização de contas de armazenamento com firewall ativada para recuperação de desastres Hiper-V a Azure.  Pode selecionar contas de armazenamento ativadas por firewall como conta-alvo ou para armazenamento em cache. Se utilizar uma conta ativada por firewall, certifique-se de que ativa a opção de permitir serviços de Confiança da Microsoft.<br/><br/> Isto é suportado para Hiper-VMs com ou sem System Center VMM.


## <a name="updates-august-2019"></a>Atualizações (agosto 2019)

### <a name="update-rollup-39"></a>Atualização rollup 39

[O rollup de atualização 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações aos agentes e fornecedores de recuperação de locais (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)


### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres em Azure VM

As novas funcionalidades para a recuperação de desastres em Azure VM são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Encriptação sem Azure AD** | A encriptação sem uma aplicação AD Azure é agora suportada para a replicação do Azure VM para discos geridos que executam o Windows.
**Recursos de rede para falhas** | Ao falhar noutra região, pode agora anexar as definições de recursos de rede (NSGs, equilíbrio de carga, endereço IP público) a um VM.

## <a name="updates-july-2019"></a>Atualizações (julho 2019)

### <a name="update-rollup-38"></a>Atualização rollup 38

[O rollup de atualização 38](https://support.microsoft.com/help/4513507/) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações aos agentes e fornecedores de recuperação de locais (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)


### <a name="general"></a>Geral

A Recuperação do Site suporta agora a utilização de contas de armazenamento v2 para armazenamento de cache ou armazenamento de alvos. Anteriormente apenas v1 foi suportado.

### <a name="vmware-to-azure-disaster-recovery"></a>VMware para recuperação após desastre do Azure

Agora pode replicar discos até 8 TB, ao replicar-se num VM Azure com discos geridos.


## <a name="updates-june-2019"></a>Atualizações (junho 2019)

### <a name="update-rollup-37"></a>Atualização rollup 37

[O rollup de atualização 37](https://support.microsoft.com/help/4508614/) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Atualizações aos agentes e fornecedores de recuperação de locais (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)


### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastres VMware/servidor físico

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Divisórias GPT** | A partir do Update Rollup 37 (versão de serviço de mobilidade 9.25.5241.1), até cinco divisórias GPT são suportadas na UEFI. Antes desta atualização, quatro foram apoiados.



## <a name="updates-may-2019"></a>Atualizações (maio 2019)

### <a name="update-rollup-36"></a>Atualização rollup 36

[O rollup de atualização 36](https://support.microsoft.com/help/4503156) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização aos agentes e fornecedores de Recuperação de Sítios (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres em Azure VM

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Replicar discos adicionados** | Ativar a replicação de discos de dados adicionados a um VM Azure que já está habilitado para a recuperação de desastres. [Saiba mais](azure-to-azure-enable-replication-added-disk.md).
**Atualizações automáticas** | Ao configurar atualizações automáticas para a extensão do serviço Mobility que funciona em VMs Azure habilitados para a recuperação de desastres, pode agora selecionar uma conta de automação existente para utilizar, em vez de utilizar a conta padrão criada pela Recuperação do Site. [Saiba mais](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastres VMware/servidor físico

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Monitorização do servidor de processo** | Para a recuperação de desastres de VMware VMware e servidores físicos, monitore e resolução de problemas, problemas de servidores com relatórios e alertas de saúde melhorados do servidor. [Saiba mais](vmware-physical-azure-monitor-process-server.md).





## <a name="updates-march-2019"></a>Atualizações (março 2019)

### <a name="update-rollup-35"></a>Atualização rollup 35

[O rollup de atualização 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização aos agentes e fornecedores de Recuperação de Sítios (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)

### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastres VMware/servidor físico

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Discos geridos** | A replicação de VMware VMware e servidores físicos está agora diretamente a gerir discos em Azure. Os dados no local são enviados para uma conta de armazenamento de cache em Azure, e os pontos de recuperação são criados em discos geridos no local alvo. Isto garante que não precisa de gerir várias contas de armazenamento de alvos.
**Servidor de configuração** | A Recuperação do Site suporta agora servidores de configuração com vários NICs. Adicione adaptadores adicionais ao VM do servidor de configuração antes de registar o servidor de configuração no cofre. Se adicionar depois, tem de voltar a registar o servidor no cofre.


## <a name="updates-february-2019"></a>Atualizações (fevereiro 2019)

### <a name="update-rollup-34"></a>Atualização rollup 34

[O rollup de atualização 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização aos agentes e fornecedores de Recuperação de Sítio (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).


### <a name="update-rollup-33"></a>Atualização rollup 33

[O rollup de atualização 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização aos agentes e fornecedores de Recuperação de Sítio (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).


### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres em Azure VM

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Mapeamento da rede** | Para a recuperação de desastres em Azure VM, pode agora utilizar qualquer rede alvo disponível quando ativar a replicação.
**SSD Standard** | Pode agora configurar a recuperação de desastres para os VMs Azure utilizando [discos SSD standard](../virtual-machines/disks-types.md#standard-ssd).
**Direto de Espaços de Armazenamento** | Pode configurar a recuperação de desastres para aplicações em execução em aplicações Azure VM, utilizando [espaços de armazenamento direto](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) para uma alta disponibilidade.  A utilização de espaços de armazenamento diretos (S2D) juntamente com a Recuperação do Local proporciona uma proteção abrangente das cargas de trabalho do Azure VM. O S2D permite-lhe acolher um cluster de hóspedes em Azure. Isto é especialmente útil quando um VM acolhe uma aplicação crítica, como a camada SAP ASCS, o SQL Server ou o servidor de ficheiros scale-out.


### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastres VMware/servidor físico

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Sistema de ficheiros Linux BRTFS** | A Recuperação do Site suporta agora a replicação de VMware VMs com o sistema de ficheiros BRTFS. A replicação não é suportada se:<br/><br/>- O sub volume do sistema de ficheiros BTRFS é alterado após a replicação.<br/><br/>- O sistema de ficheiros está espalhado por vários discos.<br/><br/>- O sistema de ficheiros BTRFS suporta RAID.
**Windows Server 2019** | Suporte adicionado para máquinas que executam o Windows Server 2019.


## <a name="updates-january-2019"></a>Atualizações (janeiro 2019)


### <a name="accelerated-networking-azure-vms"></a>Rede acelerada (Azure VMs)

A rede acelerada permite a virtualização de E/S de raiz única (SR-IOV) a um VM, melhorando o desempenho da rede. Quando ativa a replicação de um VM Azure, a Recuperação do Local deteta se a rede acelerada está ativada. Se for, após falha de funcionamento, a Recuperação do Site configura automaticamente a rede acelerada na réplica-alvo Azure VM, tanto para [o Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) como para [o Linux](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

[Saiba mais](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>Atualização rollup 32

[O rollup de atualização 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização aos agentes e fornecedores de Recuperação de Sítio (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres em Azure VM

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Apoio Técnico para Linux** | O suporte foi adicionado para redHat Workstation 6/7, e novas versões de kernel para Ubuntu, Debian e SUSE.
**Direto de Espaços de Armazenamento** | A Recuperação do Site suporta VMs Azure utilizando espaços de armazenamento direto (S2D).

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>VMware VMs/recuperação de desastres de servidores físicos

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Apoio Técnico para Linux** | O suporte foi adicionado para Redhat Enterprise Linux 7.6, RedHat Workstation 6/7, Oracle Linux 6.10 e Oracle Linux 7.6, e novas versões kernel para Ubuntu, Debian e SUSE.


### <a name="update-rollup-31"></a>Atualização rollup 31

[O rollup de atualização 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização aos agentes e fornecedores de Recuperação de Sítio (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).

### <a name="vmware-vmsphysical-servers-replication"></a>VMware VMs/replicação de servidores físicos

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Apoio Técnico para Linux** |  Foi adicionado suporte para Oracle Linux 6.8, Oracle Linux 6.9 e Oracle Linux 7.0 com o Kernel Compatível com chapéu vermelho, e para o Lançamento 5 do Kernel da Empresa Inquebrável (UEK).
**LVM** | Suporte adicionado para volumes LVM e LVM2.<br/><br/> O diretório /boot numa partição de disco e nos volumes LVM está agora suportado.
**Diretórios** | Foi adicionado suporte para estes diretórios criados como divisórias separadas, ou sistemas de ficheiros que não estão no mesmo disco do sistema:<br/><br/> /(raiz), /boot, /usr, /usr/local, /var, /etc.
**Windows Server 2008** | Suporte adicionado para discos dinâmicos.
**Ativação pós-falha** | Melhor tempo de insímio para VMware VMs onde storvsc e vsbus não são motoristas de arranque.
**Suporte UEFI** | Os VMs Azure não suportam o tipo de bota UEFI. Agora pode migrar servidores físicos no local com UEFI para Azure com a Recuperação do Site. A Recuperação do Site migra o servidor convertendo o tipo de bota para BIOS antes da migração. A Recuperação do Site apoiou previamente esta conversão apenas para VMs. O suporte está disponível para servidores físicos que executam o Windows Server 2012 ou mais tarde.

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres em Azure VM

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Apoio Técnico para Linux** | Foi adicionado suporte para Oracle Linux 6.8, Oracle Linux 6.9 e Oracle Linux 7.0 com o Kernel Compatível com chapéu vermelho, e para o Lançamento 5 do Kernel da Empresa Inquebrável (UEK).
**Sistema de ficheiros Linux BRTFS** | Apoiado para VMs Azure.
**VMs Azure em zonas de disponibilidade** | Pode permitir a replicação para outra região para VMs Azure implantados em zonas de disponibilidade. Agora pode ativar a replicação de um VM Azure, e definir o alvo para falha de falha para uma única instância VM, um VM em um conjunto de disponibilidade, ou um VM em uma zona de disponibilidade. A definição não afeta a replicação. [Leia](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) o anúncio.
**Armazenamento ativado por firewall (portal/PowerShell)** | Suporte adicionado para [contas de armazenamento ativadas por firewall](../storage/common/storage-network-security.md).<br/><br/> Pode replicar VMs Azure com discos não geridos em contas de armazenamento ativadas por firewall para outra região de Azure para a recuperação de desastres.<br/><br/> Pode utilizar contas de armazenamento ativadas por firewall como contas de armazenamento de alvo para discos não geridos.<br/><br/> Suportado no portal e utilizando o PowerShell.

## <a name="updates-december-2018"></a>Atualizações (dezembro de 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Atualizações automáticas para o serviço de Mobilidade (VMs Azure)

A Recuperação do Site adicionou uma opção para atualizações automáticas à extensão do serviço mobility. A extensão do serviço mobility é instalada em cada VM Azure replicado pela Recuperação do Local. Quando ativa a replicação, seleciona se permite a Recuperação do Site para gerir atualizações da extensão.

As atualizações não requerem um reinício de VM e não afetam a replicação. [Saiba mais](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Calculadora de preços para recuperação de desastres em Azure VM

A recuperação de desastres dos VMs Azure incorre nos custos de licenciamento de VM e nos custos de rede e armazenamento. A Azure fornece uma [calculadora de preços](https://aka.ms/a2a-cost-estimator) para ajudá-lo a descobrir estes custos. A Recuperação do Site fornece agora uma [estimativa de preços](https://aka.ms/a2a-cost-estimator) de exemplo que preços uma implementação de amostra com base numa aplicação de três níveis usando seis VMs com 12 discos HDD standard e 6 discos Premium SSD.

- A amostra pressupõe uma alteração de dados de 10 GB por dia para o padrão, e de 20 GB para prémio.
- Para a sua implantação em particular, pode alterar as variáveis para estimar custos.
- Pode especificar o número de VMs, o número e o tipo de discos geridos, e a taxa de alteração total esperada esperada em todos os VMs.
- Além disso, pode aplicar um fator de compressão para estimar os custos de largura de banda.

[Leia](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) o anúncio.


## <a name="updates-october-2018"></a>Atualizações (outubro 2018)

### <a name="update-rollup-30"></a>Atualização rollup 30

[O rollup de atualização 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização aos agentes e fornecedores de Recuperação de Sítio (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres em Azure VM
As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Suporte de região** | Suporte de recuperação do local adicionado para Austrália Central 1 e Austrália Central 2.
**Suporte para encriptação de discos** | Suporte adicionado para recuperação de desastres de VMs Azure encriptados com Encriptação de Disco Azure (ADE) com a aplicação AD AZure. [Saiba mais](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Exclusão do disco** | Os discos não iniciados são agora automaticamente excluídos durante a replicação do Azure VM.
**Armazenamento ativado por firewall (PowerShell)** | Suporte adicionado para [contas de armazenamento ativadas por firewall](../storage/common/storage-network-security.md).<br/><br/> Pode replicar VMs Azure com discos não geridos em contas de armazenamento ativadas por firewall para outra região de Azure para a recuperação de desastres.<br/><br/> Pode utilizar contas de armazenamento ativadas por firewall como contas de armazenamento de alvo para discos não geridos.<br/><br/> Suportado apenas com PowerShell.


### <a name="update-rollup-29"></a>Atualização rollup 29

[O rollup de atualização 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização aos agentes e fornecedores de Recuperação de Sítio (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).


## <a name="updates-august-2018"></a>Atualizações (agosto 2018)

### <a name="update-rollup-28"></a>Atualização rollup 28

[O rollup de atualização 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização aos agentes e fornecedores de Recuperação de Sítio (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres em Azure VM
As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Apoio Técnico para Linux** | Adicionado suportado para RedHat Enterprise Linux 6.10; CentOS 6.10.<br/><br/>
**Suporte à nuvem** | Apoiou a recuperação de desastres para os VMs do Azure na nuvem alemã.
**Recuperação de desastres de subscrição cruzada** | Apoio à replicação de VMs Azure numa região para outra região numa subscrição diferente, dentro do mesmo inquilino do Azure Ative Directory. [Saiba mais](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/recuperação de desastres de servidor físico
As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Apoio Técnico para Linux** | Suporte adicionado para RedHat Enterprise Linux 6.10, CentOS 6.10.<br/><br/> Os VMs baseados em Linux que utilizam o estilo de partição da tabela GUID (GPT) no modo de compatibilidade BIOS legacy são agora suportados. Reveja as [FAQ Azure VM](../virtual-machines/faq-for-disks.md) para obter mais informações.
**Recuperação de desastres para VMs após migração** | Suporte para permitir a recuperação de desastres para uma região secundária para um VMware VM no local migraram para Azure, sem necessidade de desinstalar o serviço de Mobilidade no VM antes de permitir a replicação.
**Windows Server 2008** | Suporte para máquinas migratórias que executam o Windows Server 2008 R2/2008 64-bits e 32-bit.<br/><br/> Migração (replicação e failover). O failback não é apoiado.

## <a name="updates-july-2018"></a>Atualizações (julho 2018)

### <a name="update-rollup-27-july-2018"></a>Atualização rollup 27 (julho 2018)

[O rollup de atualização 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualizar** | **Detalhes**
--- | ---
**Fornecedores e agentes** | Uma atualização aos agentes e fornecedores de Recuperação de Sítio (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres em Azure VM

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Apoio Técnico para Linux** | Suporte adicionado para Red Hat Enterprise Linux 7.5.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/recuperação de desastres de servidor físico

As funcionalidades adicionadas este mês são resumidas na tabela.

**Funcionalidade** | **Detalhes**
--- | ---
**Apoio Técnico para Linux** | Suporte adicionado para Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Próximos passos

Mantenha-se atualizado com as nossas atualizações na página [Azure Updates.](https://azure.microsoft.com/updates/?product=site-recovery)