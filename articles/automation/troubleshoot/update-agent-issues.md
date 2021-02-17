---
title: Problemas de agente de atualização do Windows na Azure Automation
description: Este artigo diz como resolver problemas e resolver problemas com o agente de atualização do Windows durante a Gestão de Atualizações.
services: automation
ms.date: 01/25/2020
ms.topic: troubleshooting
ms.subservice: update-management
ms.openlocfilehash: 9516210021ce48f069ae3b3b4e02503527e0db24
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580884"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>Resolver problemas do agente de atualização do Windows

Pode haver muitas razões pelas quais a sua máquina não aparece tão pronta (saudável) durante uma implementação de Gestão de Atualização. Pode verificar a saúde de um agente do Windows Hybrid Runbook Worker para determinar o problema subjacente. Seguem-se os três estados de prontidão para uma máquina:

* Pronto: O Trabalhador de Runbook Híbrido está implantado e foi visto pela última vez há menos de uma hora.
* Desligado: O Trabalhador de Runbook Híbrido está implantado e foi visto pela última vez há mais de uma hora.
* Não configurado: O Trabalhador de Runbook Híbrido não foi encontrado ou ainda não terminou a implantação.

> [!NOTE]
> Pode haver um ligeiro atraso entre o que o portal Azure mostra e o estado atual de uma máquina.

Este artigo discute como executar o resolução de problemas para máquinas Azure a partir do portal Azure, e máquinas não-Azure no [cenário offline](#troubleshoot-offline).

> [!NOTE]
> O script de resolução de problemas inclui agora verificações para serviços de atualização do Servidor do Windows (WSUS) e para a carga automática e instalação de teclas.

## <a name="start-the-troubleshooter"></a>Inicie o resolução de problemas

Para as máquinas Azure, pode lançar a página 'Relatório de Atualização de Resolução de Problemas', selecionando a ligação **'Resolução de Problemas'** na coluna **'Actualização' de Prontidão** do Agente de Atualização no portal. Para máquinas não-Azure, o link leva-o a este artigo. Consulte [a resolução de problemas offline](#troubleshoot-offline) para resolver problemas com uma máquina não-Azure.

![Screenshot da lista de atualizações de máquinas virtuais](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Para verificar a saúde do Trabalhador de Runbook Híbrido, o VM deve estar a funcionar. Se o VM não estiver em funcionamento, aparece um botão Iniciar o botão **VM.**

Na página 'Relatório de Atualização de Resolução de **Problemas',** selecione Verificações de execução para iniciar o resolução de problemas. O resolução de problemas usa [o Comando de Execução](../../virtual-machines/windows/run-command.md) para executar um script na máquina, para verificar as dependências. Quando a resolução de problemas estiver terminada, devolve o resultado dos controlos.

![Screenshot da página do Agente de Atualização de Resolução de Problemas](../media/update-agent-issues/troubleshoot-page.png)

Os resultados são apresentados na página quando estão prontos. As secções de verificação mostram o que está incluído em cada cheque.

![Screenshot das verificações do Agente de Atualização de Resolução de Problemas](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Verificações de pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A verificação do sistema operativo verifica se o Trabalhador de Runbook Híbrido está a executar um dos sistemas operativos apresentados na tabela seguinte.

|Sistema operativo  |Notas  |
|---------|---------|
|Windows Server 2012 e mais tarde |.NET Quadro 4.6 ou posteriormente é necessário. [(Descarregue o quadro .NET](/dotnet/framework/install/guide-for-developers).)<br/> É necessário windows PowerShell 5.1.  (Baixar[o Quadro de Gestão do Windows 5.1](https://www.microsoft.com/download/details.aspx?id=54616).)        |

### <a name="net-462"></a>.NET 4.6.2

A verificação do quadro .NET verifica se o sistema tem [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) ou posteriormente instalado.

### <a name="wmf-51"></a>WMF 5.1

A verificação do WMF verifica que o sistema tem a versão necessária do Quadro de Gestão do Windows (WMF), que é o [Quadro de Gestão do Windows 5.1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Esta verificação determina se está a utilizar o TLS 1.2 para encriptar as suas comunicações. O TLS 1.0 já não é suportado pela plataforma. Utilize o TLS 1.2 para comunicar com a Gestão de Atualização.

## <a name="connectivity-checks"></a>Verificações de conectividade

### <a name="registration-endpoint"></a>Ponto final de inscrição

Esta verificação determina se o agente pode comunicar corretamente com o serviço de agente.

As configurações de procuração e firewall devem permitir que o agente híbrido runbook worker comunique com o ponto final de registo. Para que uma lista de endereços e portas abra, consulte [o planeamento da Rede](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Ponto final de operações

Esta verificação determina se o agente pode comunicar corretamente com o Serviço de Dados de Tempo de Trabalho.

As configurações de procuração e firewall devem permitir que o agente híbrido do Trabalhador do Runbook comunique com o Serviço de Dados de Tempo de Trabalho. Para que uma lista de endereços e portas abra, consulte [o planeamento da Rede](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Verificações de saúde do serviço VM

### <a name="monitoring-agent-service-status"></a>Estado do serviço do agente de monitorização

Esta verificação determina se o agente Log Analytics para `healthservice` Windows () está a funcionar na máquina. Para saber mais sobre a resolução de problemas do serviço, consulte [o agente Log Analytics para windows não está a funcionar](hybrid-runbook-worker.md#mma-not-running).

Para reinstalar o agente Log Analytics para windows, consulte [instalar o agente para windows](../../azure-monitor/vm/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Eventos de serviço de agente de monitorização

Esta verificação determina se 4502 eventos aparecem no Registo do Gestor de Operações do Azure na máquina nas últimas 24 horas.

Para saber mais sobre este evento, consulte o [Evento 4502 no registo do Gestor de Operações](hybrid-runbook-worker.md#event-4502) para este evento.

## <a name="access-permissions-checks"></a>Verificações de permissões de acesso

> [!NOTE]
> O problema atualmente não encaminha o tráfego através de um servidor de procuração se um estiver configurado.

### <a name="crypto-folder-access"></a>Acesso à pasta Crypto

A verificação de acesso à pasta Crypto determina se a conta do sistema local tem acesso a C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Resolução de problemas offline

Pode utilizar o resolução de problemas num Trabalhador de Runbook Híbrido offline executando o script localmente. Obtenha o seguinte script do GitHub: [UM_Windows_Troubleshooter_Offline.ps1](https://github.com/Azure/updatemanagement/blob/main/UM_Windows_Troubleshooter_Offline.ps1). Para executar o script, você deve ter WMF 4.0 ou mais tarde instalado. Para descarregar a versão mais recente do PowerShell, consulte [instalar várias versões do PowerShell.](/powershell/scripting/install/installing-powershell)

A saída deste script parece ser o seguinte exemplo:

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

[Problemas com o trabalhador do runbook híbrido.](hybrid-runbook-worker.md)
