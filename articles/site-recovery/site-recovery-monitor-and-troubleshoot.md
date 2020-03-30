---
title: Monitorizar a recuperação do site azure [ Microsoft Docs
description: Monitorizar e resolver problemas Problemas De recuperação de sítios azure problemas e operações usando o portal
author: raynew
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: raynew
ms.openlocfilehash: aa9d776df50306ab1705426c923413b5a5d545a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68717356"
---
# <a name="monitor-site-recovery"></a>Monitorizar o Site Recovery

Neste artigo, saiba como monitorizar a [Recuperação](site-recovery-overview.md)do Site Azure, utilizando a monitorização incorporada da Recuperação do Site.  Pode monitorizar:

- A saúde e o estado das máquinas replicadas pela Recuperação do Site
- Teste de falha no estado das máquinas.
- Problemas e erros que afetam a configuração e a replicação.
- Componentes de infraestrutura, tais como servidores no local.


## <a name="before-you-start"></a>Antes de começar

Talvez queira rever [questões comuns](monitoring-common-questions.md) de monitorização antes de começar.

## <a name="monitor-in-the-dashboard"></a>Monitor no painel de instrumentos

1. No cofre, clique em **Visão Geral**. O painel de serviços de recuperação consolida todas as informações de monitorização do cofre num único local. Existem páginas tanto para a Recuperação do Site como para o serviço de backup Azure, e pode alternar entre elas.

    ![Painel de recuperação do site](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2. A partir do tablier, faça uma perfuração em diferentes áreas. 

    ![Painel de recuperação do site](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. Em **itens replicados,** clique em **Ver Tudo** para ver todos os servidores no cofre.
4. Clique nos detalhes do estado em cada secção para perfurar.
5. Na **vista da Infraestrutura,** separe a informação de monitorização pelo tipo de máquinas que está a replicar.

## <a name="monitor-replicated-items"></a>Monitorizar itens replicados

Em **itens replicados,** monitorize a saúde de todas as máquinas do cofre que tenham replicação ativada.

**Estado** | **Detalhes**
--- | ---
Bom estado de funcionamento | A replicação está a progredir normalmente. Não são detetados erros ou sintomas de alerta.
Aviso | Um ou mais sintomas de alerta que podem ter impacto na replicação são detetados.
Crítica | Um ou mais sintomas críticos de erro de replicação foram detetados.<br/><br/> Estes sintomas de erro são tipicamente indicadores de que a replicação ficou presa, ou não progredindo tão rápido quanto a taxa de mudança de dados.
Não aplicável | Servidores que não se espera que estejam a replicar-se. Isto pode incluir máquinas que foram chumbadas.

## <a name="monitor-test-failovers"></a>Falhas no teste do monitor

No sucesso do **teste Failover,** monitorize o estado de falha das máquinas no cofre.

- Recomendamos que faça um teste de falha em máquinas replicadas pelo menos uma vez a cada seis meses. É uma forma de verificar se a falha está a funcionar como esperado, sem perturbar o ambiente de produção. 
- Um failover de teste só é considerado bem sucedido após a limpeza de failover e pós-falha ter concluído com sucesso.

**Estado** | **Detalhes**
--- | ---
Teste recomendado | Máquinas que não tiveram um teste falhado desde que a proteção foi ativada.
Realizado com sucesso | Máquinas com ou mais falhas de teste bem sucedidas.
Não aplicável | Máquinas que não são elegíveis para um teste falhado. Por exemplo, as máquinas que são falhadas, têm a replicação inicial/falha de teste/falha em curso.

## <a name="monitor-configuration-issues"></a>Monitorizar problemas de configuração

Em questões de **Configuração,** monitorize quaisquer problemas que possam afetar a sua capacidade de falhar com sucesso.

- Os problemas de configuração (exceto a disponibilidade de atualização de software), são detetados por uma operação validadora periódica que funciona a cada 12 horas por padrão. Pode forçar a operação validador a funcionar imediatamente clicando no ícone de atualização ao lado da rubrica de problemas de **configuração.**
- Clique nos links para obter mais detalhes. Para problemas que impactam máquinas específicas, clique **precisa de atenção** na coluna de configurações do **Target.** Os detalhes incluem recomendações de reparação.

**Estado** | **Detalhes**
--- | ---
Configurações em falta | Falta uma definição necessária, como uma rede de recuperação ou um grupo de recursos.
Recursos em falta | Um recurso especificado não pode ser encontrado ou não está disponível na subscrição. Por exemplo, o recurso foi apagado ou migrado. Os recursos monitorizados incluíam o grupo de recursos-alvo, o target VNet/subnet, a conta de armazenamento de registo/alvo, o conjunto de disponibilidade do alvo, o endereço IP alvo.
Quota de subscrição |  O saldo disponível das quotas de subscrição é comparado com o saldo necessário para falhar em todas as máquinas do cofre.<br/><br/> Se não houver recursos suficientes, é reportado um saldo de quota insuficiente.<br/><br/> As quotas são monitorização para a contagem de núcleos vM, contagem de núcleos familiares VM, contagem de cartão de interface de rede (NIC).
Atualizações de software | A disponibilidade de novas atualizações de software e informações sobre a expiração de versões de software.


## <a name="monitor-errors"></a>Monitorizar erros

No **resumo do Erro,** monitorize atualmente sintomas de erro ativos que podem afetar a replicação dos servidores no cofre e monitorize o número de máquinas afetadas.

- Os erros com impacto nos componentes da infraestrutura no local são o início da secção. Por exemplo, não receber um batimento cardíaco do Fornecedor de Recuperação do Site Azure no servidor de configuração no local ou no hospedeiro Hyper-V.
- Em seguida, são apresentados sintomas de erro de replicação que impactam os servidores replicados.
- As entradas de tabela são ordenadas pela ordem decrescente da gravidade do erro e, em seguida, diminuindo a ordem de contagem das máquinas impactadas.
- A contagem de servidores impactada é uma forma útil de entender se um único problema subjacente pode afetar várias máquinas. Por exemplo, uma falha de rede pode potencialmente afetar todas as máquinas que se replicam para O Azure. 
- Vários erros de replicação podem ocorrer num único servidor. Neste caso, cada sintoma de erro conta esse servidor na lista dos seus servidores com impacto. Após a questão ser corrigida, os parâmetros de replicação melhoram e o erro é eliminado da máquina.

## <a name="monitor-the-infrastructure"></a>Monitorize a infraestrutura.

Na **visão da Infraestrutura,** monitorize os componentes de infraestrutura envolvidos na replicação e a saúde de conectividade entre servidores e os serviços Azure.

- Uma linha verde indica que a ligação é saudável.
- Uma linha vermelha com o ícone de erro sobreposto indica a existência de um ou mais sintomas de erro que impactam a conectividade.
-  Passe o ponteiro do rato sobre o ícone de erro para mostrar o erro e o número de entidades afetadas. Clique no ícone para obter uma lista filtrada de entidades com impacto.

    ![Vista de infraestrutura de recuperação do local (cofre)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

### <a name="tips-for-monitoring-the-infrastructure"></a>Dicas para monitorizar a infraestrutura

- Certifique-se de que os componentes de infraestrutura no local (servidor de configuração, servidores de processos, servidores VMM, anfitriões Hyper-V, máquinas VMware) estão a executar as versões mais recentes do Fornecedor de Recuperação do Site e/ou agentes.
- Para utilizar todas as funcionalidades na vista da infraestrutura, deverá estar a executar o [rollup Update 22](https://support.microsoft.com/help/4072852) para estes componentes.
- Para utilizar a vista da infraestrutura, selecione o cenário de replicação apropriado no seu ambiente. Pode perfurar na vista para mais detalhes. A tabela que se segue mostra quais os cenários representados.

    **Cenário** | **Estado**  | **Vista disponível?**
    --- |--- | ---
    **Replicação entre locais no local** | Todos os estados | Não 
    **Réplica de VM azure entre regiões de Azure**  | Replicação ativada/replicação inicial em curso | Sim
    **Réplica de VM azure entre regiões de Azure** | Falhou por cima/falhar de volta | Não   
    **Replicação do VMware para o Azure** | Replicação ativada/replicação inicial em curso | Sim     
    **Replicação do VMware para o Azure** | Falhou/falhou nas costas | Não      
    **Replicação do Hyper-V para o Azure** | Falhou/falhou nas costas | Não

- Para ver a vista da infraestrutura para uma única máquina de replicação, no menu do cofre, clique **em itens Replicados**e selecione um servidor.  




## <a name="monitor-recovery-plans"></a>Monitorizar planos de recuperação

Nos **planos de Recuperação**, monitorize o número de planos, crie novos planos e modifique os existentes.  

## <a name="monitor-jobs"></a>Monitorizar trabalhos

Em **Empregos**, monitorize o estado das operações de recuperação do local.

- A maioria das operações na Recuperação do Local de Azure são executadas de forma assíncrona, com um trabalho de rastreio a ser criado e usado para acompanhar o progresso da operação. 
- O objeto de trabalho tem toda a informação necessária para rastrear o estado e o progresso da operação. 

Monitorize os trabalhos da seguinte forma:

1. Na secção > **Jobs,** pode ver-se um resumo dos postos de trabalho que terminaram, estão em curso, ou à espera de entrada, nas últimas 24 horas. Pode clicar em qualquer estado para obter mais informações sobre os trabalhos relevantes.
2. Clique em **ver tudo** para ver todos os trabalhos nas últimas 24 horas.

    > [!NOTE]
    > Também pode aceder a informações de trabalho a partir do menu do cofre > Trabalhos de Recuperação do **Site.** 

2. Na lista de Empregos de Recuperação do **Site,** é apresentada uma lista de empregos. No menu superior pode obter detalhes de erro para um trabalho específico, filtrar a lista de empregos com base em critérios específicos e exportar detalhes de emprego selecionados para o Excel.
3. Pode perfurar um trabalho clicando nele. 

## <a name="monitor-virtual-machines"></a>Monitorizar máquinas virtuais

Em **itens replicados,** obtenha uma lista de máquinas replicadas. 
    ![Visualização da lista de itens replicados da Recuperação do Site](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Pode ver e filtrar informação. No menu de ação no topo, pode realizar ações para uma determinada máquina, incluindo executar uma falha de teste ou visualizar erros específicos.
3. Clique em **Colunas** para mostrar colunas adicionais, por exemplo para mostrar RPO, problemas de configuração do alvo e erros de replicação.
4. Clique em **Filtro** para visualizar informações baseadas em parâmetros específicos, tais como a saúde da replicação, ou uma política de replicação específica.
5. Clique à direita numa máquina para iniciar operações como falha de teste para a sua, ou para visualizar detalhes de erro específicos associados à máquina.
6. Clique numa máquina para perfurar mais detalhes. Os detalhes incluem:
   - **Informações de replicação**: Estado atual e saúde da máquina.
   - **RPO** (objetivo do ponto de recuperação): RPO atual para a máquina virtual e o tempo em que o RPO foi calculado pela última vez.
   - **Pontos de recuperação**: Os últimos pontos de recuperação disponíveis para a máquina.
   - **Prontidão de failover**: Indica se foi executado um teste de falha para a máquina, a versão do agente em execução na máquina (para máquinas que executam o serviço de Mobilidade) e quaisquer problemas de configuração.
   - **Erros**: Lista de sintomas de erro de replicação atualmente observados na máquina, e possíveis causas/ações.
   - **Eventos**: Uma lista cronológica de eventos recentes com impacto na máquina. Os detalhes do erro mostram os sintomas de erro atualmente observáveis, enquanto os eventos são um registo histórico de problemas que impactaram a máquina.
   - **Vista de infraestrutura**: Mostra estado de infraestrutura para o cenário em que as máquinas estão a replicar-se para O Azure.

     ![Detalhes/visão geral do item de recuperação do site](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

## <a name="subscribe-to-email-notifications"></a>Subscreva notificações por e-mail

Pode subscrever para receber notificações por e-mail para estes eventos críticos:
 
- Estado crítico para máquina replicada.
- Não há conectividade entre os componentes da infraestrutura no local e o serviço de recuperação do local. A conectividade entre a Recuperação do Site e os servidores no local registados num cofre é detetada usando um mecanismo de batimento cardíaco.
- Falhas de falha.

Inscreva-se da seguinte forma:

Na secção de **monitorização** > do cofre, clique em Eventos de **Recuperação do Site**.
1. Clique em **Notificações por e-mail**.
1. Na notificação por **e-mail,** ligue as notificações e especifique a quem enviar. Pode enviar a todos os administradores de subscrição notificações e endereços de e-mail opcionalmente específicos.

    ![Notificações por e-mail](./media/site-recovery-monitor-and-troubleshoot/email.png)

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre](monitor-log-analytics.md) a monitorização da Recuperação do Site com o Monitor Azure.
