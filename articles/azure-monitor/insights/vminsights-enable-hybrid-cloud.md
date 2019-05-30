---
title: Ativar o Azure Monitor (pré-visualização) para um ambiente híbrido | Documentos da Microsoft
description: Este artigo descreve como ativar Azure Monitor para as VMs para um ambiente de cloud híbrida que contém uma ou mais máquinas virtuais.
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
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: 6b8870f0a6f14536fdf3a1ff675f2fbe3ce8aeec
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524194"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>Ativar o Azure Monitor para VMs (pré-visualização) para um ambiente híbrido

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo explica como ativar o Azure Monitor para VMs (pré-visualização) para máquinas virtuais ou computadores físicos alojados no seu datacenter ou outro ambiente de cloud. No final deste processo, que será com êxito começaram a suas máquinas virtuais no seu ambiente de monitorização e saiba se ocorrerem problemas de desempenho ou disponibilidade. 

Antes de obter iniciado, não se esqueça de rever o [pré-requisitos](vminsights-enable-overview.md) e certifique-se de que a sua subscrição e os recursos de cumprir os requisitos. Reveja os requisitos e métodos de implementação para o [agente Linux do Log Analytics e o Windows](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>O Azure Monitor para agente de dependência de mapa de VMs não transmitir todos os dados em si, e ele não requer alterações às firewalls ou portas. Os dados de mapa sempre são transmitidos pelo agente do Log Analytics para o serviço Azure Monitor, diretamente ou através da [Gateway de OMS](../../azure-monitor/platform/gateway.md) se as políticas de segurança de TI não permitir que os computadores na rede para ligar à internet.

As tarefas para concluir esta tarefa são resumidas da seguinte forma:

1. Instale o agente do Log Analytics para Windows ou Linux.

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

Por exemplo, para executar o programa de instalação com o `/?` parâmetro, escreva **InstallDependencyAgent Windows.exe /?** .

Ficheiros para o agente de dependência do Windows estão instalados no *agente de dependência do C:\Program Files\Microsoft* por predefinição. Se o agente de dependência não conseguir iniciar após a configuração estiver concluída, verifique os registos para obter informações de erro detalhadas. É o diretório de registo *%Programfiles%\Microsoft dependência Agent\logs*.

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

Por exemplo, para executar o programa de instalação com o `-help` parâmetro, escreva **InstallDependencyAgent Linux64.bin-ajudar**.

Instalar o agente de dependência de Linux como raiz, executando o seguinte comando, `sh InstallDependencyAgent-Linux64.bin`.

Se o agente de dependência não iniciar, verifique os registos para obter informações de erro detalhadas. Em agentes do Linux, é o diretório de registo */var/opt/microsoft/dependency-agent/log*.

Ficheiros para o agente de dependência são colocados nos seguintes diretórios:

| Ficheiros | Localização |
|:--|:--|
| Ficheiros principais | /opt/microsoft/dependency-agent |
| Ficheiros de registo | /var/opt/microsoft/dependency-agent/log |
| Ficheiros de configuração | /etc/opt/microsoft/dependency-agent/config |
| Ficheiros executáveis de serviço | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Ficheiros de armazenamento binários | /var/opt/microsoft/dependency-agent/storage |

## <a name="enable-performance-counters"></a>Ativar os contadores de desempenho
Se a área de trabalho do Log Analytics que é referenciada pela solução já não está configurada para recolher os contadores de desempenho necessários para a solução, terá de ativá-las. Pode fazê-lo em qualquer uma das seguintes formas:
* Manualmente, conforme descrito em [Windows e Linux origens de dados de desempenho do Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Ao transferir e executar um script do PowerShell que está disponível a partir [galeria do PowerShell do Azure](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Implementar o Azure Monitor para VMs
Esse método inclui um modelo JSON que especifica a configuração para permitir que os componentes da solução na sua área de trabalho do Log Analytics.

Se não estiver familiarizado com a implantação de recursos utilizando um modelo, consulte:
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Implementar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Se optar por utilizar a CLI do Azure, tem primeiro de instalar e utilizar a CLI localmente. Tem de executar a CLI do Azure versão 2.0.27 ou posterior. Para identificar a versão, execute `az --version`. Se precisar de instalar ou atualizar a CLI do Azure, veja [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

1. Capturar os valores para *WorkspaceName*, *ResourceGroupName*, e *WorkspaceLocation*. O valor para *WorkspaceName* é o nome da sua área de trabalho do Log Analytics. O valor para *WorkspaceLocation* é a região a área de trabalho está definida.

1. Está pronto para implementar este modelo, utilizando o seguinte comando do PowerShell:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    A alteração de configuração pode demorar alguns minutos a concluir. Quando estiver concluída, é apresentada uma mensagem que é semelhante ao seguinte e inclui o resultado:

    ```powershell
    provisioningState       : Succeeded
    ```
   Depois de ativar a monitorização, poderá demorar cerca de 10 minutos antes de poder ver o estado de funcionamento e métricas para o computador híbrida.

## <a name="next-steps"></a>Passos Seguintes

Agora que a monitorização estiver ativada para as suas máquinas virtuais, estas informações são disponíveis para análise com o Azure Monitor para as VMs. Para saber como utilizar a funcionalidade de estado de funcionamento, veja [vista do Azure Monitor de estado de funcionamento de VMs](vminsights-health.md). Para ver dependências de aplicações detetadas, consulte [vista do Azure Monitor para o mapa de VMs](vminsights-maps.md). Para identificar afunilamentos e a utilização geral com o desempenho de VMs, veja [vista de desempenho da VM do Azure](vminsights-performance.md), ou para ver dependências de aplicações detetadas, consulte [vista de Azure Monitor para o mapa de VMs](vminsights-maps.md).