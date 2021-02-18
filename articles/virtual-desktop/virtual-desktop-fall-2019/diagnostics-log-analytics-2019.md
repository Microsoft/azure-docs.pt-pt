---
title: Windows Virtual Desktop (clássico) diagnósticos de análise - Azure
description: Como utilizar a análise de registo com a funcionalidade de diagnóstico virtual do Windows Desktop (clássico).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bfe8026f099c341b68a1aa1fc5e3fac425e29403
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578901"
---
# <a name="use-log-analytics-for-the-diagnostics-feature-in-windows-virtual-desktop-classic"></a>Utilize o Log Analytics para a funcionalidade de diagnóstico no Windows Virtual Desktop (clássico)

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows. Se estiver a tentar gerir os objetos de ambiente de trabalho virtuais do Azure Resource Manager Windows, consulte [este artigo](../diagnostics-log-analytics.md).

O Windows Virtual Desktop oferece uma funcionalidade de diagnóstico que permite ao administrador identificar problemas através de uma única interface. Esta funcionalidade regista informações de diagnóstico sempre que alguém atribui a função de Desktop Virtual do Windows utilizar o serviço. Cada registo contém informações sobre qual a função de Desktop Virtual do Windows envolvida na atividade, quaisquer mensagens de erro que apareçam durante a sessão, informações do inquilino e informações do utilizador. A funcionalidade de diagnóstico cria registos de atividade tanto para as ações do utilizador como para as ações administrativas. Cada registo de atividades insere-se em três categorias principais:

- Atividades de subscrição de feed: quando um utilizador tenta ligar-se ao seu feed através de aplicações do Microsoft Remote Desktop.
- Atividades de ligação: quando um utilizador tenta ligar-se a um ambiente de trabalho ou ao RemoteApp através de aplicações de Ambiente de Trabalho Remoto da Microsoft.
- Atividades de gestão: quando um administrador realiza operações de gestão no sistema, como criar piscinas de anfitriões, atribuir utilizadores a grupos de aplicações e criar atribuições de funções.

As ligações que não chegam ao Windows Virtual Desktop não aparecem nos resultados dos diagnósticos porque o próprio serviço de função de diagnóstico faz parte do Windows Virtual Desktop. Problemas de ligação virtual ao ambiente de trabalho do Windows podem ocorrer quando o utilizador está a ter problemas de conectividade de rede.

## <a name="why-you-should-use-log-analytics"></a>Por que deve usar o Log Analytics

Recomendamos que utilize o Log Analytics para analisar os dados de diagnóstico no cliente Azure que vai além da resolução de problemas de um único utilizador. Como pode puxar os contadores de desempenho VM no Log Analytics, tem uma ferramenta para recolher informações para a sua implementação.

## <a name="before-you-get-started"></a>Antes de começar

Antes de utilizar o Log Analytics com a função de diagnóstico, terá de [criar um espaço de trabalho.](../../azure-monitor/vm/quick-collect-windows-computer.md#create-a-workspace)

Depois de criar o seu espaço de trabalho, siga as instruções nos [computadores Connect Windows para o Azure Monitor](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key) para obter as seguintes informações:

- O ID do espaço de trabalho
- A chave primária do seu espaço de trabalho

Vai precisar desta informação mais tarde no processo de configuração.

## <a name="push-diagnostics-data-to-your-workspace"></a>Empurre os dados de diagnóstico para o seu espaço de trabalho

Pode empurrar os dados de diagnóstico do seu inquilino virtual do Windows desktop para o Log Analytics para o seu espaço de trabalho. Você pode configurar esta funcionalidade imediatamente quando você criar o seu inquilino pela primeira vez ligando o seu espaço de trabalho ao seu inquilino, ou você pode cone encontrá-lo mais tarde com um inquilino existente.

Para ligar o seu inquilino ao seu espaço de trabalho Log Analytics enquanto está a configurar o seu novo inquilino, execute o seguinte cmdlet para iniciar sessão no Windows Virtual Desktop com a sua conta de utilizador TenantCreator:

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Se vai ligar um inquilino existente em vez de um novo inquilino, gere este cmdlet em vez disso:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

Você precisará executar estes cmdlets para cada inquilino que você quer ligar ao Log Analytics.

>[!NOTE]
>Se não quiser ligar o espaço de trabalho do Log Analytics quando criar um inquilino, gere o `New-RdsTenant` cmdlet em vez disso.

## <a name="cadence-for-sending-diagnostic-events"></a>Cadência para envio de eventos de diagnóstico

Os eventos de diagnóstico são enviados para o Log Analytics quando concluídos.

## <a name="example-queries"></a>Consultas de exemplo

As seguintes consultas de exemplo mostram como a funcionalidade de diagnóstico gera um relatório para as atividades mais frequentes no seu sistema:

Este primeiro exemplo mostra atividades de conexão iniciadas por utilizadores com clientes de desktop remotos suportados:

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

Este próximo exemplo de consulta mostra atividades de gestão por administradores sobre inquilinos:

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

## <a name="stop-sending-data-to-log-analytics"></a>Pare de enviar dados para registar analítica

Para parar de enviar dados de um inquilino existente para log analytics, executar o seguinte cmdlet e definir cordas vazias:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

Você precisa executar este cmdlet para cada inquilino que você quer parar de enviar dados de.

## <a name="next-steps"></a>Passos seguintes

Para rever cenários de erro comuns que a funcionalidade de diagnóstico pode identificar para si, consulte [identificar e diagnosticar problemas](diagnostics-role-service-2019.md#common-error-scenarios).
