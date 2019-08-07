---
title: Monitoramento e registro em log na proteção de senha do Azure AD-Azure Active Directory
description: Entender o monitoramento e o registro em log da proteção de senha do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bd6d3abc6080c0ab1b6137511af719b23e5bcd4
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736826"
---
# <a name="azure-ad-password-protection-monitoring-and-logging"></a>Monitoramento e registro em log da proteção de senha do Azure AD

Após a implantação da proteção de senha, o monitoramento e o relatório do Azure AD são tarefas essenciais. Este artigo apresenta detalhes para ajudá-lo a entender várias técnicas de monitoramento, incluindo onde cada serviço registra informações e como relatar o uso da proteção de senha do Azure AD.

O monitoramento e os relatórios são feitos por mensagens de log de eventos ou pela execução de cmdlets do PowerShell. O agente de DC e os serviços de proxy registram mensagens de log de eventos. Todos os cmdlets do PowerShell descritos abaixo estão disponíveis somente no servidor proxy (consulte o módulo AzureADPasswordProtection PowerShell). O software do agente de controlador de domínio não instala um módulo do PowerShell.

## <a name="dc-agent-event-logging"></a>Log de eventos do agente DC

Em cada controlador de domínio, o software do serviço de agente de DC grava os resultados de cada operação de validação de senha individual (e outro status) em um log de eventos local:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

O log de admin do agente de DC é a principal fonte de informações sobre como o software está se comportando.

Observe que o log de rastreamento está desativado por padrão.

Os eventos registrados em log pelos vários componentes do agente de DC se enquadram nos seguintes intervalos:

|Componente |Intervalo da ID do evento|
| --- | --- |
|DLL de filtro de senha do agente DC| 10000-19999|
|Processo de hospedagem do serviço de agente de DC| 20000-29999|
|Lógica de validação de política de serviço do agente de DC| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>Log de eventos do administrador do agente de DC

### <a name="password-validation-outcome-events"></a>Eventos de resultado da validação de senha

Em cada controlador de domínio, o software do serviço de agente de DC grava os resultados de cada validação de senha individual no log de eventos do administrador do agente de DC.

Para uma operação de validação de senha bem-sucedida, geralmente há um evento registrado a partir da DLL de filtro de senha do agente de DC. Para uma operação de validação de senha com falha, geralmente há dois eventos registrados, um do serviço de agente de controlador de domínio e outro da DLL de filtro de senha do agente de DC.

Eventos discretos para capturar essas situações são registrados em log, com base nos seguintes fatores:

* Se uma determinada senha está sendo definida ou alterada.
* Se a validação de uma determinada senha foi aprovada ou falhou.
* Se a validação falhou devido à política global da Microsoft, à política organizacional ou à combinação.
* Se o modo somente auditoria está ativado ou desativado para a política de senha atual.

Os principais eventos relacionados à validação de senha são os seguintes:

|   |Alteração da palavra-passe |Conjunto de senhas|
| --- | :---: | :---: |
|Passagem |10014 |10015|
|Falha (devido à política de senha do cliente)| 10016, 30002| 10017, 30003|
|Falha (devido à política de senha da Microsoft)| 10016, 30004| 10017, 30005|
|Falha (devido a políticas combinadas de senha da Microsoft e do cliente)| 10016, 30026| 10017, 30027|
|Passagem somente de auditoria (falha na política de senha do cliente)| 10024, 30008| 10025, 30007|
|Passagem somente de auditoria (teria falhado na diretiva de senha da Microsoft)| 10024, 30010| 10025, 30009|
|Passagem somente de auditoria (teria falhado nas políticas de senha da Microsoft e do cliente)| 10024, 30028| 10025, 30029|

Os casos na tabela acima que se referem a "políticas combinadas" estão se referindo a situações em que a senha de um usuário foi encontrada para conter pelo menos um token da lista de senhas banidas da Microsoft e da lista de senhas banidas pelo cliente.

Quando um par de eventos é registrado juntos, ambos os eventos são explicitamente associados com a mesma CorrelationId.

