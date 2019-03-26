---
title: Monitorização e registo na proteção de palavra-passe do Azure AD - Azure Active Directory
description: Compreender a proteção de palavra-passe do Azure AD, monitorização e registo
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
ms.openlocfilehash: ae2d18541788e769e4f1b44319aa1be200921b88
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437565"
---
# <a name="azure-ad-password-protection-monitoring-and-logging"></a>Proteção de palavra-passe do Azure AD monitorização e registo

Após a implementação de proteção de palavra-passe do Azure AD, monitorização e relatórios são tarefas essenciais. Este artigo apresenta detalhes para ajudar a que compreender o várias técnicas de monitorização, incluindo onde cada serviço regista informações e como um relatório sobre a utilização de proteção de palavra-passe do Azure AD.

Monitorização e relatórios são feitas por mensagens de registo de eventos ou ao executar os cmdlets do PowerShell. Os controlador de domínio agente e o proxy de registo dos serviços de ambas as mensagens de registo de eventos. Todos os cmdlets do PowerShell descritos abaixo só estão disponíveis no servidor de proxy (consulte o módulo do AzureADPasswordProtection PowerShell). O software do agente DC não instala um módulo do PowerShell.

## <a name="dc-agent-event-logging"></a>Registo de eventos do agente DC

Em cada controlador de domínio, o software de serviço do agente de controlador de domínio grava os resultados de cada operação de validação de palavras-passe individuais (e outro Estado) para um registo de eventos local:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Registo de administração do agente do controlador de domínio é a principal fonte de informações de como o software está se comportando.

Tenha em atenção que o registo de rastreio está desativada por predefinição.

Eventos registados pelos vários componentes do agente DC coincidir com os seguintes intervalos:

|Componente |Intervalo de ID de evento|
| --- | --- |
|Dll de filtro de palavras-passe do agente de DC| 10000-19999|
|Processo de hospedagem do serviço de agente do controlador de domínio| 20000-29999|
|Lógica de validação de política de serviço de agente de DC| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>Registo de eventos da administração de agente de DC

### <a name="password-validation-outcome-events"></a>Eventos de resultado de validação de palavra-passe

Em cada controlador de domínio, o software de serviço do agente de controlador de domínio grava os resultados da validação de cada palavra-passe individuais no registo de eventos de administração de agente do controlador de domínio.

Para uma operação de validação da palavra-passe com êxito, geralmente não há um evento registado a partir da dll de filtro de palavra-passe do controlador de domínio agente. Para uma ativação operação de validação da palavra-passe, há geralmente dois eventos registados, um do serviço de agente do controlador de domínio e um a partir da dll de filtro de palavra-passe do agente de controlador de domínio.

Os eventos discretos para capturar essas situações são registados, com base em torno dos seguintes fatores:

* Se uma determinada palavra-passe está a ser definido ou foi alterada.
* Se a validação de uma determinada palavra-passe foi aprovada ou reprovada.
* Se a validação falhou devido a política global Microsoft, a política organizacional ou uma combinação.
* Se modo só de auditoria está atualmente ativada ou desativada para a política de palavra-passe atual.

Os principais eventos relacionados à validação de palavra-passe são os seguintes:

|   |Alteração da palavra-passe |Conjunto de palavra-passe|
| --- | :---: | :---: |
|Passagem |10014 |10015|
|Falhar (devido à política de palavra-passe de cliente)| 10016, 30002| 10017, 30003|
|Falhar (devido à política de palavra-passe da Microsoft)| 10016, 30004| 10017, 30005|
|Falhar (devido a políticas de palavra-passe do Microsoft e de clientes combinadas)| 10016, 30026| 10017, 30027|
|Só de auditoria Pass (seria ter falhado política de palavra-passe de cliente)| 10024, 30008| 10025, 30007|
|Só de auditoria Pass (seria ter falhado política de palavra-passe da Microsoft)| 10024, 30010| 10025, 30009|
|Só de auditoria Pass (seria ter falhado combinadas políticas de palavra-passe de Microsoft e de clientes)| 10024, 30028| 10025, 30029|

