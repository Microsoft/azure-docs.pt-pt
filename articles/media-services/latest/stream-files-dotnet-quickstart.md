---
title: Transmitir ficheiros de vídeo com os Serviços de Multimédia do Azure – .NET | Microsoft Docs
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
ms.openlocfilehash: df4092ecc3f7d075f1a2821854cdb668ee2cebe5
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191210"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---net"></a>Tutorial: Codificar um ficheiro remoto com base em URL e transmitir o vídeo - .NET

Este tutorial mostra como é fácil codificar e começar a transmitir vídeos numa grande variedade de navegadores e dispositivos usando o Azure Media Services. Um conteúdo de entrada pode ser especificado com URLs HTTPS, URLs SAS ou caminhos de ficheiros localizados no Armazenamento de blobs do Azure.
O exemplo neste tópico codifica o conteúdo que torna acessível através de um URL HTTPS. Note que, atualmente, o AMS v3 não suporta a codificação de transferência segmentada através de URLs HTTPS.

No final do tutorial poderá transmitir um vídeo.  

![Reproduzir o vídeo](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver o Visual Studio instalado, poderá obter o [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Criar uma conta de Media Services.](create-account-cli-how-to.md)<br/>Lembre-se dos valores que utilizou para o nome do grupo de recursos e nome da conta Media Services.
- Siga os passos na API access [Azure Media Services com o Azure CLI](access-api-cli-how-to.md) e guarde as credenciais. Terá de usá-los para aceder à API.

## <a name="download-and-configure-the-sample"></a>Descarregue e configure a amostra

Clone o repositório do GitHub que contém o exemplo de .NET de transmissão para o computador com o comando seguinte:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

O exemplo está localizado na pasta [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles).

Abra [as aplicações.json](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/appsettings.json) no seu projeto descarregado. Substitua os valores por credenciais que obteve ao [aceder a APIs](access-api-cli-how-to.md).

O exemplo realiza as seguintes ações:

1. Cria uma **Transformação** (primeiro, verifica se a Transform especificada existe). 
2. Cria um **Ativo** de saída que é usado como a saída do **Trabalho**de codificação.
3. Cria a entrada do **Job**que é baseada num URL HTTPS.
4. Submete o **Trabalho** de codificação utilizando a entrada e saída que foi criada anteriormente.
5. Verifica o estado da Tarefa.
6. Cria um localizador de **streaming.**
7. Cria os URLs de transmissão.

Para obter uma explicação sobre o que faz cada função no exemplo, examine o código e observe os comentários [neste ficheiro de origem](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Quando executar a aplicação, os URLs que podem ser utilizados para reproduzir o vídeo são apresentados através de protocolos diferentes. 

1. Prima Ctrl + F5 para executar a aplicação *EncodeAndStreamFiles*.
2. Escolha o protocolo **HLS** da Apple (termina com *manifest(format=m3u8-aapl)* ) e copie o URL de transmissão da consola.

![Saída](./media/stream-files-tutorial-with-api/output.png)

No [código de origem](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) do exemplo, pode ver como é criado o URL. Para criar o código, terá de concatenar o nome do anfitrião do ponto final da transmissão e o caminho de localizador da transmissão.  

## <a name="test-with-azure-media-player"></a>Testar com o Leitor de Multimédia do Azure

Para testar a transmissão, este artigo utiliza o Leitor de Multimédia do Azure. 

> [!NOTE]
> Se um leitor estiver alojado num site de https, confirme que atualiza o URL para “https”.

1. Abra um browser e navegue para [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Na caixa **URL:** , cole um dos valores do URL de transmissão que recebeu quando executou a aplicação. 
 
     Pode colar o URL em formato HLS, Dash ou Smooth e o Azure Media Player mudará para um protocolo de streaming apropriado para reprodução no seu dispositivo automaticamente.
3. Prima **Atualizar Leitor**.

O Leitor de Multimédia do Azure pode ser utilizado para fins de teste, mas não deve ser utilizado num ambiente de produção. 

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisar de nenhum dos recursos do seu grupo de recursos, incluindo os Serviços de Media e as contas de armazenamento que criou para este tutorial, elimine o grupo de recursos.

Execute o seguinte comando da CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>Examinar o código

Para obter uma explicação sobre o que faz cada função no exemplo, examine o código e observe os comentários [neste ficheiro de origem](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

O tutorial [Upload, encode, and stream files](stream-files-tutorial-with-api.md) (Carregar, codificar e transmitir ficheiros) dá-lhe um exemplo mais avançado de transmissão com explicações detalhadas. 

### <a name="job-error-codes"></a>Códigos de erro das tarefas

Ver [Códigos de Erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="multithreading"></a>Multithreading

Os SDKs dos Serviços de Multimédia do Azure v3 não são seguros para threads. Ao trabalhar com aplicações com vários threads, deve gerar um novo objeto AzureMediaServicesClient por thread.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: upload, encode, and stream files](stream-files-tutorial-with-api.md) (Tutorial: carregar, codificar e transmitir ficheiros)
