---
title: Enable Azure Monitor para um ambiente híbrido [ Microsoft Docs
description: Este artigo descreve como permite o Monitor Azure para VMs para um ambiente híbrido em nuvem que contém uma ou mais máquinas virtuais.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: 734f61c2e96002516e9e15af88d2c6b0fce00e98
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480747"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-environment"></a>Habilitar o Monitor Azure para VMs para um ambiente híbrido

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo explica como ativar o Monitor Azure para VMs para máquinas virtuais ou computadores físicos hospedados no seu datacenter ou outro ambiente na nuvem. No final deste processo, terá começado a monitorizar com sucesso as suas máquinas virtuais no seu ambiente e a saber se estão a ter algum desempenho ou problemas de disponibilidade.

Antes de começar, certifique-se de rever os [pré-requisitos](vminsights-enable-overview.md) e verificar se a sua subscrição e recursos cumprem os requisitos. Reveja os requisitos e métodos de implementação para o [log analytics linux e o agente Windows](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>O Monitor Azure para vMs Map Dependency agent não transmite quaisquer dados em si, e não requer alterações em firewalls ou portas. Os dados do Mapa são sempre transmitidos pelo agente Log Analytics ao serviço Azure Monitor, quer diretamente quer através do portal De Gestão de [Operações,](../../azure-monitor/platform/gateway.md) se as suas políticas de segurança de TI não permitirem que os computadores da rede se conectem à internet.

Os passos para completar esta tarefa são resumidos da seguinte forma:

1. Instale o agente Log Analytics para Windows ou Linux. Antes de instalar o agente, reveja o artigo de visão geral do [agente Log Analytics](../platform/log-analytics-agent.md) para compreender os pré-requisitos do sistema e os métodos de implementação.

2. Descarregue e instale o Monitor Azure para vMs Map Dependency agent para [Windows](https://aka.ms/dependencyagentwindows) ou [Linux](https://aka.ms/dependencyagentlinux).

3. Ativar a recolha de contadores de desempenho.

4. Implementar monitor azure para VMs.

>[!NOTE]
>As informações descritas neste artigo para a implementação do agente dependency também são aplicáveis à [solução do Mapa](service-map.md)de Serviços .  

## <a name="install-the-dependency-agent-on-windows"></a>Instale o agente dependency no Windows

Pode instalar manualmente o agente Dependency `InstallDependencyAgent-Windows.exe`nos computadores Windows executando . Se executar este ficheiro executável sem opções, inicia um assistente de configuração que pode seguir para instalar o agente interativamente.

>[!NOTE]
>*Os* privilégios do administrador são necessários para instalar ou desinstalar o agente.

A tabela seguinte destaca os parâmetros suportados pela configuração do agente a partir da linha de comando.

| Parâmetro | Descrição |
|:--|:--|
| /? | Devolve uma lista das opções da linha de comando. |
| /S | Realiza uma instalação silenciosa sem interação do utilizador. |

Por exemplo, para executar o `/?` programa de instalação com o parâmetro, introduza **InstalaçãoDependencyAgent-Windows.exe /?**

Os ficheiros para o agente de dependência do Windows estão instalados em *C:\Program Files\Microsoft Dependency Agent* por padrão. Se o agente dependency não começar depois de a configuração estar concluída, verifique os registos para obter informações detalhadas sobre erros. O diretório de registo é *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Instale o agente de dependência em Linux

O agente Dependency está instalado em servidores Linux a partir do *InstallDependencyAgent-Linux64.bin*, um script de concha com um binário auto-extraído. Pode executar o ficheiro `sh` utilizando ou adicionar permissões de execução ao próprio ficheiro.

>[!NOTE]
> Para instalar ou configurar o agente é preciso acesso à raiz.
>

| Parâmetro | Descrição |
|:--|:--|
| -ajuda | Obter uma lista das opções da linha de comandos. |
| -s | Realizar uma instalação automática sem que seja solicitada a interação do utilizador. |
| -check | Verifique as permissões e o sistema operativo, mas não instale o agente. |

Por exemplo, para executar o `-help` programa de instalação com o parâmetro, introduza **instalaçãoDependencyAgent-Linux64.bin -help**.

Instale o agente linux dependency `sh InstallDependencyAgent-Linux64.bin`como raiz executando o comando .

Se o agente dependency não começar, verifique os registos para obter informações detalhadas sobre erros. Nos agentes Linux, o diretório de registo é */var/opt/microsoft/dependency-agent/log*.

Os ficheiros do agente dependency são colocados nos seguintes diretórios:

| Ficheiros | Localização |
|:--|:--|
| Ficheiros principais | /opt/microsoft/dependency-agent |
| Ficheiros de registo | /var/opt/microsoft/dependency-agent/log |
| Ficheiros de configuração | /etc/opt/microsoft/dependency-agent/config |
| Ficheiros executáveis de serviço | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Ficheiros de armazenamento binários | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Exemplos de script de instalação

Para implementar facilmente o agente Dependency em muitos servidores ao mesmo tempo, o seguinte exemplo de script é fornecido para descarregar e instalar o agente Dependency em windows ou Linux.

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

Para implementar o agente dependency utilizando a Configuração do Estado Desejado (DSC), pode utilizar o módulo xPSDesiredStateConfiguration com o seguinte código de exemplo:

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

Se o espaço de trabalho log Analytics referenciado pela solução ainda não estiver configurado para recolher os contadores de desempenho exigidos pela solução, precisa de os ativar. Pode fazê-lo de duas maneiras:
* Manualmente, como descrito nas fontes de dados de [desempenho do Windows e do Linux no Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Ao descarregar e executar um script PowerShell que está disponível na [Galeria Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Implementar monitor azure para VMs

Este método inclui um modelo JSON que especifica a configuração para ativar os componentes da solução no seu espaço de trabalho Log Analytics.

Se não sabe como implementar recursos usando um modelo, consulte:
* [Implementar recursos com modelos do Resource Manager e do Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Implementar recursos com modelos de Gestor de Recursos e o Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Para utilizar o Azure CLI, é necessário instalar e utilizar o CLI localmente. Deve estar a executar a versão Azure CLI 2.0.27 ou mais tarde. Para identificar a `az --version`sua versão, corra. Para instalar ou atualizar o Azure CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

1. Guarde este ficheiro como *instalaçãoforvminsights.json* para uma pasta local.

1. Capture os valores para *WorkspaceName*, *ResourceGroupName*, e *WorkspaceLocation*. O valor para *WorkspaceName* é o nome do seu espaço de trabalho Log Analytics. O valor para *workspaceLocation* é a região onde o espaço de trabalho está definido.

1. Está pronto para implementar este modelo utilizando o seguinte comando PowerShell:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    A mudança de configuração pode demorar alguns minutos a terminar. Quando está terminado, uma mensagem mostra que é semelhante ao seguinte e inclui o resultado:

    ```powershell
    provisioningState       : Succeeded
    ```
   Depois de ter ativado a monitorização, pode demorar cerca de 10 minutos até poder ver o estado de saúde e as métricas do computador híbrido.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="vm-doesnt-appear-on-the-map"></a>VM não aparece no mapa

Se a instalação do seu agente dependency tiver sucesso, mas não vê o computador no mapa, diagnostice o problema seguindo estes passos.

1. O agente da Dependência está instalado com sucesso? Pode validar isto verificando se o serviço está instalado e em funcionamento.

    **Windows**: Procure o serviço chamado "Agente da Dependência da Microsoft".

    **Linux**: Procure o processo de execução "microsoft-dependency-agent".

2. Está no [nível de preços gratuitos do Log Analytics?](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) O plano Free permite até cinco computadores únicos. Quaisquer computadores subsequentes não aparecerão no mapa, mesmo que os cinco anteriores já não estejam a enviar dados.

3. O computador está a enviar dados de registo e perf para registos do Monitor Azure? Execute a seguinte consulta para o seu computador:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Devolveu um ou mais resultados? Os dados são recentes? Em caso afirmativo, o seu agente Log Analytics está a funcionar corretamente e a comunicar com o serviço. Caso contrário, verifique o agente no seu servidor: [Agente de Log Analytics para resolução](../platform/agent-windows-troubleshoot.md) de problemas do Windows ou agente Delog Analytics para resolução de [problemas do Linux](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>O computador aparece no mapa mas não tem processos

Se vir o seu servidor no mapa, mas não tiver dados de processo ou de ligação, isso indica que o agente Dependency está instalado e em funcionamento, mas o controlador de kernel não carregou.

Verifique o ficheiro C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log (Windows) ou /var/opt/microsoft/dependency-agent/log/service.log file (Linux). As últimas linhas do ficheiro devem indicar porque é que o núcleo não carregou. Por exemplo, o núcleo pode não ser suportado no Linux se atualizar o seu núcleo.


## <a name="next-steps"></a>Passos seguintes

Agora que a monitorização está ativada para as suas máquinas virtuais, esta informação está disponível para análise com o Monitor Azure para VMs.

- Para visualizar as dependências de aplicações descobertas, consulte o View Azure Monitor para o Mapa de [VMs](vminsights-maps.md).

- Para identificar estrangulamentos e utilização geral com o desempenho do seu VM, consulte o desempenho do [VM view Azure](vminsights-performance.md).
