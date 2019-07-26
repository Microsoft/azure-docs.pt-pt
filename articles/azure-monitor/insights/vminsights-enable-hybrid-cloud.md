---
title: Habilitar Azure Monitor (visualização) para um ambiente híbrido | Microsoft Docs
description: Este artigo descreve como habilitar Azure Monitor para VMs para um ambiente de nuvem híbrida que contém uma ou mais máquinas virtuais.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: e8241069a8671919b70dfbe44fe28c99a05358c5
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489740"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>Habilitar Azure Monitor para VMs (versão prévia) para um ambiente híbrido

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo explica como habilitar a Azure Monitor para VMs (versão prévia) para máquinas virtuais ou computadores físicos hospedados em seu datacenter ou em outro ambiente de nuvem. No final desse processo, você terá iniciado com êxito o monitoramento de suas máquinas virtuais em seu ambiente e aprenderá se elas estão enfrentando problemas de desempenho ou disponibilidade. 

Antes de começar, certifique-se de revisar os [pré-requisitos](vminsights-enable-overview.md) e verificar se sua assinatura e seus recursos atendem aos requisitos. Reveja os requisitos e métodos de implementação para o [agente Linux do Log Analytics e o Windows](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>O Azure Monitor para agente de dependência de mapa de VMs não transmitir todos os dados em si, e ele não requer alterações às firewalls ou portas. Os dados do mapa são sempre transmitidos pelo agente de Log Analytics para o serviço Azure Monitor, diretamente ou por meio do [gateway do Operations Management Suite](../../azure-monitor/platform/gateway.md) se as suas políticas de segurança de ti não permitirem que computadores na rede se conectem à Internet.

As etapas para concluir essa tarefa são resumidas da seguinte maneira:

1. Instale o agente Log Analytics para Windows ou Linux. Antes de instalar o agente, examine o artigo [visão geral do agente de log Analytics](../platform/log-analytics-agent.md) para entender os pré-requisitos do sistema e os métodos de implantação.

2. Transfira e instale o Azure Monitor para agente de dependência de mapa de VMs para [Windows](https://aka.ms/dependencyagentwindows) ou [Linux](https://aka.ms/dependencyagentlinux).

3. Ative a recolha de contadores de desempenho.

4. Implemente o Azure Monitor para as VMs.

## <a name="install-the-dependency-agent-on-windows"></a>Instale o agente de dependência no Windows

Pode instalar o agente de dependência manualmente em computadores Windows executando `InstallDependencyAgent-Windows.exe`. Se executar esse arquivo executável sem quaisquer opções, inicia um Assistente de configuração que pode seguir para instalar o agente de forma interativa.

>[!NOTE]
>*Administrador* privilégios são necessários para instalar ou desinstalar o agente.

A tabela seguinte realça os parâmetros que são suportados pelo programa de configuração para o agente a partir da linha de comandos.

| Parâmetro | Descrição |
|:--|:--|
| /? | Devolve uma lista das opções da linha de comandos. |
| /S | Executa uma instalação silenciosa, sem interação do utilizador. |

Por exemplo, para executar o programa de instalação com `/?` o parâmetro, digite **installdependencyagent-Windows. exe/?** .

Ficheiros para o agente de dependência do Windows estão instalados no *agente de dependência do C:\Program Files\Microsoft* por predefinição. Se o agente de dependência não for iniciado após a conclusão da instalação, verifique os logs para obter informações detalhadas sobre o erro. É o diretório de registo *%Programfiles%\Microsoft dependência Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Instalar o agente de dependência no Linux

O agente de dependência é instalado em servidores Linux a partir de *InstallDependencyAgent Linux64.bin*, um script de shell com um binário auto-extraível. Pode executar o ficheiro utilizando `sh` ou adicionar permissões para o próprio ficheiro de execução.

>[!NOTE]
> Para instalar ou configurar o agente é preciso acesso à raiz.
>

| Parâmetro | Descrição |
|:--|:--|
| -Ajuda | Obter uma lista das opções da linha de comandos. |
| -s | Realizar uma instalação automática sem que seja solicitada a interação do utilizador. |
| -Verifique | Verifique as permissões e o sistema operativo, mas não instale o agente. |

Por exemplo, para executar o programa de instalação com `-help` o parâmetro, insira **installdependencyagent-Linux64. bin-Help**.

Instale o agente de dependência do Linux como raiz executando o `sh InstallDependencyAgent-Linux64.bin`comando.

Se o agente de dependência não iniciar, verifique os registos para obter informações de erro detalhadas. Em agentes do Linux, é o diretório de registo */var/opt/microsoft/dependency-agent/log*.

Ficheiros para o agente de dependência são colocados nos seguintes diretórios:

| Ficheiros | Localização |
|:--|:--|
| Ficheiros principais | /opt/microsoft/dependency-agent |
| Ficheiros de registo | /var/opt/microsoft/dependency-agent/log |
| Ficheiros de configuração | /etc/opt/microsoft/dependency-agent/config |
| Ficheiros executáveis de serviço | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Ficheiros de armazenamento binários | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Exemplos de script de instalação

Para implementar facilmente o agente de dependência em vários servidores ao mesmo tempo, o exemplo de script a seguir é fornecido para transferir e instalar o agente de dependência no Windows ou Linux.

### <a name="powershell-script-for-windows"></a>Script do PowerShell para Windows

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Script de shell para Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Configuração do Estado Pretendido

Para implementar o agente de dependência com a Desired State Configuration (DSC), pode utilizar o módulo de xPSDesiredStateConfiguration com o código de exemplo seguinte:

```powershell
configuration ServiceMap {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage 
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="enable-performance-counters"></a>Ativar os contadores de desempenho

Se a área de trabalho do Log Analytics que é referenciada pela solução já não está configurada para recolher os contadores de desempenho necessários para a solução, terá de ativá-las. Você pode fazer isso de uma das duas maneiras:
* Manualmente, conforme descrito em [Windows e Linux origens de dados de desempenho do Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Baixando e executando um script do PowerShell que está disponível na [Galeria de Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Implementar o Azure Monitor para VMs

Esse método inclui um modelo JSON que especifica a configuração para permitir que os componentes da solução na sua área de trabalho do Log Analytics.

Se você não souber como implantar recursos usando um modelo, consulte:
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Implementar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Para usar o CLI do Azure, primeiro você precisa instalar e usar a CLI localmente. Tem de executar a CLI do Azure versão 2.0.27 ou posterior. Para identificar a versão, execute `az --version`. Para instalar ou atualizar o CLI do Azure, consulte [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="create-and-execute-a-template"></a>Criar e executar um modelo

1. Copie e cole a seguinte sintaxe JSON no seu ficheiro:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Guarde este ficheiro como *installsolutionsforvminsights.json* para uma pasta local.

1. Capture os valores para *WorkspaceName*, *ResourceGroupName*e *WorkspaceLocation*. O valor de *WorkspaceName* é o nome do seu espaço de trabalho do log Analytics. O valor para *WorkspaceLocation* é a região a área de trabalho está definida.

1. Está pronto para implementar este modelo, utilizando o seguinte comando do PowerShell:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    A alteração de configuração pode levar alguns minutos para ser concluída. Quando ele for concluído, uma mensagem será exibida semelhante à seguinte e inclui o resultado:

    ```powershell
    provisioningState       : Succeeded
    ```
   Depois de ativar a monitorização, poderá demorar cerca de 10 minutos antes de poder ver o estado de funcionamento e métricas para o computador híbrida.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="vm-doesnt-appear-on-the-map"></a>A VM não aparece no mapa

Se a instalação do agente de dependência for bem-sucedida, mas você não vir seu computador no mapa, diagnostique o problema seguindo estas etapas.

1. O agente de dependência é instalado com êxito? Pode confirmar isto, verificando se o serviço está instalado e em execução.

    **Windows**: Procure o serviço chamado "Microsoft Dependency Agent". 

    **Linux**: Procure o processo em execução "Microsoft-Dependency-Agent".

2. Você está no [tipo de preço gratuito do log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)? O plano gratuito permite até cinco computadores exclusivos. Todos os computadores subsequentes não aparecerão no mapa, mesmo que os cinco anteriores não estejam mais enviando dados.

3. O computador está enviando dados de log e desempenho para Azure Monitor logs? Execute a seguinte consulta para seu computador: 

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Retornou um ou mais resultados? Os dados são recentes? Nesse caso, seu agente de Log Analytics está operando corretamente e se comunicando com o serviço. Caso contrário, verifique o agente em seu servidor: Solução [de problemas do log Analytics Agent para Windows](../platform/agent-windows-troubleshoot.md) ou solução [de problemas do agente log Analytics para Linux](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>O computador aparece no mapa, mas não tem nenhum processo

Se você vir o servidor no mapa, mas ele não tiver dados de processo ou de conexão, isso indicará que o agente de dependência está instalado e em execução, mas o driver do kernel não foi carregado. 

Verifique o ficheiro de C:\Program Files\Microsoft dependência Agent\logs\wrapper.log (Windows) ou o ficheiro de /var/opt/microsoft/dependency-agent/log/service.log (Linux). As última linhas do ficheiro devem indicar por que não carregou o kernel. Por exemplo, o kernel poderá não ser suportado no Linux, se atualizou o kernel.


## <a name="next-steps"></a>Passos Seguintes

Agora que o monitoramento está habilitado para suas máquinas virtuais, essas informações estão disponíveis para análise com Azure Monitor para VMs.
 
- Para saber como usar o recurso de integridade, consulte [exibir Azure monitor para VMs integridade](vminsights-health.md).
- Para ver dependências de aplicações detetadas, consulte [vista do Azure Monitor para o mapa de VMs](vminsights-maps.md).
- Para identificar afunilamentos e a utilização geral com o desempenho da VM, consulte [Exibir o desempenho da VM do Azure](vminsights-performance.md).
- Para ver dependências de aplicações detetadas, consulte [vista do Azure Monitor para o mapa de VMs](vminsights-maps.md).