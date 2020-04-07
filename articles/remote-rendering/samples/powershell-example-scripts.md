---
title: Exemplo de scripts PowerShell
description: Exemplos que mostram como usar a parte frontal através de scripts PowerShell
author: florianborn71
ms.author: flborn
ms.date: 02/12/2020
ms.topic: sample
ms.openlocfilehash: d7238c899301d76edde86d52c6db74468ffe18ad
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679479"
---
# <a name="example-powershell-scripts"></a>Exemplo de scripts PowerShell

A renderização remota Azure fornece as seguintes duas APIs REST:

- [Conversão REST API](../how-tos/conversion/conversion-rest-api.md)
- [Sessão REST API](../how-tos/session-rest-api.md)

O [repositório](https://github.com/Azure/azure-remote-rendering) de amostras ARR contém scripts de amostra na pasta *Scripts* para interagir com as APIs rest do serviço. Este artigo descreve o seu uso.

## <a name="prerequisites"></a>Pré-requisitos

Para executar os scripts de amostra, precisa de uma configuração funcional do [Azure PowerShell](https://docs.microsoft.com/powershell/azure/).

1. Instalar o Azure PowerShell:
    1. Abra um PowerShell com direitos de administração
    1. Executar:`Install-Module -Name Az -AllowClobber`

1. Se tiver erros sobre a execução de scripts, certifique-se de que a sua política de [execução](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) está definida adequadamente:
    1. Abra um PowerShell com direitos de administração
    1. Executar:`Set-ExecutionPolicy -ExecutionPolicy Unrestricted`

1. [Prepare uma conta de Armazenamento Azure](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)

1. Inicie sessão na subscrição que contém a sua conta de renderização remota Azure:
    1. Abra uma PowerShell
    1. Executar:`Connect-AzAccount -Subscription "<your Azure subscription id>"`

1. Descarregue a pasta *Scripts* do [repositório GithHub de renderização remota azure](https://github.com/Azure/azure-remote-rendering).

## <a name="configuration-file"></a>Ficheiro de configuração

Ao lado `.ps1` dos ficheiros `arrconfig.json` há um que precisa preencher:

```json
{
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure Portal>",
        "arrAccountKey": "<fill in the account key from the Azure Portal>",
        "region": "<select from available regions>"
    },
    "renderingSessionSettings": {
        "vmSize": "<select standard or premium>",
        "maxLeaseTime": "<hh:mm:ss>"
    },
  "assetConversionSettings": {
    "resourceGroup": "<resource group which contains the storage account you created, only needed when uploading or generating SAS>",
    "storageAccountName": "<name of the storage account you created>",
    "blobInputContainerName": "<input container inside the storage container>",
    "blobOutputContainerName": "<output container inside the storage container>",
    "localAssetDirectoryPath": "<fill in a path to a local directory containing your asset (and files referenced from it like textures)>",
    "inputFolderPath": "<optional: base folderpath in the input container for asset upload. uses / as dir separator>",
    "inputAssetPath": "<the path to the asset under inputcontainer/inputfolderpath pointing to the input asset e.g. box.fbx>",
    "outputFolderPath": "<optional: base folderpath in the output container - the converted asset and log files will be placed here>",
    "outputAssetFileName": "<optional: filename for the converted asset, this will be placed in the output container under the outputpath>"
  }
}
```

> [!CAUTION]
> Certifique-se de escapar corretamente às pestanas no caminho LocalAssetDirectoryPath\\\\utilizando backslashes duplos: " e use cortes para a frente "/" em todos os outros caminhos, como inputFolderPath e inputAssetPath.

### <a name="accountsettings"></a>contasDefinições

Para `arrAccountId` `arrAccountKey`e , consulte [Criar uma conta de renderização remota Azure](../how-tos/create-an-account.md).
Para `region` ver a [lista das regiões disponíveis.](../reference/regions.md)

### <a name="renderingsessionsettings"></a>renderizaçãoSessionSettings

Esta estrutura deve ser preenchida se quiser executar **RenderingSession.ps1**.

- **vmSize:** Seleciona o tamanho da máquina virtual. Selecione *standard* ou *premium*. Desligue as sessões de renderização quando já não precisar delas.
- **maxLeaseTime:** A duração pela qual pretende arrendar o VM. Será encerrado quando o contrato expirar. O tempo de arrendamento pode ser prolongado mais tarde (ver abaixo).

### <a name="assetconversionsettings"></a>assetConversãoDeDefinições

Esta estrutura deve ser preenchida se quiser executar **Conversão.ps1**.

Para mais detalhes, consulte [Prepare uma conta de Armazenamento Azure](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts).

## <a name="script-renderingsessionps1"></a>Script: RenderingSession.ps1

Este script é usado para criar, consultar e parar de renderizar sessões.

> [!IMPORTANT]
> Certifique-se de que preencheu as *definições* de conta e *renderizando* secções SessionSettings em arrconfig.json.

### <a name="create-a-rendering-session"></a>Criar uma sessão de renderização

Uso normal com um arrconfig.json totalmente preenchido:

```PowerShell
.\RenderingSession.ps1
```

O script chamará a gestão da [sessão REST API](../how-tos/session-rest-api.md) para girar um VM renderizador com as definições especificadas. No sucesso, recuperará a *sessãoId*. Em seguida, irá sondê-lo propriedades da sessão até que a sessão esteja pronta ou ocorreu um erro.

Para utilizar um ficheiro **config alternativo:**

```PowerShell
.\RenderingSession.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Pode **substituir as definições individuais** a partir do ficheiro config:

```PowerShell
.\RenderingSession.ps1 -Region <region> -VmSize <vmsize> -MaxLeaseTime <hh:mm:ss>
```

Para **iniciar apenas uma sessão sem sondagens,** pode utilizar:

```PowerShell
.\RenderingSession.ps1 -CreateSession
```

A *sessãoId* que o script recupera deve ser passada para a maioria dos outros comandos de sessão.

### <a name="retrieve-session-properties"></a>Recuperar propriedades da sessão

Para obter as propriedades de uma sessão, corra:

```PowerShell
.\RenderingSession.ps1 -GetSessionProperties -Id <sessionID> [-Poll]
```

Use `-Poll` para esperar até que a sessão esteja *pronta* ou tenha ocorrido um erro.

### <a name="list-active-sessions"></a>Lista de sessões ativas

```PowerShell
.\RenderingSession.ps1 -GetSessions
```

### <a name="stop-a-session"></a>Pare uma sessão

```PowerShell
.\RenderingSession.ps1 -StopSession -Id <sessionID>
```

### <a name="change-session-properties"></a>Alterar propriedades da sessão

Neste momento, apenas apoiamos a alteração do maxLeaseTime de uma sessão.

> [!NOTE]
> O tempo de arrendamento é sempre contado a partir do momento em que a sessão VM foi inicialmente criada. Assim, para prolongar o aluguer da sessão por mais uma hora, aumente *o maxLeaseTime* em uma hora.

```PowerShell
.\RenderingSession.ps1 -UpdateSession -Id <sessionID> -MaxLeaseTime <hh:mm:ss>
```

## <a name="script-conversionps1"></a>Script: Conversão.ps1

Este script é usado para converter modelos de entrada no formato de tempo de execução específico de Renderização Remota Azure.

> [!IMPORTANT]
> Certifique-se de que preencheu as secções de *Definições* de conta e *de assetConversionSettings* em arrconfig.json.

O script demonstra as duas opções para usar contas de armazenamento com o serviço:

- Conta de armazenamento ligada à conta de renderização remota Azure
- Fornecer acesso ao armazenamento através de Assinaturas de Acesso Partilhado (SAS)

### <a name="linked-storage-account"></a>Conta de armazenamento ligada

Depois de ter preenchido totalmente o arrconfig.json e ligado uma conta de armazenamento, pode utilizar o seguinte comando. A ligação da sua conta de armazenamento é descrita na [Create a Account](../how-tos/create-an-account.md#link-storage-accounts).

A utilização de uma conta de armazenamento ligada é a forma preferida de utilizar o serviço de conversão, uma vez que não há necessidade de gerar Assinaturas de Acesso Partilhado.

```PowerShell
.\Conversion.ps1
```

1. Faça upload de todos `assetConversionSettings.modelLocation` os ficheiros contidos no recipiente de entrada blob sob o dado`inputFolderPath`
1. Ligue para a conversão do [modelo REST API](../how-tos/conversion/conversion-rest-api.md) para iniciar a conversão do [modelo](../how-tos/conversion/model-conversion.md)
1. Sondagem do estado de conversão até que a conversão tenha sido bem sucedida ou falhada
1. Detalhes de saída da localização dos ficheiros convertidos (conta de armazenamento, recipiente de saída, caminho de ficheiro no recipiente)

### <a name="access-to-storage-via-shared-access-signatures"></a>Acesso ao armazenamento através de Assinaturas de Acesso Partilhado

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Isto irá:

1. Faça upload do `assetConversionSettings.localAssetDirectoryPath` ficheiro local do recipiente de entrada blob
1. Gerar um SAS URI para o recipiente de entrada
1. Gerar um SAS URI para o recipiente de saída
1. Ligue para a conversão do [modelo REST API](../how-tos/conversion/conversion-rest-api.md) para iniciar a conversão do [modelo](../how-tos/conversion/model-conversion.md)
1. Sondagem do estado de conversão até que a conversão tenha sido bem sucedida ou falhada
1. Detalhes de saída da localização dos ficheiros convertidos (conta de armazenamento, recipiente de saída, caminho de ficheiro no recipiente)
1. Saída de um SAS URI para o modelo convertido no recipiente de saída blob

### <a name="additional-command-line-options"></a>Opções adicionais da linha de comando

Para utilizar um ficheiro **config alternativo:**

```PowerShell
.\Conversion.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Para iniciar apenas a conversão do **modelo sem sondagens,** pode utilizar:

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

Pode **substituir as definições individuais** a partir do ficheiro config utilizando os seguintes interruptores de linha de comando:

* **Id:** ConversãoId usado com GetConversionStatus
* **ArrAccountId:** arrAccountId das definições de conta
* **ArrAccountKey:** substituição por arrAccountKey das definições de conta
* **Região:** substituição da região das definições de contas
* **ResourceGroup:** substituição de recursosGroup de assetConversionSettings
* **Nome da conta** de armazenamento: sobreposição para armazenamentoNome de conta de activosConversão
* **BlobInputContainerName:** sobreposição para blobInputContainer de assetConversionSettings
* **LocalAssetDirectoryPath:** substituição para localAssetDirectoryPath de AssetConversionSettings
* **InputAssetPath:** substituição para entradaAssetPath de assetConversionSettings
* **BlobOutputContainerName:** sobreposição para blobOutputContainerName of assetConversionSettings
* **OutputFolderPath:** sobreposição para a saídaFolderPath de assetConversionSettings
* **OutputAssetFileName:** sobreposição para outputAssetFileName de assetConversionSettings

Por exemplo, pode combinar algumas das opções dadas como esta:

```PowerShell
.\Conversion.ps1 -LocalAssetDirectoryPath "C:\\models\\box" -InputAssetPath box.fbx -OutputFolderPath another/converted/box -OutputAssetFileName newConversionBox.arrAsset
```

### <a name="run-the-individual-conversion-stages"></a>Executar as fases individuais de conversão

Se quiser executar etapas individuais do processo, pode utilizar:

Apenas fazer upload de dados do localAssetDirectoryPath dado

```PowerShell
.\Conversion.ps1 -Upload
```

Apenas inicie o processo de conversão de um modelo já carregado para o armazenamento de blob (não faça upload, não faça sondagem no estado de conversão) O script devolverá uma *conversãoId*.

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

E pode recuperar o estado de conversão desta conversão utilizando:

```PowerShell
.\Conversion.ps1 -GetConversionStatus -Id <conversionId> [-Poll]
```

Use `-Poll` para esperar até que a conversão esteja feita ou ocorreu um erro.

## <a name="next-steps"></a>Passos seguintes

- [Quickstart: Render um modelo com Unidade](../quickstarts/render-model.md)
- [Quickstart: Converter um modelo para renderização](../quickstarts/convert-model.md)
- [Conversão de modelo](../how-tos/conversion/model-conversion.md)
