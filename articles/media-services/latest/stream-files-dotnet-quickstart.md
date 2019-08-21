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
ms.openlocfilehash: 7f997865ba33a51c3e3aa7a4c7e990037be9e534
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637340"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---net"></a>Tutorial: Codificar um arquivo remoto com base na URL e transmitir o vídeo-.NET

Este tutorial mostra como é fácil codificar e iniciar vídeos de streaming em uma ampla variedade de navegadores e dispositivos usando os serviços de mídia do Azure. Um conteúdo de entrada pode ser especificado com URLs HTTPS, URLs SAS ou caminhos de ficheiros localizados no Armazenamento de blobs do Azure.
O exemplo neste tópico codifica o conteúdo que torna acessível através de um URL HTTPS. Note que, atualmente, o AMS v3 não suporta a codificação de transferência segmentada através de URLs HTTPS.

No final do tutorial, você poderá transmitir um vídeo.  

![Reproduzir o vídeo](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver o Visual Studio instalado, poderá obter o [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Criar uma conta de Media Services](create-account-cli-how-to.md).<br/>Lembre-se de lembrar os valores que você usou para o nome do grupo de recursos e o nome da conta dos serviços de mídia.
- Siga as etapas em [acessar a API dos serviços de mídia do Azure com o CLI do Azure](access-api-cli-how-to.md) e salve as credenciais. Você precisará usá-las para acessar a API.

## <a name="download-and-configure-the-sample"></a>Baixar e configurar o exemplo

Clone o repositório do GitHub que contém o exemplo de .NET de transmissão para o computador com o comando seguinte:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

O exemplo está localizado na pasta [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles).

Abra [appSettings. JSON](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/appsettings.json) no projeto baixado. Substitua os valores por credenciais obtidas de [acessar APIs](access-api-cli-how-to.md).

O exemplo realiza as seguintes ações:

1. Cria uma **transformação** (primeiro, verifica se a transformação especificada existe). 
2. Cria um **ativo** de saída que é usado como a saída do **trabalho**de codificação.
3. Cria a entrada do **trabalho**com base em uma URL https.
4. Envia o **trabalho** de codificação usando a entrada e a saída que foram criados anteriormente.
5. Verifica o estado da Tarefa.
6. Cria um **localizador de streaming**.
7. Cria os URLs de transmissão.

Para obter uma explicação sobre o que faz cada função no exemplo, examine o código e observe os comentários [neste ficheiro de origem](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Quando executar a aplicação, os URLs que podem ser utilizados para reproduzir o vídeo são apresentados através de protocolos diferentes. 

1. Prima Ctrl + F5 para executar a aplicação *EncodeAndStreamFiles*.
2. Escolha o protocolo **HLS** da Apple (termina com *manifest(format=m3u8-aapl)* ) e copie o URL de transmissão da consola.

![Output](./media/stream-files-tutorial-with-api/output.png)

No [código de origem](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) do exemplo, pode ver como é criado o URL. Para criar o código, terá de concatenar o nome do anfitrião do ponto final da transmissão e o caminho de localizador da transmissão.  

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

## <a name="examine-the-code"></a>Examinar o código

Para obter uma explicação sobre o que faz cada função no exemplo, examine o código e observe os comentários [neste ficheiro de origem](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

O tutorial [Upload, encode, and stream files](stream-files-tutorial-with-api.md) (Carregar, codificar e transmitir ficheiros) dá-lhe um exemplo mais avançado de transmissão com explicações detalhadas. 

### <a name="job-error-codes"></a>Códigos de erro das tarefas

Consulte [códigos de erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="multithreading"></a>Multithreading

Os SDKs dos Serviços de Multimédia do Azure v3 não são seguros para threads. Ao trabalhar com aplicações com vários threads, deve gerar um novo objeto AzureMediaServicesClient por thread.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: upload, encode, and stream files](stream-files-tutorial-with-api.md) (Tutorial: carregar, codificar e transmitir ficheiros)
