---
title: Desired State Configuration de extensão com modelos Azure Resource Manager
description: Saiba mais sobre a definição do modelo do Resource Manager para a extensão do Desired State Configuration (DSC) no Azure.
services: virtual-machines-windows
author: bobbytreed
manager: carmonm
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/05/2018
ms.author: robreed
ms.openlocfilehash: e62bc0fff054f0392cd4f437565b5f4dae9cbfb7
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594428"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Desired State Configuration de extensão com modelos Azure Resource Manager

Este artigo descreve o modelo Azure Resource Manager para o [processador de extensões do Desired State Configuration (DSC)](dsc-overview.md). Muitos dos exemplos de utilização **RegistrationURL** (fornecida como uma cadeia de caracteres) e **RegistrationKey** (fornecida como um [PSCredential](/dotnet/api/system.management.automation.pscredential)) para carregar a automatização do Azure. Para obter detalhes sobre como obter esses valores, consulte [integrar computadores para gestão de configuração de estado de automatização do Azure - registo segura](/azure/automation/automation-dsc-onboarding#secure-registration).

> [!NOTE]
> Talvez encontre exemplos de esquema ligeiramente diferente. A alteração no esquema ocorreu na versão de Outubro de 2016. Para obter detalhes, consulte [atualização de um formato anterior](#update-from-a-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Exemplo de modelo para uma VM do Windows

O fragmento seguinte é incluído nos **recursos** secção do modelo.
A extensão DSC herda propriedades de extensão predefinido.
Para obter mais informações, consulte [VirtualMachineExtension classe](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet).

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-06-30",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "protectedSettings": {
      "Items": {
        "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
      }
    },
    "settings": {
      "Properties": [
        {
          "Name": "RegistrationKey",
          "Value": {
            "UserName": "PLACEHOLDER_DONOTUSE",
            "Password": "PrivateSettingsRef:registrationKeyPrivate"
          },
          "TypeName": "System.Management.Automation.PSCredential"
        },
        {
          "Name": "RegistrationUrl",
          "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
          "TypeName": "System.String"
        },
        {
          "Name": "NodeConfigurationName",
          "Value": "[parameters('nodeConfigurationName')]",
          "TypeName": "System.String"
        }
      ]
    }
  }
}
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Exemplo de modelo para o dimensionamento de máquinas virtuais do Windows define

Um nó de conjunto de dimensionamento de máquina virtual tem um **propriedades** secção que tem um **VirtualMachineProfile, extensionProfile** atributo.
Sob **extensões**, adicione os detalhes para a extensão do DSC.

