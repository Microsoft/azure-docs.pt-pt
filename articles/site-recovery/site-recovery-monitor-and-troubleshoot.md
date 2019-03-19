---
title: Monitorizar o Azure Site Recovery | Documentos da Microsoft
description: Monitorizar e resolver problemas de replicação do Azure Site Recovery e operações com o portal
author: bsiva
manager: abhemra
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: bsiva
ms.openlocfilehash: 5bbde2f1059982424c2557d6a767692b3491f6aa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58098479"
---
# <a name="monitor-and-troubleshoot-site-recovery"></a>Monitorizar e resolver problemas de recuperação de sites

Neste artigo, saiba como utilizar o Azure Site Recovery em funcionalidades de monitorização incorporadas para monitorização e resolução de problemas. 

## <a name="use-the-dashboard"></a>Utilizar o dashboard

1. No cofre, clique em **descrição geral** para abrir o dashboard do Site Recovery. Páginas de dashboard para o Site Recovery e a cópia de segurança, e pode alternar entre elas.

    ![Dashboard de recuperação de site](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2.  O dashboard consolida todas as informações de monitorização para o Cofre numa única localização. No dashboard, pode desagregar em áreas diferentes. 

    ![Dashboard de recuperação de site](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. No **itens replicados**, clique em **ver tudo** para ver todos os servidores no cofre.
4. Desagregar clicando os detalhes de estado em cada secção. Na **vista de infraestrutura**, pode classificar as informações de monitorização pelo tipo de máquinas que está a replicar.

## <a name="monitor-replicated-items"></a>Monitorizar itens replicados

A secção de itens replicados mostra o estado de funcionamento de todos os computadores que têm os replicação ativada no cofre.

**Estado** | **Detalhes**
--- | ---
Bom estado de funcionamento | Replicação está em curso normalmente. Não existem sintomas de aviso ou erro são detetados.
Aviso | Um ou mais sintomas de aviso que podem afetar a replicação são detetados.
Crítica | Foram detetados um ou mais sintomas de erro de replicação crítica.<br/><br/> Esses sintomas de erro, normalmente, são indicadores de que a replicação bloqueada ou não evoluiu tão rapidamente como taxa de alteração de dados.
Não aplicável | Servidores que não são atualmente esperados para ser a replicar. Isso pode incluir máquinas que têm falhadas.

## <a name="monitor-test-failovers"></a>Ativações pós-falha de teste do monitor

Pode ver o estado de ativação pós-falha de teste para máquinas no cofre.

- Recomendamos que execute uma ativação pós-falha de teste nas máquinas replicadas, pelo menos, uma vez a cada seis meses. É uma forma de verificar que a ativação pós-falha está a funcionar conforme esperado sem interromper o seu ambiente de produção. 
- Uma ativação pós-falha de teste é considerada concluída com êxito apenas depois de concluir a limpeza de ativação pós-falha e após a ativação pós-falha com êxito.

**Estado** | **Detalhes**
--- | ---
Teste recomendado | Máquinas que ainda não tinham uma ativação pós-falha de teste, uma vez que a proteção foi ativada.
Executado com êxito | As máquinas com ou ativações pós-falha de teste mais bem-sucedida.
Não aplicável | Máquinas que não são atualmente elegíveis para uma ativação pós-falha de teste. Por exemplo, máquinas com ativação pós-falha, tem a ativação pós-falha de replicação inicial/teste/ativação pós-falha em curso.

## <a name="monitor-configuration-issues"></a>Problemas de configuração de monitor

O **problemas de configuração** seção mostra uma lista dos problemas que possam afetar sua capacidade de falhar com êxito.

- Problemas de configuração (exceto para a disponibilidade de atualização de software), são detetados por uma operação de validador periódica que é executado a cada 12 horas por predefinição. Pode forçar a operação de validador executar imediatamente ao clicar no ícone de atualização junto a **problemas de configuração** cabeçalho de secção.
- Clique nas hiperligações para obter mais detalhes. Para problemas que afetam o máquinas específicas, clique nas **requer atenção** no **configurações de destino** coluna. Os detalhes incluem recomendações de correção.

**Estado** | **Detalhes**
--- | ---
Configurações em falta | Uma definição necessária está em falta, tal como uma rede de recuperação ou um grupo de recursos.
Recursos em falta | Um recurso especificado não foi encontrado ou não está disponível na subscrição. Por exemplo, o recurso foi eliminado ou migrado. Recursos monitorizados incluíam o grupo de recursos de destino, VNet/sub-rede de destino, conta de armazenamento de registo/destino, o conjunto de disponibilidade de destino, endereço IP de destino.
Quota de subscrição |  O saldo de quota de recursos de subscrição disponíveis é comparado com o equilíbrio necessário para efetuar a ativação pós-falha de todas as máquinas no cofre.<br/><br/> Se não existem recursos suficientes, é comunicado um saldo de quota insuficiente.<br/><br/> As quotas são monitorização para o número de núcleos VM, número de núcleos de família de VM, contagem de cartões (NIC) de interface de rede.
Atualizações de software | A disponibilidade de novas atualizações de software e informações sobre as versões de software prestes a expirar.


## <a name="monitoring-errors"></a>Erros de monitorização 
O **resumo de erro** secção mostra os sintomas de erro atualmente ativos que possam afetar a replicação dos servidores no cofre e o número de computadores afetados.

- No início da seção, são apresentados erros afetar os componentes de infraestrutura no local. Por exemplo, não-receção de um heartbeat a partir do Azure Site Recovery Provider em execução no servidor de configuração no local, o servidor VMM ou anfitrião Hyper-V.
- Em seguida, replicação erro os sintomas que podem afetar servidores replicadas são mostrados.
- As entradas de tabela são ordenadas por ordem decrescente de gravidade do erro e, em seguida, ao diminuir a ordem de contagem dos computadores afetados.
- A contagem de servidor afetado é uma forma útil para compreender se um único problema subjacente, poderá estar a afetar várias máquinas. Por exemplo, uma falha de rede potencialmente poderá afetar todas as máquinas a replicar para o Azure. 
- Vários erros de replicação podem ocorrer num único servidor. Neste caso, cada sintoma erro conta nesse servidor na lista de seus servidores afetados. Depois do problema estiver resolvido, melhorar a parâmetros de replicação e o erro é limpo da máquina.

## <a name="monitor-the-infrastructure"></a>Monitorize a infraestrutura.

O **vista de infraestrutura** mostra os componentes da infra-estrutura envolvido na replicação e estado de funcionamento de conectividade entre servidores e os serviços do Azure.

- Uma linha verde indica que a ligação está em bom estada.
- Uma linha vermelha com o ícone de erro por cima indica a existência de um ou mais sintomas de erro que afetar a conectividade.
-  Paire o ponteiro do mouse sobre o ícone de erro para mostrar o erro e o número de entidades afetados. Clique no ícone para uma lista filtrada de entidades afetadas.

    ![Vista de infraestrutura de recuperação do site (cofre)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

## <a name="tips-for-monitoring-the-infrastructure"></a>Sugestões para monitorizar a infraestrutura

- Certifique-se de que os componentes de infraestrutura no local (servidor de configuração, servidores de processos, servidores VMM, anfitriões Hyper-V, máquinas do VMware) são as versões mais recentes do Site Recovery Provider e/ou agentes em execução.
- Para utilizar todos os recursos na vista de infraestrutura, deve estar em execução [Update rollup 22](https://support.microsoft.com/help/4072852) para esses componentes.
- Para utilizar a vista de infraestrutura, selecione o cenário de replicação apropriado no seu ambiente. Pode desagregar numa vista para obter mais detalhes. A tabela seguinte mostra quais cenários são representados.

    **Cenário** | **Estado**  | **O modo de exibição disponível?**
    --- |--- | ---
    **Replicação entre sites no local** | Todos os estados | Não 
    **Replicação de VM do Azure entre regiões do Azure**  | Replicação ativada/inicial de replicação em curso | Sim
    **Replicação de VM do Azure entre regiões do Azure** | A ativação pós-falha / reativação pós-falha | Não   
    **Replicação de VMware para o Azure** | Replicação ativada/inicial de replicação em curso | Sim     
    **Replicação de VMware para o Azure** | Falha ao back/que não a ativação pós-falha | Não      
    **Replicação de Hyper-V para o Azure** | Falha ao back/que não a ativação pós-falha | Não

- Para ver a vista de infraestrutura para uma única máquina de replicação, no menu do cofre, clique em **itens replicados**e selecione um servidor.  

### <a name="common-questions"></a>Perguntas comuns


**Por que a contagem de máquinas virtuais na vista de infraestrutura de cofre diferente da contagem total de mostra os itens replicados?**

A vista de infraestrutura do cofre tem um âmbito por cenários de replicação. Apenas máquinas num cenário de replicação atualmente selecionado estão incluídas na contagem para a vista. Além disso, apenas Contamos VMs que estão configuradas para replicar para o Azure. Efetuar a ativação pós-falha de máquinas ou máquinas a replicar para um site no local não são contabilizadas na vista.

**Por que é a contagem de itens replicados, mostrado na gaveta de Essentials difere da contagem total de itens replicados no dashboard?**

Apenas máquinas para que a replicação inicial foi concluída, estão incluídas na contagem mostrada na gaveta de Essentials. Nos itens replicados a total inclui todas as máquinas no cofre, incluindo aqueles para os quais a replicação inicial está atualmente em curso.


## <a name="monitor-recovery-plans"></a>Planos de recuperação do monitor

Na **secção de planos de recuperação** pode rever o número de planos, criar novos planos e modificar as existentes.  

## <a name="monitor-jobs"></a>Monitorizar trabalhos

O **tarefas** secção reflete o estado das operações de recuperação de sites.

- A maioria das operações no Azure Site Recovery são executadas de forma assíncrona, com um controlo de tarefas que está a ser criada e usada para controlar o progresso da operação. 
- O objeto de tarefa tem todas as informações de que precisa controlar o estado e o progresso da operação. 

Monitorize tarefas da seguinte forma:

1. No dashboard > **tarefas** seção, pode ver um resumo das tarefas que foram concluídas, estão em progresso ou aguardar a entrada, nas últimas 24 horas. Pode clicar em qualquer Estado para obter mais informações sobre as tarefas relevantes.
2. Clique em **ver todos os** para ver todas as tarefas nas últimas 24 horas.

    > [!NOTE]
    > Também pode aceder a informações de tarefa no menu do cofre > **tarefas de recuperação de Site**. 

2. Na **tarefas de recuperação de Site** é apresentada a lista, uma lista de tarefas. No menu superior, pode obter detalhes do erro para um trabalhos específicos, filtrar a lista de tarefas com base em critérios específicos e exportação selecionado detalhes da tarefa para o Excel.
3. Pode explorar uma tarefa ao clicar no mesmo. 

## <a name="monitor-virtual-machines"></a>Monitorizar máquinas virtuais

No dashboard de adição, pode monitorar máquinas na página de máquinas virtuais. 

1. No cofre, clique em **itens replicados** para obter uma lista das máquinas replicadas.  Em alternativa, pode obter uma lista filtrada dos itens protegidos ao clicar em qualquer um dos atalhos âmbito na página do dashboard.

    ![Recuperação de site replicados a vista de lista de itens](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Sobre o **itens replicados** página, pode ver e filtrar informações. No menu de ação na parte superior, pode executar ações para uma máquina específica, incluindo a executar uma ativação pós-falha de teste ou ao visualizar os erros específicos.
3. Clique em **colunas** para mostrar colunas adicionais como, por exemplo, para mostrar o RPO, destino problemas de configuração e erros de replicação.
4. Clique em **filtro** para ver informações com base nos parâmetros específicos, como o estado de funcionamento da replicação ou uma política de replicação específico.
5. Com o botão direito uma máquina para iniciar a operações como a ativação pós-falha de teste para o mesmo ou para ver os detalhes de erro específico associados ao mesmo.
6. Clique numa máquina para explorar mais detalhes para o mesmo. Os detalhes incluem:
   - **Informações de replicação**: Estado atual e o estado de funcionamento da máquina.
   - **RPO** (objetivo de ponto de recuperação): RPO atual para a máquina virtual e a hora em que o RPO pela última vez foi computado.
   - **Pontos de recuperação**: Pontos de recuperação disponível mais recente para a máquina.
   - **Preparação de ativação pós-falha**: Indica se uma ativação pós-falha de teste foi executada para a máquina, a versão do agente em execução no computador (para máquinas que executa o serviço de mobilidade) e problemas de configuração.
   - **Erros**: Lista de sintomas de erro de replicação observada atualmente na máquina e ações/causas possíveis.
   - **Eventos**: Uma lista cronológica dos eventos recentes que afetem a máquina. Detalhes do erro mostra os sintomas de erro atualmente observable, enquanto os eventos é um registo histórico de problemas que tenham afetado a máquina.
   - **Vista de infraestrutura**: Mostra o estado da infraestrutura para o cenário de quando as máquinas estão a replicar para o Azure.

     ![Recuperação de site replicados item detalhes/descrição geral](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)


### <a name="common-questions"></a>Perguntas comuns

**O que diferencia RPO do ponto de recuperação mais recentes disponíveis?**


- Recuperação de site utiliza um processo assíncrono em várias etapas para replicar máquinas para o Azure.
- No passo a mais de replicação, as alterações recentes na máquina, juntamente com metadados, são copiadas para uma conta de armazenamento de registo/cache.
- Estas alterações, juntamente com a etiqueta para identificar um ponto de recuperável, são escritas para a conta de armazenamento na região de destino.
-  Recuperação de site pode agora gerar um ponto de recuperável para a máquina virtual.
- Neste momento, foi alcançado o RPO para que as alterações carregadas para a conta de armazenamento até agora. Em outras palavras, a máquina RPO neste momento é igual ao período de tempo decorrido desde o carimbo de hora correspondente para o ponto de recuperável.
- Agora, o Site Recovery escolhe os dados carregados da conta de armazenamento e o aplica os discos de réplica criados para a máquina.
- Recuperação de site, em seguida, gera um ponto de recuperação e faz com que este ponto disponíveis para recuperação a ativação pós-falha. Assim, o ponto de recuperação disponível mais recente indica o carimbo de hora correspondente para o ponto de recuperação mais recente que já tenha sido processado e aplicado para os discos de réplica.

> [!NOTE]
> O valor RPO calculado será desfasamento de um tempo de sistema incorreta na máquina de replicação de origem, ou em servidores de infraestrutura no local. Para relatórios precisos de RPO, certifique-se de que o relógio do sistema está correta em todos os servidores e máquinas. 

## <a name="subscribe-to-email-notifications"></a>Subscrever notificações de e-mail

Pode subscrever para receber notificações por e-mail para esses eventos críticos:
 
- Estado crítico para a máquina replicada.
- Sem conectividade entre os componentes de infraestrutura no local e o serviço Site Recovery. Conectividade entre o Site Recovery e os servidores registados num cofre é detetada utilizando um mecanismo de heartbeat.
- Falhas de ativação pós-falha.

Inscreva-se da seguinte forma:

No cofre > **monitorização e relatórios** secção, clique em **eventos de recuperação de Site**.
1. Clique em **notificações por E-Mail**.
1. Na **notificação por E-Mail**, ativar notificações e especificar a quem enviar para. Pode enviar de todos os administradores da subscrição ser enviadas notificações e endereços de e-mail, opcionalmente, específico.

    ![Notificações por e-mail](./media/site-recovery-monitor-and-troubleshoot/email.png)
