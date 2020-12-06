---
title: Monitor no local Azure AD Password Protection
description: Saiba como monitorizar e rever registos de Proteção de Passwords AZure AD para um ambiente de Serviços de Domínio de Diretório Ativo no local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: edc246a414401c4c1c0248787eda0381fcd63037
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741767"
---
# <a name="monitor-and-review-logs-for-on-premises-azure-ad-password-protection-environments"></a>Monitor e revisão de registos para ambientes de proteção de senhas Azure AD

Após a implementação da Proteção de Passwords AZure AD, a monitorização e o reporte são tarefas essenciais. Este artigo entra em detalhes para ajudá-lo a entender várias técnicas de monitorização, incluindo onde cada serviço regista informações e como reportar sobre a utilização da Proteção de PasswordS AD AZure.

A monitorização e o reporte são feitos através de mensagens de registo de eventos ou através da execução de cmdlets PowerShell. O agente dc e os serviços de procuração registam mensagens de registo de eventos. Todos os cmdlets PowerShell descritos abaixo estão disponíveis apenas no servidor proxy (ver módulo AzureADPasswordProtection PowerShell). O software do agente DC não instala um módulo PowerShell.

## <a name="dc-agent-event-logging"></a>Registo de eventos de agente de DC

Em cada controlador de domínio, o software de serviço do agente DC escreve os resultados de cada operação de validação de senha individual (e outro estado) para um registo de evento local:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

O registo do agente dc Admin é a principal fonte de informação para o comportamento do software.

Note que o registo trace está desligado por defeito.

Os eventos registados pelos vários componentes do agente DC enquadram-se nas seguintes gamas:

|Componente |Gama de ID de evento|
| --- | --- |
|Dc Agent password filter dll| 10000-19999|
|Processo de hospedagem de serviço de agente dc| 20000-29999|
|Lógica de validação da política de serviço de agente de DC| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>Registo de eventos do agente DC Admin

### <a name="password-validation-outcome-events"></a>Eventos de resultados de validação de palavras-passe

Em cada controlador de domínio, o software de serviço do agente DC escreve os resultados de cada validação de senha individual para o registo de eventos de administração do agente DC.

Para uma operação de validação de senha bem sucedida, existe geralmente um evento registado a partir do filtro de senha do agente DC. Para uma operação de validação de senha falhada, existem geralmente dois eventos registados, um do serviço de agente DC e um do filtro de senha do Dc Agent dll.

Os eventos discretos para capturar estas situações são registados, com base em torno dos seguintes fatores:

* Se uma determinada palavra-passe está a ser definida ou alterada.
* Se a validação de uma determinada palavra-passe passou ou falhou.
* Se a validação falhou devido à política global da Microsoft, à política organizacional ou a uma combinação.
* Se o modo de auditoria está ligado ou desligado para a atual política de senha.

Os principais eventos relacionados com validação de passwords são os seguintes:

| Evento |Alteração da palavra-passe |Conjunto de palavra-passe|
| --- | :---: | :---: |
|Aprovação |10014 |10015|
|Falha (devido à política de senha do cliente)| 10016, 30002| 10017, 30003|
|Falha (devido à política de senha da Microsoft)| 10016, 30004| 10017, 30005|
|Falha (devido às políticas combinadas de palavra-passe da Microsoft e do cliente)| 10016, 30026| 10017, 30027|
|Falha (devido ao nome de utilizador)| 10016, 30021| 10017, 30022|
|Passe só para auditoria (teria falhado a política de senha do cliente)| 10024, 30008| 10025, 30007|
|Passe só para auditoria (teria falhado a política de senha da Microsoft)| 10024, 30010| 10025, 30009|
|Passe só para auditoria (teria falhado as políticas combinadas da Microsoft e da palavra-passe do cliente)| 10024, 30028| 10025, 30029|
|Passe só para auditoria (teria falhado devido ao nome de utilizador)| 10016, 30024| 10017, 30023|

Os casos na tabela acima que se referem a "políticas combinadas" referem-se a situações em que a palavra-passe de um utilizador continha pelo menos um símbolo da lista de palavras-passe proibidas pela Microsoft e da lista de palavras-passe proibidas pelo cliente.

