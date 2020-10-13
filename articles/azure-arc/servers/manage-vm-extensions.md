---
title: Gestão de extensão VM com servidores ativados Azure Arc
description: Os servidores ativados do Azure Arc podem gerir a implementação de extensões de máquinas virtuais que fornecem tarefas de configuração e automatização pós-implantação com VMs não-Azure.
ms.date: 09/23/2020
ms.topic: conceptual
ms.openlocfilehash: 1c3d50f407f4412a14201dfe669334dbb083d323
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329079"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Gestão de extensão de máquina virtual com servidores ativados do Azure Arc

As extensões de máquinas virtuais (VM) são pequenas aplicações que fornecem tarefas de configuração e automatização pós-implantação em VMs Azure. Por exemplo, se uma máquina virtual exigir a instalação de software, a proteção antivírus ou a execução de um script, poderá ser utilizada uma extensão de VM.

Os servidores ativados pelo Azure Arc permitem-lhe implantar extensões VM Azure para Windows e Linux VMs não-Azure, simplificando a gestão da sua máquina híbrida no local, borda e outros ambientes de nuvem através do seu ciclo de vida.

## <a name="key-benefits"></a>Principais vantagens

O suporte de extensão VM ativado pelo Azure Arc fornece os seguintes benefícios principais:

* Utilize [a Configuração do Estado da Automação Azure](../../automation/automation-dsc-overview.md) para armazenar centralmente as configurações e manter o estado desejado das máquinas híbridas conectadas ativadas através da extensão DSC VM.

* Recolher dados de registo para análise com [Logs in Azure Monitor](../../azure-monitor/platform/data-platform-logs.md) ativado através da extensão VM do agente Log Analytics. Isto é útil para realizar análises complexas através de dados de várias fontes.

* Com [o Azure Monitor para VMs,](../../azure-monitor/insights/vminsights-overview.md)analisa o desempenho dos seus VMs Windows e Linux, e monitoriza os seus processos e dependências de outros recursos e processos externos. Isto é conseguido através da ativação das extensões VM do agente Log Analytics e do agente de dependência.

* Descarregue e execute scripts em máquinas híbridas conectadas utilizando a extensão de script personalizado. Esta extensão é útil para configuração de implementação de posts, instalação de software ou qualquer outra configuração ou tarefas de gestão.

## <a name="availability"></a>Disponibilidade

