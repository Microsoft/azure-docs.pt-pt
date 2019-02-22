---
title: Processador de extensões de Desired State Configuration do Azure | Documentos da Microsoft
description: Carregar e aplicar uma configuração de DSC de PowerShell na VM do Azure com a extensão de DSC
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: robreed
ms.openlocfilehash: 26b083069380d7bf107cd3be54cb2e4786789e11
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593868"
---
# <a name="powershell-dsc-extension"></a>Extensão de DSC de PowerShell

## <a name="overview"></a>Descrição geral

A extensão de DSC de PowerShell para Windows é publicada e suportada pela Microsoft. A extensão carrega e aplica-se uma configuração de DSC de PowerShell numa VM do Azure. Chama a extensão DSC do PowerShell DSC adotar a configuração de DSC recebida na VM. Este documento detalha as plataformas suportadas, configurações e opções de implementação para a extensão de máquina virtual de DSC para Windows.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão de DSC suporta o SO seguinte

Windows Server 2019, Windows Server 2016, Windows Server 2012R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão de DSC para o Windows requer que a máquina virtual de destino é capaz de comunicar com o Azure e a localização do pacote de configuração (ficheiro. zip), se ele é armazenado num local fora do Azure. 

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a parte de definições da extensão do DSC num modelo Azure Resource Manager. 

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-10-01",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
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
            "forcePullAndApply": false
            "downloadMappings": {
                "specificDependencyKey": "https://myCustomDependencyLocation"
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
  }
}
```

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor / exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2018-10-01 | date |
| publicador | Microsoft.Powershell.DSC | cadeia |
| tipo | DSC | cadeia |
| typeHandlerVersion | 2.77 | int |

### <a name="settings-property-values"></a>Valores de propriedade de definições

| Name | Tipo de Dados | Descrição
| ---- | ---- | ---- |
| settings.wmfVersion | cadeia | Especifica a versão do Windows Management Framework que deve ser instalado na sua VM. Definir essa propriedade como 'mais recente' irá instalar a versão mais atualizada do WMF. Os valores possíveis apenas atuais para esta propriedade são "4.0", '5.0' e 'mais recente'. Estes valores possíveis são sujeitos a atualizações. O valor predefinido é "mais recente". |
| settings.configuration.url | cadeia | Especifica a localização de URL para transferir o ficheiro de zip de configuração de DSC. Se o URL fornecido necessita de um token SAS para o acesso, terá de definir a propriedade de protectedSettings.configurationUrlSasToken como o valor do seu token SAS. Esta propriedade é necessária se settings.configuration.script e/ou settings.configuration.function são definidos.
| settings.configuration.script | cadeia | Especifica o nome de ficheiro do script que contém a definição da sua configuração de DSC. Este script tem de ser na pasta raiz do ficheiro zip transferido a partir do URL especificado pela propriedade configuration.url. Esta propriedade é necessária se settings.configuration.url e/ou settings.configuration.script são definidos.
| settings.configuration.function | cadeia | Especifica o nome da sua configuração de DSC. A configuração com o nome tem de estar contida no script definido pelo configuration.script. Esta propriedade é necessária se settings.configuration.url e/ou settings.configuration.function são definidos.
| settings.configurationArguments | Coleção | Define quaisquer parâmetros que pretende passar para a configuração de DSC. Esta propriedade não será encriptada.
| settings.configurationData.url | cadeia | Especifica o URL para transferir o ficheiro de dados (.pds1) de configuração para utilizar como entrada para a sua configuração de DSC. Se o URL fornecido necessita de um token SAS para o acesso, terá de definir a propriedade de protectedSettings.configurationDataUrlSasToken como o valor do seu token SAS.
| settings.privacy.dataEnabled | cadeia | Ativa ou desativa a coleção de telemetria. Os valores apenas possíveis para esta propriedade são "Ativar", "Desativar", ", ou $null. Deixar esta propriedade está em branco ou nulo irá ativar a telemetria
| settings.advancedOptions.forcePullAndApply | Bool | Permite que a extensão de DSC atualizar e aplicá configurações de DSC quando o modo de atualização é a solicitação.
| settings.advancedOptions.downloadMappings | Coleção | Define locais alternativos para transferir as dependências, como o WMF e .NET

### <a name="protected-settings-property-values"></a>Valores das propriedades de definições protegidos

| Name | Tipo de Dados | Descrição
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | cadeia | Define quaisquer parâmetros que pretende passar para a configuração de DSC. Esta propriedade será encriptada. |
| protectedSettings.configurationUrlSasToken | cadeia | Especifica o token SAS para aceder ao URL definido pelo configuration.url. Esta propriedade será encriptada. |
| protectedSettings.configurationDataUrlSasToken | cadeia | Especifica o token SAS para aceder ao URL definido pelo configurationData.url. Esta propriedade será encriptada. |


## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager.
Os modelos são ideais quando implementar um ou mais máquinas virtuais que necessitam de configuração pós-implementação.
Um modelo do Resource Manager de exemplo que inclui a extensão de DSC para o Windows pode ser encontrado no [Galeria de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91).

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Podem ser obtidos dados sobre o estado das implementações de extensão do portal do Azure e com a CLI do Azure. Para ver o estado de implementação de extensões para uma determinada VM, execute o seguinte comando com a CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Pacote de extensão é transferida e implementado para esta localização na VM do Azure
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

Ficheiro de estado de extensão contém o estado de sub-rotina e códigos de estado de erros/com êxito, juntamente com o erro detalhado e uma descrição para cada extensão de executar.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Registos de saída de extensão são registados no diretório seguinte:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Códigos de erro e seus significados

| Código de Erro | Significado | Ação possível |
| :---: | --- | --- |
| 1000 | Erro genérico | A mensagem para este erro é fornecida pela exceção específica nos registos de extensão |
| 52 | Erro de instalação da extensão | A mensagem para este erro é fornecida pela exceção específica |
| 1002 | Erro de instalação de WMF | Erro durante a instalação do WMF. |
| 1004 | Pacote Zip inválido | Zip inválido; Erro ao descompactar o zip |
| 1100 | Erro de argumento | Indica um problema na entrada fornecido pelo usuário. A mensagem de erro é fornecida pela exceção específica|


### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
