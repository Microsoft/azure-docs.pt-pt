---
title: Monitor no local Azure AD Password Protection
description: Saiba como monitorizar e rever registos para a Proteção de Passwords Azure AD para um ambiente de serviços de domínio de diretório ativo no local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: d67d867249286ad1591b441bbe5ea2637971e104
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652620"
---
# <a name="monitor-and-review-logs-for-on-premises-azure-ad-password-protection-environments"></a>Monitor e registos de revisão para ambientes de proteção de senhas azure ad no local

Após a implementação da Proteção de Passwords AD Azure, a monitorização e o reporte são tarefas essenciais. Este artigo entra em detalhes para ajudá-lo a entender várias técnicas de monitorização, incluindo onde cada serviço regista informações e como reportar sobre a utilização da Proteção de Passwords Azure AD.

A monitorização e o reporte são feitos quer através de mensagens de registo de eventos, quer através da execução de cmdlets PowerShell. O agente de DC e os serviços de procuração registam mensagens de registo de eventos. Todos os cmdlets PowerShell descritos abaixo estão disponíveis apenas no servidor proxy (ver o módulo PowerShell de Proteção de Passwords AzureAD). O software do agente DC não instala um módulo PowerShell.

## <a name="dc-agent-event-logging"></a>Dc agent loglogging

Em cada controlador de domínio, o software de serviço do agente DC escreve os resultados de cada operação individual de validação de passwords (e outro estado) para um registo de eventolocal:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

O registo do agente dC Admin é a principal fonte de informação para o comportamento do software.

Note que o registo trace está desligado por defeito.

Os eventos registados pelos vários componentes do agente DC enquadram-se nas seguintes gamas:

|Componente |Gama de ID do evento|
| --- | --- |
|Dll de filtro de senha do agente DC| 10000-19999|
|Processo de hospedagem de serviço de agente DC| 20000-29999|
|Lógica de validação da política de serviço do agente DC| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>Registo de eventos do agente dC Admin

### <a name="password-validation-outcome-events"></a>Eventos de resultados de validação de passwords

Em cada controlador de domínio, o software de serviço do agente DC escreve os resultados de cada validação individual de palavra-passe para o registo de eventos do agente DC.

Para uma operação de validação de palavras-passe bem sucedida, existe geralmente um evento registado a partir do dll de filtro de senha do agente DC. Para uma operação de validação de senhas falhada, existem geralmente dois eventos registados, um do serviço de agente DC e um do dll de filtro de senha do Agente DC.

Eventos discretos para capturar estas situações são registados, com base nos seguintes fatores:

* Se uma determinada palavra-passe está a ser definida ou alterada.
* Quer a validação de uma determinada palavra-passe tenha sido aprovada ou falhada.
* Se a validação falhou devido à política global da Microsoft, à política organizacional ou a uma combinação.
* Se o modo de auditoria está atualmente ligado ou desligado para a atual política de passwords.

Os principais eventos relacionados com a validação de palavras-passe são os seguintes:

|   |Alteração da palavra-passe |Conjunto de palavra-passe|
| --- | :---: | :---: |
|Passe |10014 |10015|
|Falhar (devido à política de passwords do cliente)| 10016, 30002| 10017, 30003|
|Falhar (devido à política de passwords da Microsoft)| 10016, 30004| 10017, 30005|
|Falhar (devido às políticas combinadas da Microsoft e da palavra-passe do cliente)| 10016, 30026| 10017, 30027|
|Passe apenas para auditoria (teria falhado na política de senha do cliente)| 10024, 30008| 10025, 30007|
|Passe apenas para auditoria (teria falhado na política de passwords da Microsoft)| 10024, 30010| 10025, 30009|
|Passe apenas para auditoria (teria falhado as políticas combinadas da Microsoft e da palavra-passe do cliente)| 10024, 30028| 10025, 30029|

Os casos na tabela acima que se referem a "políticas combinadas" referem-se a situações em que a palavra-passe de um utilizador continha pelo menos um símbolo tanto da lista de palavras-passe proibida da Microsoft como da lista de palavras-passe proibida do cliente.

