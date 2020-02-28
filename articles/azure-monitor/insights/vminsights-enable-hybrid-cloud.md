---
title: Enable Azure Monitor (pré-visualização) para um ambiente híbrido  Microsoft Docs
description: Este artigo descreve como permite o Monitor Azure para VMs para um ambiente híbrido em nuvem que contém uma ou mais máquinas virtuais.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: bd44eebf8aceaf7fe32cf8cf1b1152db32acb344
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669629"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>Habilitar o Monitor Azure para VMs (pré-visualização) para um ambiente híbrido

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo explica como ativar o Monitor Azure para VMs (pré-visualização) para máquinas virtuais ou computadores físicos hospedados no seu datacenter ou outro ambiente na nuvem. No final deste processo, terá começado a monitorizar com sucesso as suas máquinas virtuais no seu ambiente e a saber se estão a ter algum desempenho ou problemas de disponibilidade.

Antes de começar, certifique-se de rever os [pré-requisitos](vminsights-enable-overview.md) e verificar se a sua subscrição e recursos cumprem os requisitos. Reveja os requisitos e métodos de implementação para o [log analytics linux e o agente Windows](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>O Azure Monitor para agente de dependência de mapa de VMs não transmitir todos os dados em si, e ele não requer alterações às firewalls ou portas. Os dados do Mapa são sempre transmitidos pelo agente Log Analytics ao serviço Azure Monitor, quer diretamente quer através do portal De Gestão de [Operações,](../../azure-monitor/platform/gateway.md) se as suas políticas de segurança de TI não permitirem que os computadores da rede se conectem à internet.

Os passos para completar esta tarefa são resumidos da seguinte forma:

1. Instale o agente Log Analytics para Windows ou Linux. Antes de instalar o agente, reveja o artigo de visão geral do [agente Log Analytics](../platform/log-analytics-agent.md) para compreender os pré-requisitos do sistema e os métodos de implementação.

2. Descarregue e instale o Monitor Azure para vMs Map Dependency agent para [Windows](https://aka.ms/dependencyagentwindows) ou [Linux](https://aka.ms/dependencyagentlinux).

3. Ative a recolha de contadores de desempenho.

4. Implemente o Azure Monitor para as VMs.

>[!NOTE]
>As informações descritas neste artigo para a implementação do agente dependency também são aplicáveis à [solução do Mapa](service-map.md)de Serviços .  

## <a name="install-the-dependency-agent-on-windows"></a>Instale o agente de dependência no Windows

Pode instalar manualmente o agente Dependency nos computadores Windows executando `InstallDependencyAgent-Windows.exe`. Se executar esse arquivo executável sem quaisquer opções, inicia um Assistente de configuração que pode seguir para instalar o agente de forma interativa.

>[!NOTE]
>*Os* privilégios do administrador são necessários para instalar ou desinstalar o agente.

A tabela seguinte realça os parâmetros que são suportados pelo programa de configuração para o agente a partir da linha de comandos.

| Parâmetro | Descrição |
|:--|:--|
| /? | Devolve uma lista das opções da linha de comandos. |
| /S | Executa uma instalação silenciosa, sem interação do utilizador. |

Por exemplo, para executar o programa de instalação com o parâmetro `/?`, introduza **InstalaçãoDependencyAgent-Windows.exe /?**

Os ficheiros para o agente de dependência do Windows estão instalados em *C:\Program Files\Microsoft Dependency Agent* por padrão. Se o agente dependency não começar depois de a configuração estar concluída, verifique os registos para obter informações detalhadas sobre erros. O diretório de registo é *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Instalar o agente de dependência no Linux

O agente Dependency está instalado em servidores Linux a partir do *InstallDependencyAgent-Linux64.bin*, um script de concha com um binário auto-extraído. Pode executar o ficheiro utilizando `sh` ou adicionar permissões de execução ao próprio ficheiro.

>[!NOTE]
> Para instalar ou configurar o agente é preciso acesso à raiz.
>

| Parâmetro | Descrição |
|:--|:--|
| -Ajuda | Obter uma lista das opções da linha de comandos. |
| -s | Realizar uma instalação automática sem que seja solicitada a interação do utilizador. |
| -Verifique | Verifique as permissões e o sistema operativo, mas não instale o agente. |

Por exemplo, para executar o programa de instalação com o parâmetro `-help`, introduza **a InstalaçãoDependencagente-Linux64.bin -help**.

Instale o agente linux dependency como raiz executando o comando `sh InstallDependencyAgent-Linux64.bin`.

Se o agente de dependência não iniciar, verifique os registos para obter informações de erro detalhadas. Nos agentes Linux, o diretório de registo é */var/opt/microsoft/dependency-agent/log*.

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

Se a área de trabalho do Log Analytics que é referenciada pela solução já não está configurada para recolher os contadores de desempenho necessários para a solução, terá de ativá-las. Pode fazê-lo de duas maneiras:
* Manualmente, como descrito nas fontes de dados de [desempenho do Windows e do Linux no Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Ao descarregar e executar um script PowerShell que está disponível na [Galeria Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Implementar o Azure Monitor para VMs

Esse método inclui um modelo JSON que especifica a configuração para permitir que os componentes da solução na sua área de trabalho do Log Analytics.

Se não sabe como implementar recursos usando um modelo, consulte:
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Implementar recursos com modelos de Gestor de Recursos e o Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Para utilizar o Azure CLI, é necessário instalar e utilizar o CLI localmente. Tem de executar a CLI do Azure versão 2.0.27 ou posterior. Para identificar a sua versão, execute `az --version`. Para instalar ou atualizar o Azure CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

1. Guarde este ficheiro como *instalaçãoforvminsights.json* para uma pasta local.

1. Capture os valores para *WorkspaceName*, *ResourceGroupName*, e *WorkspaceLocation*. O valor para *WorkspaceName* é o nome do seu espaço de trabalho Log Analytics. O valor para *workspaceLocation* é a região onde o espaço de trabalho está definido.

1. Está pronto para implementar este modelo, utilizando o seguinte comando do PowerShell:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    A mudança de configuração pode demorar alguns minutos a terminar. Quando está terminado, uma mensagem mostra que é semelhante ao seguinte e inclui o resultado:

    ```powershell
    provisioningState       : Succeeded
    ```
   Depois de ativar a monitorização, poderá demorar cerca de 10 minutos antes de poder ver o estado de funcionamento e métricas para o computador híbrida.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="vm-doesnt-appear-on-the-map"></a>VM não aparece no mapa

Se a instalação do seu agente dependency tiver sucesso, mas não vê o computador no mapa, diagnostice o problema seguindo estes passos.

1. O agente de dependência é instalado com êxito? Pode confirmar isto, verificando se o serviço está instalado e em execução.

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

Verifique o ficheiro de C:\Program Files\Microsoft dependência Agent\logs\wrapper.log (Windows) ou o ficheiro de /var/opt/microsoft/dependency-agent/log/service.log (Linux). As última linhas do ficheiro devem indicar por que não carregou o kernel. Por exemplo, o kernel poderá não ser suportado no Linux, se atualizou o kernel.


## <a name="next-steps"></a>Passos seguintes

Agora que a monitorização está ativada para as suas máquinas virtuais, esta informação está disponível para análise com o Monitor Azure para VMs.

- Para visualizar as dependências de aplicações descobertas, consulte o View Azure Monitor para o Mapa de [VMs](vminsights-maps.md).

- Para identificar estrangulamentos e utilização geral com o desempenho do seu VM, consulte o desempenho do [VM view Azure](vminsights-performance.md).
