---
title: Windows Virtual Desktop diagnostics log analytics - Azure
description: Como utilizar a análise de registo com a funcionalidade de diagnóstico do Ambiente de Trabalho Virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bd28117350913bc25f5bf7cec08d28683ad9daca
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020069"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Utilize o Log Analytics para a funcionalidade de diagnóstico

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows. Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019 sem objetos do Gestor de Recursos Azure, consulte [este artigo](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md).
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

O Windows Virtual Desktop utiliza o [Monitor Azure](../azure-monitor/overview.md) para monitorização e alertas como muitos outros serviços Do Azure. Isto permite que os administradores identifiquem problemas através de uma única interface. O serviço cria registos de atividade tanto para ações de utilizador como administrativas. Cada registo de atividade pertence às seguintes categorias:  

- Atividades de Gestão:
    - Acompanhe se as tentativas de alterar os objetos do Windows Virtual Desktop utilizando APIs ou PowerShell são bem-sucedidas. Por exemplo, alguém pode criar com sucesso uma piscina de anfitriões usando o PowerShell?
- Alimentação: 
    - Os utilizadores podem subscrever com sucesso espaços de trabalho? 
    - Os utilizadores vêem todos os recursos publicados no cliente Remote Desktop?
- Ligações: 
    - Quando os utilizadores iniciam e completam as ligações ao serviço. 
- Registo do anfitrião: 
    - O anfitrião da sessão foi registado com sucesso com o serviço após a ligação?
- Erros: 
    - Os utilizadores estão a encontrar algum problema com atividades específicas? Esta funcionalidade pode gerar uma tabela que rastreia os dados de atividade para si, desde que a informação seja acompanhada das atividades.
- Pontos de verificação:  
    - Passos específicos na vida de uma atividade que foram alcançadas. Por exemplo, durante uma sessão, um utilizador foi carregado equilibrado para um determinado hospedeiro, então o utilizador foi assinado durante uma ligação, e assim por diante.

As ligações que não chegam ao Windows Virtual Desktop não vão aparecer nos resultados de diagnóstico porque o próprio serviço de funções de diagnóstico faz parte do Windows Virtual Desktop. Os problemas de ligação ao Windows Virtual Desktop podem acontecer quando o utilizador está a ter problemas de conectividade de rede.

O Azure Monitor permite-lhe analisar os dados do Windows Virtual Desktop e rever os contadores de desempenho da máquina virtual (VM), tudo dentro da mesma ferramenta. Este artigo irá dizer-lhe mais sobre como ativar diagnósticos para o seu ambiente de ambiente de trabalho virtual windows. 

>[!NOTE] 
>Para aprender a monitorizar os seus VMs em Azure, consulte [Monitoring Azure virtual machines with Azure Monitor](../azure-monitor/insights/monitor-vm-azure.md). Além disso, certifique-se de [rever os limiares de contador](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds) de desempenho para uma melhor compreensão da sua experiência de utilizador no anfitrião da sessão.

## <a name="before-you-get-started"></a>Antes de começar

Antes de poder utilizar o Log Analytics, terá de criar um espaço de trabalho. Para isso, siga as instruções num dos dois seguintes artigos:

