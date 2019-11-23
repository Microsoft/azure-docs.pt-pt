---
title: Extensão de configuração de estado desejado com modelos de Azure Resource Manager
description: Saiba mais sobre a definição de modelo do Resource Manager para a extensão de DSC (configuração de estado desejado) no Azure.
services: virtual-machines-windows
author: bobbytreed
manager: carmonm
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/05/2018
ms.author: robreed
ms.openlocfilehash: ef781653332984a7fb6d71ef91d53cbf77e6c91c
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72437949"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Extensão de configuração de estado desejado com modelos de Azure Resource Manager

Este artigo descreve o modelo de Azure Resource Manager para o [manipulador de extensão de configuração de estado desejado (DSC)](dsc-overview.md). Muitos dos exemplos usam **RegistrationURL** (fornecido como uma cadeia de caracteres) e **RegistrationKey** (fornecido como um [PSCredential](/dotnet/api/system.management.automation.pscredential)) para integração com a automação do Azure. Para obter detalhes sobre como obter esses valores, consulte [máquinas de integração para gerenciamento pela configuração de estado da automação do Azure – registro seguro](/azure/automation/automation-dsc-onboarding#secure-registration).

> [!NOTE]
> Você pode encontrar exemplos de esquema ligeiramente diferentes. A alteração no esquema ocorreu na versão de outubro de 2016. Para obter detalhes, consulte [Atualizar de um formato anterior](#update-from-a-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Exemplo de modelo para uma VM do Windows

O trecho a seguir vai na seção de **recursos** do modelo.
A extensão de DSC herda as propriedades de extensão padrão.
Para obter mais informações, consulte [classe VirtualMachineExtension](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet).

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

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Exemplo de modelo para conjuntos de dimensionamento de máquinas virtuais do Windows

Um nó do conjunto de dimensionamento de máquinas virtuais tem uma seção **Propriedades** que tem um atributo **VirtualMachineProfile, extensionProfile** .
Em **extensões**, adicione os detalhes da extensão DSC.

A extensão de DSC herda as propriedades de extensão padrão.
Para obter mais informações, consulte [classe VirtualMachineScaleSetExtension](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

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

## <a name="detailed-settings-information"></a>Informações de configurações detalhadas

Use o esquema a seguir na seção **configurações** da extensão DSC do Azure em um modelo do Resource Manager.

Para obter uma lista dos argumentos que estão disponíveis para o script de configuração padrão, consulte [script de configuração padrão](#default-configuration-script).

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

| Nome da propriedade | Tipo | Descrição |
| --- | --- | --- |
| settings.wmfVersion |string |Especifica a versão do Windows Management Framework (WMF) que deve ser instalada em sua VM. Definir essa propriedade como **mais recente** instala a versão mais recente do WMF. Atualmente, os únicos valores possíveis para essa propriedade são **4,0**, **5,0**, **5,1**e **mais recente**. Esses valores possíveis estão sujeitos a atualizações. O valor padrão é **mais recente**. |
| settings.configuration.url |string |Especifica o local da URL do qual baixar o arquivo. zip de configuração DSC. Se a URL fornecida exigir um token SAS para acesso, defina a propriedade **protectedSettings. configurationUrlSasToken** como o valor do seu token SAS. Essa propriedade será necessária se **Settings. Configuration. script** ou **Settings. Configuration. Function** forem definidas. Se nenhum valor for fornecido para essas propriedades, a extensão chamará o script de configuração padrão para definir metadados de local Configuration Manager (LCM) e os argumentos deverão ser fornecidos. |
| settings.configuration.script |string |Especifica o nome do arquivo do script que contém a definição de sua configuração DSC. Esse script deve estar na pasta raiz do arquivo. zip que é baixado da URL especificada pela propriedade **Settings. Configuration. URL** . Essa propriedade será necessária se **Settings. Configuration. URL** ou **Settings. Configuration. script** forem definidas. Se nenhum valor for fornecido para essas propriedades, a extensão chamará o script de configuração padrão para definir os metadados do LCM e os argumentos deverão ser fornecidos. |
| settings.configuration.function |string |Especifica o nome da sua configuração DSC. A configuração nomeada deve ser incluída no script que o **Settings. Configuration. script** define. Essa propriedade será necessária se **Settings. Configuration. URL** ou **Settings. Configuration. Function** forem definidas. Se nenhum valor for fornecido para essas propriedades, a extensão chamará o script de configuração padrão para definir os metadados do LCM e os argumentos deverão ser fornecidos. |
| settings.configurationArguments |Coleção |Define todos os parâmetros que você deseja passar para sua configuração DSC. Esta propriedade não está criptografada. |
| settings.configurationData.url |string |Especifica a URL da qual baixar seu arquivo de dados de configuração (. psd1) para usar como entrada para sua configuração DSC. Se a URL fornecida exigir um token SAS para acesso, defina a propriedade **protectedSettings. configurationDataUrlSasToken** como o valor do seu token SAS. |
| settings.privacy.dataCollection |string |Habilita ou desabilita a coleta de telemetria. Os únicos valores possíveis para essa propriedade são **habilitar**, **desabilitar**, **' '** ou **$NULL**. Deixar essa propriedade em branco ou NULL habilita a telemetria. O valor padrão é **' '** . Para obter mais informações, consulte [coleta de dados de extensão de DSC do Azure](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/). |
| settings.advancedOptions.downloadMappings |Coleção |Define locais alternativos dos quais baixar o WMF. Para obter mais informações, consulte [extensão de DSC do Azure 2,8 e como mapear downloads das dependências de extensão para seu próprio local](https://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx). |
| protectedSettings.configurationArguments |Coleção |Define todos os parâmetros que você deseja passar para sua configuração DSC. Esta propriedade é criptografada. |
| protectedSettings.configurationUrlSasToken |string |Especifica o token SAS a ser usado para acessar a URL que o **Settings. Configuration. URL** define. Esta propriedade é criptografada. |
| protectedSettings.configurationDataUrlSasToken |string |Especifica o token SAS a ser usado para acessar a URL que o **Settings. configurationData. URL** define. Esta propriedade é criptografada. |

## <a name="default-configuration-script"></a>Script de configuração padrão

Para obter mais informações sobre os valores a seguir, consulte [configurações básicas de Configuration Manager local](/powershell/scripting/dsc/managing-nodes/metaConfig#basic-settings).
Você pode usar o script de configuração padrão da extensão DSC para configurar apenas as propriedades do LCM listadas na tabela a seguir.

| Nome da propriedade | Tipo | Descrição |
| --- | --- | --- |
| protectedSettings.configurationArguments.RegistrationKey |PSCredential |Propriedade necessária. Especifica a chave usada para o registro de um nó com o serviço de automação do Azure como a senha de um objeto de credencial do PowerShell. Esse valor pode ser descoberto automaticamente usando o método **listkeys** em relação à conta de automação.  Consulte o [exemplo](#example-using-referenced-azure-automation-registration-values). |
| settings.configurationArguments.RegistrationUrl |string |Propriedade necessária. Especifica a URL do ponto de extremidade de automação onde o nó tenta se registrar. Esse valor pode ser descoberto automaticamente usando o método de **referência** em relação à conta de automação. |
| settings.configurationArguments.NodeConfigurationName |string |Propriedade necessária. Especifica a configuração de nó na conta de automação a ser atribuída ao nó. |
| settings.configurationArguments.ConfigurationMode |string |Especifica o modo para o LCM. As opções válidas incluem **ApplyOnly**, **ApplyandMonitor**e **ApplyandAutoCorrect**.  O valor padrão é **ApplyandMonitor**. |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | Especifica com que frequência o LCM tenta verificar se há atualizações na conta de automação.  O valor padrão é **30**.  O valor mínimo é **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | Especifica com que frequência o LCM valida a configuração atual. O valor padrão é **15**. O valor mínimo é **15**. |
| settings.configurationArguments.RebootNodeIfNeeded | boolean | Especifica se um nó pode ser reinicializado automaticamente se uma operação de DSC solicitar. O valor padrão é **false**. |
| settings.configurationArguments.ActionAfterReboot | string | Especifica o que acontece após uma reinicialização ao aplicar uma configuração. As opções válidas são **ContinueConfiguration** e **StopConfiguration**. O valor padrão é **ContinueConfiguration**. |
| settings.configurationArguments.AllowModuleOverwrite | boolean | Especifica se o LCM substitui os módulos existentes no nó. O valor padrão é **false**. |

## <a name="settings-vs-protectedsettings"></a>configurações versus protectedSettings

Todas as configurações são salvas em um arquivo de texto de configurações na VM.
As propriedades listadas em **configurações** são propriedades públicas.
As propriedades públicas não são criptografadas no arquivo de texto de configurações.
As propriedades listadas em **protectedSettings** são criptografadas com um certificado e não são mostradas em texto sem formatação no arquivo de configurações na VM.

Se a configuração precisar de credenciais, você poderá incluir as credenciais em **protectedSettings**:

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

## <a name="example-configuration-script"></a>Exemplo de script de configuração

O exemplo a seguir mostra o comportamento padrão para a extensão de DSC, que é fornecer configurações de metadados para LCM e registrar com o serviço de DSC de Automação.
Argumentos de configuração são necessários.
Os argumentos de configuração são passados para o script de configuração padrão para definir metadados do LCM.

```json
"settings": {
    "configurationArguments": {
        "RegistrationUrl" : "[parameters('registrationUrl1')]",
        "NodeConfigurationName" : "nodeConfigurationNameValue1"
    }
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

O exemplo a seguir é da [visão geral do manipulador de extensão de DSC](dsc-overview.md).
Este exemplo usa modelos do Resource Manager em vez de cmdlets para implantar a extensão.
Salve a configuração IisInstall. ps1, coloque-a em um arquivo. zip (exemplo: `iisinstall.zip`) e, em seguida, carregue o arquivo em uma URL acessível.
Este exemplo usa o armazenamento de BLOBs do Azure, mas você pode baixar arquivos. zip de qualquer local arbitrário.

No modelo do Resource Manager, o código a seguir instrui a VM a baixar o arquivo correto e, em seguida, executar a função apropriada do PowerShell:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/iisinstall.zip",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="example-using-referenced-azure-automation-registration-values"></a>Exemplo usando valores de registro de automação do Azure referenciados

O exemplo a seguir obtém o **RegistrationUrl** e o **RegistrationKey** referenciando as propriedades da conta de automação do Azure e usando o método **Listkeys** para recuperar a chave primária (0).  Neste exemplo, os parâmetros **automationAccountName** e **NodeConfigName** foram fornecidos para o modelo.

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

Todas as configurações em um formato anterior da extensão (e que têm as propriedades públicas **ModulesUrl**, **ModuleName**, **ModuleVersion**, **ConfigurationFunction**, **SasToken**ou **Properties**) se adaptam automaticamente ao formato atual da extensão.
Eles são executados exatamente como antes.

O esquema a seguir mostra a aparência do esquema de configurações anterior:

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

Veja como o formato anterior se adapta ao formato atual:

| Nome da propriedade atual | Equivalente ao esquema anterior |
| --- | --- |
| settings.wmfVersion |settings.WMFVersion |
| settings.configuration.url |settings.ModulesUrl |
| settings.configuration.script |Primeira parte das configurações. ConfigurationFunction (antes de \\\\) |
| settings.configuration.function |Segunda parte das configurações. ConfigurationFunction (após \\\\) |
| settings.configuration.module.name | settings.ModuleSource |
| settings.configuration.module.version | settings.ModuleVersion |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |protectedSettings. DataBlobUri (sem token SAS) |
| settings.privacy.dataCollection |settings.Privacy.dataCollection |
| settings.advancedOptions.downloadMappings |settings.AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |Token SAS de protectedSettings. DataBlobUri |

## <a name="troubleshooting"></a>Resolução de problemas

Aqui estão alguns dos erros que você pode encontrar e como você pode corrigi-los.

### <a name="invalid-values"></a>Valores inválidos

"Privacy. dataCollection é '{0}'.
Os únicos valores possíveis são ' ', ' Enable ' e ' Disable ' ".
"WmfVersion é '{0}'.
Somente os valores possíveis são... e ' Latest ' ".

**Problema**: um valor fornecido não é permitido.

**Solução**: altere o valor inválido para um valor válido.
Para obter mais informações, consulte a tabela em [detalhes](#details).

### <a name="invalid-url"></a>URL inválido

"ConfigurationData. URL é '{0}'. Esta não é uma URL válida "" DataBlobUri é '{0}'. Esta não é uma URL válida "" Configuration. a URL é '{0}'. Esta não é uma URL válida "

**Problema**: uma URL fornecida não é válida.

**Solução**: Verifique todas as URLs fornecidas.
Verifique se todas as URLs são resolvidas para locais válidos que a extensão pode acessar no computador remoto.

### <a name="invalid-registrationkey-type"></a>Tipo de RegistrationKey inválido

"Tipo inválido para o parâmetro RegistrationKey do tipo PSCredential."

**Problema**: o valor de *RegistrationKey* em protectedSettings. configurationArguments não pode ser fornecido como qualquer tipo diferente de um PSCredential.

**Solução**: altere a entrada ProtectedSettings. ConfigurationArguments para RegistrationKey para um tipo PSCredential usando o seguinte formato:

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

**Problema**: a propriedade *ConfigurationArguments* não pode ser resolvida para um objeto de **tabela de hash** .

**Solução**: Torne sua propriedade *ConfigurationArguments* uma **tabela de hash**.
Siga o formato fornecido nos exemplos anteriores. Observe as aspas, vírgulas e chaves.

### <a name="duplicate-configurationarguments"></a>ConfigurationArguments duplicado

"Encontrados argumentos duplicados '{0}' no configurationArguments público e protegido"

**Problema**: o *ConfigurationArguments* em configurações públicas e *ConfigurationArguments* nas configurações protegidas têm propriedades com o mesmo nome.

**Solução**: Remova uma das propriedades duplicadas.

### <a name="missing-properties"></a>Propriedades ausentes

das. Configuration. Function requer que Settings. Configuration. URL ou Settings. Configuration. Module seja especificado "

das. Configuration. URL requer que Settings. Configuration. script seja especificado "

das. Configuration. script requer que Settings. Configuration. URL seja especificado "

das. Configuration. URL requer que Settings. Configuration. Function seja especificado "

"protectedSettings. ConfigurationUrlSasToken requer que Settings. Configuration. URL seja especificado"

"protectedSettings. ConfigurationDataUrlSasToken requer que Settings. configurationData. URL seja especificado"

**Problema**: uma propriedade definida precisa de outra propriedade, que está ausente.

**Soluções**:

- Forneça a propriedade ausente.
- Remova a propriedade que precisa da propriedade ausente.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como [usar conjuntos de dimensionamento de máquinas virtuais com a extensão DSC do Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- Encontre mais detalhes sobre o [Gerenciamento de credenciais seguras da DSC](dsc-credentials.md).
- Obtenha uma [introdução ao manipulador de extensões de DSC do Azure](dsc-overview.md).
- Para obter mais informações sobre o DSC do PowerShell, acesse o [centro de documentação do PowerShell](/powershell/scripting/dsc/overview/overview).
