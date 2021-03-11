---
title: Manipulador de extensão de configuração do estado desejado Azure
description: Carreje e aplique uma configuração DSC powerShell num VM Azure usando extensão DSC
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: bobbytreed
ms.author: robreed
ms.collection: windows
ms.date: 03/26/2018
ms.openlocfilehash: 72f66aeee64133a13ce0e49155c4b2a90240a3fb
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559992"
---
# <a name="powershell-dsc-extension"></a>Extensão DSC powershell

## <a name="overview"></a>Descrição Geral

A extensão DSC do PowerShell para windows é publicada e suportada pela Microsoft. A extensão carrega e aplica uma configuração DSC PowerShell num VM Azure. A extensão DSC chama o PowerShell DSC para decretar a configuração DSC recebida no VM. Este documento detalha as plataformas, configurações e opções de implementação suportadas para a extensão da máquina virtual DSC para Windows.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão do DSC suporta os seguintes OS's

Windows Server 2019, Windows Server 2016, Windows Server 2012R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão DSC para o Windows requer que a máquina virtual alvo seja capaz de comunicar com o Azure e a localização do pacote de configuração (.zip ficheiro) se for armazenado num local fora do Azure. 

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

### <a name="property-values"></a>Valores patrimoniais

| Name | Valor / Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2018-10-01 | data |
| publicador | Microsoft.Powershell.DSC | string |
| tipo | DSC | string |
| typeHandlerVersion | 2.77 | int |

### <a name="settings-property-values"></a>Definições Valores de propriedade

| Name | Tipo de Dados | Descrição
| ---- | ---- | ---- |
| definições.wmfVersion | string | Especifica a versão do Quadro de Gestão do Windows que deve ser instalada no seu VM. Definir esta propriedade para 'mais recente' instalará a versão mais atualizada do WMF. Os únicos valores atuais possíveis para esta propriedade são '4.0', '5.0' e 'último'. Estes valores possíveis estão sujeitos a atualizações. O valor predefinido é 'mais recente'. |
| settings.configuration.url | string | Especifica a localização do URL para descarregar o seu ficheiro zip de configuração DSC. Se o URL fornecido necessitar de um token SAS para acesso, você precisará definir a propriedade protectedSettings.configurationUrlSasToken para o valor do seu token SAS. Esta propriedade é necessária se settings.configuration.script e/ou settings.configuration.fun são definidos.
| settings.configuration.script | string | Especifica o nome do ficheiro do script que contém a definição da sua configuração DSC. Este script deve estar na pasta raiz do ficheiro zip descarregado a partir do URL especificado pela propriedade configuration.url. Esta propriedade é necessária se settings.configuration.url e/ou settings.configuration.script forem definidos.
| settings.configuration.function | string | Especifica o nome da sua configuração DSC. A configuração nomeada deve ser contida no script definido por configuração.script. Esta propriedade é necessária se settings.configuration.url e/ou settings.configuration.fun são definidos.
| settings.configurationArments | Coleção | Define quaisquer parâmetros que gostaria de passar para a sua configuração DSC. Esta propriedade não será encriptada.
| settings.configurationData.url | string | Especifica o URL a partir do qual descarrega o ficheiro de dados de configuração (.pds1) para utilizar como entrada para a sua configuração DSC. Se o URL fornecido necessitar de um token SAS para acesso, terá de definir a propriedade protectedSettings.configurationDataUrlSasToken para o valor do seu token SAS.
| definições.privacy.dataEnabled | string | Permite ou desativa a recolha de telemetria. Os únicos valores possíveis para este imóvel são 'Ativar', 'Desativar', ", ou $null. Deixar esta propriedade em branco ou nulo permitirá a telemetria
| definições.advancedOptions.forcePullAndApply | Booleano | Esta definição destina-se a melhorar a experiência de trabalhar com a extensão para registar nós com a Azure Automation DSC.  Se o valor `$true` for, a extensão aguardará a primeira execução da configuração retirada do serviço antes de devolver o sucesso/falha.  Se o valor for definido para $false, o estado devolvido pela extensão apenas refere-se ao facto de o nó ter sido registado com sucesso na Configuração do Estado da Automação Azure e a configuração do nó não ser executada durante o registo.
| definições.advancedOptions.downloadMappings | Coleção | Define localizações alternativas para descarregar dependências como WMF e .NET

### <a name="protected-settings-property-values"></a>Definições protegidas Valores de propriedade

| Name | Tipo de Dados | Descrição
| ---- | ---- | ---- |
| protectedSettings.configurationArments | string | Define quaisquer parâmetros que gostaria de passar para a sua configuração DSC. Esta propriedade será encriptada. |
| protectedSettings.configurationUrlSasToken | string | Especifica o token SAS para aceder ao URL definido por configuração.url. Esta propriedade será encriptada. |
| protectedSettings.configurationDataUrlSasToken | string | Especifica o token SAS para aceder ao URL definido por configurationData.url. Esta propriedade será encriptada. |


## <a name="template-deployment"></a>Implementação de modelos

As extensões Azure VM podem ser implementadas com modelos Azure Resource Manager.
Os modelos são ideais quando se implanta uma ou mais máquinas virtuais que requerem configuração de implantação de posts.
Um modelo de gestor de recursos de amostra que inclui a extensão DSC para windows pode ser encontrado na [Galeria Azure Quick Start](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91).

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das extensões podem ser recuperados a partir do portal Azure e utilizando o CLI Azure. Para ver o estado de implantação das extensões para um determinado VM, executar o seguinte comando utilizando o Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

O pacote de extensão é descarregado e implantado para este local no Azure VM
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

O ficheiro de estado de extensão contém o sub-estado e códigos de sucesso/erro de estado, juntamente com o erro e descrição detalhados de cada extensão.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Os registos de saída de extensão são registados no seguinte diretório:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Códigos de erro e os seus significados

| Código de Erro | Significado | Possível Ação |
| :---: | --- | --- |
| 1000 | Erro genérico | A mensagem para este erro é fornecida pela exceção específica nos registos de extensão |
| 52 | Erro de instalação de extensão | A mensagem para este erro é fornecida pela exceção específica |
| 1002 | Erro de instalação da WMF | Erro durante a instalação do WMF. |
| 1004 | Pacote Zip inválido | Zip inválido; Erro de desempacotar o fecho |
| 1100 | Erro de argumento | Indica um problema na entrada fornecida pelo utilizador. A mensagem para o erro é fornecida pela exceção específica|


### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
