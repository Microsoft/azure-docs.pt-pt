---
title: Extensão de configuração do Estado desejada com modelos de Gestor de Recursos Azure
description: Conheça a definição de modelo do Gestor de Recursos para a extensão de Configuração do Estado Desejado (DSC) em Azure.
services: virtual-machines-windows
author: bobbytreed
manager: carmonm
tags: azure-resource-manager
keywords: dSC
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/05/2018
ms.author: robreed
ms.openlocfilehash: 89bae60c30cfe82d38c61f385dbaef574b4152d8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547790"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Extensão de configuração do Estado desejada com modelos de Gestor de Recursos Azure

Este artigo descreve o modelo do Gestor de Recursos Azure para o manipulador de extensão de [configuração do Estado Desejado (DSC).](dsc-overview.md) Muitos dos exemplos utilizam o **RegistrationURL** (fornecido como string) e **a Chave** de Registo (fornecida como [PSCredential)](/dotnet/api/system.management.automation.pscredential)para bordo com a Automação Azure. Para mais detalhes sobre a obtenção desses valores, consulte máquinas de [embarque para gestão pela Configuração do Estado da Automação Azure - Registo seguro](/azure/automation/automation-dsc-onboarding#onboarding-securely-using-registration).

> [!NOTE]
> Pode encontrar exemplos de esquemas ligeiramente diferentes. A mudança de esquema ocorreu no lançamento de outubro de 2016. Para mais detalhes, consulte [Atualização de um formato anterior](#update-from-a-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Exemplo de modelo para um VM do Windows

O seguinte corte vai na secção **de Recursos** do modelo.
A extensão DSC herda propriedades de extensão por defeito.
Para mais informações, consulte a [classe VirtualMachineExtension](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet).

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

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Exemplo de modelo para conjuntos de escala de máquinavirtual do Windows

Um nó de conjunto de escala de máquina virtual tem uma secção **de propriedades** que tem um **VirtualMachineProfile, atributo de extensãoPerfil.**
Em **extensões,** adicione os detalhes para a extensão dSC.

A extensão DSC herda propriedades de extensão por defeito.
Para mais informações, consulte a [classe VirtualMachineScaleSetExtension](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

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

## <a name="detailed-settings-information"></a>Informações detalhadas sobre configurações

Utilize o seguinte esquema na secção de **definições** da extensão Azure DSC num modelo de Gestor de Recursos.

Para obter uma lista dos argumentos disponíveis para o script de configuração predefinido, consulte o script de [configuração Predefinido](#default-configuration-script).

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
| definições.wmfVersion |string |Especifica a versão do Windows Management Framework (WMF) que deve ser instalada no seu VM. A definição desta propriedade para a **mais recente** instala a versão mais recente da WMF. Atualmente, os únicos valores possíveis para este imóvel são **4.0**, **5.0**, **5.1**, e **mais recentes.** Estes valores possíveis estão sujeitos a atualizações. O valor predefinido é **mais recente.** |
| configurações.configuração.url |string |Especifica a localização do URL a partir da qual descarregar a configuração DSC .ficheiro zip. Se o URL fornecido necessitar de um token SAS para acesso, delineie a propriedade **protectedSettings.configurationUrlSasToken** para o valor do seu token SAS. Esta propriedade é necessária se **as configurações.configuração.script** ou **configurações.configuração.função** for definida. Se não for dado qualquer valor para estas propriedades, a extensão chama o script de configuração padrão para definir metadados do Gestor de Configuração de Localização (LCM) e os argumentos devem ser fornecidos. |
| configurações.configuração.script.script |string |Especifica o nome de ficheiro do script que contém a definição da sua configuração DSC. Este script deve estar na pasta raiz do ficheiro .zip que é descarregado a partir do URL especificado pela propriedade de **configurações.url.** Esta propriedade é necessária se **as configurações.configuração.url** ou **configurações.configuração.script.script** for definida. Se não for dado qualquer valor para estas propriedades, a extensão chama o script de configuração padrão para definir metadados LCM, e os argumentos devem ser fornecidos. |
| configurações.configuração.função |string |Especifica o nome da sua configuração DSC. A configuração que está nomeada deve ser incluída no script que **as configurações.configuração.script** define. Esta propriedade é necessária se **as configurações.configuração.url** ou **configurações.configuração.função** for definida. Se não for dado qualquer valor para estas propriedades, a extensão chama o script de configuração padrão para definir metadados LCM, e os argumentos devem ser fornecidos. |
| configurações.configuraçõesArguments |Coleção |Define quaisquer parâmetros que queira passar para a sua configuração DSC. Esta propriedade não está encriptada. |
| configurações.configuraçãoData.url |string |Especifica o URL a partir do qual descarregar os seus dados de configuração (.psd1) para utilizar como entrada para a sua configuração DSC. Se o URL fornecido necessitar de um token SAS para acesso, delineie a propriedade **ProtectedSettings.configurationDataUrlSasToken** para o valor do seu token SAS. |
| definições.privacy.dataCollection |string |Permite ou desativa a recolha de telemetria. Os únicos valores possíveis para esta propriedade são **Enable**, **Disable,** **'ou** **$null**. Deixar esta propriedade em branco ou nula permite a telemetria. O valor predefinido é **''**. Para mais informações, consulte a recolha de dados de [extensão Azure DSC.](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| definições.advancedOptions.downloadMappings |Coleção |Define locais alternativos a partir dos quais descarregar WMF. Para mais informações, consulte a [extensão Azure DSC 2.8 e como mapear os downloads das dependências de extensão para a sua própria localização](https://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx). |
| definições protegidas.configuraçãoArguments |Coleção |Define quaisquer parâmetros que queira passar para a sua configuração DSC. Esta propriedade está encriptada. |
| definições protegidasUrlSasToken |string |Especifica o token SAS para usar para aceder ao URL que **configura.configuração.url** define. Esta propriedade está encriptada. |
| definições protegidas.configuraçãoDataUrlSasToken |string |Especifica o token SAS para usar para aceder ao URL que **configura.configuraçãoData.url** define. Esta propriedade está encriptada. |

## <a name="default-configuration-script"></a>Script de configuração padrão

Para obter mais informações sobre os seguintes valores, consulte [as definições básicas do Gestor de Configuração Local](/powershell/scripting/dsc/managing-nodes/metaConfig#basic-settings).
Pode utilizar o script de configuração predefinido de extensão DSC para configurar apenas as propriedades LCM listadas na tabela seguinte.

| Nome da propriedade | Tipo | Descrição |
| --- | --- | --- |
| definições protegidas.configuraçõesArguments.RegistrationKey |PSCredential |Propriedade necessária. Especifica a chave que é usada para um nó para se registar no serviço De Automação Azure como a palavra-passe de um objeto credencial PowerShell. Este valor pode ser automaticamente descoberto utilizando o método **listkeys** contra a conta Automation.  Veja o [exemplo.](#example-using-referenced-azure-automation-registration-values) |
| definições.configuraçõesArguments.RegistrationUrl |string |Propriedade necessária. Especifica o URL do ponto final da Automatização onde o nó tenta registar-se. Este valor pode ser automaticamente descoberto utilizando o método de **referência** contra a conta Automation. |
| configurações.configuraçõesArguments.NodeConfigurationName |string |Propriedade necessária. Especifica a configuração do nó na conta Automation para atribuir ao nó. |
| configurações.configuraçõesArguments.Modo de Configuração |string |Especifica o modo para LCM. As opções válidas incluem **ApplyOnly,** **ApplyandMonitor**e **ApplyandAutoCorrect**.  O valor predefinido é **Apply andMonitor**. |
| configurações.configuraçõesArguments.RefreshFrequencyMins | uint32 | Especifica com que frequência a LCM tenta verificar com a conta Automation as atualizações.  O valor predefinido é **de 30**.  O valor mínimo é **de 15**. |
| configurações.configuraçõesArguments.ConfigurationModeFrequencyMins | uint32 | Especifica a frequência com que o LCM valida a configuração atual. O valor predefinido é **de 15**. O valor mínimo é **de 15**. |
| configurações.configuraçõesArguments.RebootNodeIfNeeded | boolean | Especifica se um nó pode ser automaticamente reiniciado se uma operação DSC o solicitar. O valor predefinido é **falso.** |
| configurações.configuraçõesArguments.ActionAfterReboot | string | Especifica o que acontece após um reboot ao aplicar uma configuração. As opções válidas são **ContinueConfiguration** e **StopConfiguration**. O valor predefinido é **ContinueConfiguration**. |
| configurações.configuraçõesArguments.AllowModuleOverwrite | boolean | Especifica se o LCM substitui os módulos existentes no nó. O valor predefinido é **falso.** |

## <a name="settings-vs-protectedsettings"></a>definições vs. definições protegidas

Todas as definições são guardadas num ficheiro de texto de definições no VM.
Os imóveis listados em **configurações** são propriedades públicas.
As propriedades públicas não estão encriptadas no ficheiro de texto de definições.
As propriedades listadas em **Definições protegidas** são encriptadas com um certificado e não são mostradas em texto simples no ficheiro de definições no VM.

Se a configuração necessitar de credenciais, pode incluir as credenciais em **Definições protegidas:**

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

O exemplo seguinte mostra o comportamento predefinido para a extensão DSC, que é fornecer definições de metadados para LCM e registar-se com o serviço DSC de Automatização.
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

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Exemplo usando o script de configuração no Armazenamento Azure

O exemplo que se segue é da visão geral do manipulador de [extensão DSC](dsc-overview.md).
Este exemplo utiliza modelos de Gestor de Recursos em vez de cmdlets para implementar a extensão.
Guarde a configuração IisInstall.ps1, coloque-a `iisinstall.zip`num ficheiro .zip (exemplo: ), e, em seguida, carregue o ficheiro num URL acessível.
Este exemplo utiliza o armazenamento Azure Blob, mas pode descarregar ficheiros .zip a partir de qualquer local arbitrário.

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

O exemplo seguinte obtém o **RegistrationUrl** e **RegistrationKey,** referindo-se às propriedades da conta Azure Automation e utilizando o método **listkeys** para recuperar a Chave Primária (0).  Neste exemplo, os parâmetros **automatizando O Nome de Conta** e o **NodeConfigName** foram fornecidos ao modelo.

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

## <a name="update-from-a-previous-format"></a>Atualização de um formato anterior

Quaisquer configurações num formato anterior da extensão (e que tenham as propriedades públicas **MódulosUrl,** **MóduloSource**, **ModuleVersion**, **ConfiguraçãoFunction,** **SasToken,** ou **Propriedades**) adaptam-se automaticamente ao formato atual da extensão.
Correm como antes.

O seguinte esquema mostra como eram as configurações anteriores:

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
| configurações.configuração.url |Configurações. MódulosUrl |
| configurações.configuração.script.script |Primeira parte das definições. Função de \\ \\Configuração (antes) |
| configurações.configuração.função |Segunda parte das definições. Função de \\ \\Configuração (depois) |
| settings.configuration.module.name | Configurações. MóduloSource |
| configurações.configuração.module.version | Configurações. Versão do módulo |
| configurações.configuraçõesArguments |Configurações. Propriedades |
| configurações.configuraçãoData.url |definições protegidas.DataBlobUri (sem token SAS) |
| definições.privacy.dataCollection |Configurações. Privacidade.dataColeção |
| definições.advancedOptions.downloadMappings |Configurações. AdvancedOptions.DownloadMappings |
| definições protegidas.configuraçãoArguments |definições protegidas.Propriedades |
| definições protegidasUrlSasToken |Configurações. SasToken |
| definições protegidas.configuraçãoDataUrlSasToken |Ficha SAS de Definições Protegidas.DataBlobUri |

## <a name="troubleshooting"></a>Resolução de problemas

Aqui estão alguns dos erros que pode sacá-los e como pode corrigi-los.

### <a name="invalid-values"></a>Valores inválidos

"Privacidade.dataCollection é{0}' '.
Os únicos valores possíveis são '', 'Enable' e 'Disable'".
"WmfVersion é'.{0}
Só os valores possíveis são... e 'mais recente'".

**Problema**: Não é permitido um valor fornecido.

**Solução**: Mude o valor inválido para um valor válido.
Para mais informações, consulte a tabela em [Detalhes](#details).

### <a name="invalid-url"></a>URL inválido

"ConfiguraçãoData.url{0}é ' '. Este não é um URL válido" "DataBlobUri é '{0}'. Isto não é um URL válido"{0}"Configuração.url é ' '. Este não é um URL válido"

**Problema**: Um URL fornecido não é válido.

**Solução**: Verifique todos os seus URLs fornecidos.
Certifique-se de que todos os URLs decidem locais válidos a que a extensão pode aceder na máquina remota.

### <a name="invalid-registrationkey-type"></a>Inscrição InválidaTipoTipo Tipo de chave

"Tipo inválido para registo de parâmetrosChave do tipo PSCredential."

**Problema**: O valor *chave de registo* em Definições protegidas.configuraçãoOs argumentos não podem ser fornecidos como qualquer tipo diferente de UM PSCredential.

**Solução**: Alterar as definições protegidas.configuraçãoA entrada para registoChave para um tipo PSCredential utilizando o seguinte formato:

```json
"configurationArguments": {
    "RegistrationKey": {
        "userName": "NOT_USED",
        "Password": "RegistrationKey"
    }
}
```

### <a name="invalid-configurationargument-type"></a>Tipo de argumento de configuração inválido

"Configuração inválidaTipoTipo {0}de argumentos"

**Problema**: A propriedade *ConfigurationArguments* não pode resolver com um objeto de **mesa Hash.**

**Solução**: Faça da sua propriedade *ConfigurationArguments* uma **tabela Hash**.
Siga o formato fornecido nos exemplos anteriores. Cuidado com as citações, vírgulas e aparelhos.

### <a name="duplicate-configurationarguments"></a>Argumentos de configuração duplicados

"Encontrados argumentos duplicados '{0}' em configurações públicas e protegidasArgumentos"

**Problema**: Os Argumentos de *Configuração* em configurações públicas e os Argumentos de *Configuração* em configurações protegidas têm propriedades com o mesmo nome.

**Solução**: Retire uma das propriedades duplicadas.

### <a name="missing-properties"></a>Propriedades desaparecidas

"configurações. Configuração.função requer que configurações.url.url ou configurações.configuração.módulo é especificado"

"configurações. Configuração.url requer que as configurações.configuração.script é especificada"

"configurações. Configuração.script requer que as configurações.configuração.url é especificada"

"configurações. Configuração.url requer que as configurações.configuração.função é especificada"

"Definições protegidas.ConfiguraçãoUrlSasToken requer que as configurações.configuração.url é especificada"

"definições protegidas.ConfiguraçãoDataUrlSasToken requer que as configurações Data.url seja especificada"

**Problema**: Uma propriedade definida precisa de outro imóvel, que está em falta.

**Soluções:**

- Forneça a propriedade desaparecida.
- Remova a propriedade que precisa da propriedade desaparecida.

## <a name="next-steps"></a>Passos seguintes

- Aprenda a [utilizar conjuntos de escala de máquina virtual com a extensão Azure DSC](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- Encontre mais detalhes sobre a [gestão segura da credencial da DSC.](dsc-credentials.md)
- Obtenha uma introdução ao manipulador de [extensão Azure DSC](dsc-overview.md).
- Para mais informações sobre o PowerShell DSC, vá ao centro de [documentação PowerShell](/powershell/scripting/dsc/overview/overview).
