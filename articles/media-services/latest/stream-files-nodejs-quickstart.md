---
title: Transmita ficheiros de vídeo com a Azure Media Services - Node.js
description: Siga os passos deste tutorial para criar uma nova conta Azure Media Services, codificar um ficheiro e transmiti-lo ao Azure Media Player.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: serviços de multimédia do azure, transmitir
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc, devx-track-js
ms.date: 02/17/2021
ms.author: inhenkel
ms.openlocfilehash: 0eb334111a8f5ffc63d0f858966e4e65f99d3b16
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095957"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---nodejs"></a>Tutorial: Codificar um ficheiro remoto baseado em URL e transmitir o vídeo - Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este tutorial mostra-lhe como é fácil codificar e começar a transmitir vídeos numa grande variedade de navegadores e dispositivos que utilizam o Azure Media Services. Um ficheiro de vídeo de entrada pode ser especificado usando URLs HTTPS, URLs SAS ou caminhos para ficheiros localizados no armazenamento de Azure Blob.

A amostra deste artigo codifica conteúdo que torna acessível através de um URL HTTPS. Note que, atualmente, o AMS v3 não suporta a codificação de transferência segmentada através de URLs HTTPS.

No final do tutorial saberá fazer upload, codificar e transmitir um vídeo utilizando um leitor de clientes HLS ou DASH.

![Reproduzir o vídeo](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Instalar [Node.js](https://nodejs.org/en/download/)
- [Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social.<br/>Lembre-se dos valores que utilizou para o nome do grupo de recursos e nome da conta dos Media Services.
- Siga os passos na [Access Azure Media Services API com o Azure CLI](./access-api-howto.md) e guarde as credenciais. Terá de usá-los para aceder à API.
- Caminhe pelo [Configure e Conecte-se com Node.js](./configure-connect-nodejs-howto.md) primeiro para entender como usar o Node.js cliente SDK

## <a name="download-and-configure-the-sample"></a>Descarregue e configuure a amostra

Clone um repositório GitHub que contenha a amostra de Node.js de streaming para a sua máquina utilizando o seguinte comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

A amostra está localizada na pasta [StreamFilesSample.](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample)

[Index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.ts) aberto em projeto descarregado. Atualize o ficheiro sample.env na pasta raiz com os valores e credenciais que obteve ao [aceder às APIs](./access-api-howto.md). Mude o nome do ficheiro sample.env para .env. 

O exemplo realiza as seguintes ações:

1. Cria uma **Transformação** (em primeiro lugar, verifica se existe a Transformação especificada). 
2. Cria uma saída **Ativo** que é usado como a saída de **Job** codificante.
1. Envia opcionalmente um ficheiro local utilizando a blob de armazenamento SDK.
1. Cria a entrada do **Job** que se baseia num URL HTTPS ou num ficheiro carregado
1. Submete o **trabalho** de codificação com a [predefinição de codificação de conteúdos conscientes,](./content-aware-encoding.md)utilizando a entrada e saída criadas anteriormente.
1. Verifica o estado da Tarefa.
1. Descarrega a saída do trabalho de codificação para uma pasta local.
1. Cria um **localizador de streaming** para utilizar no leitor.
1. Constrói URLs de streaming para HLS e DASH.
1. Reproduz o conteúdo numa aplicação para o jogador - Azure Media Player.

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

1. A aplicação descarrega ficheiros codificados. Crie uma pasta onde pretende que os ficheiros de saída vão e atualize o valor da variável **outputFolder** no ficheiro [index.ts.](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L59) Está definido para "Temporário" por defeito.
1. Abrir **o comando,** navegar no diretório da amostra e executar os seguintes comandos.
1. Alterar o diretório na pasta AMSv3Samples
    ```bash
    cd AMSv3Samples
    ```

1. Instale as embalagens utilizadas no packages.js
    ```bash
    npm install 
    ```

1. Altere o diretório na pasta StreamFilesSample
    ```bash
    cd StreamFilesSample
    ```

1. Lançar Código de Estúdio Visual a partir da Pasta AMSv3Samples. Isto é necessário para ser lançado a partir da pasta onde se encontra a pasta ".vscode" e tsconfig.jsnos ficheiros

    ```bash
    cd ..
    code .
    ```

Abra a pasta para StreamFilesSample e abra o ficheiro index.ts no editor visual Studio Code.
Enquanto está no ficheiro index.ts, prima F5 para lançar o depurar.


## <a name="test-with-azure-media-player"></a>Testar com o Leitor de Multimédia do Azure

Para testar a transmissão, este artigo utiliza o Leitor de Multimédia do Azure. Também pode utilizar qualquer jogador compatível com HLS ou DASH, como o jogador Shaka, HLS.js, Dash.js ou outros.

Deverá ser capaz de clicar no link gerador na amostra e lançar o leitor AMP com o manifesto DASH já carregado.

> [!NOTE]
> Se um leitor estiver alojado num site de https, confirme que atualiza o URL para “https”.

1. Abra um navegador web e navegue para [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. Na caixa **URL:**, cole um dos valores do URL de transmissão que recebeu quando executou a aplicação. 
 
     Pode colar o URL no formato HLS, Dash ou Smooth e o Azure Media Player mudará para um protocolo de streaming apropriado para reprodução no seu dispositivo automaticamente.
3. Prima **Atualizar Leitor**.

O Leitor de Multimédia do Azure pode ser utilizado para fins de teste, mas não deve ser utilizado num ambiente de produção. 

## <a name="clean-up-resources"></a>Limpar os recursos

Se já não precisar de nenhum dos recursos do seu grupo de recursos, incluindo os Serviços de Media e as contas de armazenamento que criou para este tutorial, elimine o grupo de recursos.

Execute o seguinte comando CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Mais documentação do programador para Node.js em Azure
- [Azure para desenvolvedores de & Node.js JavaScript](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Código fonte dos Serviços de Mídia no @azure/azure-sdk-for-js Git Hub repo](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Documentação do pacote Azure para desenvolvedores de Node.js](https://docs.microsoft.com/javascript/api/overview/azure/?view=azure-node-latest)

## <a name="see-also"></a>Ver também

- [Códigos de erro de trabalho](/rest/api/media/jobs/get#joberrorcode).
- [instalação npm @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure para desenvolvedores de & Node.js JavaScript](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Código fonte dos Serviços de Mídia no @azure/azure-sdk-for-js repo](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conceitos de Serviços de Mídia](concepts-overview.md)
