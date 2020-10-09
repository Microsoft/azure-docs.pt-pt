---
title: Stream de ficheiros de vídeo com a Azure Media Services - Node.js Microsoft Docs
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
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 38207ac47d2e58c689f1933a6ade6d5c86cd44ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91268677"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---nodejs"></a>Tutorial: Codificar um ficheiro remoto baseado em URL e transmitir o vídeo - Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este tutorial mostra-lhe como é fácil codificar e começar a transmitir vídeos numa grande variedade de navegadores e dispositivos que utilizam o Azure Media Services. Um conteúdo de entrada pode ser especificado com URLs HTTPS, URLs SAS ou caminhos de ficheiros localizados no Armazenamento de blobs do Azure.

A amostra deste artigo codifica conteúdo que torna acessível através de um URL HTTPS. Note que, atualmente, o AMS v3 não suporta a codificação de transferência segmentada através de URLs HTTPS.

No final do tutorial poderá transmitir um vídeo.  

![Reproduzir o vídeo](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Instalar [Node.js](https://nodejs.org/en/download/)
- [Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social.<br/>Lembre-se dos valores que utilizou para o nome do grupo de recursos e nome da conta dos Media Services.
- Siga os passos na [Access Azure Media Services API com o Azure CLI](./access-api-howto.md) e guarde as credenciais. Terá de usá-los para aceder à API.

## <a name="download-and-configure-the-sample"></a>Descarregue e configuure a amostra

Clone um repositório GitHub que contenha a amostra de Node.js de streaming para a sua máquina utilizando o seguinte comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

A amostra está localizada na pasta [StreamFilesSample.](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample)

Abra [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25) em projeto descarregado. Substitua os `endpoint config` valores por credenciais que obteve ao [aceder às APIs.](./access-api-howto.md)

O exemplo realiza as seguintes ações:

1. Cria uma **Transformação** (em primeiro lugar, verifica se existe a Transformação especificada). 
2. Cria uma saída **Ativo** que é usado como a saída de **Job**codificante.
3. Cria a entrada do **Job**que se baseia num URL HTTPS.
4. Submete o **trabalho** de codificação usando a entrada e saída que foi criada anteriormente.
5. Verifica o estado da Tarefa.
6. Cria um **localizador de streaming.**
7. Cria os URLs de transmissão.

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

1. A aplicação descarrega ficheiros codificados. Crie uma pasta onde pretende que os ficheiros de saída vão e atualize o valor da variável **outputFolder** no ficheiro [index.js.](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39)
1. Abrir **o comando,** navegar no diretório da amostra e executar os seguintes comandos.

    ```
    npm install 
    node index.js
    ```

Depois de ter terminado de funcionar, deverá ver uma saída semelhante:

![Screenshot de uma janela de comando com saída da aplicação de amostra StreamFileSample mostrando os URLs de três ficheiros descarregados para o diretório local.](./media/stream-files-nodejs-quickstart/run.png)

## <a name="test-with-azure-media-player"></a>Testar com o Leitor de Multimédia do Azure

Para testar a transmissão, este artigo utiliza o Leitor de Multimédia do Azure. 

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

## <a name="see-also"></a>Consulte também

[Códigos de erro de trabalho](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conceitos de Serviços de Mídia](concepts-overview.md)
