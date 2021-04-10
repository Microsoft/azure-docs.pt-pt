---
title: Stream de ficheiros de vídeo com a Azure Media Services - .NET
description: Siga os passos deste tutorial para usar .NET para criar uma nova conta Azure Media Services, codificar um ficheiro e transmiti-lo ao Azure Media Player.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: serviços de multimédia do azure, transmitir
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: e7c35e079a6f4bf6c40992debace60e10631a8ba
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962054"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---net"></a>Tutorial: Codificar um ficheiro remoto com base no URL e transmitir o vídeo - .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este tutorial mostra-lhe como é fácil codificar e começar a transmitir vídeos numa grande variedade de navegadores e dispositivos que utilizam o Azure Media Services. Um conteúdo de entrada pode ser especificado com URLs HTTPS, URLs SAS ou caminhos de ficheiros localizados no Armazenamento de blobs do Azure.
O exemplo neste tópico codifica o conteúdo que torna acessível através de um URL HTTPS. Note que, atualmente, o AMS v3 não suporta a codificação de transferência segmentada através de URLs HTTPS.

No final do tutorial poderá transmitir um vídeo.  

![Reproduzir o vídeo](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver o Visual Studio instalado, poderá obter o [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Criar uma conta de Serviços de Comunicação](./account-create-how-to.md)Social.<br/>Lembre-se dos valores que utilizou para o nome do grupo de recursos e nome da conta dos Media Services.
- Siga os passos na [Access Azure Media Services API com o Azure CLI](./access-api-howto.md) e guarde as credenciais. Terá de usá-los para aceder à API.

## <a name="download-and-configure-the-sample"></a>Descarregue e configuure a amostra

Clone o repositório do GitHub que contém o exemplo de .NET de transmissão para o computador com o comando seguinte:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

O exemplo está localizado na pasta [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles).

Abra [appsettings.jsno](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/appsettings.json) seu projeto descarregado. Substitua os valores por credenciais que obteve ao [aceder às APIs.](./access-api-howto.md)

O exemplo realiza as seguintes ações:

1. Cria uma **Transformação** (em primeiro lugar, verifica se existe a Transformação especificada). 
2. Cria uma saída **Ativo** que é usado como a saída de **Job** codificante.
3. Cria a entrada do **Job** que se baseia num URL HTTPS.
4. Submete o **trabalho** de codificação usando a entrada e saída que foi criada anteriormente.
5. Verifica o estado da Tarefa.
6. Cria um **localizador de streaming.**
7. Cria os URLs de transmissão.

Para obter uma explicação sobre o que faz cada função no exemplo, examine o código e observe os comentários [neste ficheiro de origem](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Quando executar a aplicação, os URLs que podem ser utilizados para reproduzir o vídeo são apresentados através de protocolos diferentes. 

1. Prima Ctrl + F5 para executar a aplicação *EncodeAndStreamFiles*.
2. Escolha o protocolo **HLS** da Apple (termina com *manifest(format=m3u8-aapl)*) e copie o URL de transmissão da consola.

![Screenshot da saída da aplicação EncodeAndStreamFiles no Estúdio Visual mostrando três URLs de streaming para uso no Azure Media Player.](./media/stream-files-tutorial-with-api/output.png)

No [código de origem](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) do exemplo, pode ver como é criado o URL. Para criar o código, terá de concatenar o nome do anfitrião do ponto final da transmissão e o caminho de localizador da transmissão.  

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

## <a name="examine-the-code"></a>Examinar o código

Para obter uma explicação sobre o que faz cada função no exemplo, examine o código e observe os comentários [neste ficheiro de origem](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

O tutorial [Upload, encode, and stream files](stream-files-tutorial-with-api.md) (Carregar, codificar e transmitir ficheiros) dá-lhe um exemplo mais avançado de transmissão com explicações detalhadas. 

### <a name="job-error-codes"></a>Códigos de erro das tarefas

Ver [códigos de erro](/rest/api/media/jobs/get#joberrorcode).

## <a name="multithreading"></a>Multithreading

Os SDKs dos Serviços de Multimédia do Azure v3 não são seguros para threads. Ao trabalhar com aplicações com vários threads, deve gerar um novo objeto AzureMediaServicesClient por thread.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: upload, encode, and stream files](stream-files-tutorial-with-api.md) (Tutorial: carregar, codificar e transmitir ficheiros)