A extensão DSC herda propriedades de extensão predefinido.
Para obter mais informações, consulte [VirtualMachineScaleSetExtension classe](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

```json
"extensionProfile": {
    "extensions": [
      {
        "name": "Microsoft.Powershell.DSC",
        "properties": {
          "publisher": "Microsoft.Powershell",
          "type": "DSC",
          "typeHandlerVersion": "2.77",
          "autoUpgradeMinorVersion": true,
          "protectedSettings": {
            "Items": {
              "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
            }
          },
          "settings": {
            "Properties": [
              {
                "Name": "RegistrationKey",
                "Value": {
                  "UserName": "PLACEHOLDER_DONOTUSE",
                  "Password": "PrivateSettingsRef:registrationKeyPrivate"
                },
                "TypeName": "System.Management.Automation.PSCredential"
              },
              {
                "Name": "RegistrationUrl",
                "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
                "TypeName": "System.String"
              },
              {
                "Name": "NodeConfigurationName",
                "Value": "[parameters('nodeConfigurationName')]",
                "TypeName": "System.String"
              }
            ]
          }
        }
      }
    ]
  }
```

## <a name="detailed-settings-information"></a>Informações de definições detalhadas

Utilize o seguinte esquema no **definições** secção da extensão do DSC do Azure num modelo do Resource Manager.

Para obter uma lista dos argumentos que estão disponíveis para o script de configuração padrão, consulte [predefinida de script de configuração](#default-configuration-script).

```json
"settings": {
    "wmfVersion": "latest",
    "configuration": {
        "url": "http://validURLToConfigLocation",
        "script": "ConfigurationScript.ps1",
        "function": "ConfigurationFunction"
    },
    "configurationArguments": {
        "argument1": "Value1",
        "argument2": "Value2"
    },
    "configurationData": {
        "url": "https://foo.psd1"
    },
    "privacy": {
        "dataCollection": "enable"
    },
    "advancedOptions": {
        "downloadMappings": {
            "customWmfLocation": "http://myWMFlocation"
        }
    }
},
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
            "userName": "UsernameValue1",
            "password": "PasswordValue1"
        },
        "parameterOfTypePSCredential2": {
            "userName": "UsernameValue2",
            "password": "PasswordValue2"
        }
    },
    "configurationUrlSasToken": "?g!bber1sht0k3n",
    "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}
```

## <a name="details"></a>Detalhes

| Nome da propriedade | Type | Descrição |
| --- | --- | --- |
| settings.wmfVersion |cadeia |Especifica a versão do Windows Management Framework (WMF) que deve ser instalado na sua VM. Definir esta propriedade como **mais recente** instala a versão mais recente do WMF. Atualmente, são só possíveis valores para esta propriedade **4.0**, **5.0**, **5.1**, e **mais recente**. Estes valores possíveis são sujeitos a atualizações. O valor predefinido é **mais recente**. |
| settings.configuration.url |cadeia |Especifica a localização de URL para transferir o ficheiro de. zip de configuração de DSC. Se o URL fornecido requer um token SAS para o acesso, defina o **protectedSettings.configurationUrlSasToken** propriedade para o valor do seu token SAS. Esta propriedade é necessária se **settings.configuration.script** ou **settings.configuration.function** são definidos. Se nenhum valor é atribuído para estas propriedades, a extensão chama um script de configuração para definir os metadados do Gestor de configuração local (LCM) e deve ser fornecido nenhum argumento. |
| settings.configuration.script |cadeia |Especifica o nome de ficheiro do script que contém a definição da sua configuração de DSC. Este script tem de ser na pasta raiz do ficheiro. zip que é transferido a partir do URL especificado o **settings.configuration.url** propriedade. Esta propriedade é necessária se **settings.configuration.url** ou **settings.configuration.script** são definidos. Se nenhum valor é atribuído para estas propriedades, a extensão chama um script de configuração para configurar o LCM metadados e deve ser fornecido nenhum argumento. |
| settings.configuration.function |cadeia |Especifica o nome da sua configuração de DSC. A configuração com o nome tem de ser incluída no script que **settings.configuration.script** define. Esta propriedade é necessária se **settings.configuration.url** ou **settings.configuration.function** são definidos. Se nenhum valor é atribuído para estas propriedades, a extensão chama um script de configuração para configurar o LCM metadados e deve ser fornecido nenhum argumento. |
| settings.configurationArguments |Coleção |Define quaisquer parâmetros que pretende passar para a configuração de DSC. Esta propriedade não está encriptada. |
| settings.configurationData.url |cadeia |Especifica o URL para transferir o ficheiro de dados (. psd1) de configuração para utilizar como entrada para a sua configuração de DSC. Se o URL fornecido requer um token SAS para o acesso, defina o **protectedSettings.configurationDataUrlSasToken** propriedade para o valor do seu token SAS. |
| settings.privacy.dataCollection |cadeia |Ativa ou desativa a coleção de telemetria. Os valores apenas possíveis para esta propriedade são **habilitar**, **desativar**, **'**, ou **$null**. Deixar esta propriedade está em branco ou nulo permite a telemetria. O valor predefinido é **'**. Para obter mais informações, consulte [recolha de dados de extensão DSC do Azure](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/). |
| settings.advancedOptions.downloadMappings |Coleção |Define locais alternativos para transferir WMF. Para obter mais informações, consulte [2.8 de extensão de DSC do Azure e como mapear os downloads das dependências de extensão para sua própria localização](https://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx). |
| protectedSettings.configurationArguments |Coleção |Define quaisquer parâmetros que pretende passar para a configuração de DSC. Esta propriedade é encriptada. |
| protectedSettings.configurationUrlSasToken |cadeia |Especifica o token SAS para utilizar para aceder ao URL que **settings.configuration.url** define. Esta propriedade é encriptada. |
| protectedSettings.configurationDataUrlSasToken |cadeia |Especifica o token SAS para utilizar para aceder ao URL que **settings.configurationData.url** define. Esta propriedade é encriptada. |

## <a name="default-configuration-script"></a>Script de configuração predefinida

Para obter mais informações sobre os seguintes valores, consulte [definições básicas do Gestor de configuração Local](/powershell/dsc/metaconfig#basic-settings).
Pode utilizar o script de configuração do padrão de extensão DSC para configurar apenas as propriedades de LCM que estão listadas na tabela seguinte.

| Nome da propriedade | Type | Descrição |
| --- | --- | --- |
| protectedSettings.configurationArguments.RegistrationKey |PSCredential |Propriedade necessária. Especifica a chave que é utilizada para um nó para registar com o serviço de automatização do Azure como a palavra-passe de um objeto de credencial do PowerShell. Este valor pode ser detetado automaticamente ao utilizar o **listkeys** método contra a conta de automatização.  Consulte a [exemplo](#example-using-referenced-azure-automation-registration-values). |
| settings.configurationArguments.RegistrationUrl |cadeia |Propriedade necessária. Especifica o URL do ponto de extremidade de automatização em que o nó tenta registrar. Este valor pode ser detetado automaticamente ao utilizar o **referência** método contra a conta de automatização. |
| settings.configurationArguments.NodeConfigurationName |cadeia |Propriedade necessária. Especifica a configuração do nó na conta de automatização para atribuir ao nó. |
| settings.configurationArguments.ConfigurationMode |cadeia |Especifica o modo para LCM. As opções válidas incluem **ApplyOnly**, **ApplyandMonitor**, e **ApplyandAutoCorrect**.  O valor predefinido é **ApplyandMonitor**. |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | Especifica a frequência com que o LCM tenta verificar com a conta de automatização para atualizações.  Valor predefinido é **30**.  Valor mínimo é **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | Especifica a frequência com que o LCM valida a configuração atual. Valor predefinido é **15**. Valor mínimo é **15**. |
| settings.configurationArguments.RebootNodeIfNeeded | boolean | Especifica se um nó pode ser reiniciado automaticamente, se uma operação de DSC solicitá-lo. Valor predefinido é **false**. |
| settings.configurationArguments.ActionAfterReboot | cadeia | Especifica o que acontece após um reinício ao aplicar uma configuração. As opções válidas são **ContinueConfiguration** e **StopConfiguration**. Valor predefinido é **ContinueConfiguration**. |
| settings.configurationArguments.AllowModuleOverwrite | boolean | Especifica se o LCM é substituído pela módulos existentes no nó. Valor predefinido é **false**. |

## <a name="settings-vs-protectedsettings"></a>definições do vs. protectedSettings

Todas as definições são guardadas num ficheiro de texto de definições na VM.
Propriedades listadas na **definições** são propriedades públicas.
Propriedades públicas não são encriptadas no ficheiro de texto de definições.
Propriedades listadas na **protectedSettings** estão encriptadas com um certificado e não são apresentados em texto simples no ficheiro de definições na VM.

Se a configuração necessita de credenciais, pode incluir as credenciais no **protectedSettings**:

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
               "userName": "UsernameValue1",
               "password": "PasswordValue1"
        }
    }
}
```

## <a name="example-configuration-script"></a>Script de configuração de exemplo

O exemplo seguinte mostra o comportamento predefinido para a extensão DSC, que consiste em fornecer as definições de metadados para LCM e registar com o serviço de DSC de automatização.
Argumentos de configuração são necessários.
Argumentos de configuração são transmitidos para um script de configuração para configurar o LCM metadados.

```json
"settings": {
    "RegistrationUrl" : "[parameters('registrationUrl1')]",
    "NodeConfigurationName" : "nodeConfigurationNameValue1"
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "registrationKey"
        }
    }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Exemplo usando o script de configuração no armazenamento do Azure

O exemplo seguinte é a partir da [descrição geral do processador de extensão DSC](dsc-overview.md).
Este exemplo utiliza os modelos do Resource Manager, em vez de cmdlets para implementar a extensão.
Guardar a configuração de IisInstall.ps1, coloque-o num ficheiro. zip e, em seguida, carregue o ficheiro de um URL acessível.
Este exemplo utiliza o armazenamento de Blobs do Azure, mas pode baixar arquivos. zip a partir de qualquer local arbitrário.

No modelo do Resource Manager, o código a seguir instrui a VM para transferir o ficheiro correto e, em seguida, execute a função adequada do PowerShell:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="example-using-referenced-azure-automation-registration-values"></a>Exemplo usando referenciado valores de registo de automatização do Azure

O exemplo seguinte obtém o **RegistrationUrl** e **RegistrationKey** ao referenciar as propriedades da conta de automatização do Azure e utilizar o **listkeys** método para obter a chave primária (0).  Neste exemplo, os parâmetros **automationAccountName** e **NodeConfigName** foram fornecidos para o modelo.

```json
"settings": {
    "RegistrationUrl" : "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
    "NodeConfigurationName" : "[parameters('NodeConfigName')]"
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-01-15').Keys[0].value]"
        }
    }
}
```

## <a name="update-from-a-previous-format"></a>Atualizar a partir de um formato anterior

Todas as definições num formato anterior da extensão (e que têm as propriedades públicas **ModulesUrl**, **ModuleSource**, **ModuleVersion**,  **ConfigurationFunction**, **SasToken**, ou **propriedades**) adaptar-se automaticamente para o formato atual da extensão.
Eles são executados apenas como antes.

O esquema abaixo mostra que a anterior definições esquema era a aparência:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties": {
        "ParameterToConfigurationFunction1": "Value1",
        "ParameterToConfigurationFunction2": "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1"
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": {
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Eis como o formato anterior adapta-se para o formato atual:

| Nome da propriedade atual | Esquema anterior equivalente |
| --- | --- |
| settings.wmfVersion |settings.WMFVersion |
| settings.configuration.url |settings.ModulesUrl |
| settings.configuration.script |Primeira parte das definições. ConfigurationFunction (antes \\ \\) |
| settings.configuration.function |Segunda parte das definições. ConfigurationFunction (após \\ \\) |
| settings.configuration.module.name | settings.ModuleSource |
| settings.configuration.module.version | settings.ModuleVersion |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |protectedSettings.DataBlobUri (sem o token SAS) |
| settings.privacy.dataCollection |settings.Privacy.dataCollection |
| settings.advancedOptions.downloadMappings |settings.AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |Token de SAS do protectedSettings.DataBlobUri |

## <a name="troubleshooting"></a>Resolução de problemas

Aqui estão alguns dos erros que pode se deparar e como corrigi-los.

### <a name="invalid-values"></a>Valores inválidos

"Privacy.dataCollection é '{0}'.
Os valores possíveis só são ","Ativar"e"Desativar"".
"WmfVersion é '{0}'.
Apenas os valores possíveis são... e 'mais recente' ".

**Problema**: Um valor fornecido não é permitido.

**Solução**: Altere o valor inválido para um valor válido.
Para obter mais informações, consulte a tabela [detalhes](#details).

### <a name="invalid-url"></a>URL inválido

"ConfigurationData.url é '{0}'. Não se trata de um URL válido"" DataBlobUri é '{0}'. Não se trata de um URL válido"" Configuration.url é '{0}'. Não se trata de um URL válido"

**Problema**: Um URL fornecido não é válido.

**Solução**: Verifique todos os URLs de fornecido.
Certifique-se de que todos os URLs são resolvidas para localizações válidas que a extensão pode aceder na máquina remota.

### <a name="invalid-registrationkey-type"></a>Tipo de RegistrationKey inválido

"Tipo inválido para o parâmetro RegistrationKey do tipo PSCredential."

**Problema**: O *RegistrationKey* valor na protectedSettings.configurationArguments não pode ser fornecido como qualquer tipo diferente de uma PSCredential.

**Solução**: Altere sua participação protectedSettings.configurationArguments para RegistrationKey para um tipo de PSCredential com o seguinte formato:

```json
"configurationArguments": {
    "RegistrationKey": {
        "userName": "NOT_USED",
        "Password": "RegistrationKey"
    }
}
```

### <a name="invalid-configurationargument-type"></a>Tipo de ConfigurationArgument inválido

"Tipo de configurationArguments inválido {0}"

**Problema**: O *ConfigurationArguments* propriedade não é possível resolver a um **tabela de Hash** objeto.

**Solução**: Tornar seu *ConfigurationArguments* propriedade um **tabela de Hash**.
Siga o formato fornecido nos exemplos anteriores. Procure chavetas, vírgulas e aspas.

### <a name="duplicate-configurationarguments"></a>Duplicar ConfigurationArguments

"Foram encontrados duplicados argumentos{0}' no configurationArguments públicos e protegidos"

**Problema**: O *ConfigurationArguments* nas definições do públicas e o *ConfigurationArguments* em definições protegidas têm propriedades com o mesmo nome.

**Solução**: Remova uma das propriedades duplicadas.

### <a name="missing-properties"></a>Propriedades em falta

"as definições. Configuration.Function requer que é especificado settings.configuration.url ou settings.configuration.module"

"as definições. Configuration.URL requer que settings.configuration.script é especificado"

"as definições. Configuration.script requer que settings.configuration.url é especificado"

"as definições. Configuration.URL requer que settings.configuration.function é especificado"

"protectedSettings.ConfigurationUrlSasToken requer que settings.configuration.url é especificado"

"protectedSettings.ConfigurationDataUrlSasToken requer que settings.configurationData.url é especificado"

**Problema**: Uma propriedade definida tem outra propriedade, o que está em falta.

**Soluções**:

- Forneça a propriedade em falta.
- Remova a propriedade que tem a propriedade em falta.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [utilizar o dimensionamento de máquinas virtuais conjuntos com a extensão DSC do Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- Encontrar mais detalhes sobre [gestão de seguros de credenciais do DSC](dsc-credentials.md).
- Obter um [introdução ao manipulador de extensão DSC do Azure](dsc-overview.md).
- Para obter mais informações sobre o DSC de PowerShell, vá para o [Centro de documentação do PowerShell](/powershell/dsc/overview).
