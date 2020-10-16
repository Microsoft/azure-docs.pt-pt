---
title: Windows Virtual Desktop diagnostics log analytics - Azure
description: Como utilizar a análise de registo com a funcionalidade de diagnóstico virtual do Windows Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a3fccc934fafd8ff7db2cffbd6ba641329ba8de2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89006810"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Utilize o Log Analytics para a funcionalidade de diagnóstico

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md).

O Windows Virtual Desktop utiliza [o Azure Monitor](../azure-monitor/overview.md) para monitorização e alertas como muitos outros serviços Azure. Isto permite aos administradores identificarem problemas através de uma única interface. O serviço cria registos de atividades tanto para as ações de utilizador como para ações administrativas. Cada registo de atividades insere-se nas seguintes categorias:

- Atividades de Gestão:
    - Acompanhe se as tentativas de alterar objetos de ambiente de trabalho virtuais do Windows utilizando APIs ou PowerShell são bem sucedidas. Por exemplo, alguém pode criar com sucesso uma piscina de anfitriões usando o PowerShell?
- Alimentação:
    - Os utilizadores podem subscrever com sucesso espaços de trabalho?
    - Os utilizadores vêem todos os recursos publicados no cliente Remote Desktop?
- Ligações:
    - Quando os utilizadores iniciarem e completarem as ligações ao serviço.
- Inscrição do anfitrião:
    - O anfitrião da sessão foi registado com sucesso com o serviço na ligação?
- Erros:
    - Os utilizadores estão a deparar-se com algum problema com atividades específicas? Esta funcionalidade pode gerar uma tabela que rastreie dados de atividade para si desde que a informação seja acompanhada das atividades.
- Postos de controlo:
    - Passos específicos na vida de uma atividade que foram alcançadas. Por exemplo, durante uma sessão, um utilizador foi carregado equilibrado para um determinado hospedeiro, então o utilizador foi contratado durante uma ligação, e assim por diante.

As ligações que não chegam ao Windows Virtual Desktop não aparecem nos resultados dos diagnósticos porque o próprio serviço de função de diagnóstico faz parte do Windows Virtual Desktop. Problemas de ligação virtual ao ambiente de trabalho do Windows podem ocorrer quando o utilizador está a ter problemas de conectividade de rede.

O Azure Monitor permite analisar os dados de desktop virtual do Windows e rever os contadores de desempenho da máquina virtual (VM), todos dentro da mesma ferramenta. Este artigo irá dizer-lhe mais sobre como ativar diagnósticos para o seu ambiente de trabalho virtual do Windows.

>[!NOTE]
>Para aprender a monitorizar os seus VMs em Azure, consulte [as máquinas virtuais Monitorar Azure com o Azure Monitor](../azure-monitor/insights/monitor-vm-azure.md). Além disso, certifique-se [de rever os limiares do contador](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds) de desempenho para uma melhor compreensão da sua experiência do utilizador no anfitrião da sessão.

## <a name="before-you-get-started"></a>Antes de começar

Antes de poder utilizar o Log Analytics, terá de criar um espaço de trabalho. Para isso, siga as instruções de um dos dois artigos seguintes:

