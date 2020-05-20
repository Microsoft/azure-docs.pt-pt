---
title: Problemas de resolução de problemas problemas com problemas de agente de atualização do Windows na Automação Azure
description: Este artigo diz como resolver problemas com o agente de atualização do Windows durante a Atualização.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: ff996227e23836bf85cc3885d9184ae6d7d6c61d
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680839"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>Problemas problemas com problemas com o agente de atualização do Windows

Pode haver muitas razões pelas quais a sua máquina não aparece tão pronta (saudável) durante uma implementação de Gestão de Atualização. Pode verificar a saúde de um agente do Trabalhador do Runbook Híbrido windows para determinar o problema subjacente. Seguem-se os três estados de prontidão para uma máquina:

* Ready: The Hybrid Runbook Worker está implantado e foi visto pela última vez há menos de uma hora.
* Desligado: O Trabalhador híbrido do livro de corridas está implantado e foi visto pela última vez há mais de uma hora.
* Não configurado: O Trabalhador híbrido do runbook não é encontrado ou ainda não terminou a implementação.

> [!NOTE]
> Pode haver um ligeiro atraso entre o que o portal Azure mostra e o estado atual de uma máquina.

Este artigo discute como executar o resolução de problemas para máquinas Azure a partir do portal Azure, e máquinas não-Azure no [cenário offline](#troubleshoot-offline). 

> [!NOTE]
> O script de resolução de problemas inclui agora verificações para os Serviços de Atualização do Servidor do Windows (WSUS) e para as chaves de descarregamento automático e instalação. 

## <a name="start-the-troubleshooter"></a>Inicie o resolução de problemas

Para máquinas Azure, pode lançar a página Do Agente de Atualização de Problemas selecionando o link **Troubleshoot** sob a coluna **de prontidão** do agente de atualização no portal. Para máquinas não-Azure, o link leva-o a este artigo. Consulte [o Troubleshoot offline](#troubleshoot-offline) para filmar uma máquina não-Azure.

![Screenshot da lista de Gestão de Atualizações de máquinas virtuais](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Para verificar a saúde do Trabalhador do Livro De Execução Híbrido, o VM deve estar em execução. Se o VM não estiver a funcionar, aparece um botão **VM.**

Na página do Agente de Atualização de Problemas, selecione **verificações de executar** para iniciar o resolução de problemas. O resoluto de problemas usa [o Comando de Execução](../../virtual-machines/windows/run-command.md) para executar um guião na máquina, para verificar dependências. Quando o resoluto de problemas estiver terminado, devolve o resultado dos cheques.

![Screenshot da página do Agente de Atualização de Problemas](../media/update-agent-issues/troubleshoot-page.png)

Os resultados são mostrados na página quando estão prontos. As secções de verificação mostram o que está incluído em cada cheque.

![Screenshot do agente de atualização de problemas verifica](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Verificações de pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A verificação do sistema operativo verifica se o Trabalhador do Livro híbrido está a executar um dos sistemas operativos apresentados na tabela seguinte.

|Sistema operativo  |Notas  |
|---------|---------|
|Windows Server 2012 e mais tarde |.NET Quadro 4.6 ou posterior é necessário. ([Descarregue o .NET Framework](/dotnet/framework/install/guide-for-developers).)<br/> É necessário o Windows PowerShell 5.1.  (Baixar a Estrutura de Gestão de[Janelas 5.1](https://www.microsoft.com/download/details.aspx?id=54616).)        |

### <a name="net-462"></a>.net 4.6.2

A verificação de quadro .NET verifica que o sistema tem [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) ou posteriormente instalado.

### <a name="wmf-51"></a>WMF 5.1

A verificação wmf verifica que o sistema tem a versão necessária da Estrutura de Gestão do Windows (WMF), que é a Estrutura de Gestão do [Windows 5.1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Esta verificação determina se está a utilizar o TLS 1.2 para encriptar as suas comunicações. O TLS 1.0 já não é suportado pela plataforma. Utilize o TLS 1.2 para comunicar com a Atualização.

## <a name="connectivity-checks"></a>Verificações de conectividade

### <a name="registration-endpoint"></a>Ponto final de inscrição

Esta verificação determina se o agente pode comunicar corretamente com o serviço do agente.

As configurações de proxy e firewall devem permitir que o agente Hybrid Runbook Worker comunique com o ponto final de registo. Para que uma lista de endereços e portas seja aberta, consulte [o planeamento da rede.](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Ponto final de operações

Esta verificação determina se o agente pode comunicar corretamente com o Serviço de Dados do Funcionano de Trabalho.

As configurações de proxy e firewall devem permitir que o agente Hybrid Runbook Worker se comunique com o Serviço de Dados do Tempo de Execução de Emprego. Para que uma lista de endereços e portas seja aberta, consulte [o planeamento da rede.](../automation-hybrid-runbook-worker.md#network-planning)

## <a name="vm-service-health-checks"></a>Verificações de saúde de serviço VM

### <a name="monitoring-agent-service-status"></a>Estado do serviço do agente de monitorização

Esta verificação determina se o agente Log Analytics para Windows `healthservice` () está a funcionar na máquina. Para saber mais sobre a resolução de problemas do serviço, consulte [o agente Log Analytics para windows não está a funcionar](hybrid-runbook-worker.md#mma-not-running).

Para reinstalar o agente Log Analytics para Windows, consulte [Instalar o agente para windows](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Eventos de serviço de agente de monitorização

Esta verificação determina se quaisquer eventos de 4502 aparecem no registo do Gestor de Operações Azure na máquina nas últimas 24 horas.

Para saber mais sobre este evento, consulte o [Evento 4502 no registo do Gestor](hybrid-runbook-worker.md#event-4502) de Operações para este evento.

## <a name="access-permissions-checks"></a>Verificações de permissões de acesso

> [!NOTE]
> O resoluto de problemas atualmente não encaminha o tráfego através de um servidor proxy se um estiver configurado.

### <a name="crypto-folder-access"></a>Acesso a pasta Crypto

A verificação de acesso da pasta Crypto determina se a conta do sistema local tem acesso a C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Resolução de problemas offline

Você pode usar o resoludor de problemas em um Hybrid Runbook Worker offline executando o script localmente. Obtenha o seguinte script na Galeria PowerShell: [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration). Para executar o script, deve ter WMF 4.0 ou posteriormente instalado. Para descarregar a versão mais recente do PowerShell, consulte [a instalação de várias versões do PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell).

A saída deste guião parece ser o seguinte exemplo:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.2.9200 (Windows Server 2012) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Log Analytics for Windows service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Log Analytics agent for Windows, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Log Analytics agent for Windows service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Log Analytics agent for Windows, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Passos seguintes

[Problemas problemas híbridos do trabalhador](hybrid-runbook-worker.md)do livro de corridas.
