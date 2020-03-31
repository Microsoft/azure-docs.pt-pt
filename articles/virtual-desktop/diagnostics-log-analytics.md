---
title: Windows Virtual Desktop diagnostics log analytics - Azure
description: Como utilizar a análise de registo com a funcionalidade de diagnóstico do Ambiente de Trabalho Virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 355acb081afef8c78cdf971c7a82acdb91ab5593
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127959"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Utilize o Log Analytics para a funcionalidade de diagnóstico

O Windows Virtual Desktop oferece uma funcionalidade de diagnóstico que permite ao administrador identificar problemas através de uma única interface. Esta funcionalidade regista informações de diagnóstico sempre que alguém designado função de Ambiente de Trabalho Virtual do Windows utiliza o serviço. Cada registo contém informações sobre qual a função do Windows Virtual Desktop envolvida na atividade, quaisquer mensagens de erro que apareçam durante a sessão, informações do inquilino e informações do utilizador. A função de diagnóstico cria registos de atividade tanto para as ações do utilizador como para as ações administrativas. Cada registo de atividade pertence a três categorias principais: 

- Atividades de subscrição de feed: quando um utilizador tenta ligar-se ao seu feed através de aplicações do Microsoft Remote Desktop.
- Atividades de ligação: quando um utilizador tenta ligar-se a um ambiente de trabalho ou a um RemoteApp através de aplicações do Microsoft Remote Desktop.
- Atividades de gestão: quando um administrador realiza operações de gestão no sistema, tais como a criação de piscinas de anfitriões, a atribuição de utilizadores a grupos de aplicações e a criação de atribuições de funções.

As ligações que não chegam ao Windows Virtual Desktop não vão aparecer nos resultados de diagnóstico porque o próprio serviço de funções de diagnóstico faz parte do Windows Virtual Desktop. Os problemas de ligação ao Windows Virtual Desktop podem acontecer quando o utilizador está a ter problemas de conectividade de rede.

## <a name="why-you-should-use-log-analytics"></a>Por que deve usar log analytics

Recomendamos que utilize o Log Analytics para analisar dados de diagnóstico no cliente Azure que vão além da resolução de problemas de um único utilizador. Como pode puxar os contadores de desempenho vM para o Log Analytics, tem uma ferramenta para recolher informações para a sua implementação.

## <a name="before-you-get-started"></a>Antes de começar

Antes de poder utilizar o Log Analytics com a função de diagnóstico, terá de [criar um espaço](../azure-monitor/learn/quick-collect-windows-computer.md#create-a-workspace)de trabalho .

Depois de criar o seu espaço de trabalho, siga as instruções em [Connect Windows computers to Azure Monitor](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) para obter as seguintes informações: 

- O ID do espaço de trabalho
- A chave principal do seu espaço de trabalho

Vai precisar desta informação mais tarde no processo de configuração.

## <a name="push-diagnostics-data-to-your-workspace"></a>Empurre os dados de diagnóstico para o seu espaço de trabalho 

Pode empurrar dados de diagnóstico do seu inquilino do Windows Virtual Desktop para o Log Analytics para o seu espaço de trabalho. Você pode configurar esta funcionalidade imediatamente quando você criar o seu inquilino pela primeira vez ligando o seu espaço de trabalho ao seu inquilino, ou você pode configurar-lo mais tarde com um inquilino existente.

Para ligar o seu inquilino ao seu espaço de trabalho Log Analytics enquanto está a configurar o seu novo inquilino, execute o seguinte cmdlet para iniciar sessão no Windows Virtual Desktop com a sua conta de utilizador TenantCreator: 

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com 
```

Se vai ligar um inquilino existente em vez de um novo inquilino, faça este cmdlet em vez disso: 

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

Você precisará executar estes cmdlets para cada inquilino que você quiser ligar ao Log Analytics. 

>[!NOTE]
>Se não quiser ligar o espaço de trabalho do Log Analytics `New-RdsTenant` quando criar um inquilino, faça o cmdlet em vez disso. 

## <a name="cadence-for-sending-diagnostic-events"></a>Cadência para envio de eventos de diagnóstico

Os eventos de diagnóstico são enviados para o Log Analytics quando concluídos.  

## <a name="example-queries"></a>Consultas de exemplo

As seguintes consultas de exemplo mostram como a funcionalidade de diagnóstico gera um relatório para as atividades mais frequentes no seu sistema:

Este primeiro exemplo mostra atividades de conexão iniciadas por utilizadores com clientes de ambiente de trabalho remoto suportados:

```powershell
WVDActivityV1_CL 

| where Type_s == "Connection" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```

Este próximo exemplo mostra as atividades de gestão por administradores sobre inquilinos:

```powershell
WVDActivityV1_CL 

| where Type_s == "Management" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```
 
## <a name="stop-sending-data-to-log-analytics"></a>Pare de enviar dados para Log Analytics 

Para parar de enviar dados de um inquilino existente para o Log Analytics, executar o seguinte cmdlet e definir cordas vazias:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

Você precisa executar este cmdlet para todos os inquilinos que você quiser parar de enviar dados. 

## <a name="next-steps"></a>Passos seguintes 

Para rever cenários de erro comuns que a funcionalidade de diagnóstico pode identificar para si, consulte [identificar e diagnosticar problemas](diagnostics-role-service.md#common-error-scenarios).