Quando um par de eventos é registado em conjunto, ambos os eventos são explicitamente associados por ter o mesmo CorrelationId.

### <a name="password-validation-summary-reporting-via-powershell"></a>Relatório sumário de validação de palavras-passe via PowerShell

O `Get-AzureADPasswordProtectionSummaryReport` cmdlet pode ser utilizado para produzir uma visão sumária da atividade de validação de palavras-passe. Uma saída exemplo deste cmdlet é a seguinte:

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

O âmbito do relatório do cmdlet pode ser influenciado utilizando um dos parâmetros -Floresta, Domínio ou – DomainController. Não especificar um parâmetro implica - Floresta.

O `Get-AzureADPasswordProtectionSummaryReport` cmdlet funciona consultando o registo de eventos do agente DC e, em seguida, contando o número total de eventos que correspondem a cada categoria de resultados exibidos. A tabela seguinte contém os mapeamentos entre cada resultado e o respetivo ID do evento:

|Get-AzureADPasswordProtectionSummaryReport |ID do evento correspondente|
| :---: | :---: |
|PasswordChangesValidado |10014|
|PasswordSetsValidado |10015|
|PasswordChangesRejeitada |10016|
|PasswordSetsRejeitado |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|Erros de mudança de palavras-passe |10012|
|Erros de Definição de Passwords |10013|

Note que `Get-AzureADPasswordProtectionSummaryReport` o cmdlet é enviado no formulário de script PowerShell e, se necessário, pode ser referenciado diretamente no seguinte local:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> Este cmdlet funciona abrindo uma sessão PowerShell a cada controlador de domínio. Para ter sucesso, o suporte à sessão remota PowerShell deve ser ativado em cada controlador de domínio, e o cliente deve ter privilégios suficientes. Para obter mais informações sobre os requisitos de sessão remota powerShell, faça 'Get-Help about_Remote_Troubleshooting' numa janela PowerShell.

> [!NOTE]
> Este cmdlet funciona consultando remotamente o registo de eventos do administrador de cada agente dc. Se os registos do evento contiverem um grande número de eventos, o cmdlet pode demorar muito tempo a ser concluído. Além disso, as consultas de rede a granel de grandes conjuntos de dados podem ter impacto no desempenho do controlador de domínio. Por conseguinte, este cmdlet deve ser utilizado cuidadosamente em ambientes de produção.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Mensagem de registo de eventos de amostra para Id do evento 10014 (mudança de senha bem sucedida)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Amostra de mensagem de registo de eventos para O ID do Evento 10017 e 30003 (conjunto de senha sem falhas)

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

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Amostra de mensagem de registo de eventos para Id do evento 30001 (palavra-passe aceite devido a nenhuma política disponível)

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

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Mensagem de registo de eventos de amostra para id do evento 30006 (nova política a ser aplicada)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>Mensagem de registo de eventos de amostra para id do evento 30019 (A Proteção de Passwords Azure AD está desativada)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>Registo operacional do agente DC

O serviço de agente DC também registará eventos relacionados com a operacionalidade no seguinte registo:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>Registo de rastreio do agente DC

O serviço de agente DC também pode registar eventos de vestígios verbose-nível de depuração para o seguinte registo:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

A exploração de rastreios é desativada por defeito.

> [!WARNING]
> Quando ativado, o registo Trace recebe um elevado volume de eventos e pode impactar o desempenho do controlador de domínio. Por conseguinte, este registo reforçado só deve ser ativado quando um problema requer uma investigação mais profunda e, em seguida, apenas por um período mínimo de tempo.

## <a name="dc-agent-text-logging"></a>Dc Agent slogging

O serviço de agente DC pode ser configurado para escrever para um registo de texto, definindo o seguinte valor de registo:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

A exploração de sms é desativada por defeito. É necessário reiniciar o serviço de agente dc para que as alterações a este valor entrem em vigor. Quando ativado o serviço de agente DC escreverá para um ficheiro de registo localizado em:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> O registo de texto recebe as mesmas entradas de nível de depuração que podem ser registadas no registo trace, mas está geralmente num formato mais fácil de analisar e analisar.

