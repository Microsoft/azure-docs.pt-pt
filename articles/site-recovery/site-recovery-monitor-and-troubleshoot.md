---
title: Monitor Azure Site Recovery | Microsoft Docs
description: Monitor e resolução de problemas Problemas de recuperação do site problemas e operações usando o portal
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.openlocfilehash: a80f68c230bfed5caad4fd5ce474d8d06fd3d093
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579313"
---
# <a name="monitor-site-recovery"></a>Monitorizar o Site Recovery

Neste artigo, aprenda a monitorizar a [Recuperação do Sítio](site-recovery-overview.md)Azure, utilizando a monitorização incorporada da Recuperação do Local.  Pode monitorizar:

- A saúde e o estado das máquinas replicadas pela Recuperação do Local
- Teste estado de falha das máquinas.
- Problemas e erros que afetam a configuração e a replicação.
- Componentes de infraestrutura, tais como servidores no local.


## <a name="before-you-start"></a>Antes de começar

É melhor rever [questões comuns de monitorização](monitoring-common-questions.md) antes de começar.

## <a name="monitor-in-the-dashboard"></a>Monitor no painel de instrumentos

1. No cofre, clique em **Overview**. O painel de instrumentos de recuperação consolida todas as informações de monitorização do cofre num único local. Existem páginas para a Recuperação do Site e para o serviço de backup Azure, e pode alternar entre elas.

    ![Painel de recuperação do local](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2. Do painel de instrumentos, descame em diferentes áreas. 

    ![Screenshot que mostra as áreas no painel de instrumentos onde você pode perfurar.](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. Em **Itens Replicados,** clique **em Ver Tudo** para ver todos os servidores no cofre.
4. Clique nos detalhes do estado em cada secção para perfurar.
5. Na **visão de Infraestrutura,** serdene a informação de monitorização pelo tipo de máquinas que está a replicar.

## <a name="monitor-replicated-items"></a>Monitorar itens replicados

Em **itens replicados,** monitorize a saúde de todas as máquinas do cofre que tenham ativação de replicação.

**Estado** | **Detalhes**
--- | ---
Bom estado de funcionamento | A replicação está a progredir normalmente. Não são detetados erros ou sintomas de aviso.
Aviso | Um ou mais sintomas de alerta que podem ter impacto na replicação são detetados.
Crítico | Um ou mais sintomas críticos de erro de replicação foram detetados.<br/><br/> Estes sintomas de erro são tipicamente indicadores que a replicação ficou, ou não progredindo tão rápido quanto a taxa de mudança de dados.
Não aplicável | Servidores que não se espera que estejam atualmente a replicar-se. Isto pode incluir máquinas que foram falhadas.

## <a name="monitor-test-failovers"></a>Monitorização de falhas no teste

No **sucesso do teste Failover,** monitorize o estado de falha das máquinas no cofre.

- Recomendamos que faça um teste de failover em máquinas replicadas pelo menos uma vez a cada seis meses. É uma forma de verificar se o fracasso está a funcionar como esperado, sem perturbar o seu ambiente de produção. 
- Um teste de failover só é considerado bem sucedido após a limpeza falhada e pós-falha ter concluído com sucesso.

**Estado** | **Detalhes**
--- | ---
Teste recomendado | Máquinas que não tiveram um teste de falha desde que a proteção foi ativada.
Realizado com sucesso | Máquinas com ou mais falhas de teste bem sucedidas.
Não aplicável | Máquinas que não são elegíveis para um teste de failover. Por exemplo, as máquinas que são falhadas, têm replicação inicial/teste failover/failover em curso.

## <a name="monitor-configuration-issues"></a>Problemas de configuração do monitor

Em **problemas de Configuração,** monitorize quaisquer problemas que possam afetar a sua capacidade de falhar com sucesso.

- Os problemas de configuração (exceto a disponibilidade de atualização de software), são detetados por uma operação validador periódica que funciona a cada 12 horas por predefinição. Pode forçar a operação do validador a funcionar imediatamente clicando no ícone de atualização ao lado do título da secção de problemas de **configuração.**
- Clique nos links para obter mais detalhes. Para problemas com impacto em máquinas específicas, clique em **necessidade de atenção** na coluna **de configurações Target.** Os detalhes incluem recomendações de reparação.

**Estado** | **Detalhes**
--- | ---
Configurações em falta | Falta uma definição necessária, como uma rede de recuperação ou um grupo de recursos.
Recursos em falta | Um recurso especificado não pode ser encontrado ou não está disponível na subscrição. Por exemplo, o recurso foi eliminado ou migrado. Os recursos monitorizados incluíam o grupo de recursos-alvo, o VNet/subnet alvo, a conta de armazenamento log/target, o conjunto de disponibilidade de destino, o endereço IP alvo.
Quota de assinatura |  O saldo disponível do contingente de recursos de subscrição é comparado com o saldo necessário para falhar em todas as máquinas do cofre.<br/><br/> Se não houver recursos suficientes, é reportado um saldo de quotas insuficiente.<br/><br/> As quotas estão a monitorizar a contagem de núcleos VM, a contagem de núcleos da família VM, a contagem de cartões de interface de rede (NIC).
Atualizações de software | A disponibilidade de novas atualizações de software e informações sobre a expiração das versões de software.


## <a name="monitor-errors"></a>Monitorizar erros

No **resumo do Erro,** monitorize atualmente sintomas de erro ativos que possam ter impacto na replicação de servidores no cofre e monitorize o número de máquinas com impacto.

- Os erros que afetam os componentes de infraestruturas no local são mostrados no início da secção. Por exemplo, não receber um batimento cardíaco do Fornecedor de Recuperação do Local Azure no servidor de configuração no local ou no anfitrião Hyper-V.
- Em seguida, são mostrados sintomas de erro de replicação que impactam servidores replicados.
- As entradas da tabela são ordenadas diminuindo a ordem da gravidade do erro e, em seguida, diminuindo a ordem de contagem das máquinas impactadas.
- A contagem de servidores impactadas é uma forma útil de entender se um único problema subjacente pode afetar várias máquinas. Por exemplo, uma falha de rede pode potencialmente afetar todas as máquinas que se replicam ao Azure. 
- Podem ocorrer vários erros de replicação num único servidor. Neste caso, cada sintoma de erro conta o servidor na lista dos seus servidores impactados. Após a fixação do problema, os parâmetros de replicação melhoram e o erro é apagado da máquina.

## <a name="monitor-the-infrastructure"></a>Monitorize a infraestrutura.

Na **visão de Infraestrutura,** monitorize os componentes de infraestrutura envolvidos na replicação e na saúde de conectividade entre os servidores e os serviços Azure.

- Uma linha verde indica que a ligação é saudável.
- Uma linha vermelha com o ícone de erro sobreposto indica a existência de um ou mais sintomas de erro que impactam a conectividade.
-  Passe o ponteiro do rato sobre o ícone de erro para mostrar o erro e o número de entidades impactadas. Clique no ícone para obter uma lista filtrada de entidades impactadas.

    ![Vista da infraestrutura de recuperação do local (cofre)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

### <a name="tips-for-monitoring-the-infrastructure"></a>Dicas para monitorizar a infraestrutura

- Certifique-se de que os componentes de infraestrutura no local (servidor de configuração, servidores de processos, servidores VMM, anfitriões Hiper-V, máquinas VMware) estão a executar as versões mais recentes do Fornecedor de Recuperação do Site e/ou agentes.
- Para utilizar todas as funcionalidades na vista da infraestrutura, deve estar a executar [o rollup 22](https://support.microsoft.com/help/4072852) de atualização para estes componentes.
- Para utilizar a vista de infraestrutura, selecione o cenário de replicação apropriado no seu ambiente. Pode aprofundar-se na vista para mais detalhes. A tabela a seguir mostra quais os cenários representados.

    **Cenário** | **Estado**  | **Vista disponível?**
    --- |--- | ---
    **Replicação entre locais no local** | Todos os estados | No 
    **Replicação Azure VM entre regiões de Azure**  | Replicação ativada/replicação inicial em curso | Sim
    **Replicação Azure VM entre regiões de Azure** | Falha por cima/falha de volta | No   
    **Replicação do VMware para o Azure** | Replicação ativada/replicação inicial em curso | Sim     
    **Replicação do VMware para o Azure** | Falhou/falhou nas costas | No      
    **Replicação do Hyper-V para o Azure** | Falhou/falhou nas costas | No

- Para ver a vista de infraestrutura para uma única máquina de replicação, no menu do cofre, clique em **itens replicados** e selecione um servidor.  




## <a name="monitor-recovery-plans"></a>Monitorizar planos de recuperação

Nos **planos de recuperação,** monitorize o número de planos, crie novos planos e modifique os existentes.  

## <a name="monitor-jobs"></a>Monitorizar trabalhos

Em **Jobs,** monitorize o estado das operações de Recuperação do Local.

- A maioria das operações na Recuperação do Local de Azure são executadas assíncronamente, com um trabalho de rastreio sendo criado e usado para acompanhar o progresso da operação. 
- O objeto do trabalho tem toda a informação necessária para seguir o estado e o progresso da operação. 

Monitorize os trabalhos da seguinte forma:

1. Na secção > **Jobs,** pode ver-se um resumo dos trabalhos que terminaram, estão em curso, ou à espera de entrada, nas últimas 24 horas. Você pode clicar em qualquer estado para obter mais informações sobre os trabalhos relevantes.
2. Clique **em Ver todos** para ver todos os trabalhos nas últimas 24 horas.

    > [!NOTE]
    > Também pode aceder a informações de trabalho a partir do menu do cofre > **Trabalhos de Recuperação do Local.** 

2. Na lista de **Empregos de Recuperação de Locais,** é apresentada uma lista de postos de trabalho. No menu superior pode obter detalhes de erro para um trabalho específico, filtrar a lista de empregos com base em critérios específicos e exportar detalhes de emprego selecionados para o Excel.
3. Pode perfurar um trabalho clicando nele. 

## <a name="monitor-virtual-machines"></a>Monitorizar máquinas virtuais

Em **itens replicados,** obtenha uma lista de máquinas replicadas. 
    ![Vista da lista de itens replicados da Recuperação do Site](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Pode ver e filtrar informações. No menu de ação no topo, pode executar ações para uma determinada máquina, incluindo executar um teste de failover, ou visualizar erros específicos.
3. Clique **em Colunas** para mostrar colunas adicionais, por exemplo para mostrar RPO, problemas de configuração de alvo e erros de replicação.
4. Clique **em Filter** para ver informações com base em parâmetros específicos, como a saúde da replicação, ou uma determinada política de replicação.
5. Clique com o botão direito numa máquina para iniciar operações como o teste de failover para o mesmo, ou para visualizar detalhes de erro específicos associados a ela.
6. Clique numa máquina para perfurar mais detalhes para a obter. Os detalhes incluem:
   - **Informações de replicação**: Estado atual e saúde da máquina.
   - **RPO** (objetivo do ponto de recuperação): RPO atual para a máquina virtual e o momento em que a RPO foi calculada pela última vez.
   - **Pontos de recuperação**: Últimos pontos de recuperação disponíveis para a máquina.
   - **Prontidão de falha**: Indica se foi executado um teste de falha para a máquina, a versão do agente em funcionamento na máquina (para as máquinas que executam o serviço mobility) e quaisquer problemas de configuração.
   - **Erros**: Lista de sintomas de erro de replicação atualmente observados na máquina e possíveis causas/ações.
   - **Eventos**: Uma lista cronológica de eventos recentes que impactam a máquina. Os detalhes de erro mostram os sintomas de erro atualmente observáveis, enquanto os eventos são um registo histórico de problemas que impactaram a máquina.
   - **Vista de infraestrutura**: Mostra estado de infraestrutura para o cenário quando as máquinas estão a replicar-se para Ozure.

     ![Detalhes/visão geral replicado da recuperação do site](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

## <a name="subscribe-to-email-notifications"></a>Subscreva notificações por e-mail

Pode subscrever notificações por e-mail para estes eventos críticos:
 
- Estado crítico para máquina replicada.
- Sem conectividade entre os componentes de infraestrutura no local e o serviço de Recuperação de Locais. A conectividade entre a Recuperação do Local e os servidores no local registados num cofre é detetada através de um mecanismo de batimentocardíaco.
- Falhas de falha.

Inscreva-se da seguinte forma:

Na secção de **monitorização** do cofre >, clique em **Eventos de Recuperação do Local**.
1. Clique em **Notificações por e-mail**.
1. Na **notificação por e-mail,** ligue as notificações e especifique para quem enviar. Pode enviar para todos os administradores de subscrição notificações e endereços de e-mail opcionalmente específicos.

    ![Notificações por e-mail](./media/site-recovery-monitor-and-troubleshoot/email.png)

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre](monitor-log-analytics.md) a monitorização da recuperação do local com o Monitor Azure.
