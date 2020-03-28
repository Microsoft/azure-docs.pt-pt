---
title: Transmita ficheiros de vídeo com a Azure Media Services - Node.js / Microsoft Docs
description: Siga os passos deste tutorial para criar uma nova conta Azure Media Services, codificar um ficheiro e transmiti-lo ao Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: serviços de multimédia do azure, transmitir
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: fa9fbf3bac55ca0b26c3644b7f6818fa96088612
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "69639398"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---nodejs"></a>Tutorial: Codificar um ficheiro remoto com base em URL e transmitir o vídeo - Node.js

Este tutorial mostra como é fácil codificar e começar a transmitir vídeos numa grande variedade de navegadores e dispositivos usando o Azure Media Services. Um conteúdo de entrada pode ser especificado com URLs HTTPS, URLs SAS ou caminhos de ficheiros localizados no Armazenamento de blobs do Azure.

A amostra deste artigo codifica o conteúdo que torna acessível através de um URL HTTPS. Note que, atualmente, o AMS v3 não suporta a codificação de transferência segmentada através de URLs HTTPS.

No final do tutorial poderá transmitir um vídeo.  

![Reproduzir o vídeo](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Instalar o [Node. js](https://nodejs.org/en/download/)
- [Criar uma conta de Media Services.](create-account-cli-how-to.md)<br/>Lembre-se dos valores que utilizou para o nome do grupo de recursos e nome da conta Media Services.
- Siga os passos na API access [Azure Media Services com o Azure CLI](access-api-cli-how-to.md) e guarde as credenciais. Terá de usá-los para aceder à API.

## <a name="download-and-configure-the-sample"></a>Descarregue e configure a amostra

Clone um repositório GitHub que contenha a amostra de node.js de streaming à sua máquina utilizando o seguinte comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

A amostra está localizada na pasta [StreamFilesSample.](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample)

Open [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25) em you downloaded projeto. Substitua `endpoint config` os valores por credenciais que obteve ao [aceder a APIs](access-api-cli-how-to.md).

O exemplo realiza as seguintes ações:

1. Cria uma **Transformação** (primeiro, verifica se a Transform especificada existe). 
2. Cria um **Ativo** de saída que é usado como a saída do **Trabalho**de codificação.
3. Cria a entrada do **Job**que é baseada num URL HTTPS.
4. Submete o **Trabalho** de codificação utilizando a entrada e saída que foi criada anteriormente.
5. Verifica o estado da Tarefa.
6. Cria um localizador de **streaming.**
7. Cria os URLs de transmissão.

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

1. A aplicação descarrega ficheiros codificados. Crie uma pasta onde pretenda que os ficheiros de saída sejam e atualize o valor da variável **outputFolder** no ficheiro [index.js.](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39)
1. Abra o pedido de **comando,** navegue no diretório da amostra e execute os seguintes comandos.

    ```
    npm install 
    node index.js
    ```

Depois de terminar a corrida, deve ver uma saída semelhante:

![Executar](./media/stream-files-nodejs-quickstart/run.png)

## <a name="test-with-azure-media-player"></a>Testar com o Leitor de Multimédia do Azure

Para testar a transmissão, este artigo utiliza o Leitor de Multimédia do Azure. 

> [!NOTE]
> Se um leitor estiver alojado num site de https, confirme que atualiza o URL para “https”.

1. Abra um navegador web [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)e navegue para .
2. Na caixa **URL:**, cole um dos valores do URL de transmissão que recebeu quando executou a aplicação. 
 
     Pode colar o URL em formato HLS, Dash ou Smooth e o Azure Media Player mudará para um protocolo de streaming apropriado para reprodução no seu dispositivo automaticamente.
3. Prima **Atualizar Leitor**.

O Leitor de Multimédia do Azure pode ser utilizado para fins de teste, mas não deve ser utilizado num ambiente de produção. 

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisar de nenhum dos recursos do seu grupo de recursos, incluindo os Serviços de Media e as contas de armazenamento que criou para este tutorial, elimine o grupo de recursos.

Executar o seguinte comando CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Consulte também

[Códigos](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)de erro de trabalho.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conceitos de Serviços de Media](concepts-overview.md)