### <a name="password-validation-summary-reporting-via-powershell"></a>Relatório de Resumo de validação de senha por meio do PowerShell

O `Get-AzureADPasswordProtectionSummaryReport` cmdlet pode ser usado para produzir uma exibição resumida da atividade de validação de senha. Um exemplo de saída desse cmdlet é o seguinte:

```powershell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

O escopo do relatório do cmdlet pode ser influenciado usando um dos parâmetros – floresta,-Domain ou – DomainController. Não especificar um parâmetro implica – Forest.

O `Get-AzureADPasswordProtectionSummaryReport` cmdlet funciona consultando o log de eventos do administrador do agente de DC e contando o número total de eventos que correspondem a cada categoria de resultado exibida. A tabela a seguir contém os mapeamentos entre cada resultado e sua ID de evento correspondente:

|Propriedade Get-AzureADPasswordProtectionSummaryReport |ID do evento correspondente|
| :---: | :---: |
|PasswordChangesValidated |10014|
|PasswordSetsValidated |10015|
|PasswordChangesRejected |10016|
|PasswordSetsRejected |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

Observe que o `Get-AzureADPasswordProtectionSummaryReport` cmdlet é enviado no formulário de script do PowerShell e, se necessário, pode ser referenciado diretamente no seguinte local:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> Esse cmdlet funciona abrindo uma sessão do PowerShell para cada controlador de domínio. Para obter sucesso, o suporte à sessão remota do PowerShell deve ser habilitado em cada controlador de domínio e o cliente deve ter privilégios suficientes. Para obter mais informações sobre os requisitos de sessão remota do PowerShell, execute ' Get-Help about_Remote_Troubleshooting ' em uma janela do PowerShell.

> [!NOTE]
> Esse cmdlet funciona consultando remotamente cada log de eventos de administrador do serviço de agente de DC. Se os logs de eventos contiverem um grande número de eventos, o cmdlet poderá levar muito tempo para ser concluído. Além disso, as consultas de rede em massa de grandes conjuntos de dados podem afetar o desempenho do controlador de domínio. Portanto, esse cmdlet deve ser usado com cuidado em ambientes de produção.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Mensagem de log de eventos de exemplo para a ID de evento 10014 (alteração de senha bem-sucedida)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Mensagem de log de eventos de exemplo para a ID de evento 10017 e 30003 (falha no conjunto de senhas)

10017:

```text
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```text
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Mensagem de log de eventos de exemplo para a ID de evento 30001 (Senha aceita devido a nenhuma política disponível)

```text
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Mensagem de log de eventos de exemplo para a ID de evento 30006 (nova política sendo imposta)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>Mensagem de log de eventos de exemplo para a ID de evento 30019 (a proteção de senha do Azure AD está desabilitada)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>Log operacional do agente DC

O serviço de agente de controlador de domínio também registrará eventos relacionados à operação no seguinte log:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>Log de rastreamento do agente DC

O serviço de agente de controlador de domínio também pode registrar eventos detalhados de rastreamento no nível de depuração no seguinte log:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

O log de rastreamento é desabilitado por padrão.

> [!WARNING]
> Quando habilitado, o log de rastreamento recebe um alto volume de eventos e pode afetar o desempenho do controlador de domínio. Portanto, esse log avançado só deve ser habilitado quando um problema exigir uma investigação mais profunda e, em seguida, apenas por um período mínimo de tempo.

## <a name="dc-agent-text-logging"></a>Log de texto do agente DC

O serviço de agente de controlador de domínio pode ser configurado para gravar em um log de texto definindo o seguinte valor de registro:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

O log de texto é desabilitado por padrão. Uma reinicialização do serviço de agente de controlador de domínio é necessária para que as alterações nesse valor entrem em vigor. Quando habilitado, o serviço do agente DC gravará em um arquivo de log localizado em:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> O log de texto recebe as mesmas entradas de nível de depuração que podem ser registradas no log de rastreamento, mas geralmente está em um formato mais fácil de revisar e analisar.

