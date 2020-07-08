---
title: Ativar o Azure Monitor para um ambiente híbrido Microsoft Docs
description: Este artigo descreve como permite o Azure Monitor para VMs para um ambiente híbrido em nuvem que contém uma ou mais máquinas virtuais.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: 734f61c2e96002516e9e15af88d2c6b0fce00e98
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79480747"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-environment"></a>Ativar o Monitor Azure para VMs para um ambiente híbrido

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo explica como ativar o Azure Monitor para VMs para máquinas virtuais ou computadores físicos alojados no seu datacenter ou noutro ambiente em nuvem. No final deste processo, terá começado com sucesso a monitorizar as suas máquinas virtuais no seu ambiente e a saber se estão a ter algum problema de desempenho ou disponibilidade.

Antes de começar, certifique-se de rever os [pré-requisitos](vminsights-enable-overview.md) e verificar se a sua subscrição e recursos cumprem os requisitos. Reveja os requisitos e métodos de implementação do [Log Analytics Linux e do windows agent](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>O Azure Monitor para o agente VMs Map Dependency não transmite quaisquer dados em si, e não requer alterações em firewalls ou portas. Os dados do Mapa são sempre transmitidos pelo agente Log Analytics ao serviço Azure Monitor, quer diretamente quer através do [gateway da Suite de Gestão de Operações,](../../azure-monitor/platform/gateway.md) se as suas políticas de segurança de TI não permitirem que os computadores da rede se conectem à internet.

Os passos para completar esta tarefa são resumidos da seguinte forma:

1. Instale o agente Log Analytics para Windows ou Linux. Antes de instalar o agente, reveja o artigo [de visão geral](../platform/log-analytics-agent.md) do agente Do Log Analytics para compreender os pré-requisitos do sistema e os métodos de implantação.

2. Descarregue e instale o Azure Monitor para o agente VMs Map Dependency para [Windows](https://aka.ms/dependencyagentwindows) ou [Linux](https://aka.ms/dependencyagentlinux).

3. Ativar a recolha de contadores de desempenho.

4. Implementar monitor de Azure para VMs.

>[!NOTE]
>As informações descritas neste artigo para a implantação do agente Desadependimento também são aplicáveis à [solução Mapa de Serviço.](service-map.md)  

## <a name="install-the-dependency-agent-on-windows"></a>Instale o agente de dependência no Windows

Pode instalar manualmente o agente Dependency nos computadores Windows, executando `InstallDependencyAgent-Windows.exe` . Se executar este ficheiro executável sem opções, inicia um assistente de configuração que pode seguir para instalar o agente interativamente.

>[!NOTE]
>*Os* privilégios do administrador são necessários para instalar ou desinstalar o agente.

A tabela seguinte realça os parâmetros suportados pela configuração do agente a partir da linha de comando.

| Parâmetro | Descrição |
|:--|:--|
| /? | Devolve uma lista das opções de linha de comando. |
| /S | Executa uma instalação silenciosa sem interação do utilizador. |

Por exemplo, para executar o programa de instalação com o `/?` parâmetro, **introduzaInstallDependencyAgent-Windows.exe /?**. .

Os ficheiros do agente de dependência do Windows são instalados em *C:\Program Files\Microsoft Dependency Agent* por padrão. Se o agente Desasusiting inificado após a configuração terminar, verifique se os registos são informativo sobre erros. O diretório de registos é *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Instale o agente de dependência no Linux

O agente Dependency é instalado em servidores Linux da *InstallDependencyAgent-Linux64.bin*, um script de concha com um binário auto-extrator. Pode executar o ficheiro utilizando `sh` ou adicionar permissões de execução ao próprio ficheiro.

>[!NOTE]
> Para instalar ou configurar o agente é preciso acesso à raiz.
>

| Parâmetro | Descrição |
|:--|:--|
| - ajudar | Obter uma lista das opções da linha de comandos. |
| -s | Realizar uma instalação automática sem que seja solicitada a interação do utilizador. |
| --verificar | Verifique as permissões e o sistema operativo, mas não instale o agente. |

Por exemplo, para executar o programa de instalação com o `-help` parâmetro, **insira installDependencyAgent-Linux64.bin -help**.

Instale o agente de dependência Linux como raiz executando o comando `sh InstallDependencyAgent-Linux64.bin` .

Se o agente de dependência não arrancar, verifique se os registos são informativo de erro. Nos agentes Linux, o diretório de registos é */var/opt/microsoft/dependency-agent/log*.

Os ficheiros do agente de dependência são colocados nos seguintes diretórios:

| Ficheiros | Localização |
|:--|:--|
| Ficheiros principais | /opt/microsoft/dependency-agent |
| Ficheiros de registo | /var/opt/microsoft/dependency-agent/log |
| Ficheiros de configuração | /etc/opt/microsoft/dependency-agent/config |
| Ficheiros executáveis de serviço | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Ficheiros de armazenamento binários | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Exemplos de script de instalação

Para implementar facilmente o agente Dependency em muitos servidores ao mesmo tempo, é fornecido o seguinte exemplo de script para descarregar e instalar o agente Dependency no Windows ou linux.

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

Para implementar o agente de dependência utilizando a Configuração do Estado Desejado (DSC), pode utilizar o módulo xPSDesiredStateConfiguration com o seguinte código de exemplo:

```powershell
configuration VMInsights {

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

## <a name="enable-performance-counters"></a>Ativar contadores de desempenho

Se o espaço de trabalho Log Analytics que é referenciado pela solução ainda não estiver configurado para recolher os contadores de desempenho necessários pela solução, é necessário ativá-los. Pode fazê-lo de duas maneiras:
* Manualmente, conforme descrito nas [fontes de dados de desempenho do Windows e Do Linux no Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Ao descarregar e executar um script PowerShell que está disponível na [Galeria Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Implementar monitor de Azure para VMs

Este método inclui um modelo JSON que especifica a configuração para ativar os componentes da solução no seu espaço de trabalho Log Analytics.

Se não souber como implementar recursos utilizando um modelo, consulte:
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Implementar recursos com modelos de Gestor de Recursos e o CLI Azure](../../azure-resource-manager/templates/deploy-cli.md)

Para utilizar o Azure CLI, primeiro tem de instalar e utilizar o CLI localmente. Deve estar a executar a versão Azure CLI 2.0.27 ou posterior. Para identificar a sua versão, corra `az --version` . Para instalar ou atualizar o Azure CLI, consulte [instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

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
                        "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'VMInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Guarde este ficheiro à medida *queinstallsolutionsforvminsights.jsnuma* pasta local.

1. Capture os valores para *WorkspaceName,* *ResourceGroupName*e *WorkspaceLocation*. O valor para *WorkspaceName* é o nome do seu espaço de trabalho Log Analytics. O valor para *workspaceLocation* é a região em que o espaço de trabalho é definido.

1. Está pronto para implementar este modelo utilizando o seguinte comando PowerShell:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    A alteração de configuração pode demorar alguns minutos a terminar. Quando termina, uma mensagem mostra que é semelhante ao seguinte e inclui o resultado:

    ```powershell
    provisioningState       : Succeeded
    ```
   Depois de ter ativado a monitorização, pode demorar cerca de 10 minutos até poder ver o estado de saúde e as métricas para o computador híbrido.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="vm-doesnt-appear-on-the-map"></a>VM não aparece no mapa

Se a instalação do seu agente de dependência tiver sido bem sucedida, mas não vir o computador no mapa, diagnostice o problema seguindo estes passos.

1. O Dependency Agent foi instalado com sucesso? Pode verificar esta situação ao confirmar que o serviço está instalado e em execução.

    **Windows**: Procure o serviço chamado "Microsoft Dependency agent".

    **Linux**: Procure o processo de execução "microsoft-dependency-agent".

2. Está no [nível de preços gratuitos do Log Analytics?](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) O plano Free permite até cinco computadores únicos. Quaisquer computadores subsequentes não aparecerão no mapa, mesmo que os cinco anteriores já não estejam a enviar dados.

3. O computador está a enviar registos e dados perf para registos do Monitor Azure? Execute a seguinte consulta para o seu computador:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Devolveu um ou mais resultados? Os dados são recentes? Em caso afirmativo, o seu agente Log Analytics está a funcionar corretamente e a comunicar com o serviço. Caso contrário, verifique o agente no seu servidor: [Registar o agente Analytics para a resolução de problemas](../platform/agent-windows-troubleshoot.md) do Windows ou para o agente [Desapeito de Registo para a resolução de problemas do Linux](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>Computador aparece no mapa mas não tem processos

Se vir o seu servidor no mapa, mas não tiver dados de processo ou ligação, isso indica que o agente Dependency está instalado e a funcionar, mas o controlador de kernel não carregou.

Verifique o ficheiro C:\Programas\Microsoft Dependency Agent\logs\wrapper.log (Windows) ou o ficheiro /var/opt/microsoft/dependency-agent/log/service.log (Linux). As últimas linhas do ficheiro devem indicar o motivo pelo qual kernel não carregou. Por exemplo, o kernel poderá não ser suportado pelo Linux se tiver atualizado o kernel.


## <a name="next-steps"></a>Próximos passos

Agora que a monitorização está ativada para as suas máquinas virtuais, esta informação está disponível para análise com o Azure Monitor para VMs.

- Para ver as dependências de aplicações descobertas, consulte [o Monitor do Azure para o Mapa de VMs](vminsights-maps.md).

- Para identificar estrangulamentos e utilização global com o desempenho do seu VM, consulte o desempenho do [See Azure VM](vminsights-performance.md).