Os casos na tabela acima, que se referem a "políticas combinadas"-se a situações em que a senha de um usuário foi encontrada para conter, pelo menos, um token da lista de palavra-passe do Microsoft banida e a lista de palavra-passe de cliente banida.

Quando um par de eventos tem sessão iniciado em conjunto, os dois eventos são explicitamente associados ao ter a mesma CorrelationId.

### <a name="password-validation-summary-reporting-via-powershell"></a>Resumo de validação de palavra-passe de relatórios através do PowerShell

O `Get-AzureADPasswordProtectionSummaryReport` cmdlet pode ser utilizado para produzir uma vista de resumo de actividade de validação de palavras-passe. Um exemplo de saída deste cmdlet é o seguinte:

```PowerShell
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

O âmbito do relatório do cmdlet pode ser influenciado através de um dos parâmetros de – – DomainController, - domínio ou floresta. Não especificar um parâmetro implica – floresta.

O `Get-AzureADPasswordProtectionSummaryReport` cmdlet funciona consultando o registo de eventos de administração de agente do controlador de domínio e, em seguida, contar o número total de eventos que correspondem a cada categoria de resultado apresentado. A tabela seguinte contém os mapeamentos entre cada resultado e o respetivo id de evento correspondente:

|Propriedade de GET-AzureADPasswordProtectionSummaryReport |Id de evento correspondentes|
| :---: | :---: |
|PasswordChangesValidated |10014|
|PasswordSetsValidated |10015|
|PasswordChangesRejected |10016|
|PasswordSetsRejected |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

Tenha em atenção que o `Get-AzureADPasswordProtectionSummaryReport` cmdlet é fornecido no formulário de script do PowerShell e, se necessário Maio referenciado diretamente na seguinte localização:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> Este cmdlet funciona ao abrir uma sessão do PowerShell para cada controlador de domínio. Para ter êxito, o suporte de sessão remota do PowerShell tem de estar ativado em cada controlador de domínio e o cliente tem de ter privilégios suficientes. Para obter mais informações sobre requisitos de sessão remota do PowerShell, execute "Get-Help about_Remote_Troubleshooting" numa janela do PowerShell.

> [!NOTE]
> Este cmdlet funciona ao consultar o registo de eventos do administrador do serviço de agente cada controlador de domínio remotamente. Se os registos de eventos contêm um grande número de eventos, o cmdlet poderá demorar muito tempo a concluir. Além disso, consultas de rede em massa de grandes conjuntos de dados podem afetar o desempenho do controlador de domínio. Por conseguinte, este cmdlet deve ser usado com cuidado em ambientes de produção.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Mensagem de registo de eventos de exemplo para o ID de evento 10014 (alteração da palavra-passe com êxito)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Mensagem de registo de eventos de exemplo para o ID de evento 10017 e 30003 (conjunto de tentativas de senha)

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

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Mensagem de registo de eventos de exemplo para o ID de evento 30001 (palavra-passe aceite devido a nenhuma política disponível)

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

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Mensagem de registo de eventos de exemplo para o ID de evento 30006 (nova política a ser imposta)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>Mensagem de registo de eventos de exemplo para o ID de evento 30019 (proteção de palavra-passe do Azure AD está desativada)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>Registo operacional de agente do controlador de domínio

O serviço de agente do controlador de domínio também irá registar eventos operacionais relacionados com o registo seguinte:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>Registo de rastreio de agente do controlador de domínio

O serviço de agente do controlador de domínio também pode registar eventos de rastreio verboso do nível de depuração no registo seguinte:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Registo de rastreio está desativado por predefinição.

> [!WARNING]
> Quando ativada, o registo de rastreio recebe um grande volume de eventos e pode afetar o desempenho do controlador de domínio. Por conseguinte, este registo melhorado só deve ser ativado quando um problema requer uma investigação mais profunda e, em seguida, apenas para uma quantidade mínima de tempo.

## <a name="dc-agent-text-logging"></a>Registo de texto do agente de DC

O serviço de agente do controlador de domínio pode ser configurado para escrever para um registo de texto ao definir o valor de registo seguinte:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

O registo de texto é desativado por predefinição. Reiniciar o serviço de agente do controlador de domínio é necessário para que as alterações a este valor para entrar em vigor. Quando o controlador de domínio de ativado o serviço de agente irá escrever num arquivo de log, localizado em:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> O registo de texto recebe as mesmo entradas de nível de depuração que podem ser registadas no registo de rastreio, mas geralmente é um formato mais fácil para rever e analisar.

> [!WARNING]
> Quando ativada, este registo recebe um grande volume de eventos e pode afetar o desempenho do controlador de domínio. Por conseguinte, este registo melhorado só deve ser ativado quando um problema requer uma investigação mais profunda e, em seguida, apenas para uma quantidade mínima de tempo.

## <a name="dc-agent-performance-monitoring"></a>A monitorização de desempenho do agente de DC

O software de serviço do agente DC instala um objeto de contador de desempenho chamado **proteção de palavra-passe do Azure AD**. Os seguintes contadores de desempenho estão atualmente disponíveis:

|Nome do contador de desempenho | Descrição|
| --- | --- |
|Palavras-passe processadas |Este contador mostra o número total de palavras-passe processados (aceites ou rejeitados) desde o último reinício.|
|Palavras-passe aceites |Este contador mostra o número total de palavras-passe que foram aceites desde o último reinício.|
|Palavras-passe rejeitadas |Este contador mostra o número total de palavras-passe que foram rejeitados desde o último reinício.|
|Pedidos de filtro de palavra-passe em curso |Este contador mostra o número de pedidos de filtro de palavras-passe atualmente em curso.|
|Pedidos de filtro de palavra-passe de pico |Este contador mostra o número máximo de pedidos de filtro de palavras-passe em simultâneo, desde o último reinício.|
|Erros de pedido de filtro de palavras-passe |Este contador mostra o número total de pedidos de filtro de palavra-passe falhados devido a um erro, desde o último reinício. Podem ocorrer erros quando o serviço de agente do Azure AD palavra-passe DC de proteção não está em execução.|
|Filtro de palavras-passe pedidos/seg |Este contador mostra a taxa a que as palavras-passe estão a ser processadas.|
|Tempo de processamento de solicitação de filtro de palavras-passe |Este contador mostra o tempo médio necessário para processar um pedido de filtro de palavras-passe.|
|Tempo de processamento de solicitação de filtro de palavras-passe de pico |Este contador mostra o pedido de filtro de palavra-passe de pico de processamento tempo desde o último reinício.|
|As palavras-passe aceitaram devido ao modo de auditoria |Este contador mostra o número total de palavras-passe que faria normalmente foram rejeitadas, mas foram aceites uma vez que a política de palavra-passe tiver sido configurada para estar no modo de auditoria (desde o último reinício).|

## <a name="dc-agent-discovery"></a>Deteção de agente de DC

O `Get-AzureADPasswordProtectionDCAgent` cmdlet pode ser utilizado para apresentar informações básicas sobre os vários agentes de controlador de domínio em execução num domínio ou floresta. Estas informações são obtidas os objetos de serviceConnectionPoint registados, os serviços de agente do controlador de domínio em execução.

Um exemplo de saída deste cmdlet é o seguinte:

```PowerShell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

