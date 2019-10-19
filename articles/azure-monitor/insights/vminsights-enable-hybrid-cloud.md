---
title: Habilitar Azure Monitor (visualização) para um ambiente híbrido | Microsoft Docs
description: Este artigo descreve como habilitar Azure Monitor para VMs para um ambiente de nuvem híbrida que contém uma ou mais máquinas virtuais.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: e9e5504125920cedaf383f8fa4299a4b1b1d60ed
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553864"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>Habilitar Azure Monitor para VMs (versão prévia) para um ambiente híbrido

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo explica como habilitar a Azure Monitor para VMs (versão prévia) para máquinas virtuais ou computadores físicos hospedados em seu datacenter ou em outro ambiente de nuvem. No final desse processo, você terá iniciado com êxito o monitoramento de suas máquinas virtuais em seu ambiente e aprenderá se elas estão enfrentando problemas de desempenho ou disponibilidade. 

Antes de começar, certifique-se de revisar os [pré-requisitos](vminsights-enable-overview.md) e verificar se sua assinatura e seus recursos atendem aos requisitos. Examine os requisitos e métodos de implantação para o [log Analytics agente do Windows e do Linux](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>O agente de dependência do mapa de Azure Monitor para VMs não transmite nenhum dado e não requer nenhuma alteração em firewalls ou portas. Os dados do mapa são sempre transmitidos pelo agente de Log Analytics para o serviço Azure Monitor, diretamente ou por meio do [gateway do Operations Management Suite](../../azure-monitor/platform/gateway.md) se as suas políticas de segurança de ti não permitirem que computadores na rede se conectem à Internet.

As etapas para concluir essa tarefa são resumidas da seguinte maneira:

1. Instale o agente Log Analytics para Windows ou Linux. Antes de instalar o agente, examine o artigo [visão geral do agente de log Analytics](../platform/log-analytics-agent.md) para entender os pré-requisitos do sistema e os métodos de implantação.

2. Baixe e instale o agente de dependência do mapa de Azure Monitor para VMs para [Windows](https://aka.ms/dependencyagentwindows) ou [Linux](https://aka.ms/dependencyagentlinux).

3. Habilite a coleção de contadores de desempenho.

4. Implantar Azure Monitor para VMs.

>[!NOTE]
>As informações descritas neste artigo para a implantação do Dependency Agent também são aplicáveis à [solução de mapa do serviço](service-map.md).  

## <a name="install-the-dependency-agent-on-windows"></a>Instalar o Dependency Agent no Windows

Você pode instalar o agente de dependência manualmente em computadores Windows executando `InstallDependencyAgent-Windows.exe`. Se você executar esse arquivo executável sem nenhuma opção, ele iniciará um assistente de instalação que você pode seguir para instalar o agente interativamente.

>[!NOTE]
>São necessários privilégios de *administrador* para instalar ou desinstalar o agente.

A tabela a seguir realça os parâmetros com suporte na instalação do agente da linha de comando.

| Parâmetro | Descrição |
|:--|:--|
| /? | Retorna uma lista das opções de linha de comando. |
| /S | Executa uma instalação silenciosa sem interação do usuário. |

Por exemplo, para executar o programa de instalação com o parâmetro `/?`, digite **installdependencyagent-Windows. exe/?** .

Os arquivos para o agente de dependência do Windows são instalados em *C:\Program Files\Microsoft Dependency Agent* por padrão. Se o agente de dependência não for iniciado após a conclusão da instalação, verifique os logs para obter informações detalhadas sobre o erro. O diretório de log é *%ProgramFiles%\Microsoft Dependency Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Instalar o Dependency Agent no Linux

O Dependency Agent é instalado em servidores Linux de *installdependencyagent-Linux64. bin*, um script de shell com um binário de extração automática. Você pode executar o arquivo usando `sh` ou adicionar permissões de execução ao próprio arquivo.

>[!NOTE]
> Para instalar ou configurar o agente é preciso acesso à raiz.
>

| Parâmetro | Descrição |
|:--|:--|
| -ajuda | Obter uma lista das opções da linha de comandos. |
| -s | Realizar uma instalação automática sem que seja solicitada a interação do utilizador. |
| --verificar | Verifique as permissões e o sistema operacional, mas não instale o agente. |

Por exemplo, para executar o programa de instalação com o parâmetro `-help`, insira **installdependencyagent-Linux64. bin-Help**.

Instale o agente de dependência do Linux como raiz executando o comando `sh InstallDependencyAgent-Linux64.bin`.

Se o agente de dependência não for iniciado, verifique os logs para obter informações detalhadas sobre o erro. Em agentes do Linux, o diretório de log é */var/opt/Microsoft/Dependency-Agent/log*.

Os arquivos para o agente de dependência são colocados nos seguintes diretórios:

| Ficheiros | Localização |
|:--|:--|
| Ficheiros principais | /opt/microsoft/dependency-agent |
| Ficheiros de registo | /var/opt/microsoft/dependency-agent/log |
| Ficheiros de configuração | /etc/opt/microsoft/dependency-agent/config |
| Ficheiros executáveis de serviço | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Ficheiros de armazenamento binários | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Exemplos de script de instalação

Para implantar facilmente o agente de dependência em vários servidores de uma vez, o exemplo de script a seguir é fornecido para baixar e instalar o agente de dependência no Windows ou no Linux.

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

Para implantar o Dependency Agent usando a DSC (configuração de estado desejado), você pode usar o módulo xPSDesiredStateConfiguration com o seguinte código de exemplo:

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

## <a name="enable-performance-counters"></a>Habilitar contadores de desempenho

Se o espaço de trabalho Log Analytics referenciado pela solução ainda não estiver configurado para coletar os contadores de desempenho exigidos pela solução, você precisará habilitá-los. Você pode fazer isso de uma das duas maneiras:
* Manualmente, conforme descrito em [fontes de dados de desempenho do Windows e do Linux no log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Baixando e executando um script do PowerShell que está disponível na [Galeria de Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Implantar Azure Monitor para VMs

Esse método inclui um modelo JSON que especifica a configuração para habilitar os componentes da solução em seu espaço de trabalho Log Analytics.

Se você não souber como implantar recursos usando um modelo, consulte:
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Implantar recursos com modelos do Resource Manager e o CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Para usar o CLI do Azure, primeiro você precisa instalar e usar a CLI localmente. Você deve estar executando o CLI do Azure versão 2.0.27 ou posterior. Para identificar sua versão, execute `az --version`. Para instalar ou atualizar o CLI do Azure, consulte [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

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
                    }
                ]
            }
        ]
    }
    ```

1. Salve esse arquivo como *installsolutionsforvminsights. JSON* em uma pasta local.

1. Capture os valores para *WorkspaceName*, *ResourceGroupName*e *WorkspaceLocation*. O valor de *WorkspaceName* é o nome do seu espaço de trabalho do log Analytics. O valor de *WorkspaceLocation* é a região em que o espaço de trabalho é definido.

1. Você está pronto para implantar este modelo usando o seguinte comando do PowerShell:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    A alteração de configuração pode levar alguns minutos para ser concluída. Quando ele for concluído, uma mensagem será exibida semelhante à seguinte e inclui o resultado:

    ```powershell
    provisioningState       : Succeeded
    ```
   Depois de habilitar o monitoramento, pode levar cerca de 10 minutos para que você possa exibir o estado de integridade e as métricas para o computador híbrido.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="vm-doesnt-appear-on-the-map"></a>A VM não aparece no mapa

Se a instalação do agente de dependência for bem-sucedida, mas você não vir seu computador no mapa, diagnostique o problema seguindo estas etapas.

1. O agente de dependência foi instalado com êxito? Você pode validar isso verificando se o serviço está instalado e em execução.

    **Windows**: Procure o serviço chamado "Microsoft Dependency Agent". 

    **Linux**: Procure o processo em execução "Microsoft-Dependency-Agent".

2. Você está no [tipo de preço gratuito do log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)? O plano gratuito permite até cinco computadores exclusivos. Todos os computadores subsequentes não aparecerão no mapa, mesmo que os cinco anteriores não estejam mais enviando dados.

3. O computador está enviando dados de log e desempenho para Azure Monitor logs? Execute a seguinte consulta para seu computador: 

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Retornou um ou mais resultados? Os dados são recentes? Nesse caso, seu agente de Log Analytics está operando corretamente e se comunicando com o serviço. Caso contrário, verifique o agente em seu servidor: [log Analytics agente para solução de problemas do Windows](../platform/agent-windows-troubleshoot.md) ou solução [de problemas do agente log Analytics para Linux](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>O computador aparece no mapa, mas não tem nenhum processo

Se você vir o servidor no mapa, mas ele não tiver dados de processo ou de conexão, isso indicará que o agente de dependência está instalado e em execução, mas o driver do kernel não foi carregado. 

Verifique o arquivo C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log (Windows) ou/var/opt/Microsoft/Dependency-Agent/log/Service.log file (Linux). As últimas linhas do arquivo devem indicar por que o kernel não foi carregado. Por exemplo, o kernel pode não ter suporte no Linux se você tiver atualizado o kernel.


## <a name="next-steps"></a>Passos seguintes

Agora que o monitoramento está habilitado para suas máquinas virtuais, essas informações estão disponíveis para análise com Azure Monitor para VMs.
 
- Para exibir dependências de aplicativo descobertas, consulte [exibir mapa de Azure monitor para VMs](vminsights-maps.md).

- Para identificar afunilamentos e a utilização geral com o desempenho da VM, consulte [Exibir o desempenho da VM do Azure](vminsights-performance.md).