Os casos na tabela acima que se referem a "nome de utilizador" referem-se a situações em que a palavra-passe de um utilizador continha o nome da conta do utilizador e/ou um dos nomes amigáveis do utilizador. Qualquer um dos cenários fará com que a palavra-passe do utilizador seja rejeitada quando a política estiver definida para Impor, ou aprovada se a política estiver no modo de Auditoria.

Quando um par de eventos é registado em conjunto, ambos os eventos são explicitamente associados por ter o mesmo CorrelationId.

### <a name="password-validation-summary-reporting-via-powershell"></a>Relatório sumário de validação de palavra-passe através do PowerShell

O `Get-AzureADPasswordProtectionSummaryReport` cmdlet pode ser utilizado para produzir uma visão sumária da atividade de validação de palavras-passe. Um exemplo de saída deste cmdlet é o seguinte:

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

O âmbito da comunicação do cmdlet pode ser influenciado utilizando um dos parâmetros -Floresta, Domínio ou Domínio do Domínio. Não especificar um parâmetro implica :Floresta.

O `Get-AzureADPasswordProtectionSummaryReport` cmdlet funciona consultando o registo de eventos do agente DC e, em seguida, contando o número total de eventos que correspondem a cada categoria de resultados apresentados. A tabela a seguir contém os mapeamentos entre cada resultado e o seu iD do evento correspondente:

|Get-AzureADPasswordProtectionSummaryReport propriedade |ID do evento correspondente|
| :---: | :---: |
|PasswordSSEsaloridada |10014|
|PasswordsSetsTuradas |10015|
|PasswordChangesRejected |10016|
|PasswordSetsRejados |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

Note que o `Get-AzureADPasswordProtectionSummaryReport` cmdlet é enviado no formulário de script PowerShell e, se necessário, pode ser referenciado diretamente no seguinte local:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> Este cmdlet funciona abrindo uma sessão PowerShell a cada controlador de domínio. Para ter sucesso, o suporte de sessão remota PowerShell deve ser ativado em cada controlador de domínio, e o cliente deve ter privilégios suficientes. Para obter mais informações sobre os requisitos de sessão remota powerShell, execute 'Get-Help about_Remote_Troubleshooting' numa janela PowerShell.

> [!NOTE]
> Este cmdlet funciona consultando remotamente o registo de eventos de cada agente de DC. Se os registos do evento contiverem um grande número de eventos, o cmdlet pode demorar muito tempo a ser concluído. Além disso, as consultas de rede a granel de grandes conjuntos de dados podem ter impacto no desempenho do controlador de domínio. Por conseguinte, este cmdlet deve ser utilizado cuidadosamente em ambientes de produção.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Mensagem de registo de evento de amostra para o ID do evento 10014 (alteração de senha bem sucedida)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Mensagem de registo de evento de amostra para o ID do evento 10017 e 30003 (conjunto de senhas falhadas)

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

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Mensagem de registo de eventos de amostra para o Evento ID 30001 (palavra-passe aceite devido a nenhuma política disponível)

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

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Amostra de mensagem de registo de eventos para o Evento ID 30006 (nova política a ser aplicada)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>Mensagem de registo de eventos de amostra para o Evento ID 30019 (Azure AD Password Protection está desativada)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>Registo operacional do agente DC

O serviço de agente dc também registará eventos relacionados com a operação ao seguinte registo:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>Registo de vestígios de agente dc

O serviço de agente DC também pode registar eventos de rastreia ao nível de depurado verbose para o seguinte registo:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

A registo de vestígios é desativada por predefinição.

> [!WARNING]
> Quando ativado, o registo Trace recebe um elevado volume de eventos e pode ter impacto no desempenho do controlador de domínio. Portanto, este registo melhorado só deve ser ativado quando um problema requer uma investigação mais profunda e, em seguida, apenas por um mínimo de tempo.

## <a name="dc-agent-text-logging"></a>Registo de texto do agente DC

O serviço de agente DC pode ser configurado para escrever a um registo de texto definindo o seguinte valor de registo:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

