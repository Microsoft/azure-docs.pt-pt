---
title: Stream ficheiros de vídeo com os serviços de multimédia do Azure - node. js | Documentos da Microsoft
description: Siga os passos deste início rápido para criar uma nova conta dos Serviços de Multimédia do Azure, codificar um ficheiro e transmiti-lo para o Leitor de Multimédia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: serviços de multimédia do azure, transmitir
ms.service: media-services
ms.workload: media
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/26/2019
ms.author: juliako
ms.openlocfilehash: 22b7f2380b509daa4cb9931d6fc57c1297628e3d
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522865"
---
# <a name="quickstart-stream-video-files---nodejs"></a>Início rápido: Ficheiros de vídeo do Stream - node. js

Este início rápido mostra como é fácil codificar e começar a transmitir vídeos numa ampla variedade de browsers e dispositivos com os Serviços de Multimédia do Azure. Um conteúdo de entrada pode ser especificado com URLs HTTPS, URLs SAS ou caminhos de ficheiros localizados no Armazenamento de blobs do Azure.
O exemplo neste tópico codifica o conteúdo que torna acessível através de um URL HTTPS. Note que, atualmente, o AMS v3 não suporta a codificação de transferência segmentada através de URLs HTTPS.

No final do início rápido, será capaz de transmitir um vídeo.  

![Reproduzir o vídeo](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Instalar o [Node. js](https://nodejs.org/en/download/)
- [Criar uma conta de Media Services](create-account-cli-how-to.md).<br/>Lembre-se de que não se esqueça dos valores que utilizou para o nome do grupo de recursos e o nome de conta de serviços de multimédia.
- Siga os passos em [acesso à API de serviços de multimédia do Azure com a CLI do Azure](access-api-cli-how-to.md) e guarde as credenciais. Terá de utilizá-los para aceder à API.

## <a name="download-and-configure-the-sample"></a>Transferir e configurar o exemplo

Clone um repositório do GitHub que contém o exemplo de node. js de transmissão em fluxo para o seu computador com o seguinte comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

O exemplo está localizado no [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample) pasta.

Open [Index](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25) no que transferiu o projeto. Substitua a `endpoint config` valores com as credenciais que recebeu do [aceder a APIs](access-api-cli-how-to.md).

O exemplo realiza as seguintes ações:

1. Cria um **transformar** (primeiro, verifica se existe a transformação especificada). 
2. Cria uma saída **Asset** que é utilizado como a codificação **tarefa**de saída.
3. Cria o **tarefa**entrada do que se baseia-se um URL HTTPS.
4. Envia a codificação **tarefa** usando a entrada e saída que foi criada anteriormente.
5. Verifica o estado da Tarefa.
6. Cria um **localizador de transmissão em fluxo**.
7. Cria os URLs de transmissão.

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

1. A aplicação transfere ficheiros codificados. Crie uma pasta em que pretende para os ficheiros de saída aceder e atualizar o valor do **outputFolder** variável a [Index](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39) ficheiro.
1. Open **linha de comandos**, navegar para de diretório o exemplo e execute os seguintes comandos.

    ```
    npm install 
    node index.js
    ```

Depois de ter sido em execução, deverá ver um resultado semelhante:

![Executar](./media/stream-files-nodejs-quickstart/run.png)

## <a name="test-with-azure-media-player"></a>Testar com o Leitor de Multimédia do Azure

Para testar a transmissão, este artigo utiliza o Leitor de Multimédia do Azure. 

> [!NOTE]
> Se um leitor estiver alojado num site de https, confirme que atualiza o URL para “https”.

1. Abra um browser e navegue para [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Na caixa **URL:**, cole um dos valores do URL de transmissão que recebeu quando executou a aplicação. 
 
     Pode colar o URL HLS, traço, ou formato uniforme e leitor de multimédia do Azure irão mudar para um protocolo de transmissão em fluxo apropriado para reprodução em seu dispositivo automaticamente.
3. Prima **Atualizar Leitor**.

O Leitor de Multimédia do Azure pode ser utilizado para fins de teste, mas não deve ser utilizado num ambiente de produção. 

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisar de nenhum dos recursos presentes no grupo de recursos, incluindo a conta dos Serviços de Multimédia e a conta de armazenamento que criou neste Início Rápido, elimine o grupo de recursos.

Execute o seguinte comando da CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Consulte também

[Códigos de erro da tarefa](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Conceitos de serviços de multimédia](concepts-overview.md)