- Se preferir utilizar o portal Azure, consulte [criar um espaço de trabalho log Analytics no portal Azure.](../azure-monitor/learn/quick-create-workspace.md)
- Se preferir PowerShell, consulte [Criar um espaço de trabalho Log Analytics com PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

Depois de criar o seu espaço de trabalho, siga as instruções nos [computadores Connect Windows para o Azure Monitor](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key) para obter as seguintes informações:

- O ID do espaço de trabalho
- A chave primária do seu espaço de trabalho

Vai precisar desta informação mais tarde no processo de configuração.

Certifique-se de rever a gestão de permissões para o Azure Monitor para permitir o acesso de dados a quem monitoriza e mantém o ambiente de trabalho virtual do Windows. Para mais informações, consulte [Começar com papéis, permissões e segurança com o Azure Monitor.](../azure-monitor/platform/roles-permissions-security.md)

## <a name="push-diagnostics-data-to-your-workspace"></a>Empurre os dados de diagnóstico para o seu espaço de trabalho

Pode empurrar os dados de diagnóstico dos seus objetos de ambiente de trabalho virtuais do Windows para o Log Analytics para o seu espaço de trabalho. Pode configurar esta funcionalidade imediatamente quando criar os seus objetos pela primeira vez.

Para configurar o Log Analytics para um novo objeto:

1. Inscreva-se no portal Azure e vá para o **Windows Virtual Desktop**.

2. Navegue para o objeto (como uma piscina de anfitriões, grupo de aplicações ou espaço de trabalho) para o que pretende capturar registos e eventos.

3. Selecione **definições de diagnóstico** no menu no lado esquerdo do ecrã.

4. **Selecione Adicionar a definição** de diagnóstico no menu que aparece no lado direito do ecrã.

    As opções mostradas na página Definições de Diagnóstico variam consoante o tipo de objeto que está a editar.

    Por exemplo, quando está a ativar diagnósticos para um grupo de aplicações, verá opções para configurar pontos de verificação, erros e gestão. Para espaços de trabalho, estas categorias configuram um feed para acompanhar quando os utilizadores subscrevem a lista de aplicações. Para saber mais sobre as definições de diagnóstico consulte [Criar definição de diagnóstico para recolher registos de recursos e métricas em Azure](../azure-monitor/platform/diagnostic-settings.md).

     >[!IMPORTANT]
     >Lembre-se de ativar os diagnósticos de cada objeto Azure Resource Manager que pretende monitorizar. Os dados estarão disponíveis para atividades após o diagnóstico ter sido ativado. Pode levar algumas horas depois da primeira preparação.

5. Introduza um nome para a configuração das suas definições e, em seguida, selecione **Enviar para registar analíticos**. O nome que usa não deve ter espaços e deve estar em conformidade com as [convenções de nomeação Azure.](../azure-resource-manager/management/resource-name-rules.md) Como parte dos registos, pode selecionar todas as opções que pretende adicionar ao seu Log Analytics, tais como Checkpoint, Error, Management, e assim por diante.

6. Selecione **Guardar**.

>[!NOTE]
>O Log Analytics dá-lhe a opção de transmitir dados para [Os Centros de Eventos](../event-hubs/event-hubs-about.md) ou arquivá-lo numa conta de armazenamento. Para saber mais sobre esta funcionalidade, consulte [os dados de monitorização do Stream Azure para um centro](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) de eventos e [registos de recursos do Archive Azure para a conta de armazenamento.](../azure-monitor/platform/resource-logs-collect-storage.md)

## <a name="how-to-access-log-analytics"></a>Como aceder ao Log Analytics

Pode aceder aos espaços de trabalho do Log Analytics no portal Azure ou no Azure Monitor.

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>Acesso Log Analytics em um espaço de trabalho Log Analytics

1. Inicie sessão no Portal do Azure.

2. Procure por **Log Analytics espaço de trabalho**.

3. Em Serviços, selecione **Log Analytics workspaces**.

4. Na lista, selecione o espaço de trabalho configurado para o seu objeto de ambiente de trabalho Virtual do Windows.

5. Uma vez no seu espaço de trabalho, **selecione Registos**. Pode filtrar a sua lista de menus com a **função Procurar.**

### <a name="access-log-analytics-on-azure-monitor"></a>Access Log Analytics no Azure Monitor

1. Iniciar sessão no portal do Azure

2. Procure e selecione **Monitor.**

3. Selecionar **Registos**.

4. Siga as instruções na página de registo para definir o âmbito da sua consulta.

5. Está pronto para consultar os diagnósticos. Todas as tabelas de diagnóstico têm um prefixo "WVD".

>[!NOTE]
>Para obter informações mais detalhadas sobre as tabelas armazenadas nos registos do Monitor Azure, consulte a [reconsessão de dados do Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/reference/). Todas as tabelas relacionadas com o Windows Virtual Desktop estão rotuladas como "WVD".

## <a name="cadence-for-sending-diagnostic-events"></a>Cadência para envio de eventos de diagnóstico

Os eventos de diagnóstico são enviados para o Log Analytics quando concluídos.

Log Analytics apenas reporta nestes estados intermédios para atividades de conexão:

- Iniciado: quando um utilizador seleciona e liga a uma aplicação ou ambiente de trabalho no cliente Remote Desktop.
- Ligado: quando o utilizador se conecta com sucesso ao VM onde a aplicação ou o ambiente de trabalho estão hospedados.
- Concluído: quando o utilizador ou servidor desliga a sessão, a atividade ocorreu.

## <a name="example-queries"></a>Consultas de exemplo

Acesso a consultas de exemplo através do Azure Monitor Log Analytics UI:
1. Vá ao seu espaço de trabalho Log Analytics e, em seguida, **selecione Logs**. O exemplo de consulta UI é mostrado automaticamente.
1. Mude o filtro para **categoria**.
1. Selecione **o Windows Virtual Desktop** para rever as consultas disponíveis.
1. Selecione **Executar** para executar a consulta selecionada.

Saiba mais sobre a interface de consulta de amostras em [consultas Saved in Azure Monitor Log Analytics](../azure-monitor/log-query/saved-queries.md).

A lista de consultas que se segue permite rever as informações de ligação ou problemas para um único utilizador. Pode executar estas consultas no [editor de consultas do Log Analytics.](../azure-monitor/log-query/get-started-portal.md#write-and-run-basic-queries) Para cada consulta, `userupn` substitua-a pela UPN do utilizador que pretende procurar.


Para encontrar todas as ligações para um único utilizador:

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
```


Para encontrar o número de vezes que um utilizador conectado por dia:

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d)
```

Para encontrar a duração da sessão pelo utilizador:

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId)
on CorrelationId
| project Duration = EndTime - StartTime, ResourceAlias
| sort by Duration asc
```

Para encontrar erros para um utilizador específico:

```kusto
WVDErrors
| where UserName == "userupn"
|take 100
```

Para saber se ocorreu um erro específico para outros utilizadores:

```kusto
WVDErrors
| where CodeSymbolic =="ErrorSymbolicCode"
| summarize count(UserName) by CodeSymbolic
```


>[!NOTE]
>- Quando um utilizador abre o Full Desktop, a utilização da aplicação na sessão não é rastreada como pontos de verificação na tabela WVDCheckpoints.
>- A coluna ResourcesAlias na tabela WVDConnections mostra se um utilizador ligou-se a um ambiente de trabalho completo ou a uma aplicação publicada. A coluna mostra apenas a primeira aplicação que abrem durante a ligação. Quaisquer aplicações publicadas que o utilizador abre são rastreadas em WVDCheckpoints.
>- A tabela WVDErrors mostra erros de gestão, problemas de registo de anfitrião e outros problemas que ocorrem enquanto o utilizador subscreve uma lista de aplicações ou desktops.
>- A WVDErrors ajuda-o a identificar problemas que podem ser resolvidos através de tarefas de administração. O valor no ServiceError diz sempre "falso" para este tipo de problemas. Se o ServiceError = "verdadeiro", terá de aumentar o problema para a Microsoft. Certifique-se de que fornece o CorrelationID para os erros que agrava.

## <a name="next-steps"></a>Passos seguintes

Para rever cenários de erro comuns que a funcionalidade de diagnóstico pode identificar para si, consulte [identificar e diagnosticar problemas](diagnostics-role-service.md#common-error-scenarios).
