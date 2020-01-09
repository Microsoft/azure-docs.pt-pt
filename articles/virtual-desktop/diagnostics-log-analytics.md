---
title: Log Analytics de diagnóstico de área de trabalho virtual do Windows – Azure
description: Como usar o log Analytics com o recurso de diagnóstico de área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
ms.openlocfilehash: f50e5144080ffb1c91e834c757082bb57eeaffcb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479231"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Usar Log Analytics para o recurso de diagnóstico

A área de trabalho virtual do Windows oferece um recurso de diagnóstico que permite ao administrador identificar problemas por meio de uma única interface. Esse recurso registra as informações de diagnóstico sempre que alguém tiver atribuído a função de área de trabalho virtual do Windows usando o serviço. Cada log contém informações sobre qual função de área de trabalho virtual do Windows estava envolvida na atividade, todas as mensagens de erro que aparecem durante a sessão, informações de locatário e informações do usuário. O recurso de diagnóstico cria logs de atividade para ações administrativas e de usuário. Cada log de atividades cai em três categorias principais: 

- Atividades de assinatura de feed: quando um usuário tenta se conectar ao feed por meio de aplicativos Área de Trabalho Remota da Microsoft.
- Atividades de conexão: quando um usuário tenta se conectar a um desktop ou RemoteApp por meio de aplicativos Área de Trabalho Remota da Microsoft.
- Atividades de gerenciamento: quando um administrador executa operações de gerenciamento no sistema, como a criação de pools de hosts, a atribuição de usuários a grupos de aplicativos e a criação de atribuições de função.

As conexões que não chegam à área de trabalho virtual do Windows não aparecerão nos resultados do diagnóstico porque o próprio serviço de função de diagnóstico faz parte da área de trabalho virtual do Windows. Problemas de conexão de área de trabalho virtual do Windows podem ocorrer quando o usuário está enfrentando problemas de conectividade de rede.

## <a name="why-you-should-use-log-analytics"></a>Por que você deve usar Log Analytics

Recomendamos que você use Log Analytics para analisar dados de diagnóstico no cliente do Azure que vão além da solução de problemas de usuário único. Como você pode efetuar pull em contadores de desempenho de VM em Log Analytics você tem uma ferramenta para coletar informações para sua implantação.

## <a name="before-you-get-started"></a>Antes de começar

Para poder usar Log Analytics com o recurso de diagnóstico, você precisará [criar um espaço de trabalho](../azure-monitor/learn/quick-collect-windows-computer.md#create-a-workspace).

Depois de criar seu espaço de trabalho, siga as instruções em [conectar computadores Windows a Azure monitor](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) para obter as seguintes informações: 

- A ID do espaço de trabalho
- A chave primária do seu espaço de trabalho

Você precisará dessas informações posteriormente no processo de instalação.

## <a name="push-diagnostics-data-to-your-workspace"></a>Enviar dados de diagnóstico por push para seu espaço de trabalho 

Você pode enviar por push dados de diagnóstico do seu locatário da área de trabalho virtual do Windows para o Log Analytics do seu espaço de trabalho. Você pode configurar esse recurso imediatamente quando você cria seu locatário pela primeira vez, vinculando seu espaço de trabalho ao seu locatário ou pode configurá-lo posteriormente com um locatário existente.

Para vincular seu locatário ao seu espaço de trabalho do Log Analytics enquanto estiver configurando seu novo locatário, execute o seguinte cmdlet para entrar na área de trabalho virtual do Windows com sua conta de usuário do TenantCreator: 

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com 
```

Se você for vincular um locatário existente, em vez de um novo locatário, execute este cmdlet: 

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

Você precisará executar esses cmdlets para todos os locatários aos quais deseja vincular Log Analytics. 

>[!NOTE]
>Se você não quiser vincular o espaço de trabalho Log Analytics ao criar um locatário, execute o cmdlet `New-RdsTenant` em vez disso. 

## <a name="cadence-for-sending-diagnostic-events"></a>Cadência para enviar eventos de diagnóstico

Os eventos de diagnóstico são enviados para Log Analytics quando concluídos.  

## <a name="example-queries"></a>Exemplos de consultas

As consultas de exemplo a seguir mostram como o recurso de diagnóstico gera um relatório para as atividades mais frequentes em seu sistema:

Este primeiro exemplo mostra as atividades de conexão iniciadas pelos usuários com clientes de área de trabalho remota com suporte:

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

Esta consulta de exemplo a seguir mostra as atividades de gerenciamento por administradores em locatários:

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
 
## <a name="stop-sending-data-to-log-analytics"></a>Parar de enviar dados para Log Analytics 

Para interromper o envio de dados de um locatário existente para Log Analytics, execute o seguinte cmdlet e defina cadeias de caracteres vazias:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

Você precisará executar esse cmdlet para todos os locatários dos quais deseja interromper o envio de dados. 

## <a name="next-steps"></a>Passos seguintes 

Para examinar os cenários de erro comuns que o recurso de diagnóstico pode identificar para você, consulte [identificar e diagnosticar problemas](diagnostics-role-service.md#common-error-scenarios).