- Se preferir utilizar o portal Azure, consulte Criar um espaço de [trabalho Log Analytics no portal Azure](../azure-monitor/learn/quick-create-workspace.md).
- Se preferir o PowerShell, consulte Criar um espaço de [trabalho Log Analytics com powerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

Depois de criar o seu espaço de trabalho, siga as instruções em [Connect Windows computers to Azure Monitor](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) para obter as seguintes informações:

- O ID do espaço de trabalho
- A chave principal do seu espaço de trabalho

Vai precisar desta informação mais tarde no processo de configuração.

Certifique-se de que revê a gestão da permissão do Monitor Azure para permitir o acesso de dados a quem monitoriza e mantém o ambiente de ambiente de trabalho virtual do Windows. Para mais informações, consulte [Começar com funções, permissões e segurança com o Monitor Azure.](../azure-monitor/platform/roles-permissions-security.md) 

## <a name="push-diagnostics-data-to-your-workspace"></a>Empurre os dados de diagnóstico para o seu espaço de trabalho

Pode empurrar os dados de diagnóstico dos seus objetos de ambiente de trabalho virtual do Windows para o Log Analytics para o seu espaço de trabalho. Pode configurar esta funcionalidade imediatamente quando criar os seus objetos pela primeira vez.

Para configurar o Log Analytics para um novo objeto:

1. Inscreva-se no portal Azure e vá ao **Windows Virtual Desktop**. 

2. Navegue para o objeto (como um pool de anfitriões, grupo de aplicações ou espaço de trabalho) para o que pretende capturar registos e eventos. 

3. Selecione **definições de diagnóstico** no menu do lado esquerdo do ecrã. 

4. **Selecione Adicionar definição de diagnóstico** no menu que aparece no lado direito do ecrã. 
   
    As opções apresentadas na página Definições de Diagnóstico variarão dependendo do tipo de objeto que está a editar.

    Por exemplo, quando estiver a ativar diagnósticos para um grupo de aplicações, verá opções para configurar pontos de verificação, erros e gestão. Para espaços de trabalho, estas categorias configuram um feed para rastrear quando os utilizadores subscrevem a lista de aplicações. Para saber mais sobre as definições de diagnóstico consulte [Criar definição de diagnóstico para recolher registos e métricas](../azure-monitor/platform/diagnostic-settings.md)de recursos no Azure . 

     >[!IMPORTANT] 
     >Lembre-se de ativar diagnósticos para cada objeto do Gestor de Recursos Azure que pretende monitorizar. Os dados estarão disponíveis para atividades após a ativação do diagnóstico. Pode levar algumas horas depois da primeira instalação.  

5. Introduza um nome para a configuração das definições e, em seguida, selecione **Enviar para Registar Análises**. O nome que usa não deve ter espaços e deve estar em conformidade com as convenções de [nomeação do Azure.](../azure-resource-manager/management/resource-name-rules.md) Como parte dos registos, pode selecionar todas as opções que deseja adicionar ao seu Log Analytics, tais como Checkpoint, Error, Management, e assim por diante.

6. Selecione **Guardar**.

>[!NOTE]
>O Log Analytics dá-lhe a opção de transmitir dados para [Os Hubs](../event-hubs/event-hubs-about.md) de Eventos ou arquivá-lo numa conta de armazenamento. Para saber mais sobre esta funcionalidade, consulte os dados de [monitorização do Stream Azure para um hub](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) de eventos e registos de [recursos do Archive Azure para a conta](../azure-monitor/platform/resource-logs-collect-storage.md)de armazenamento . 

## <a name="how-to-access-log-analytics"></a>Como aceder ao Log Analytics

Pode aceder aos espaços de trabalho do Log Analytics no portal Azure ou no Monitor Azure.

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>Aceder ao Log Analytics num espaço de trabalho de Log Analytics

1. Inicie sessão no Portal do Azure.

2. Pesquisar por **Log Analytics espaço de trabalho**. 

3. Em Serviços, selecione espaços de **trabalho Log Analytics**. 
   
4. A partir da lista, selecione o espaço de trabalho configurado para o seu objeto de ambiente de trabalho Windows Virtual.

5. Uma vez no seu espaço de trabalho, selecione **Registos**. Pode filtrar a sua lista de menus com a função **'Procurar'.** 

### <a name="access-log-analytics-on-azure-monitor"></a>Aceder ao Log Analytics no Monitor Azure

1. Iniciar sessão no portal do Azure

2. Procure e selecione **Monitor**. 

3. Selecionar **Registos**.

4. Siga as instruções na página de registo para definir o âmbito da sua consulta.  

5. Está pronto para consultar diagnósticos. Todas as tabelas de diagnóstico têm um prefixo "WVD".

## <a name="cadence-for-sending-diagnostic-events"></a>Cadência para envio de eventos de diagnóstico

Os eventos de diagnóstico são enviados para o Log Analytics quando concluídos.

Log Analytics apenas reporta nestes estados intermédios para atividades de conexão:

- Iniciado: quando um utilizador seleciona e se conecta a uma aplicação ou desktop no cliente Remote Desktop.
- Ligado: quando o utilizador se conecta com sucesso ao VM onde a aplicação ou o ambiente de trabalho estão hospedados.
- Concluído: quando o utilizador ou servidor desliga a sessão a atividade ocorreu.

## <a name="example-queries"></a>Consultas de exemplo

As seguintes consultas de exemplo mostram como a funcionalidade de diagnóstico gera um relatório para as atividades mais frequentes do seu sistema.

Para obter uma lista de ligações feitas pelos seus utilizadores, execute este cmdlet:

```kusto
WVDConnections 
| project-away TenantId,SourceSystem 
| summarize arg_max(TimeGenerated, *), StartTime =  min(iff(State== 'Started', TimeGenerated , datetime(null) )), ConnectTime = min(iff(State== 'Connected', TimeGenerated , datetime(null) ))   by CorrelationId 
| join kind=leftouter ( 
    WVDErrors 
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId 
    ) on CorrelationId     
| join kind=leftouter ( 
   WVDCheckpoints 
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId 
   | mv-apply Checkpoints on 
    ( 
        order by todatetime(Checkpoints['Time']) asc 
        | summarize Checkpoints=makelist(Checkpoints) 
    ) 
   ) on CorrelationId 
| project-away CorrelationId1, CorrelationId2 
| order by  TimeGenerated desc 
```

Para ver a atividade de alimentação dos seus utilizadores:

```kusto
WVDFeeds  
| project-away TenantId,SourceSystem  
| join kind=leftouter (  
    WVDErrors  
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId  
    ) on CorrelationId      
| join kind=leftouter (  
   WVDCheckpoints  
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId  
   | mv-apply Checkpoints on  
    (  
        order by todatetime(Checkpoints['Time']) asc  
        | summarize Checkpoints=makelist(Checkpoints)  
    )  
   ) on CorrelationId  
| project-away CorrelationId1, CorrelationId2  
| order by  TimeGenerated desc 
```

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

Para saber se ocorreu um erro específico:

```kusto
WVDErrors 
| where CodeSymbolic =="ErrorSymbolicCode" 
| summarize count(UserName) by CodeSymbolic 
```

Para encontrar a ocorrência de um erro em todos os utilizadores:

```kusto
WVDErrors 
| where ServiceError =="false" 
| summarize usercount = count(UserName) by CodeSymbolic 
| sort by usercount desc
| render barchart 
```

>[!NOTE]
>A mesa mais importante para a resolução de problemas são os WVDErrors. Utilize esta consulta para entender quais os problemas que ocorrem para atividades do utilizador, como ligações ou feeds quando um utilizador subscreve a lista de apps ou desktops. A tabela irá mostrar-lhe erros de gestão, bem como problemas de registo de anfitriões.
>
>Durante a pré-visualização pública, se precisar de ajuda para resolver um problema, certifique-se de que dá o CorrelationID pelo erro no seu pedido de ajuda. Além disso, certifique-se de que o valor do Erro de Serviço diz sempre serviceError = "falso". Um valor "falso" significa que a questão pode ser resolvida através de uma tarefa administrativa da sua parte. Se serviceError = "verdadeiro", terá de agravar o problema para a Microsoft.

## <a name="next-steps"></a>Próximos passos 

Para rever cenários de erro comuns que a funcionalidade de diagnóstico pode identificar para si, consulte [identificar e diagnosticar problemas](diagnostics-role-service.md#common-error-scenarios).