As diversas propriedades são atualizadas por cada serviço de agente do controlador de domínio numa base horária aproximado. Os dados estão ainda sujeitos a latência de replicação do Active Directory.

O âmbito de consulta do cmdlet pode ser influenciado através de um – parâmetros de floresta ou – o domínio.

Se o valor de HeartbeatUTC ficar obsoleto, isso pode ser um sintoma de que o agente do Azure AD palavra-passe DC de proteção nesse controlador de domínio não está em execução ou foi desinstalado ou a máquina foi despromovida e já não é um controlador de domínio.

Se o obtém de valor PasswordPolicyDateUTC obsoletos, isso pode ser um sintoma de que tem o agente de proteção de DC de palavra-passe do Azure AD nessa máquina não está a funcionar corretamente.

## <a name="proxy-service-event-logging"></a>Registo de eventos do serviço de proxy

O serviço de Proxy emite um conjunto mínimo de eventos para os seguintes registos de eventos:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Tenha em atenção que o registo de rastreio está desativada por predefinição.

> [!WARNING]
> Quando ativada, o registo de rastreio recebe um grande volume de eventos e isto pode afetar o desempenho do anfitrião proxy. Por conseguinte, este registo deve ser ativado apenas quando um problema requer mais investigação e, em seguida, apenas para uma quantidade mínima de tempo.

