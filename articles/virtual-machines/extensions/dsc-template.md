---
title: Extensão de configuração de estado desejada com modelos de gestor de recursos Azure
description: Saiba mais sobre a definição do modelo do Gestor de Recursos para a extensão de Configuração do Estado Desejado (DSC) em Azure.
services: virtual-machines-windows
author: bobbytreed
manager: carmonm
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/05/2018
ms.author: robreed
ms.openlocfilehash: 8b862238c0c04fae72659d644dbaf882d00cca19
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735695"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Extensão de configuração de estado desejada com modelos de gestor de recursos Azure

Este artigo descreve o modelo do Gestor de Recursos Azure para o [manipulador de extensão de configuração do estado desejado (DSC).](dsc-overview.md) Muitos dos exemplos utilizam **o RegistrationURL** (fornecido como string) e **o RegistrationKey** (fornecido como [um PSCredential](/dotnet/api/system.management.automation.pscredential) a bordo com a Azure Automation. Para obter mais informações sobre a obtenção desses valores, consulte [máquinas de embarque para gestão através da Azure Automation State Configuration - Registo seguro](../../automation/automation-dsc-onboarding.md#enable-machines-securely-using-registration).

> [!NOTE]
> Pode encontrar exemplos de esquemas ligeiramente diferentes. A mudança de esquema ocorreu no lançamento de outubro de 2016. Para mais informações, consulte [Update de um formato anterior.](#update-from-a-previous-format)

## <a name="template-example-for-a-windows-vm"></a>Exemplo do modelo para um VM do Windows

O seguinte corte vai na secção **de Recursos** do modelo.
A extensão DSC herda propriedades de extensão padrão.
Para mais informações, consulte [a classe VirtualMachineExtension](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension).

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-06-01",
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

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Exemplo de modelo para conjuntos de escala de máquina virtual do Windows

Um nó de conjunto de escala de máquina virtual tem uma secção **de propriedades** que tem um **VirtualMachineProfile, atributo extensãoProfile.**
Em **extensões**, adicione os detalhes para extensão DSC.

A extensão DSC herda propriedades de extensão padrão.
Para obter mais informações, consulte [a classe VirtualMachineScaleSetExtension](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension).

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

## <a name="detailed-settings-information"></a>Informações detalhadas sobre definições

Utilize o seguinte esquema na secção de **definições** da extensão Azure DSC num modelo de Gestor de Recursos.

Para obter uma lista dos argumentos disponíveis para o script de configuração padrão, consulte o [script de configuração padrão](#default-configuration-script).

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
| definições.wmfVersion |string |Especifica a versão do Quadro de Gestão do Windows (WMF) que deve ser instalada no seu VM. Definir esta propriedade para as **mais recentes** instala a versão mais recente do WMF. Atualmente, os únicos valores possíveis para esta propriedade são **4.0,** **5.0**, **5.1,** e **os mais recentes.** Estes valores possíveis estão sujeitos a atualizações. O valor predefinido é **o mais recente**. |
| settings.configuration.url |string |Especifica a localização do URL a partir do qual descarregar a sua configuração DSC .zip ficheiro. Se o URL fornecido necessitar de um token SAS para acesso, desacorra a **propriedade urationUrlSasTokenprotectedSettings.config** o valor do seu token SAS. Esta propriedade é necessária se **settings.configuration.script** ou **settings.configuration.fun** forem definidos. Se não for dado qualquer valor para estas propriedades, a extensão chama o script de configuração padrão para definir metadados do Gestor de Configuração de Localização (LCM) e os argumentos devem ser fornecidos. |
| settings.configuration.script |string |Especifica o nome do ficheiro do script que contém a definição da sua configuração DSC. Este script deve estar na pasta raiz do ficheiro .zip que é descarregado a partir do URL especificado pela propriedade **uration.urlsettings.config.** Esta propriedade é necessária se **settings.configuration.url** ou **settings.configuration.script** forem definidos. Se não for dado qualquer valor para estas propriedades, a extensão chama o script de configuração padrão para definir metadados LCM, e os argumentos devem ser fornecidos. |
| settings.configuration.function |string |Especifica o nome da sua configuração DSC. A configuração que é nomeada deve ser incluída no script que **settings.configuration.script** define. Esta propriedade é necessária se **settings.configuration.url** ou **settings.configuration.fun** forem definidos. Se não for dado qualquer valor para estas propriedades, a extensão chama o script de configuração padrão para definir metadados LCM, e os argumentos devem ser fornecidos. |
| settings.configurationArments |Coleção |Define quaisquer parâmetros que pretenda passar para a sua configuração DSC. Esta propriedade não está encriptada. |
| settings.configurationData.url |string |Especifica o URL a partir do qual descarregar o ficheiro de dados de configuração (.psd1) para utilizar como entrada para a sua configuração DSC. Se o URL fornecido necessitar de um token SAS para acesso, desacorra a propriedade **protectedSettings.configurationDataUrlSasToken** para o valor do seu token SAS. |
| definições.privacy.dataCollection |string |Permite ou desativa a recolha de telemetria. Os únicos valores possíveis para esta propriedade são **Ativar,** **Desativar,** **''** ou **$null**. Deixar esta propriedade em branco ou nulo permite telemetria. O valor predefinido é **''**. Para obter mais informações, consulte [a recolha de dados de extensão Azure DSC.](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/) |
| definições.advancedOptions.downloadMappings |Coleção |Define locais alternativos a partir dos quais o WMF. Para mais informações, consulte [a extensão 2.8 do Azure DSC e como mapear as dependências de extensão para a sua própria localização.](https://devblogs.microsoft.com/powershell/azure-dsc-extension-2-8-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location/) |
| protectedSettings.configurationArments |Coleção |Define quaisquer parâmetros que pretenda passar para a sua configuração DSC. Esta propriedade está encriptada. |
| protectedSettings.configurationUrlSasToken |string |Especifica o token SAS para usar para aceder ao URL que **settings.configuration.url** define. Esta propriedade está encriptada. |
| protectedSettings.configurationDataUrlSasToken |string |Especifica o token SAS para usar para aceder ao URL que  **settings.configurationData.url** define. Esta propriedade está encriptada. |

## <a name="default-configuration-script"></a>Script de configuração padrão

Para obter mais informações sobre os seguintes valores, consulte [as definições básicas do Gestor de Configurações Local](/powershell/scripting/dsc/managing-nodes/metaConfig#basic-settings).
Pode utilizar o script de configuração padrão de extensão DSC para configurar apenas as propriedades LCM que estão listadas na tabela seguinte.

| Nome da propriedade | Tipo | Descrição |
| --- | --- | --- |
| protectedSettings.configurationArguments.RegistrationKey |PSCredential |Propriedade requerida. Especifica a chave que é usada para um nó para registar-se com o serviço Azure Automation como a palavra-passe de um objeto credencial PowerShell. Este valor pode ser descoberto automaticamente utilizando o método **listkeys** contra a conta Automation.  Veja o [exemplo.](#example-using-referenced-azure-automation-registration-values) |
| settings.configurationArguments.RegistrationUrl |string |Propriedade requerida. Especifica o URL do ponto final da Automatização onde o nó tenta registar-se. Este valor pode ser descoberto automaticamente utilizando o método **de referência** contra a conta Automação. |
| settings.configurationArguments.NodeConfigurationName |string |Propriedade requerida. Especifica a configuração do nó na conta Automação para atribuir ao nó. |
| settings.configurationArguments.ConfigurationMode |string |Especifica o modo de LCM. As opções válidas incluem **ApplyOnly,** **ApplyandMonitor,** e **ApplyandAutoCorrect**.  O valor predefinido é **ApplyandMonitor**. |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | Especifica com que frequência a LCM tenta verificar com a conta Dem automação atualizações.  O valor predefinido é **de 30**.  O valor mínimo é **de 15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | Especifica a frequência com que o LCM valida a configuração atual. O valor predefinido é **de 15**. O valor mínimo é **de 15**. |
| settings.configurationArguments.RebootNodeIfNeed | boolean | Especifica se um nó pode ser automaticamente reiniciado se uma operação DSC o solicitar. O valor predefinido é **falso.** |
| settings.configurationArguments.ActionAfterReboot | string | Especifica o que acontece após um reboot ao aplicar uma configuração. As opções válidas são **ContinuaConfiguration** e **StopConfiguration**. O valor predefinido é **continuação da configuração**. |
| settings.configurationArguments.AllowModuleOverwrite | boolean | Especifica se o LCM substitui os módulos existentes no nó. O valor predefinido é **falso.** |

## <a name="settings-vs-protectedsettings"></a>definições vs. protectedSettings

Todas as definições são guardadas num ficheiro de texto de definições no VM.
As propriedades listadas em **configurações** são propriedades públicas.
As propriedades públicas não são encriptadas no ficheiro de texto de definições.
As propriedades listadas em **configurações protegidas** são encriptadas com um certificado e não são mostradas em texto simples no ficheiro de definições no VM.

Se a configuração precisar de credenciais, pode incluir as credenciais em **configurações protegidas:**

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

O exemplo a seguir mostra o comportamento padrão para a extensão DSC, que é fornecer definições de metadados para LCM e registar-se com o serviço DSC de automação.
São necessários argumentos de configuração.
Os argumentos de configuração são passados para o script de configuração padrão para definir metadados LCM.

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

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Exemplo usando o script de configuração no Azure Storage

O exemplo a seguir é da visão geral do [controlador de extensão DSC](dsc-overview.md).
Este exemplo utiliza modelos de Gestor de Recursos em vez de cmdlets para implementar a extensão.
Guarde a configuração IisInstall.ps1, coloque-a num ficheiro .zip (exemplo: `iisinstall.zip` ), e, em seguida, carregue o ficheiro num URL acessível.
Este exemplo utiliza o armazenamento do Azure Blob, mas pode descarregar ficheiros .zip de qualquer localização arbitrária.

No modelo de Gestor de Recursos, o seguinte código instrui o VM a descarregar o ficheiro correto e, em seguida, executar a função PowerShell apropriada:

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

## <a name="example-using-referenced-azure-automation-registration-values"></a>Exemplo utilizando valores de registo de automação Azure referenciados

O exemplo a seguir obtém o **RegistrationUrl** and **RegistrationKey,** referindo-se às propriedades da conta Azure Automation e utilizando o método  **listkeys** para recuperar a Chave Primária (0).  Neste exemplo, os parâmetros **automationAm o Nome** e **o NodeConfigName** foram fornecidos ao modelo.

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

## <a name="update-from-a-previous-format"></a>Atualização a partir de um formato anterior

Quaisquer configurações num formato anterior da extensão (e que tenham as propriedades públicas **ModulesUrl**, **ModuleSource**, **ModuleVersion**, **ConfigurationFunction,** **SasToken** ou **Properties**) adaptam-se automaticamente ao formato atual da extensão.
Correm como antes.

O esquema que se segue mostra como era o esquema de definições anteriores:

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

Eis como o formato anterior se adapta ao formato atual:

| Nome atual da propriedade | Equivalente de esquema anterior |
| --- | --- |
| definições.wmfVersion |Configurações. WMFVersion |
| settings.configuration.url |Configurações. MódulosUrl |
| settings.configuration.script |Primeira parte do settings.ConfigurationFunction \\ \\ (antes) |
| settings.configuration.function |Segunda parte do settings.ConfigurationFunction \\ \\ (depois) |
| settings.configuration.module.name | Configurações. MóduloSource |
| settings.configversão uration.module. | Configurações. ModíssliaVer |
| settings.configurationArments |Configurações. Propriedades |
| settings.configurationData.url |protectedSettings.DataBlobUri (sem token SAS) |
| definições.privacy.dataCollection |Configurações. Privacidade.dadosCollection |
| definições.advancedOptions.downloadMappings |Configurações. AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |Configurações. Rio SasToken |
| protectedSettings.configurationDataUrlSasToken |Ficha SAS de protectedSettings.DataBlobUri |

## <a name="troubleshooting"></a>Resolução de problemas

Aqui estão alguns dos erros que podes encontrar e como podes corrigi-los.

### <a name="invalid-values"></a>Valores inválidos

"Privacidade.dataMlection {0} é'
Os únicos valores possíveis são '', 'Ativar' e 'Desativar'".
"WmfVersion {0} é."
Só os valores possíveis são... e "último".

**Problema:** Não é permitido um valor fornecido.

**Solução**: Altere o valor inválido para um valor válido.
Para mais informações, consulte a tabela em [Detalhes.](#details)

### <a name="invalid-url"></a>URL inválido

"ConfigurationData.url {0} é'. Este não é um URL válido" "DataBlobUri {0} é" Este não é um URL válido" "Configuration.url {0} é ' Este não é um URL válido"

**Problema:** Um URL fornecido não é válido.

**Solução**: Verifique todos os urls fornecidos.
Certifique-se de que todos os URLs resolvem locais válidos que a extensão pode aceder na máquina remota.

### <a name="invalid-registrationkey-type"></a>Tipo Inválido De Registos

"Tipo inválido para registo de parâmetrosA do tipo PSCredential."

**Problema**: O valor *RegistreKey* em protectedSettings.configurationArguments não pode ser fornecido como qualquer tipo que não um PSCredential.

**Solução**: Altere a sua protectedSettings.configentrada urationArguments para RegistreKey para um tipo PSCredential utilizando o seguinte formato:

```json
"configurationArguments": {
    "RegistrationKey": {
        "userName": "NOT_USED",
        "Password": "RegistrationKey"
    }
}
```

### <a name="invalid-configurationargument-type"></a>Tipo de configuração inválido

"Configuração inválida Tipo de {0} argumentos"

**Problema**: A *propriedade ConfigurationArguments* não pode resolver-se com um objeto **de mesa Hash.**

**Solução**: Faça da sua *configuraçãoalos de* propriedade de uma **mesa Hash**.
Siga o formato fornecido nos exemplos anteriores. Cuidado com citações, vírgulas e aparelhos.

### <a name="duplicate-configurationarguments"></a>Configuração duplicadaResarguments

"Encontrem argumentos {0} duplicados" tanto em configurações públicas como protegidasArguments"

**Problema**: Os *argumentos de configuração* nas definições públicas e os *Argumentos de Configuração* em configurações protegidas têm propriedades com o mesmo nome.

**Solução**: Remova uma das propriedades duplicadas.

### <a name="missing-properties"></a>Propriedades em falta

"settings.Configuration.function requer que settings.configuration.url ou settings.configuration.module seja especificado"

"settings.Configuration.url requer que settings.configuration.script seja especificado"

"settings.Configuration.script requer que settings.configuration.url seja especificado"

"settings.Configuration.url requer que settings.configuration.fun seja especificado"

"protectedSettings.ConfigurationUrlSasToken requer que settings.configuration.url seja especificado"

"protectedSettings.ConfigurationDataUrlSasToken requer que settings.configurationData.url seja especificado"

**Problema:** Uma propriedade definida precisa de outra propriedade, que está em falta.

**Soluções:**

- Forneça a propriedade desaparecida.
- Remova a propriedade que precisa da propriedade desaparecida.

## <a name="next-steps"></a>Próximos passos

- Saiba mais [sobre a utilização de conjuntos de balança de máquinas virtuais com a extensão Azure DSC](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- Saiba mais detalhes sobre a [gestão de credenciais seguras da DSC.](dsc-credentials.md)
- Obtenha uma [introdução ao controlador de extensão Azure DSC](dsc-overview.md).
- Para obter mais informações sobre o PowerShell DSC, aceda ao [centro de documentação PowerShell](/powershell/scripting/dsc/overview/overview).