A sessão de registo de texto é desativada por predefinição. É necessário reiniciar o serviço de agente DC para que as alterações a este valor entrem em vigor. Quando ativado, o serviço de agente DC escreverá num ficheiro de registo localizado sob:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> O registo de texto recebe as mesmas entradas de nível de depurado que podem ser registadas no registo Trace, mas geralmente está num formato mais fácil de rever e analisar.

> [!WARNING]
> Quando ativado, este registo recebe um elevado volume de eventos e pode ter impacto no desempenho do controlador de domínio. Portanto, este registo melhorado só deve ser ativado quando um problema requer uma investigação mais profunda e, em seguida, apenas por um mínimo de tempo.

## <a name="dc-agent-performance-monitoring"></a>Monitorização do desempenho do agente DC

O software de serviço do agente DC instala um objeto de contador de desempenho chamado **Azure AD Password Protection**. Os seguintes contadores perf estão atualmente disponíveis:

|Nome do contador Perf | Description|
| --- | --- |
|Palavras-passe processadas |Este contador apresenta o número total de palavras-passe processadas (aceites ou rejeitadas) desde o último recomeço.|
|Palavras-passe aceites |Este contador apresenta o número total de palavras-passe que foram aceites desde o último recomeço.|
|Palavras-passe rejeitadas |Este contador apresenta o número total de palavras-passe que foram rejeitadas desde o último recomeço.|
|Pedidos de filtro de senha em curso |Este contador apresenta o número de pedidos de filtro de senha atualmente em curso.|
|Pedidos de filtro de senha de pico |Este contador apresenta o número máximo de pedidos de filtro de senha simultânea desde o último reinício.|
|Erros de pedido de filtro de senha |Este contador apresenta o número total de pedidos de filtro de senha que falharam devido a um erro desde o último reinício. Podem ocorrer erros quando o serviço de agente de proteção de passwords Azure AD não está a funcionar.|
|Pedidos de filtro de senha/seg |Este contador apresenta a taxa a que as palavras-passe estão a ser processadas.|
|Tempo de processamento do pedido de senha |Este contador apresenta o tempo médio necessário para processar um pedido de filtro de senha.|
|Tempo de processamento do filtro de senha de pico |Este contador apresenta o tempo de processamento do filtro de senha de pico desde o último reinício.|
|Palavras-passe aceites devido ao modo de auditoria |Este contador apresenta o número total de palavras-passe que normalmente teriam sido rejeitadas, mas foram aceites porque a política de palavra-passe estava configurada para estar em modo de auditoria (desde o último reinício).|

## <a name="dc-agent-discovery"></a>Descoberta do agente dc

O `Get-AzureADPasswordProtectionDCAgent` cmdlet pode ser usado para apresentar informações básicas sobre os vários agentes dc em execução em um domínio ou floresta. Estas informações são obtidas a partir do(s) object(s) serviceConnectionPoint registados pelo(s) serviço de agente DC em execução.

Um exemplo de saída deste cmdlet é o seguinte:

```powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

As várias propriedades são atualizadas por cada serviço de agente DC numa base aproximada de hora a hora. Os dados ainda estão sujeitos à latência de replicação do Ative Directory.

O âmbito da consulta do cmdlet pode ser influenciado utilizando os parâmetros -Floresta ou Domínio.

Se o valor HeartbeatUTC ficar velho, este pode ser um sintoma de que o Agente DC de Proteção de Passwords AZure AD nesse controlador de domínio não está a funcionar, ou foi desinstalado, ou a máquina foi despromovida e já não é um controlador de domínio.

Se o valor PasswordPolicyDateUTC ficar velho, este pode ser um sintoma de que o Agente DC de Proteção de Passwords AD AZure nessa máquina não está a funcionar corretamente.

## <a name="dc-agent-newer-version-available"></a>Versão mais recente do agente DC disponível

O serviço de agente DC registará um evento de alerta 30034 no registo operacional ao detetar que uma versão mais recente do software do agente DC está disponível, por exemplo:

```text
An update for Azure AD Password Protection DC Agent is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
```

O evento acima não especifica a versão do software mais recente. Você deve ir para o link na mensagem do evento para essa informação.

> [!NOTE]
> Apesar das referências à "autoavaliação" na mensagem de evento acima, o software do agente DC não suporta atualmente esta funcionalidade.

## <a name="proxy-service-event-logging"></a>Registo de eventos de serviço proxy

O serviço Proxy emite um conjunto mínimo de eventos para os seguintes registos de eventos:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Note que o registo trace está desligado por defeito.

> [!WARNING]
> Quando ativado, o registo Trace recebe um elevado volume de eventos e isso pode ter impacto no desempenho do hospedeiro proxy. Portanto, este registo só deve ser ativado quando um problema requer uma investigação mais profunda e, em seguida, apenas por um mínimo de tempo.

Os eventos são registados pelos vários componentes Proxy utilizando as seguintes gamas:

|Componente |Gama de ID de evento|
| --- | --- |
|Processo de hospedagem de serviço proxy| 10000-19999|
|Lógica de negócio principal de serviço proxy| 20000-29999|
|Cmdlets do PowerShell| 30000-39999|

## <a name="proxy-service-text-logging"></a>Registo de texto de serviço proxy

O serviço Proxy pode ser configurado para escrever num registo de texto definindo o seguinte valor de registo:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters! ActivarTextLogging = 1 (valor REG_DWORD)

A sessão de registo de texto é desativada por predefinição. É necessário reiniciar o serviço Proxy para que as alterações a este valor entrem em vigor. Quando ativado, o serviço Proxy escreverá num ficheiro de registo localizado sob:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> O registo de texto recebe as mesmas entradas de nível de depurado que podem ser registadas no registo Trace, mas geralmente está num formato mais fácil de rever e analisar.

> [!WARNING]
> Quando ativado, este registo recebe um elevado volume de eventos e pode afetar o desempenho da máquina. Portanto, este registo melhorado só deve ser ativado quando um problema requer uma investigação mais profunda e, em seguida, apenas por um mínimo de tempo.

## <a name="powershell-cmdlet-logging"></a>Exploração de cmdlet PowerShell

Os cmdlets PowerShell que resultam numa mudança de estado (por exemplo, Register-AzureADPasswordProtectionProxy) normalmente registarão um evento de resultados no registo Operacional.

Além disso, a maioria dos cmdlets de proteção de senha azure AD irá escrever para um registo de texto localizado sob:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Se ocorrer um erro de cmdlet e a causa e a solução não forem facilmente visíveis, estes registos de texto também podem ser consultados.

## <a name="proxy-discovery"></a>Descoberta por procuração

O `Get-AzureADPasswordProtectionProxy` cmdlet pode ser utilizado para apresentar informações básicas sobre os vários serviços de proteção de senhas Azure AD que estão a decorrer num domínio ou floresta. Estas informações são obtidas a partir do(s) object(s) serviceConnectionPoint registados pelo(s) serviço(s) proxy em execução.

Um exemplo de saída deste cmdlet é o seguinte:

```powershell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

As várias propriedades são atualizadas por cada serviço Proxy numa base aproximada de hora a hora. Os dados ainda estão sujeitos à latência de replicação do Ative Directory.

O âmbito da consulta do cmdlet pode ser influenciado utilizando os parâmetros -Floresta ou Domínio.

Se o valor HeartbeatUTC ficar velho, este pode ser um sintoma de que o Proxy de Proteção de Passwords AZure AD nessa máquina não está em funcionamento ou foi desinstalado.

## <a name="proxy-agent-newer-version-available"></a>Versão mais recente do agente Proxy disponível

O serviço Proxy registará um evento de alerta de 20002 no registo operacional ao detetar que uma versão mais recente do software proxy está disponível, por exemplo:

```text
An update for Azure AD Password Protection Proxy is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
.
```

O evento acima não especifica a versão do software mais recente. Você deve ir para o link na mensagem do evento para essa informação.

Este evento será emitido mesmo que o agente Proxy esteja configurado com auto-actualização ativada.

## <a name="next-steps"></a>Passos seguintes

[Resolução de problemas para proteção de senha AD AD Azure](howto-password-ban-bad-on-premises-troubleshoot.md)

Para obter mais informações sobre as listas de senhas proibidas global e personalizada, consulte o artigo [Proibir más senhas](concept-password-ban-bad.md)
