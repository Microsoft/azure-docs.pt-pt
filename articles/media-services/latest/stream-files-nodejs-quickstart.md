---
title: Transmitir arquivos de vídeo com os serviços de mídia do Azure-node. js | Microsoft Docs
description: Siga as etapas deste tutorial para criar uma nova conta dos serviços de mídia do Azure, codificar um arquivo e transmiti-lo para Player de Mídia do Azure.
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
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639398"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---nodejs"></a>Tutorial: Codificar um arquivo remoto com base na URL e transmitir o vídeo-node. js

Este tutorial mostra como é fácil codificar e iniciar vídeos de streaming em uma ampla variedade de navegadores e dispositivos usando os serviços de mídia do Azure. Um conteúdo de entrada pode ser especificado com URLs HTTPS, URLs SAS ou caminhos de ficheiros localizados no Armazenamento de blobs do Azure.

O exemplo neste artigo codifica o conteúdo que você torna acessível por meio de uma URL HTTPS. Note que, atualmente, o AMS v3 não suporta a codificação de transferência segmentada através de URLs HTTPS.

No final do tutorial, você poderá transmitir um vídeo.  

![Reproduzir o vídeo](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Instalar o [Node. js](https://nodejs.org/en/download/)
- [Criar uma conta de Media Services](create-account-cli-how-to.md).<br/>Lembre-se de lembrar os valores que você usou para o nome do grupo de recursos e o nome da conta dos serviços de mídia.
- Siga as etapas em [acessar a API dos serviços de mídia do Azure com o CLI do Azure](access-api-cli-how-to.md) e salve as credenciais. Você precisará usá-las para acessar a API.

## <a name="download-and-configure-the-sample"></a>Baixar e configurar o exemplo

Clone um repositório GitHub que contém o exemplo de streaming node. js em seu computador usando o seguinte comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

O exemplo está localizado na pasta [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample) .

Abra o [index. js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25) no projeto baixado. Substitua os `endpoint config` valores por credenciais obtidas de [acessar APIs](access-api-cli-how-to.md).

O exemplo realiza as seguintes ações:

1. Cria uma **transformação** (primeiro, verifica se a transformação especificada existe). 
2. Cria um **ativo** de saída que é usado como a saída do **trabalho**de codificação.
3. Cria a entrada do **trabalho**com base em uma URL https.
4. Envia o **trabalho** de codificação usando a entrada e a saída que foram criados anteriormente.
5. Verifica o estado da Tarefa.
6. Cria um **localizador de streaming**.
7. Cria os URLs de transmissão.

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

1. O aplicativo baixa arquivos codificados. Crie uma pasta na qual você deseja que os arquivos de saída sejam atualizados e atualize o valor da variável **outputFolder** no arquivo [index. js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39) .
1. Abra o **prompt de comando**, navegue até o diretório do exemplo e execute os comandos a seguir.

    ```
    npm install 
    node index.js
    ```

Após a execução, você deverá ver uma saída semelhante:

![Executar](./media/stream-files-nodejs-quickstart/run.png)

## <a name="test-with-azure-media-player"></a>Testar com o Leitor de Multimédia do Azure

Para testar a transmissão, este artigo utiliza o Leitor de Multimédia do Azure. 

> [!NOTE]
> Se um leitor estiver alojado num site de https, confirme que atualiza o URL para “https”.

1. Abra um browser e navegue para [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Na caixa **URL:** , cole um dos valores do URL de transmissão que recebeu quando executou a aplicação. 
 
     Você pode colar a URL no formato HLS, Dash ou Smooth e Player de Mídia do Azure mudará para um protocolo de streaming apropriado para reprodução em seu dispositivo automaticamente.
3. Prima **Atualizar Leitor**.

O Leitor de Multimédia do Azure pode ser utilizado para fins de teste, mas não deve ser utilizado num ambiente de produção. 

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais de nenhum dos recursos em seu grupo de recursos, incluindo os serviços de mídia e as contas de armazenamento que você criou para este tutorial, exclua o grupo de recursos.

Execute o seguinte comando da CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Consulte também

[Códigos de erro do trabalho](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Conceitos dos serviços de mídia](concepts-overview.md)
