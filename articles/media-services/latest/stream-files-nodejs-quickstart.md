---
title: Como codificar e transmitir ficheiros de vídeo com Node.JS
description: Como transmitir ficheiros de vídeo com Node.JS. Siga os passos deste tutorial para criar uma nova conta Azure Media Services, codificar um ficheiro e transmiti-lo ao Azure Media Player.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: serviços de comunicação azul, stream, Node.JS
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: inhenkel
ms.openlocfilehash: 566a7a978a08bf7e37b6c4ab8b0609222b34e549
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724528"
---
# <a name="how-to-encode-and-stream-video-files-with-nodejs"></a>Como codificar e transmitir ficheiros de vídeo com Node.JS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este início rápido mostra como é fácil codificar e começar a transmitir vídeos numa ampla variedade de browsers e dispositivos com os Serviços de Multimédia do Azure. Um ficheiro de vídeo de entrada pode ser especificado usando URLs HTTPS, URLs SAS ou caminhos para ficheiros localizados no armazenamento de Azure Blob.

No final desta rapidez saberá:

- Como codificar com Node.JS
- Como transmitir com Node.JS
- Como fazer o upload de um ficheiro a partir de um URL HTTPS com Node.JS
- Como utilizar um leitor de clientes HLS ou DASH com Node.JS

A amostra deste artigo codifica conteúdo que torna acessível através de um URL HTTPS. Note que, atualmente, o AMS v3 não suporta a codificação de transferência segmentada através de URLs HTTPS.

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

[Index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.ts) aberto no seu projeto descarregado. Atualize o ficheiro *sample.env* na pasta raiz com os valores e credenciais que obteve ao [aceder às APIs](./access-api-howto.md). Mude o nome do ficheiro *sample.env* para *.env* (Sim, apenas a extensão).

O exemplo realiza as seguintes ações:

1. Cria uma **transformação** com uma [predefinição de codificação consciente do conteúdo.](./content-aware-encoding.md) Verifica primeiro se existe a Transformação especificada.
1. Cria um **Ativo** de saída que é usado pelo **trabalho** de codificação para conter a saída
1. Envia opcionalmente um ficheiro local usando o Storage Blob SDK
1. Cria a entrada **Job** que se baseia num URL HTTPS ou num ficheiro carregado
1. Submete o **trabalho** de codificação, usando a entrada e saída que foi criada anteriormente
1. Verifica o estado do Trabalho
1. Descarrega a saída do trabalho de codificação para uma pasta local
1. Cria um **localizador de streaming** para usar no leitor
1. Constrói URLs de streaming para HLS e DASH
1. Reproduz o conteúdo de volta numa aplicação do jogador - Azure Media Player

## <a name="run-the-sample"></a>Executar o exemplo

1. A aplicação descarrega ficheiros codificados. Crie uma pasta onde pretende que os ficheiros de saída vão e atualize o valor da variável **outputFolder** no ficheiro [index.ts.](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L59) Está definido para "Temporário" por defeito.
1. Abra um **pedido de comando,** navegue no diretório da amostra.
1. Mude o diretório para a pasta AMSv3Samples.

    ```bash
    cd AMSv3Samples
    ```

1. Instale as embalagens utilizadas no *packages.jsno* ficheiro.

    ```bash
    npm install 
    ```

1. Altere o diretório para a pasta *StreamFilesSample.*

    ```bash
    cd StreamFilesSample
    ```

1. Lançar Código de Estúdio Visual a partir da Pasta *AMSv3Samples.* (Isto é necessário para ser lançado a partir da pasta onde se encontra a pasta *.vscode* e *tsconfig.jsnos* ficheiros.)

    ```bash
    cd ..
    code .
    ```

Abra a pasta para *StreamFilesSample* e abra o ficheiro *index.ts* no editor visual Studio Code.
Enquanto está no ficheiro *index.ts,* prima F5 para lançar o depurar.

## <a name="test-with-azure-media-player"></a>Testar com o Leitor de Multimédia do Azure

Utilize o Azure Media Player para testar o fluxo. Também pode utilizar qualquer jogador compatível com HLS ou DASH, como o jogador Shaka, HLS.js, Dash.js ou outros.

Deverá ser capaz de clicar no link gerado na amostra e lançar o leitor AMP com o manifesto DASH já carregado.

> [!NOTE]
> Se um leitor estiver alojado num site de https, confirme que atualiza o URL para “https”.

1. Abra um navegador web e navegue para [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. No **URL:** caixa, cole um dos valores de URL de streaming que obteve quando executou a aplicação. Pode colar o URL no formato HLS, Dash ou Smooth e o Azure Media Player mudará para um protocolo de streaming apropriado para reprodução no seu dispositivo automaticamente.
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

> [Conceitos de Serviços de Mídia](concepts-overview.md)
