---
title: Manipulador de extensão de configuração de estado desejado do Azure | Microsoft Docs
description: Carregar e aplicar uma configuração de DSC do PowerShell em uma VM do Azure usando a extensão de DSC
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: robreed
ms.openlocfilehash: ee5a6c732bcb48cd347b8d87b95d2896d7230a08
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092379"
---
# <a name="powershell-dsc-extension"></a>Extensão de DSC do PowerShell

## <a name="overview"></a>Descrição geral

A extensão de DSC do PowerShell para Windows é publicada e tem suporte da Microsoft. A extensão carrega e aplica uma configuração de DSC do PowerShell em uma VM do Azure. A extensão DSC chama a DSC do PowerShell para aplicar a configuração de DSC recebida na VM. Este documento detalha as plataformas com suporte, as configurações e as opções de implantação para a extensão da máquina virtual DSC para Windows.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão DSC dá suporte ao seguinte sistema operacional

Windows Server 2019, Windows Server 2016, Windows Server 2012R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão de DSC para Windows requer que a máquina virtual de destino seja capaz de se comunicar com o Azure e o local do pacote de configuração (arquivo. zip) se ele estiver armazenado em um local fora do Azure. 

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a parte de configurações da extensão de DSC em um modelo de Azure Resource Manager. 

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
            "forcePullAndApply": false,
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
| publisher | Microsoft. PowerShell. DSC | Cadeia de caracteres |
| type | DSC | Cadeia de caracteres |
| typeHandlerVersion | 2,77 | int |

### <a name="settings-property-values"></a>Valores da propriedade de configurações

| Name | Tipo de Dados | Descrição
| ---- | ---- | ---- |
| settings.wmfVersion | Cadeia de caracteres | Especifica a versão do Windows Management Framework que deve ser instalada em sua VM. Definir essa propriedade como ' mais recente ' instalará a versão mais atualizada do WMF. Os únicos valores possíveis atuais para essa propriedade são ' 4,0 ', ' 5,0 ' e ' Latest '. Esses valores possíveis estão sujeitos a atualizações. O valor padrão é ' Latest '. |
| settings.configuration.url | Cadeia de caracteres | Especifica o local da URL do qual baixar o arquivo zip de configuração DSC. Se a URL fornecida exigir um token SAS para acesso, você precisará definir a propriedade protectedSettings. configurationUrlSasToken como o valor do seu token SAS. Essa propriedade será necessária se Settings. Configuration. script e/ou Settings. Configuration. Function forem definidas.
| settings.configuration.script | Cadeia de caracteres | Especifica o nome do arquivo do script que contém a definição de sua configuração DSC. Esse script deve estar na pasta raiz do arquivo zip baixado da URL especificada pela propriedade Configuration. URL. Essa propriedade será necessária se Settings. Configuration. URL e/ou Settings. Configuration. script forem definidos.
| settings.configuration.function | Cadeia de caracteres | Especifica o nome da sua configuração DSC. A configuração chamada deve estar contida no script definido por Configuration. script. Essa propriedade será necessária se Settings. Configuration. URL e/ou Settings. Configuration. Function forem definidos.
| settings.configurationArguments | Collection | Define quaisquer parâmetros que você gostaria de passar para sua configuração DSC. Esta propriedade não será criptografada.
| settings.configurationData.url | Cadeia de caracteres | Especifica a URL da qual baixar seu arquivo de dados de configuração (. pds1) para usar como entrada para sua configuração DSC. Se a URL fornecida exigir um token SAS para acesso, você precisará definir a propriedade protectedSettings. configurationDataUrlSasToken como o valor do seu token SAS.
| settings.privacy.dataEnabled | Cadeia de caracteres | Habilita ou desabilita a coleta de telemetria. Os únicos valores possíveis para essa propriedade são ' habilitar ', ' desabilitar ', "ou $null. Deixar essa propriedade em branco ou NULL permitirá a telemetria
| settings.advancedOptions.forcePullAndApply | Bool | Essa configuração foi projetada para aprimorar a experiência de trabalhar com a extensão para registrar nós com o DSC de Automação do Azure.  Se o valor for `$true`, a extensão aguardará a primeira execução da configuração extraída do serviço antes de retornar êxito/falha.  Se o valor for definido como $false, o status retornado pela extensão somente fará referência a se o nó foi registrado com êxito na configuração de estado da automação do Azure e a configuração do nó não será executada durante o registro.
| settings.advancedOptions.downloadMappings | Collection | Define locais alternativos para baixar dependências como WMF e .NET

### <a name="protected-settings-property-values"></a>Valores de propriedade de configurações protegidas

| Name | Tipo de Dados | Descrição
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | Cadeia de caracteres | Define quaisquer parâmetros que você gostaria de passar para sua configuração DSC. Esta propriedade será criptografada. |
| protectedSettings.configurationUrlSasToken | Cadeia de caracteres | Especifica o token SAS para acessar a URL definida por Configuration. URL. Esta propriedade será criptografada. |
| protectedSettings.configurationDataUrlSasToken | Cadeia de caracteres | Especifica o token SAS para acessar a URL definida por configurationData. URL. Esta propriedade será criptografada. |


## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager.
Os modelos são ideais ao implantar uma ou mais máquinas virtuais que exigem a configuração pós-implantação.
Um modelo do Resource Manager de exemplo que inclui a extensão de DSC para Windows pode ser encontrado na [Galeria de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91).

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Podem ser obtidos dados sobre o estado das implementações de extensão do portal do Azure e com a CLI do Azure. Para ver o estado de implementação de extensões para uma determinada VM, execute o seguinte comando com a CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

O pacote de extensão é baixado e implantado neste local na VM do Azure
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

O arquivo de status de extensão contém os códigos de status de êxito/erro de subseqüentes, juntamente com o erro detalhado e a descrição para cada execução de extensão.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Os logs de saída de extensão são registrados no seguinte diretório:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Códigos de erro e seus significados

| Código de Erro | Significado | Ação possível |
| :---: | --- | --- |
| 1000 | Erro genérico | A mensagem para esse erro é fornecida pela exceção específica nos logs de extensão |
| 52 | Erro de instalação da extensão | A mensagem para esse erro é fornecida pela exceção específica |
| 1002 | Erro de instalação do WMF | Erro ao instalar o WMF. |
| 1004 | Pacote zip inválido | Zip inválido; Erro ao desempacotar o zip |
| 1100 | Erro de argumento | Indica um problema na entrada fornecida pelo usuário. A mensagem para o erro é fornecida pela exceção específica|


### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
