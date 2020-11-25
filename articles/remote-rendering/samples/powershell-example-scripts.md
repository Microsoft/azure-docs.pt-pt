---
title: Scripts do PowerShell de exemplo
description: Exemplos que mostram como usar a extremidade frontal através de scripts PowerShell
author: florianborn71
ms.author: flborn
ms.date: 02/12/2020
ms.topic: sample
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cb8cc98a020cb382a6941c1e410eab4543594629
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009760"
---
# <a name="example-powershell-scripts"></a>Scripts do PowerShell de exemplo

A Azure Remote Rendering fornece as seguintes duas APIs DE REST:

- [API DE REPOUSO de Conversão](../how-tos/conversion/conversion-rest-api.md)
- [Sessão REST API](../how-tos/session-rest-api.md)

O [repositório de amostras de ARR](https://github.com/Azure/azure-remote-rendering) contém scripts de amostra na pasta *Scripts* para interagir com as APIs REST do serviço. Este artigo descreve o seu uso.

> [!TIP]
> Há também uma [ferramenta baseada em UI chamada ARRT](azure-remote-rendering-asset-tool.md) para interagir com o serviço, que é uma alternativa conveniente para usar scripts. ![ARRT](./media/azure-remote-rendering-asset-tool.png "Screenshot arrt")

> [!CAUTION]
> Chamar as funções de API REST com demasiada frequência fará com que o servidor se ausse e volte a falhar eventualmente. O código de falha http id neste caso é 429 ("muitos pedidos"). Em regra, deve haver um atraso de **5-10 segundos entre chamadas subsequentes**.

## <a name="prerequisites"></a>Pré-requisitos

Para executar os scripts de amostra, precisa de uma configuração funcional do [Azure PowerShell](/powershell/azure/).

1. Instalar o Azure PowerShell:
    1. Abra uma janela PowerShell com direitos de administração.
    1. Corra: `Install-Module -Name Az -AllowClobber`

1. Se tiver erros em executar scripts, certifique-se de que a sua [política de execução](/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) está definida adequadamente:
    1. Abra uma janela PowerShell com direitos de administração.
    1. Corra: `Set-ExecutionPolicy -ExecutionPolicy Unrestricted`

1. [Preparar uma conta de Armazenamento Azure](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)

1. Faça login na sua subscrição contendo a sua conta de renderização remota Azure:
    1. Abra uma janela do PowerShell.
    1. Corra: `Connect-AzAccount` e siga as instruções no ecrã.

    > [!NOTE]
    > Caso a sua organização tenha mais do que uma subscrição, poderá necessitar de especificar os argumentos do SubscriptionId e do Inquilino. Encontre detalhes na [documentação Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

1. Descarregue a pasta *Scripts* a partir do [repositório GitHub de renderização remota Azure.](https://github.com/Azure/azure-remote-rendering)

## <a name="configuration-file"></a>Ficheiro de configuração

Ao lado dos `.ps1` ficheiros há um `arrconfig.json` que precisa de preencher:

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
> Certifique-se de que escapa corretamente às costas no caminho LocalAssetDirectoryPath utilizando duas costas: \\ \\ " e use barras para a frente "/" em todos os outros caminhos, como inputFolderPath e inputAssetPath.

> [!CAUTION]
> Os valores opcionais têm de ser preenchidos ou é necessário remover completamente a chave e o valor. Por exemplo, se não utilizar o  `"outputAssetFileName"` parâmetro, tem de eliminar toda a linha interior `arrconfig.json` .

### <a name="accountsettings"></a>contasSettings

Para `arrAccountId` e , consulte Criar uma conta de `arrAccountKey` [renderização remota Azure](../how-tos/create-an-account.md).
Para `region` ver a lista das [regiões disponíveis.](../reference/regions.md)

### <a name="renderingsessionsettings"></a>renderingSessionSettings

Esta estrutura deve ser preenchida se quiser correr **RenderingSession.ps1:**

- **vmSize:** Selecione o tamanho da máquina virtual. Selecione [*standard*](../reference/vm-sizes.md) ou [*premium*](../reference/vm-sizes.md). Desligue as sessões de renderização quando já não precisar delas.
- **maxLeaseTime:** A duração para a qual pretende arrendar o VM. Será encerrado quando o contrato expirar. O tempo de locação pode ser prolongado mais tarde (ver abaixo).

### <a name="assetconversionsettings"></a>activosConversionSettings

Esta estrutura deve ser preenchida se quiser correr **Conversion.ps1**.

Para mais detalhes, consulte [preparar uma conta de Armazenamento Azure](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts).

## <a name="script-renderingsessionps1"></a>Script: RenderingSession.ps1

Este script é usado para criar, consultar e parar sessões de renderização.

> [!IMPORTANT]
> Certifique-se de que preencheu as *secções de contasSes* e *renderingSesettings* em arrconfig.js.

### <a name="create-a-rendering-session"></a>Criar uma sessão de renderização

Utilização normal com um arrconfig.jscompletamente preenchido em:

```PowerShell
.\RenderingSession.ps1
```

O script chamará a gestão da [sessão REST API](../how-tos/session-rest-api.md) para girar um VM de renderização com as definições especificadas. No sucesso, recuperará a *sessãoId.* Em seguida, irá sondar as propriedades da sessão até que a sessão esteja pronta ou ocorreu um erro.

Para utilizar um ficheiro **config alternativo:**

```PowerShell
.\RenderingSession.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Pode **substituir as definições individuais** do ficheiro config:

```PowerShell
.\RenderingSession.ps1 -Region <region> -VmSize <vmsize> -MaxLeaseTime <hh:mm:ss>
```

Para iniciar apenas **uma sessão sem sondagens,** pode utilizar:

```PowerShell
.\RenderingSession.ps1 -CreateSession
```

A *sessãoId* que o script recupera deve ser passada para a maioria dos comandos de sessão.

### <a name="retrieve-session-properties"></a>Recuperar propriedades de sessão

Para obter as propriedades de uma sessão, corra:

```PowerShell
.\RenderingSession.ps1 -GetSessionProperties -Id <sessionID> [-Poll]
```

Utilize `-Poll` para aguardar até que a sessão esteja *pronta* ou tenha ocorrido um erro.

### <a name="list-active-sessions"></a>Listar sessões ativas

```PowerShell
.\RenderingSession.ps1 -GetSessions
```

### <a name="stop-a-session"></a>Parar uma sessão

```PowerShell
.\RenderingSession.ps1 -StopSession -Id <sessionID>
```

### <a name="change-session-properties"></a>Alterar propriedades da sessão

De momento, só apoiamos a alteração do maxLeaseTime de uma sessão.

> [!NOTE]
> O tempo de arrendamento é sempre contado a partir do momento em que a sessão VM foi inicialmente criada. Assim, para prolongar o arrendamento da sessão por mais uma hora, aumente *o maxLeaseTime* em uma hora.

```PowerShell
.\RenderingSession.ps1 -UpdateSession -Id <sessionID> -MaxLeaseTime <hh:mm:ss>
```

## <a name="script-conversionps1"></a>Script: Conversion.ps1

Este script é utilizado para converter modelos de entrada no formato de execução específico de renderização remota Azure.

> [!IMPORTANT]
> Certifique-se de que preencheu as *secções de contasSettings* e *activosConversionSettings* em arrconfig.js.

O script demonstra as duas opções de utilização de contas de armazenamento com o serviço:

- Conta de armazenamento ligada à Conta de Renderização Remota Azure
- Fornecer acesso ao armazenamento através de Assinaturas de Acesso Partilhado (SAS)

### <a name="linked-storage-account"></a>Conta de armazenamento ligada

Uma vez preenchido completamente arrconfig.jse ligado a uma conta de armazenamento, pode utilizar o seguinte comando. A ligação da sua conta de armazenamento é descrita na [Criar uma Conta](../how-tos/create-an-account.md#link-storage-accounts).

A utilização de uma conta de armazenamento ligada é a forma preferida de utilizar o serviço de conversão, uma vez que não há necessidade de gerar Assinaturas de Acesso Partilhado.

```PowerShell
.\Conversion.ps1
```

1. Faça o upload de todos os ficheiros contidos no `assetConversionSettings.modelLocation` recipiente de bolhas de entrada sob o `inputFolderPath` dado ..
1. Ligue para a conversão do [modelo REST API](../how-tos/conversion/conversion-rest-api.md) para iniciar a conversão do [modelo](../how-tos/conversion/model-conversion.md)
1. Regisi-se o estado de conversão até que a conversão tenha sido bem sucedida ou falhada.
1. Detalhes de saída da localização do ficheiro convertido (conta de armazenamento, recipiente de saída, caminho de arquivo no recipiente).

### <a name="access-to-storage-via-shared-access-signatures"></a>Acesso ao armazenamento através de assinaturas de acesso partilhado

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Isto será:

1. Faça o upload do ficheiro local do `assetConversionSettings.localAssetDirectoryPath` recipiente de entrada blob.
1. Gere um SAS URI para o recipiente de entrada.
1. Gere um SAS URI para o recipiente de saída.
1. Ligue para a conversão do [modelo REST API](../how-tos/conversion/conversion-rest-api.md) para iniciar a conversão do [modelo](../how-tos/conversion/model-conversion.md).
1. Regisi-se o estado de conversão até que a conversão tenha sido bem sucedida ou falhada.
1. Detalhes de saída da localização do ficheiro convertido (conta de armazenamento, recipiente de saída, caminho de arquivo no recipiente).
1. Desata um SAS URI para o modelo convertido no recipiente de bolhas de saída.

### <a name="additional-command-line-options"></a>Opções adicionais de linha de comando

Para utilizar um ficheiro **config alternativo:**

```PowerShell
.\Conversion.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Para iniciar apenas **a conversão de modelos sem sondagens,** pode utilizar:

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

Pode **substituir as definições individuais** do ficheiro config utilizando os seguintes comutadores de linha de comando:

* **Id:** ConversionId usado com GetConversionStatus
* **ArrAccountId:** arrAccountD de accountsettings
* **ArrAccountKey:** substituição por arrAccountKey de contasSetings
* **Região:** sobreposição de regiões de contasSetings
* **Grupo de Recursos:** substituição de recursos Grupo de activosConversionSettings
* **ArmazenamentoCocountName:** substituição para armazenamentoAcontão de activosConversionSettings
* **BlobInputContainerName:** substituição de blobInputContainer de activosConversionSettings
* **LocalAssetDirectoryPath:** substituição para localAssetDirectoryPath de activosConversionSettings
* **InputAssetPath:** substituição para inputAssetPath de activosConversionSettings
* **BlobOutputContainerName:** substituição para blobOutputContainerName de activosConversionSettings
* **OutputFolderPath:** substituição para a saídaFolderPath de activosConversionSettings
* **OutputAssetFileName:** substituição para outputAssetFileName de activosConversionSettings

Por exemplo, pode combinar uma série de opções dadas como esta:

```PowerShell
.\Conversion.ps1 -LocalAssetDirectoryPath "C:\\models\\box" -InputAssetPath box.fbx -OutputFolderPath another/converted/box -OutputAssetFileName newConversionBox.arrAsset
```

### <a name="run-the-individual-conversion-stages"></a>Executar as fases de conversão individuais

Se quiser executar etapas individuais do processo, pode utilizar:

Faça o upload de dados a partir do LocalAssetDirectoryPath.

```PowerShell
.\Conversion.ps1 -Upload
```

Basta iniciar o processo de conversão de um modelo já carregado para o armazenamento de bolhas (não executar Upload, não sondar o estado de conversão) O script devolverá uma *conversãoId*.

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

E pode recuperar o estado de conversão desta conversão utilizando:

```PowerShell
.\Conversion.ps1 -GetConversionStatus -Id <conversionId> [-Poll]
```

Utilize `-Poll` para aguardar até que a conversão seja feita ou ocorreu um erro.

## <a name="next-steps"></a>Passos seguintes

- [Quickstart: Renderiza um modelo com Unidade](../quickstarts/render-model.md)
- [Início Rápido: Converter um modelo para composição](../quickstarts/convert-model.md)
- [Conversão de modelo](../how-tos/conversion/model-conversion.md)