> [!WARNING]
> Quando ativado, este registo recebe um elevado volume de eventos e pode impactar o desempenho do controlador de domínio. Por conseguinte, este registo reforçado só deve ser ativado quando um problema requer uma investigação mais profunda e, em seguida, apenas por um período mínimo de tempo.

## <a name="dc-agent-performance-monitoring"></a>Monitorização do desempenho do agente DC

O software de serviço do agente DC instala um objeto de contra-ataque de desempenho chamado **Proteção de Passwords AD Azure**. Os seguintes contadores perf estão atualmente disponíveis:

|Contranome Perf | Descrição|
| --- | --- |
|Palavras-passe processadas |Este contador apresenta o número total de senhas processadas (aceites ou rejeitadas) desde o último reinício.|
|Palavras-passe aceites |Este contador apresenta o número total de senhas que foram aceites desde o último reinício.|
|Palavras-passe rejeitadas |Este contador apresenta o número total de senhas que foram rejeitadas desde o último reinício.|
|Pedidos de filtro de senha em andamento |Este contador apresenta o número de pedidos de filtro de senha atualmente em curso.|
|Pedidos de filtro de senha de pico |Este contador apresenta o número máximo de pedidos simultâneos de filtro de senha desde o último reinício.|
|Erros de pedido de filtro de senha |Este contador apresenta o número total de pedidos de filtro de senha que falharam devido a um erro desde o último reinício. Erros podem ocorrer quando o serviço de agente DC de proteção de senhas Azure AD não está em funcionamento.|
|Pedidos de filtro de senha/seg |Este contador apresenta a taxa a que as palavras-passe estão a ser processadas.|
|Tempo de processamento de pedido de filtro de senha |Este contador apresenta o tempo médio necessário para processar um pedido de filtro de senha.|
|Tempo de processamento de pedido de filtro de senha de pico |Este contador apresenta o tempo de processamento do pedido de filtro de senha de pico desde o último reinício.|
|Palavras-passe aceites devido ao modo de auditoria |Este contador apresenta o número total de senhas que normalmente teriam sido rejeitadas, mas foram aceites porque a política de passwords estava configurada para estar em modo de auditoria (desde o último reinício).|

## <a name="dc-agent-discovery"></a>Descoberta do Agente DC

O `Get-AzureADPasswordProtectionDCAgent` cmdlet pode ser utilizado para apresentar informações básicas sobre os vários agentes de DC que correm num domínio ou floresta. Estas informações são obtidas a partir do(s) objeto(s) de serviçoConnectionPoint registado pelo(s) serviço(s) de agente em execução dc.

Uma saída exemplo deste cmdlet é a seguinte:

```powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

As várias propriedades são atualizadas por cada serviço de agente DC numa base aproximada de hora a hora. Os dados ainda estão sujeitos à latência de replicação do Diretório Ativo.

O âmbito da consulta do cmdlet pode ser influenciado utilizando os parâmetros –Floresta ou -Domínio.

Se o valor HeartbeatUTC ficar velho, este pode ser um sintoma de que o Agente DC de Proteção de Senhas AD Azure nesse controlador de domínio não está em execução, ou foi desinstalado, ou a máquina foi despromovida e já não é um controlador de domínio.

Se o valor PasswordPolicyDateUTC ficar velho, este pode ser um sintoma de que o Agente DC de Proteção de Senhas AD Azure naquela máquina não está a funcionar corretamente.

## <a name="dc-agent-newer-version-available"></a>Dc agent versão mais recente disponível

O serviço de agente DC irá registar um evento de alerta 30034 no registo operacional ao detetar que uma versão mais recente do software do agente DC está disponível, por exemplo:

```text
An update for Azure AD Password Protection DC Agent is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
```

O evento acima não especifica a versão do software mais recente. Devias ir ao link na mensagem do evento para essa informação.

> [!NOTE]
> Apesar das referências à "atualização automática" na mensagem de evento acima, o software do agente DC não suporta atualmente esta funcionalidade.

## <a name="proxy-service-event-logging"></a>Exploração de eventos de serviço proxy

O serviço Proxy emite um conjunto mínimo de eventos para os seguintes registos de eventos:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Note que o registo trace está desligado por defeito.

> [!WARNING]
> Quando ativado, o registo Trace recebe um elevado volume de eventos e isso pode ter impacto no desempenho do hospedeiro proxy. Por conseguinte, este registo só deve ser ativado quando um problema requer uma investigação mais aprofundada e, em seguida, apenas por um período mínimo de tempo.

Os eventos são registados pelos vários componentes proxy utilizando as seguintes gamas:

|Componente |Gama de ID do evento|
| --- | --- |
|Processo de hospedagem de serviço proxy| 10000-19999|
|Lógica de negócio principal do serviço proxy| 20000-29999|
|Cmdlets do PowerShell| 30000-39999|

## <a name="proxy-service-text-logging"></a>Exploração de texto de serviço proxy

O serviço Proxy pode ser configurado para escrever para um registo de texto, definindo o seguinte valor de registo:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters! EnableTextLogging = 1 (REG_DWORD valor)

A exploração de sms é desativada por defeito. É necessário reiniciar o serviço Proxy para que as alterações a este valor entrem em vigor. Quando ativado, o serviço Proxy escreverá para um ficheiro de registo localizado em:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> O registo de texto recebe as mesmas entradas de nível de depuração que podem ser registadas no registo trace, mas está geralmente num formato mais fácil de analisar e analisar.

> [!WARNING]
> Quando ativado, este registo recebe um elevado volume de eventos e pode afetar o desempenho da máquina. Por conseguinte, este registo reforçado só deve ser ativado quando um problema requer uma investigação mais profunda e, em seguida, apenas por um período mínimo de tempo.

## <a name="powershell-cmdlet-logging"></a>PowerShell cmdlet logging

Os cmdlets PowerShell que resultam numa mudança de estado (por exemplo, Register-AzureADPasswordProtectionProxy) normalmente registam um evento de resultados no registo operacional.

Além disso, a maioria dos cmdlets de proteção de senhas AD Azure AD escreverá para um registo de texto localizado em:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Se ocorrer um erro de cmdlet e a causa e a solução não forem facilmente aparentes, estes registos de texto também podem ser consultados.

## <a name="proxy-discovery"></a>Descoberta por procuração

O `Get-AzureADPasswordProtectionProxy` cmdlet pode ser utilizado para apresentar informações básicas sobre os vários serviços de procuração de passwords Azure AD que estão em execução num domínio ou floresta. Estas informações são obtidas a partir do(s) objeto(s) de serviçoConnectionPoint registado pelo ou serviço s Proxy em execução.

Uma saída exemplo deste cmdlet é a seguinte:

```powershell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

As várias propriedades são atualizadas por cada serviço Proxy numa base horária aproximada. Os dados ainda estão sujeitos à latência de replicação do Diretório Ativo.

O âmbito da consulta do cmdlet pode ser influenciado utilizando os parâmetros –Floresta ou -Domínio.

Se o valor HeartbeatUTC ficar velho, este pode ser um sintoma de que o Proxy de Proteção de Passwords AD Azure naquela máquina não está a funcionar ou foi desinstalado.

## <a name="proxy-agent-newer-version-available"></a>Versão mais recente do agente proxy disponível

O serviço Proxy irá registar um evento de alerta de 20002 no registo operacional ao detetar que uma versão mais recente do software proxy está disponível, por exemplo:

```text
An update for Azure AD Password Protection Proxy is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
.
```

O evento acima não especifica a versão do software mais recente. Devias ir ao link na mensagem do evento para essa informação.

Este evento será emitido mesmo que o agente Proxy esteja configurado com a atualização automática ativada.

## <a name="next-steps"></a>Passos seguintes

[Resolução de problemas para proteção de senha seletiva Azure AD](howto-password-ban-bad-on-premises-troubleshoot.md)

Para obter mais informações sobre as listas de senhas banidas globais e personalizadas, consulte o artigo [Ban bad passwords](concept-password-ban-bad.md)