Os eventos são registados pelos vários componentes de Proxy a utilizar os seguintes intervalos:

|Componente |Intervalo de ID de evento|
| --- | --- |
|Processo de hospedagem de serviço do proxy| 10000-19999|
|Lógica de negócio de principal de serviço de proxy| 20000-29999|
|Cmdlets do PowerShell| 30000-39999|

## <a name="proxy-service-text-logging"></a>Registo de texto do serviço de proxy

O serviço de Proxy pode ser configurado para escrever para um registo de texto ao definir o valor de registo seguinte:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters!EnableTextLogging = 1 (REG_DWORD value)

O registo de texto é desativado por predefinição. Reiniciar o serviço de Proxy é necessário para que as alterações a este valor para entrar em vigor. Quando ativar o Proxy de serviço irá escrever num arquivo de log, localizado em:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> O registo de texto recebe as mesmo entradas de nível de depuração que podem ser registadas no registo de rastreio, mas geralmente é um formato mais fácil para rever e analisar.

> [!WARNING]
> Quando ativada, este registo recebe um grande volume de eventos e pode afetar o desempenho da máquina. Por conseguinte, este registo melhorado só deve ser ativado quando um problema requer uma investigação mais profunda e, em seguida, apenas para uma quantidade mínima de tempo.

## <a name="powershell-cmdlet-logging"></a>Registo de cmdlet do PowerShell

Cmdlets do PowerShell que resultam numa alteração de estado (por exemplo, registre-se-AzureADPasswordProtectionProxy) normalmente irá registrar um evento de resultado para o registo operacional.

Além disso, a maioria dos cmdlets do PowerShell de proteção de palavra-passe do Azure AD irá escrever para um registo de texto localizado em:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Se ocorrer um erro de cmdlet e a solução de e/ou direitos de causa não é imediatamente aparente, estes registos de texto podem também ser consultada com a maior.

## <a name="proxy-discovery"></a>Deteção de proxy

O `Get-AzureADPasswordProtectionProxy` cmdlet pode ser utilizado para apresentar informações básicas sobre os vários serviços de Proxy de proteção de palavra-passe do Azure AD em execução num domínio ou floresta. Estas informações são obtidas os objetos de serviceConnectionPoint registados, os serviços de Proxy em execução.

Um exemplo de saída deste cmdlet é o seguinte:

```PowerShell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

As diversas propriedades são atualizadas por cada serviço de Proxy à hora aproximada. Os dados estão ainda sujeitos a latência de replicação do Active Directory.

O âmbito de consulta do cmdlet pode ser influenciado através de um – parâmetros de floresta ou – o domínio.

Se o valor de HeartbeatUTC ficar obsoleto, isso pode ser um sintoma de que o Proxy de proteção de palavra-passe do Azure AD nessa máquina não está em execução ou foi desinstalado.

## <a name="next-steps"></a>Passos Seguintes

[Resolução de problemas para proteção de palavras-passe do AD do Azure](howto-password-ban-bad-on-premises-troubleshoot.md)

Para obter mais informações sobre as listas globais e personalizadas banidas palavra-passe, consulte o artigo [banir palavras-passe incorretas](concept-password-ban-bad.md)
