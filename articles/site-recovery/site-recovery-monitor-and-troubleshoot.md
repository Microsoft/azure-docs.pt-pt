---
title: Monitorar Azure Site Recovery | Microsoft Docs
description: Monitorar e solucionar problemas de Azure Site Recovery de replicação e operações usando o portal
author: raynew
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: raynew
ms.openlocfilehash: aa9d776df50306ab1705426c923413b5a5d545a5
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717356"
---
# <a name="monitor-site-recovery"></a>Site Recovery de monitor

Neste artigo, saiba como monitorar o Azure [site Recovery](site-recovery-overview.md), usando site Recovery monitoramento embutido.  Você pode monitorar:

- A integridade e o status dos computadores replicados por Site Recovery
- Status do failover de teste de computadores.
- Problemas e erros que afetam a configuração e a replicação.
- Componentes de infraestrutura, como servidores locais.


## <a name="before-you-start"></a>Antes de começar

Talvez você queira examinar as [perguntas comuns de monitoramento](monitoring-common-questions.md) antes de começar.

## <a name="monitor-in-the-dashboard"></a>Monitorar no painel

1. No cofre, clique em **visão geral**. O painel dos serviços de recuperação consolida todas as informações de monitoramento do cofre em um único local. Há páginas para Site Recovery e o serviço de backup do Azure, e você pode alternar entre elas.

    ![Painel do Site Recovery](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2. No painel, faça uma busca detalhada em áreas diferentes. 

    ![Painel do Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. Em **itens replicados**, clique em **Exibir tudo** para ver todos os servidores no cofre.
4. Clique nos detalhes de status em cada seção para fazer drill down.
5. No **modo de exibição de infraestrutura**, classifique as informações de monitoramento pelo tipo de máquinas que você está replicando.

## <a name="monitor-replicated-items"></a>Monitorar itens replicados

Em **itens replicados**, monitore a integridade de todos os computadores no cofre que têm a replicação habilitada.

**Status** | **Detalhes**
--- | ---
Bom estado de funcionamento | A replicação está progredindo normalmente. Nenhum sintoma de erro ou aviso detectado.
Aviso | Um ou mais sintomas de aviso que podem afetar a replicação são detectados.
Crítico | Um ou mais sintomas críticos de erro de replicação foram detectados.<br/><br/> Esses sintomas de erro normalmente são indicadores de que a replicação travou ou que não está progredindo tão rápido quanto a taxa de alteração de dados.
Não aplicável | Servidores que atualmente não são esperados para replicação. Isso pode incluir computadores com failover.

## <a name="monitor-test-failovers"></a>Monitorar failovers de teste

Em **teste de failover com êxito**, monitore o status de failover para computadores no cofre.

- Recomendamos que você execute um failover de teste em máquinas replicadas pelo menos uma vez a cada seis meses. É uma maneira de verificar se o failover está funcionando conforme o esperado, sem interromper o ambiente de produção. 
- Um failover de teste é considerado com êxito somente depois que o failover e a limpeza após o failover forem concluídos com êxito.

**Status** | **Detalhes**
--- | ---
Teste recomendado | Computadores que não tinham um failover de teste desde que a proteção foi habilitada.
Executado com êxito | Computadores com ou mais failovers de teste bem-sucedidos.
Não aplicável | Computadores que atualmente não estão qualificados para um failover de teste. Por exemplo, máquinas que passaram por failover, têm failover inicial de replicação/teste/failover em andamento.

## <a name="monitor-configuration-issues"></a>Monitorar problemas de configuração

Em **problemas de configuração**, monitore quaisquer problemas que possam afetar a sua capacidade de fazer failover com êxito.

- Os problemas de configuração (exceto para a disponibilidade de atualização de software) são detectados por uma operação de validador periódica que é executada a cada 12 horas por padrão. Você pode forçar a execução imediata da operação do validador clicando no ícone de atualização ao lado do título da seção **problemas de configuração** .
- Clique nos links para obter mais detalhes. Para problemas que afetam computadores específicos, clique em **requer atenção** na coluna **configurações de destino** . Os detalhes incluem recomendações de correção.

**Status** | **Detalhes**
--- | ---
Configurações ausentes | Uma configuração necessária está ausente, como uma rede de recuperação ou um grupo de recursos.
Recursos ausentes | Um recurso especificado não pode ser encontrado ou não está disponível na assinatura. Por exemplo, o recurso foi excluído ou migrado. Os recursos monitorados incluíam o grupo de recursos de destino, VNet/sub-rede de destino, conta de armazenamento de log/destino, conjunto de disponibilidade de destino, endereço IP de destino.
Cota de assinatura |  O saldo de cota de recursos de assinatura disponível é comparado com o saldo necessário para fazer failover de todos os computadores no cofre.<br/><br/> Se não houver recursos suficientes, um saldo de cota insuficiente será relatado.<br/><br/> As cotas estão monitorando a contagem de núcleos de VM, contagem de núcleos da família de VMs, contagem de NIC (placa de interface de rede).
Atualizações de software | A disponibilidade de novas atualizações de software e informações sobre a expiração de versões de software.


## <a name="monitor-errors"></a>Monitorar erros

Em **Resumo de erros**, monitore os sintomas de erro ativos no momento que podem afetar a replicação de servidores no cofre e monitore o número de máquinas afetadas.

- Os erros que afetam os componentes da infraestrutura local são mostrados no início da seção. Por exemplo, não receber uma pulsação do provedor de Azure Site Recovery no servidor de configuração local ou host do Hyper-V.
- Em seguida, os sintomas de erro de replicação que afetam os servidores replicados são mostrados.
- As entradas de tabela são classificadas por ordem decrescente da gravidade do erro e, em seguida, por meio da redução da ordem de contagem dos computadores afetados.
- A contagem de servidores impactados é uma maneira útil de entender se um único problema subjacente pode afetar vários computadores. Por exemplo, uma falha de rede pode afetar potencialmente todos os computadores que replicam para o Azure. 
- Vários erros de replicação podem ocorrer em um único servidor. Nesse caso, cada sintoma de erro conta esse servidor na lista de seus servidores afetados. Depois que o problema for corrigido, os parâmetros de replicação melhorarão e o erro será removido do computador.

## <a name="monitor-the-infrastructure"></a>Monitore a infraestrutura.

No **modo de exibição de infraestrutura**, monitore os componentes de infraestrutura envolvidos na replicação e a integridade da conectividade entre os servidores e os serviços do Azure.

- Uma linha verde indica que a conexão está íntegra.
- Uma linha vermelha com o ícone de erro sobreposto indica a existência de um ou mais sintomas de erro que afetam a conectividade.
-  Passe o ponteiro do mouse sobre o ícone de erro para mostrar o erro e o número de entidades afetadas. Clique no ícone de uma lista filtrada de entidades afetadas.

    ![Exibição de infraestrutura do Site Recovery (cofre)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

### <a name="tips-for-monitoring-the-infrastructure"></a>Dicas para monitorar a infraestrutura

- Verifique se os componentes da infraestrutura local (servidor de configuração, servidores de processo, servidores VMM, hosts Hyper-V, máquinas VMware) estão executando as versões mais recentes do provedor de Site Recovery e/ou agentes.
- Para usar todos os recursos no modo de exibição de infraestrutura, você deve estar executando o [pacote cumulativo de atualizações 22](https://support.microsoft.com/help/4072852) para esses componentes.
- Para usar o modo de exibição de infraestrutura, selecione o cenário de replicação apropriado em seu ambiente. Você pode fazer uma busca detalhada na exibição para obter mais detalhes. A tabela a seguir mostra quais cenários são representados.

    **Cenário** | **Status**  | **Exibição disponível?**
    --- |--- | ---
    **Replicação entre sites locais** | Todos os estados | Não 
    **Replicação de VM do Azure entre regiões do Azure**  | Replicação habilitada/replicação inicial em andamento | Sim
    **Replicação de VM do Azure entre regiões do Azure** | Failover/failback | Não   
    **Replicação do VMware para o Azure** | Replicação habilitada/replicação inicial em andamento | Sim     
    **Replicação do VMware para o Azure** | Failover/failback reprovado | Não      
    **Replicação do Hyper-V para o Azure** | Failover/failback reprovado | Não

- Para ver o modo de exibição de infraestrutura para um único computador de replicação, no menu do cofre, clique em **itens replicados**e selecione um servidor.  




## <a name="monitor-recovery-plans"></a>Monitorar planos de recuperação

Em **planos de recuperação**, monitore o número de planos, crie novos planos e modifique os existentes.  

## <a name="monitor-jobs"></a>Monitorizar trabalhos

Em **trabalhos**, monitore o status das operações de site Recovery.

- A maioria das operações no Azure Site Recovery são executadas de forma assíncrona, com um trabalho de rastreamento sendo criado e usado para acompanhar o progresso da operação. 
- O objeto de trabalho tem todas as informações de que você precisa para acompanhar o estado e o andamento da operação. 

Monitore trabalhos da seguinte maneira:

1. Na seção Dashboard > **Jobs** , você pode ver um resumo dos trabalhos que foram concluídos, estão em andamento ou aguardando a entrada nas últimas 24 horas. Você pode clicar em qualquer Estado para obter mais informações sobre os trabalhos relevantes.
2. Clique em **Exibir tudo** para ver todos os trabalhos nas últimas 24 horas.

    > [!NOTE]
    > Você também pode acessar as informações do trabalho no menu do cofre > **site Recovery trabalhos**. 

2. Na lista **trabalhos de site Recovery** , uma lista de trabalhos é exibida. No menu superior, você pode obter detalhes do erro para um trabalho específico, filtrar a lista de trabalhos com base em critérios específicos e exportar detalhes do trabalho selecionado para o Excel.
3. Você pode analisar um trabalho clicando nele. 

## <a name="monitor-virtual-machines"></a>Monitorizar máquinas virtuais

Em **itens replicados**, obtenha uma lista de máquinas replicadas. 
    ![Exibição da lista de itens replicados Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Você pode exibir e filtrar informações. No menu Ação na parte superior, você pode executar ações para um computador específico, incluindo a execução de um failover de teste ou a exibição de erros específicos.
3. Clique em **colunas** para mostrar colunas adicionais, por exemplo, para mostrar RPO, problemas de configuração de destino e erros de replicação.
4. Clique em **Filtrar** para exibir informações com base em parâmetros específicos, como integridade da replicação ou uma determinada política de replicação.
5. Clique com o botão direito do mouse em um computador para iniciar operações como failover de teste para ela ou para exibir detalhes de erro específicos associados a ela.
6. Clique em um computador para analisar mais detalhes sobre ele. Os detalhes incluem:
   - **Informações de replicação**: Status atual e integridade do computador.
   - **RPO** (objetivo de ponto de recuperação): O RPO atual para a máquina virtual e a hora em que o RPO foi calculado pela última vez.
   - **Pontos de recuperação**: Pontos de recuperação mais recentes disponíveis para o computador.
   - **Prontidão de failover**: Indica se um failover de teste foi executado para o computador, a versão do agente em execução no computador (para computadores que executam o serviço de mobilidade) e quaisquer problemas de configuração.
   - **Erros**: Lista de sintomas de erro de replicação observados no momento no computador e possíveis causas/ações.
   - **Eventos**: Uma lista cronológica de eventos recentes que afetam o computador. Os detalhes do erro mostram os sintomas de erro observáveis, enquanto os eventos são um registro histórico de problemas que afetaram a máquina.
   - **Exibição de infraestrutura**: Mostra o estado da infraestrutura para o cenário quando os computadores estão replicando para o Azure.

     ![Detalhes do item replicado Site Recovery/visão geral](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

## <a name="subscribe-to-email-notifications"></a>Assinar notificações por email

Você pode assinar para receber notificações por email para esses eventos críticos:
 
- Estado crítico para o computador replicado.
- Nenhuma conectividade entre os componentes de infraestrutura local e o serviço de Site Recovery. A conectividade entre Site Recovery e servidores locais registrados em um cofre é detectada usando um mecanismo de pulsação.
- Falhas de failover.

Assine o seguinte:

Na seção **monitoramento** de > do cofre, clique em **site Recovery eventos**.
1. Clique em **notificações por email**.
1. Em **notificação por email**, ative as notificações e especifique para quem enviar. Você pode enviar para todos os administradores de assinatura para receber notificações e, opcionalmente, endereços de email específicos.

    ![Notificações de e-mail](./media/site-recovery-monitor-and-troubleshoot/email.png)

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais sobre como](monitor-log-analytics.md) monitorar Site Recovery com Azure monitor.
