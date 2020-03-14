---
title: Manipulador de extensão de configuração de estado desejado Azure
description: Carregar e aplicar uma configuração PowerShell DSC num VM Azure utilizando extensão DSC
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
ms.openlocfilehash: 592c731d1851ac36cf9b57864750df0603b6c3fd
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253965"
---
# <a name="powershell-dsc-extension"></a>Extensão Powershell DSC

## <a name="overview"></a>Descrição geral

A extensão PowerShell DSC para Windows é publicada e suportada pela Microsoft. A extensão carrega e aplica uma configuração DeSC PowerShell num VM Azure. A extensão DSC chama o PowerShell DSC para decretar a configuração DSC recebida no VM. Este documento detalha as plataformas, configurações e opções de implementação suportadas para a extensão da máquina virtual DSC para windows.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão DSC suporta os seguintes Os's

Windows Server 2019, Windows Server 2016, Windows Server 2012R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão DSC para windows requer que a máquina virtual alvo seja capaz de comunicar com o Azure e a localização do pacote de configuração (.ficheiro zip) se for armazenada num local fora do Azure. 

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a parte de definições da extensão DSC num modelo de Gestor de Recursos Azure. 

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
| publicador | Microsoft.Powershell.DSC | string |
| tipo | DSC | string |
| typeHandlerVersion | 2.77 | int |

### <a name="settings-property-values"></a>Definições Valores de propriedade

| Nome | Tipo de Dados | Descrição
| ---- | ---- | ---- |
| settings.wmfVersion | string | Especifica a versão do Quadro de Gestão do Windows que deve ser instalada no seu VM. A definição desta propriedade para 'mais recente' instalará a versão mais atualizada da WMF. Os únicos valores atuais possíveis para este imóvel são '4.0', '5.0', e 'mais recentes'. Estes valores possíveis estão sujeitos a atualizações. O valor predefinido é "mais recente". |
| settings.configuration.url | string | Especifica a localização do URL a partir da qual descarregar o ficheiro zip de configuração DSC. Se o URL fornecido necessitar de um token SAS para acesso, terá de definir a propriedade ProtectedSettings.configurationUrlSasToken ao valor do seu token SAS. Esta propriedade é necessária se as configurações.configuração.script e/ou configurações.configuração.função for definida.
| settings.configuration.script | string | Especifica o nome de ficheiro do script que contém a definição da sua configuração DSC. Este script deve estar na pasta raiz do ficheiro zip descarregado a partir do URL especificado pela propriedade configuração.url. Esta propriedade é necessária se as configurações.configuração.url e/ou configurações.configuração.script for definida.
| settings.configuration.function | string | Especifica o nome da sua configuração DSC. A configuração nomeada deve ser contida no script definido pela configuração.script. Esta propriedade é necessária se as configurações.configuração.url e/ou configurações.configuração.função for definida.
| settings.configurationArguments | Coleção | Define quaisquer parâmetros que queira passar para a sua configuração DSC. Esta propriedade não será encriptada.
| settings.configurationData.url | string | Especifica o URL a partir do qual descarregar os seus dados de configuração (.pds1) para usar como entrada para a sua configuração DSC. Se o URL fornecido necessitar de um token SAS para acesso, terá de definir a propriedade ProtectedSettings.configurationDataUrlSasToken ao valor do seu token SAS.
| settings.privacy.dataEnabled | string | Permite ou desativa a recolha de telemetria. Os únicos valores possíveis para esta propriedade são 'Enable', 'Disable', ou $null. Deixar esta propriedade em branco ou nula permitirá a telemetria
| settings.advancedOptions.forcePullAndApply | Bool | Esta definição destina-se a melhorar a experiência de trabalhar com a extensão para registar nós com o Azure Automation DSC.  Se o valor for `$true`, a extensão aguardará a primeira execução da configuração retirada do serviço antes de devolver o sucesso/falha.  Se o valor for definido para $false, o estado devolvido pela extensão apenas se referirá se o nó foi registado com sucesso na Configuração do Estado da Automação Azure e a configuração do nó não será executada durante o registo.
| settings.advancedOptions.downloadMappings | Coleção | Define locais alternativos para descarregar dependências como WMF e .NET

### <a name="protected-settings-property-values"></a>Valores de propriedade de Definições Protegidas

| Nome | Tipo de Dados | Descrição
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | string | Define quaisquer parâmetros que queira passar para a sua configuração DSC. Esta propriedade será encriptada. |
| protectedSettings.configurationUrlSasToken | string | Especifica o token SAS para aceder ao URL definido pela configuração.url. Esta propriedade será encriptada. |
| protectedSettings.configurationDataUrlSasToken | string | Especifica o token SAS para aceder ao URL definido por configuraçãoData.url. Esta propriedade será encriptada. |


## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager.
Os modelos são ideais ao implantar uma ou mais máquinas virtuais que requerem a configuração da implementação do post.
Um modelo de Gestor de Recursos de amostra que inclui a extensão DSC para Windows pode ser encontrado na [Galeria De Arranque Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91).

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Podem ser obtidos dados sobre o estado das implementações de extensão do portal do Azure e com a CLI do Azure. Para ver o estado de implementação de extensões para uma determinada VM, execute o seguinte comando com a CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

O pacote de extensão é descarregado e implantado para este local no Azure VM
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

O ficheiro de estado de extensão contém os códigos de sucesso/erro do estado sub-status, juntamente com o erro e descrição detalhados para cada execução de extensão.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Os registos de saída de extensão são registados no seguinte diretório:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Códigos de erro e seus significados

| Código de Erro | Significado | Ação possível |
| :---: | --- | --- |
| 1000 | Erro genérico | A mensagem para este erro é fornecida pela exceção específica nos registos de extensão |
| 52 | Erro de instalação de extensão | A mensagem para este erro é fornecida pela exceção específica |
| 1002 | Erro de instalação da WMF | Erro ao instalar o WMF. |
| 1004 | Pacote Zip inválido | Fecho inválido; Erro desembalar o fecho |
| 1100 | Erro de argumento | Indica um problema na entrada fornecida pelo utilizador. A mensagem para o erro é fornecida pela exceção específica|


### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o suporte do [Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