> [!WARNING]
> Quando habilitado, esse log recebe um alto volume de eventos e pode afetar o desempenho do controlador de domínio. Portanto, esse log avançado só deve ser habilitado quando um problema exigir uma investigação mais profunda e, em seguida, apenas por um período mínimo de tempo.

## <a name="dc-agent-performance-monitoring"></a>Monitoramento de desempenho do agente DC

O software do serviço de agente de controlador de domínio instala um objeto de contador de desempenho chamado **proteção de senha do Azure ad**. Os seguintes contadores de desempenho estão disponíveis no momento:

|Nome do contador de desempenho | Descrição|
| --- | --- |
|Senhas processadas |Este contador exibe o número total de senhas processadas (aceitas ou rejeitadas) desde a última reinicialização.|
|Senhas aceitas |Esse contador exibe o número total de senhas que foram aceitas desde a última reinicialização.|
|Senhas rejeitadas |Este contador exibe o número total de senhas que foram rejeitadas desde a última reinicialização.|
|Solicitações de filtro de senha em andamento |Esse contador exibe o número de solicitações de filtro de senha atualmente em andamento.|
|Solicitações de filtro de senha de pico |Esse contador exibe o número máximo de solicitações de filtro de senha simultâneas desde a última reinicialização.|
|Erros de solicitação de filtro de senha |Este contador exibe o número total de solicitações de filtro de senha que falharam devido a um erro desde a última reinicialização. Podem ocorrer erros quando o serviço de agente de DC da proteção de senha do Azure AD não está em execução.|
|Solicitações de filtro de senha/s |Esse contador exibe a taxa em que as senhas estão sendo processadas.|
|Tempo de processamento da solicitação de filtro de senha |Esse contador exibe o tempo médio necessário para processar uma solicitação de filtro de senha.|
|Tempo de processamento da solicitação de filtro de senha de pico |Esse contador exibe o tempo de processamento da solicitação de filtro de senha de pico desde a última reinicialização.|
|Senhas aceitas devido ao modo de auditoria |Esse contador exibe o número total de senhas que normalmente teriam sido rejeitadas, mas foram aceitas porque a política de senha foi configurada para estar no modo de auditoria (desde a última reinicialização).|

## <a name="dc-agent-discovery"></a>Descoberta de agente de DC

O `Get-AzureADPasswordProtectionDCAgent` cmdlet pode ser usado para exibir informações básicas sobre os vários agentes de DC em execução em um domínio ou floresta. Essas informações são recuperadas dos objetos serviceConnectionPoint registrados pelos serviços do agente DC em execução.

Um exemplo de saída desse cmdlet é o seguinte:

```powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

As várias propriedades são atualizadas por cada serviço de agente de controlador de domínio de acordo com a hora aproximada. Os dados ainda estão sujeitos a Active Directory latência de replicação.

O escopo da consulta do cmdlet pode ser influenciado usando os parâmetros – Forest ou-Domain.

Se o valor de HeartbeatUTC ficar obsoleto, isso pode ser um sintoma de que o agente DC de proteção de senha do Azure AD nesse controlador de domínio não está em execução ou foi desinstalado, ou o computador foi rebaixado e não é mais um controlador de domínio.

Se o valor de PasswordPolicyDateUTC ficar obsoleto, isso pode ser um sintoma de que o agente DC da proteção por senha do Azure AD no computador não está funcionando corretamente.

## <a name="dc-agent-newer-version-available"></a>Versão mais recente do agente de DC disponível

O serviço de agente de controlador de domínio registrará um evento de aviso 30034 no log operacional ao detectar que uma versão mais recente do software do agente de DC está disponível, por exemplo:

```text
An update for Azure AD Password Protection DC Agent is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
```

O evento acima não especifica a versão do software mais recente. Você deve ir para o link na mensagem de evento para obter essas informações.

> [!NOTE]
> Apesar das referências a "AutoUpgrade" na mensagem de evento acima, o software do agente de controlador de domínio não oferece suporte a esse recurso no momento.

## <a name="proxy-service-event-logging"></a>Log de eventos do serviço de proxy

O serviço de proxy emite um conjunto mínimo de eventos para os seguintes logs de eventos:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Observe que o log de rastreamento está desativado por padrão.

> [!WARNING]
> Quando habilitado, o log de rastreamento recebe um alto volume de eventos e isso pode afetar o desempenho do host de proxy. Portanto, esse log só deve ser habilitado quando um problema exigir investigação mais profunda e, em seguida, apenas por um período mínimo de tempo.

Os eventos são registrados por vários componentes de proxy usando os seguintes intervalos:

|Componente |Intervalo da ID do evento|
| --- | --- |
|Processo de hospedagem do serviço proxy| 10000-19999|
|Lógica comercial principal do serviço de proxy| 20000-29999|
|Cmdlets do PowerShell| 30000-39999|

## <a name="proxy-service-text-logging"></a>Log de texto do serviço de proxy

O serviço de proxy pode ser configurado para gravar em um log de texto definindo o seguinte valor de registro:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters!EnableTextLogging = 1 (REG_DWORD value)

O log de texto é desabilitado por padrão. Uma reinicialização do serviço de proxy é necessária para que as alterações nesse valor entrem em vigor. Quando habilitado, o serviço de proxy gravará em um arquivo de log localizado em:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> O log de texto recebe as mesmas entradas de nível de depuração que podem ser registradas no log de rastreamento, mas geralmente está em um formato mais fácil de revisar e analisar.

> [!WARNING]
> Quando habilitado, esse log recebe um alto volume de eventos e pode afetar o desempenho do computador. Portanto, esse log avançado só deve ser habilitado quando um problema exigir uma investigação mais profunda e, em seguida, apenas por um período mínimo de tempo.

## <a name="powershell-cmdlet-logging"></a>Log de cmdlet do PowerShell

Os cmdlets do PowerShell que resultam em uma alteração de estado (por exemplo, Register-AzureADPasswordProtectionProxy) normalmente registrarão um evento de resultado no log operacional.

Além disso, a maioria dos cmdlets do PowerShell de proteção por senha do Azure AD será gravada em um log de texto localizado em:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Se ocorrer um erro de cmdlet e a solução de causa e/ou não for prontamente aparente, esses logs de texto também poderão ser consultados.

## <a name="proxy-discovery"></a>Descoberta de proxy

O `Get-AzureADPasswordProtectionProxy` cmdlet pode ser usado para exibir informações básicas sobre os vários serviços de proxy de proteção de senha do Azure AD em execução em um domínio ou floresta. Essas informações são recuperadas dos objetos serviceConnectionPoint registrados pelos serviços de proxy em execução.

Um exemplo de saída desse cmdlet é o seguinte:

```powershell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

As várias propriedades são atualizadas por cada serviço de proxy de acordo com a hora aproximada. Os dados ainda estão sujeitos a Active Directory latência de replicação.

O escopo da consulta do cmdlet pode ser influenciado usando os parâmetros – Forest ou-Domain.

Se o valor de HeartbeatUTC ficar obsoleto, isso pode ser um sintoma de que o proxy de proteção de senha do Azure AD nesse computador não está em execução ou foi desinstalado.

## <a name="proxy-agent-newer-version-available"></a>Versão mais recente do agente de proxy disponível

O serviço de proxy registrará um evento de aviso 20002 no log operacional ao detectar que uma versão mais recente do software proxy está disponível, por exemplo:

```text
An update for Azure AD Password Protection Proxy is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
.
```

O evento acima não especifica a versão do software mais recente. Você deve ir para o link na mensagem de evento para obter essas informações.

Esse evento será emitido mesmo se o agente de proxy estiver configurado com a atualização autohabilitada.

## <a name="next-steps"></a>Passos Seguintes

[Solução de problemas da proteção de senha do Azure AD](howto-password-ban-bad-on-premises-troubleshoot.md)

Para obter mais informações sobre as listas de senhas banidas globais e personalizadas, consulte o artigo [proibir senhas ruins](concept-password-ban-bad.md)