A funcionalidade de extensão VM só está disponível na lista de [regiões apoiadas.](overview.md#supported-regions) Certifique-se de que a bordo da sua máquina numa destas regiões.

## <a name="extensions"></a>Extensões

Nesta versão, apoiamos as seguintes extensões VM nas máquinas Windows e Linux.

|Extensão |SO |Publisher |Informações adicionais |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Extensão de script personalizado do Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft.PowerShell|[Extensão DSC do Windows PowerShell](../../virtual-machines/extensions/dsc-windows.md)|
|Agente do Log Analytics |Windows |Microsoft.EnterpriseCloud.Monitoring |[Log Analytics VM extensão para Windows](../../virtual-machines/extensions/oms-windows.md)|
|Agente de dependência da Microsoft | Windows |Microsoft.Compute | [Extensão de máquina virtual de agente de dependência para Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|PersonalScript|Linux |Microsoft.Azure.Extension |[Extensão de script personalizado Linux Versão 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft.OSTCExtensions |[Extensão DSC powerShell para Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Agente do Log Analytics |Linux |Microsoft.EnterpriseCloud.Monitoring |[Log Analytics VM extensão para Linux](../../virtual-machines/extensions/oms-linux.md) |
|Agente de dependência da Microsoft | Linux |Microsoft.Compute | [Extensão da máquina virtual do agente de dependência para Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |

As extensões VM podem ser executadas com modelos Azure Resource Manager, a partir do portal Azure, ou Azure PowerShell em servidores híbridos geridos por servidores ativados pelo Arc.

Para saber mais sobre o pacote do agente Azure Connected Machine e detalhes sobre o componente do agente de extensão, consulte [a visão geral do Agente](agent-overview.md#agent-component-details).

## <a name="prerequisites"></a>Pré-requisitos

Esta funcionalidade depende dos seguintes fornecedores de recursos Azure na sua subscrição:

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

Se ainda não estiverem registados, siga os passos dos [fornecedores de recursos Register Azure](agent-overview.md#register-azure-resource-providers).

A extensão VM do agente Log Analytics para Linux requer que o Python 2.x seja instalado na máquina-alvo.

### <a name="connected-machine-agent"></a>Agente de máquina conectado

Verifique se a sua máquina corresponde às [versões suportadas](agent-overview.md#supported-operating-systems) do sistema operativo Windows e Linux para o agente Azure Connected Machine.

A versão mínima do agente 'Máquina Conectada' que é suportada com esta funcionalidade no Windows e Linux é a versão 1.0.

Para atualizar a sua máquina para a versão do agente necessário, consulte o [agente de atualização](manage-agent.md#upgrading-agent).

## <a name="enable-extensions-from-the-portal"></a>Permitir extensões a partir do portal

As extensões VM podem ser aplicadas ao seu Arco para máquina gerida pelo servidor através do portal Azure.

1. A partir do seu navegador, aceda ao [portal Azure.](https://portal.azure.com)

2. No portal, navegue pelos **Servidores - Azure Arc** e selecione a sua máquina híbrida da lista.

3. Escolha **extensões**e, em seguida, **selecione Adicionar**. Escolha a extensão desejada na lista de extensões disponíveis e siga as instruções no assistente. Neste exemplo, implementaremos a extensão VM do Log Analytics.

    ![Selecione extensão VM para máquina selecionada](./media/manage-vm-extensions/add-vm-extensions.png)

    O exemplo a seguir mostra a instalação da extensão VM log analytics a partir do portal Azure:

    ![Instalar extensão VM do Log Analytics](./media/manage-vm-extensions/mma-extension-config.png)

    Para completar a instalação, é-lhe exigido que forneça o ID do espaço de trabalho e a chave primária. Se não estiver familiarizado com a forma de encontrar esta informação, consulte [a identificação do espaço de trabalho e a chave.](../../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)

4. Depois de confirmar as informações necessárias fornecidas, selecione **Criar**. É apresentado um resumo da implantação e pode rever o estado da implantação.

>[!NOTE]
>Embora as extensões múltiplas possam ser emgrurentadas e processadas, são instaladas em série. Uma vez concluída a primeira instalação de extensão, tentará-se a instalação da próxima extensão.

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

As extensões VM podem ser adicionadas a um modelo de Gestor de Recursos Azure e executadas com a implementação do modelo. Com as extensões VM suportadas por servidores ativados pelo Arc, pode implementar a extensão VM suportada nas máquinas Linux ou Windows utilizando o Azure PowerShell. Cada amostra abaixo inclui um ficheiro de modelo e um arquivo de parâmetros com valores de amostra para fornecer ao modelo.

>[!NOTE]
>Embora as extensões múltiplas possam ser emgrurentadas e processadas, são instaladas em série. Uma vez concluída a primeira instalação de extensão, tentará-se a instalação da próxima extensão.

### <a name="deploy-the-log-analytics-vm-extension"></a>Implementar a extensão VM do Log Analytics

Para implantar facilmente o agente Log Analytics, é fornecida a seguinte amostra para instalar o agente no Windows ou no Linux.

#### <a name="template-file-for-linux"></a>Arquivo de modelo para Linux

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/OMSAgentForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>Arquivo de modelo para Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/MicrosoftMonitoringAgent')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Arquivo de parâmetros

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<vmName>"
        },
        "location": {
            "value": "<region>"
        },
        "workspaceId": {
            "value": "<MyWorkspaceID>"
        },
        "workspaceKey": {
            "value": "<MyWorkspaceKey>"
        }
    }
}
```

Guarde o modelo e os ficheiros de parâmetros para o disco e edite o ficheiro de parâmetros com os valores apropriados para a sua implementação. Em seguida, pode instalar a extensão em todas as máquinas ligadas dentro de um grupo de recursos com o seguinte comando. O comando utiliza o parâmetro *TemplateFile* para especificar o modelo e o parâmetro *ModeloParameterFile* para especificar um ficheiro que contenha parâmetros e valores de parâmetros.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgentWin.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentWinParms.json"
```

### <a name="deploy-the-custom-script-extension"></a>Implementar a extensão de script personalizado

Para utilizar a extensão de script personalizada, a amostra a seguir é fornecida para ser executada no Windows e Linux. Se não estiver familiarizado com a extensão do Script Personalizado, consulte a [extensão do Script Personalizado para Windows](../../virtual-machines/extensions/custom-script-windows.md) ou [extensão de script personalizada para Linux](../../virtual-machines/extensions/custom-script-linux.md). Existem algumas características diferentes que deve entender ao utilizar esta extensão com máquinas híbridas:

* A lista de sistemas operativos suportados com a extensão Azure VM Custom Script não é aplicável aos servidores ativados do Azure Arc. A lista de OSs suportados para servidores ativados pelo Arco pode ser encontrada [aqui](agent-overview.md#supported-operating-systems).

* Não são aplicáveis detalhes de configuração relativos a conjuntos de balanças de máquinas virtuais Azure ou VMs clássicos.

* Se as suas máquinas precisarem de descarregar um script externamente e só puderem comunicar através de um servidor proxy, é necessário [configurar o agente 'Máquina Conectada'](manage-agent.md#update-or-remove-proxy-settings) para definir a variável ambiental do servidor proxy.

A configuração de extensão de script personalizado especifica coisas como a localização do script e o comando a ser executado. Esta configuração é especificada num modelo Azure Resource Manager, fornecido abaixo tanto para máquinas híbridas Linux como para Windows.

#### <a name="template-file-for-linux"></a>Arquivo de modelo para Linux

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "fileUris": {
      "type": "array"
    },
    "commandToExecute": {
      "type": "securestring"
    }
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/CustomScript')]",
      "type": "Microsoft.HybridCompute/machines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2019-08-02-preview",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "autoUpgradeMinorVersion": true,
        "settings": {},
        "protectedSettings": {
          "commandToExecute": "[parameters('commandToExecute')]",
          "fileUris": "[parameters('fileUris')]"
        }
      }
    }
  ]
}
```

#### <a name="template-file-for-windows"></a>Arquivo de modelo para Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "fileUris": {
            "type": "string"
        },
        "arguments": {
            "type": "securestring",
            "defaultValue": " "
        }
    },
    "variables": {
        "UriFileNamePieces": "[split(parameters('fileUris'), '/')]",
        "firstFileNameString": "[variables('UriFileNamePieces')[sub(length(variables('UriFileNamePieces')), 1)]]",
        "firstFileNameBreakString": "[split(variables('firstFileNameString'), '?')]",
        "firstFileName": "[variables('firstFileNameBreakString')[0]]"
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/CustomScriptExtension')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "fileUris": "[split(parameters('fileUris'), ' ')]"
                },
                "protectedSettings": {
                    "commandToExecute": "[concat ('powershell -ExecutionPolicy Unrestricted -File ', variables('firstFileName'), ' ', parameters('arguments'))]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Arquivo de parâmetros

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "customScriptExt",
        "label": "Add Custom Script Extension",
        "elements": [
          {
            "name": "fileUris",
            "type": "Microsoft.Common.FileUpload",
            "label": "Script files",
            "toolTip": "The script files that will be downloaded to the virtual machine.",
            "constraints": {
              "required": false
            },
            "options": {
              "multiple": true,
              "uploadMode": "url"
            },
            "visible": true
          },
          {
            "name": "commandToExecute",
            "type": "Microsoft.Common.TextBox",
            "label": "Command",
            "defaultValue": "sh script.sh",
            "toolTip": "The command to execute, for example: sh script.sh",
            "constraints": {
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "fileUris": "[steps('customScriptExt').fileUris]",
      "commandToExecute": "[steps('customScriptExt').commandToExecute]"
    }
  }
}
```

### <a name="deploy-the-powershell-dsc-extension"></a>Implementar a extensão DSC powerShell

Para utilizar a extensão DSC powerShell, é fornecida a seguinte amostra para funcionar no Windows e Linux. Se não estiver familiarizado com a extensão DSC powerShell, consulte a [visão geral do controlador de extensão DSC](../../virtual-machines/extensions/dsc-overview.md). Existem algumas características diferentes que deve entender ao utilizar esta extensão com máquinas híbridas:

* A lista de sistemas operativos suportados com a extensão DSC Azure VM PowerShell não é aplicável aos servidores ativados do Azure Arc. A lista de OSs suportados para servidores ativados pelo Arco pode ser encontrada [aqui](agent-overview.md#supported-operating-systems).

* Se as suas máquinas precisarem de descarregar um script externamente e só puderem comunicar através de um servidor proxy, é necessário [configurar o agente 'Máquina Conectada'](manage-agent.md#update-or-remove-proxy-settings) para definir a variável ambiental do servidor proxy.

#### <a name="template-file-for-linux"></a>Arquivo de modelo para Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vm, will be used as DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "mode": {
            "type": "string",
            "defaultValue": "Push",
            "metadata": {
                "description": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)"
            },
            "allowedValues": [
                "Push",
                "Pull",
                "Install",
                "Register"
            ]
        },
        "fileUri": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The uri of the MOF file/Meta MOF file/resource ZIP file"
            }
        },
        "registrationUrl": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The URL of the Azure Automation account"
            }
        },
        "registrationKey": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The access key of the Azure Automation account"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/DSCForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.OSTCExtensions",
                "type": "DSCForLinux",
                "settings": {
                    "Mode": "[parameters('mode')]",
                    "FileUri": "[parameters('fileUri')]"
                },
                "protectedSettings": {
                    "RegistrationUrl": "[parameters('registrationUrl')]",
                    "RegistrationKey": "[parameters('registrationKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>Arquivo de modelo para Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "modulesUrl": {
            "type": "string"
        },
        "configurationFunction": {
            "type": "string"
        },
        "properties": {
            "type": "string",
            "defaultValue": ""
        },
        "dataBlobUri": {
            "type": "string",
            "defaultValue": ""
        },
        "wmfVersion": {
            "type": "string",
            "defaultValue": "latest",
            "allowedValues": [
                "4.0",
                "5.0",
                "5.1",
                "latest"
            ]
        },
        "privacy": {
            "type": "string",
            "defaultValue": ""
        },
        "autoUpdate": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/Microsoft.Powershell.DSC')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Powershell",
                "type": "DSC",
                "autoUpgradeMinorVersion": "[parameters('autoUpdate')]",
                "settings": {
                    "ModulesUrl": "[parameters('modulesUrl')]",
                    "ConfigurationFunction": "[parameters('configurationFunction')]",
                    "Properties": "[parameters('properties')]",
                    "WmfVersion": "[parameters('wmfVersion')]",
                    "Privacy": {
                        "DataCollection": "[parameters('privacy')]"
                    }
                },
                "protectedSettings": {
                    "DataBlobUri": "[parameters('dataBlobUri')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Arquivo de parâmetros

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "dscExtension",
        "label": "Add DSC Extension",
        "elements": [
          {
            "name": "Mode",
            "type": "Microsoft.Common.OptionsGroup",
            "label": "Mode",
            "defaultValue": 0,
            "toolTip": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Push",
                  "value": "Push"
                },
                {
                  "label": "Pull",
                  "value": "Pull"
                },
                {
                  "label": "Install",
                  "value": "Install"
                },
                {
                  "label": "Register",
                  "value": "Register"
                }
              ]
            },
            "visible": true
          },
          {
            "name": "FileUri",
            "type": "Microsoft.Common.FileUpload",
            "label": "File URI",
            "toolTip": "The uri of the MOF file/Meta MOF file/resource ZIP file",
            "constraints": {
              "required": false,
              "accept": ".psd1"
            },
            "options": {
              "multiple": false,
              "uploadMode": "url",
              "openMode": "binary",
              "encoding": "UTF-8"
            }
          },
          {
            "name": "RegistrationUrl",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration URL",
            "toolTip": "The URL of the Azure Automation account",
            "constraints": {
              "required": false
            }
          },
          {
            "name": "RegistrationKey",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration key",
            "toolTip": "The access key of the Azure Automation account",
            "constraints": {
              "required": false
            }
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "mode": "[steps('dscExtension').Mode]",
      "fileUri": "[steps('dscExtension').FileUri]",
      "registrationUrl": "[steps('dscExtension').RegistrationUrl]",
      "registrationKey": "[steps('dscExtension').RegistrationKey]"
    }
  }
}
```

### <a name="dependency-agent"></a>Agente de Dependência

Para utilizar a extensão do agente Azure Monitor Dependency, é fornecida a seguinte amostra para funcionar no Windows e Linux. Se não estiver familiarizado com o agente de dependência, consulte [a visão geral dos agentes do Monitor Azure](../../azure-monitor/platform/agents-overview.md#dependency-agent).

#### <a name="template-file-for-linux"></a>Arquivo de modelo para Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

#### <a name="template-file-for-windows"></a>Arquivo de modelo para Windows

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Windows machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

## <a name="uninstall-extension"></a>Desinstalar extensão

A remoção de uma ou mais extensões de um servidor ativado pelo Arco só pode ser realizada a partir do portal Azure. Execute os seguintes passos para remover uma extensão.

1. A partir do seu navegador, aceda ao [portal Azure.](https://portal.azure.com)

2. No portal, navegue pelos **Servidores - Azure Arc** e selecione a sua máquina híbrida da lista.

3. Escolha **extensões**e, em seguida, selecione uma extensão da lista de extensões instaladas.

4. Selecione **Desinstalar** e quando for solicitado para verificar, selecione **Sim** para prosseguir.

## <a name="next-steps"></a>Passos seguintes

* As informações relativas à resolução de problemas podem ser encontradas no guia de [extensões VM de resolução de problemas.](troubleshoot-vm-extensions.md)

* Aprenda a gerir a sua máquina utilizando [a Azure Policy](../../governance/policy/overview.md), para coisas como [a configuração do hóspede](../../governance/policy/concepts/guest-configuration.md)VM , verificando se a máquina está a reportar ao espaço de trabalho esperado do Log Analytics, permitir a monitorização com o [Azure Monitor com VMs](../../azure-monitor/insights/vminsights-enable-policy.md), e muito mais.

* Saiba mais sobre o [agente Log Analytics.](../../azure-monitor/platform/log-analytics-agent.md) O agente Log Analytics para Windows e Linux é necessário quando pretende recolher dados de monitorização do sistema operativo e da carga de trabalho, geri-lo utilizando livros de aplicação da Automação ou funcionalidades como Update Management, ou utilizar outros serviços Azure como [o Azure Security Center](../../security-center/security-center-intro.md).