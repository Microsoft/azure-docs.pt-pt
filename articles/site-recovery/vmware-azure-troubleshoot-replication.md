---
title: Resolver problemas de replicação para a recuperação após desastre de VMs de VMware e servidores físicos para o Azure com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo fornece informações de resolução de problemas de replicação comuns durante a recuperação após desastre de VMs de VMware e servidores físicos para o Azure com o Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 06/27/2019
ms.author: mayg
ms.openlocfilehash: c005dcee78e2a9338dc7a816e06d9a78a2f355b6
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491678"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Resolver problemas de replicação de VMs de VMware e servidores físicos

Este artigo descreve alguns problemas comuns e os erros específicos que poderá encontrar ao replicar VMs de VMware no local e servidores físicos para o Azure com [recuperação de Site](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Passo 1: Estado de funcionamento de servidor de processos de monitor

Utilizações de recuperação de sites do [servidor de processos](vmware-physical-azure-config-process-server-overview.md#process-server) para receber e otimizar os dados replicados e enviá-lo para o Azure.

Recomendamos que monitoriza o estado de funcionamento de servidores de processos no portal, para garantir que estão ligados e a funcionar corretamente e que a replicação está em curso para as máquinas de origem associadas ao servidor de processo.

- [Saiba mais sobre](vmware-physical-azure-monitor-process-server.md) monitorização de servidores de processos.
- [Consultar as melhores práticas](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Resolver problemas de](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) processar o estado de funcionamento do servidor.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Passo 2: Resolver problemas de conectividade e replicação

Falhas de replicação iniciais e contínuas, muitas vezes, são causadas por problemas de conectividade entre o servidor de origem e o servidor de processos ou entre o servidor de processos e o Azure. 

Para resolver estes problemas, [resolver problemas de conectividade e replicação](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Passo 3: Resolver problemas relacionados com máquinas de origem que não estão disponíveis para a replicação

Quando tenta selecionar a máquina de origem para ativar a replicação utilizando a recuperação de Site, a máquina pode não estar disponível para um dos seguintes motivos:

* **Duas máquinas virtuais com o mesmo UUID da instância**: Se duas máquinas virtuais sob o vCenter tiver a mesma instância UUID, a primeira máquina virtual detetada pelo servidor de configuração é apresentada no portal do Azure. Para resolver este problema, certifique-se de que não existem duas máquinas virtuais têm a mesma instância UUID. Este cenário é frequentemente visto em instâncias em que uma VM de cópia de segurança é ativada e está registada na nossa registos de deteção. Consulte [do Azure Site Recovery VMware para o Azure: Como limpar entradas duplicadas ou obsoletas](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) para resolver.
* **As credenciais de utilizador do vCenter incorreto**: Certifique-se de que adicionou as credenciais do vCenter correto quando configurou o servidor de configuração com o modelo OVF ou a configuração unificada. Para verificar as credenciais que foi adicionado durante a configuração, consulte [modificar as credenciais para a deteção automática](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **privilégios insuficientes do vCenter**: Se as permissões fornecidas para aceder ao vCenter não tem as permissões necessárias, pode ocorrer Falha ao detetar máquinas virtuais. Certifique-se de que as permissões descritas [preparar uma conta para a deteção automática](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) são adicionados para a conta de utilizador do vCenter.
* **Servidores de gestão do Azure Site Recovery**: Se a máquina virtual for utilizada como servidor de gestão numa ou mais das seguintes funções - configuração /scale-out de servidor servidor de processos / principal do servidor de destino, em seguida, não poderá escolher a máquina virtual a partir do portal. Servidores de gestão não podem ser replicadas.
* **Já protegidos/ativação pós-falha através dos serviços do Azure Site Recovery**: Se a máquina virtual já está protegida ou a ativação pós-falha através do Site Recovery, a máquina virtual não está disponível para selecionar para a proteção no portal. Certifique-se de que a máquina virtual que está procurando no portal do já não está protegida por qualquer outro utilizador ou numa subscrição diferente.
* **vCenter não ligado**: Verifique se o vCenter está no estado ligado. Para verificar, aceda ao Cofre dos Recovery Services > infraestrutura do Site Recovery > servidores de configuração > clique no servidor de configuração respetivos > é aberto um painel de diretamente com os detalhes de servidores associados. Verifique se o vCenter está ligado. Se estiver num estado "Não ligado", resolver o problema e, em seguida [atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server) no portal. Depois disso, máquina virtual será listada no portal.
* **ESXi está desligada**: Se o anfitrião ESXi em que reside a máquina virtual está num Estado, desligado, em seguida, máquina virtual não será apresentada ou não podem ser selecionada no portal do Azure. Ligar o anfitrião ESXi, [atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server) no portal. Depois disso, máquina virtual será listada no portal.
* **Reinício pendente**: Se existir um reinício pendente na máquina virtual, em seguida, não será possível selecionar a máquina no portal do Azure. Certifique-se para concluir as atividades de reinício pendente [atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Depois disso, máquina virtual será listada no portal.
* **IP não foi encontrado**: Se a máquina virtual não tiver um endereço IP válido associado ao mesmo, em seguida, não será possível selecionar a máquina no portal do Azure. Certifique-se de atribuir um endereço IP válido para a máquina virtual, [atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Depois disso, máquina virtual será listada no portal.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Máquinas virtuais protegidas esbatidas no portal de resolução de problemas

Máquinas virtuais que são replicadas na recuperação de sites não estão disponíveis no portal do Azure se existirem entradas duplicadas no sistema. Para saber como eliminar entradas obsoletas e resolver o problema, consulte [Azure Site Recovery VMware para o Azure: Como limpar entradas duplicadas ou obsoletas](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="common-errors-and-solutions"></a>Erros comuns e soluções

### <a name="initial-replication-issues-error-78169"></a>Problemas da replicação inicial [Erro 78169]

Ao longo de um garantir acima que existem sem conectividade, largura de banda ou tempo sincronizar problemas relacionados, certifique-se de que:

- Nenhum software antivírus está a bloquear o Azure Site Recovery. Saiba mais [mais](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) no exclusões de pastas necessárias para o Azure Site Recovery.

### <a name="missing-app-consistent-recovery-points-error-78144"></a>Pontos de recuperação consistente com a aplicação em falta [Erro 78144]

 Isto acontece devido a problemas com a cópia de sombra de volumes (VSS) do serviço. Para resolver este problema: 
 
- Certifique-se de que a versão instalada do agente do Azure Site Recovery é, pelo menos, 9.22.2. 
- Certifique-se de que o fornecedor do VSS está instalado como um serviço nos serviços do Windows e também verificar se o serviço de componentes do MMC para verificar se o fornecedor de VSS do Azure Site Recovery está listado.
- Se o fornecedor do VSS não está instalado, consulte a [artigo de resolução de problemas: Falha na instalação](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

- Se estiver desativado o VSS,
    - Certifique-se de que o tipo de arranque do serviço fornecedor do VSS está definido como **automática**.
    - Reinicie os seguintes serviços:
        - Serviço VSS
        - Fornecedor VSS do Azure Site Recovery
        - Serviço VDS

- Se estiver a executar cargas de trabalho do SQL ou o Exchange, verifique os registos desses criadores de aplicações para as falhas. Erros frequentes e respectivas resoluções são capturados nos seguintes artigos:
    -  [Opção de fecho automático da base de dados do SQL Server está definida como TRUE](https://support.microsoft.com/help/4504104)
    - [Gerar um erro sem repetição do SQL Server 2008 R2](https://support.microsoft.com/help/4504103)
    - [Problema conhecido no SQL Server 2016 e 2017](https://support.microsoft.com/help/4493364)
    - [Problema comum com servidores de Exchange 2013 e 2016](https://support.microsoft.com/help/4037535)


### <a name="source-machines-with-high-churn-error-78188"></a>Máquinas de origem com grande fluxo de dados [Erro 78188]

Causas possíveis:
- A taxa de alteração de dados (bytes escritos/seg) nos discos listados da máquina virtual é mais do que o [limites de suporte do Azure Site Recovery](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) para o tipo de conta de armazenamento de destino de replicação.
- Não existe um pico da taxa de abandono devido a qual elevada quantidade de dados está pendente para carregamento.

Para resolver o problema:
- Certifique-se de que o tipo de conta de armazenamento destino (Standard ou Premium) é aprovisionado de acordo com o requisito de taxa de alterações a dados na origem.
- Se o volume de alterações observado é temporário, aguarde algumas horas até que o carregamento de dados pendentes para acompanhar e criar pontos de recuperação.
- Se o problema persistir, utilize o Site Recovery [Planeador de implementações](site-recovery-deployment-planner.md#overview) para ajudar a planear a replicação.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Máquinas de origem com nenhum heartbeat [Erro 78174]

Isto acontece quando o agente de mobilidade do Azure Site Recovery na máquina de origem não está a comunicar com o servidor de configuração (CS).

Para resolver o problema, utilize os seguintes passos para verificar a conectividade de rede de VM de origem para o servidor de configuração:

1. Certifique-se de que a máquina de origem está em execução.
2. Inicie sessão no computador de origem com uma conta que tenha privilégios de administrador.
3. Certifique-se de que os seguintes serviços estão em execução e se não reiniciar os serviços:
   - Svagents (InMage Scout VX Agent)
   - Serviço InMage Scout Application
4. No computador de origem, examine os registos no local para obter detalhes de erro:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>Servidor de processos com nenhum heartbeat [Erro 806]
Caso não haja nenhum heartbeat do processo de servidor (PS), verifique se a:
1. PS VM está em execução
2. Verifique o seguinte inicia sessão PS para detalhes do erro:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Servidor de destino mestre com nenhum heartbeat [Erro 78022]

Isto acontece quando o agente de mobilidade de recuperação de Site do Azure no destino mestre não está a comunicar com o servidor de configuração.

Para resolver o problema, utilize os seguintes passos para verificar o estado do serviço:

1. Certifique-se de que a VM de destino mestre está em execução.
2. Inicie sessão para a VM de destino mestre usando uma conta que tenha privilégios de administrador.
    - Certifique-se de que o serviço de svagents está em execução. Se estiver em execução, reinicie o serviço
    - Verifique os registos no local para obter detalhes de erro:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log



## <a name="next-steps"></a>Passos Seguintes

Se precisar de mais ajuda, publique sua pergunta no [fórum do Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Temos uma Comunidade ativa e um dos nossos engenheiros pode a ajudá-lo.
