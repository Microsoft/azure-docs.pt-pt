---
title: Transmitir ficheiros de vídeo com os Serviços de Multimédia do Azure – .NET | Microsoft Docs
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
ms.date: 02/20/2019
ms.author: juliako
ms.openlocfilehash: 2556942119fdd4f58f6bd47bc88160dd2e13753d
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56585565"
---
# <a name="quickstart-stream-video-files---net"></a>Início rápido: Transmitir ficheiros de vídeo - .NET

Este início rápido mostra como é fácil codificar e começar a transmitir vídeos numa ampla variedade de browsers e dispositivos com os Serviços de Multimédia do Azure. Um conteúdo de entrada pode ser especificado com URLs HTTPS, URLs SAS ou caminhos de ficheiros localizados no Armazenamento de blobs do Azure.
O exemplo neste tópico codifica o conteúdo que torna acessível através de um URL HTTPS. Note que, atualmente, o AMS v3 não suporta a codificação de transferência segmentada através de URLs HTTPS.

No final do início rápido, será capaz de transmitir um vídeo.  

![Reproduzir o vídeo](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver o Visual Studio instalado, poderá obter o [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Criar uma conta de Media Services](create-account-cli-how-to.md).<br/>Lembre-se de que não se esqueça dos valores que utilizou para o nome do grupo de recursos e o nome de conta de serviços de multimédia.
- Siga os passos em [acesso à API de serviços de multimédia do Azure com a CLI do Azure](access-api-cli-how-to.md) e guarde as credenciais. Terá de utilizá-los para aceder à API.

## <a name="download-and-configure-the-sample"></a>Transferir e configurar o exemplo

Clone o repositório do GitHub que contém o exemplo de .NET de transmissão para o computador com o comando seguinte:  

 ```bash
 git clone http://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

O exemplo está localizado na pasta [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles).

Open [appSettings](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/appsettings.json) no que transferiu o projeto. Substitua os valores com as credenciais que obteve da [aceder a APIs](access-api-cli-how-to.md).

O exemplo realiza as seguintes ações:

1. Cria um **transformar** (primeiro, verifica se existe a transformação especificada). 
2. Cria uma saída **Asset** que é utilizado como a codificação **tarefa**de saída.
3. Cria o **tarefa**entrada do que se baseia-se um URL HTTPS.
4. Envia a codificação **tarefa** usando a entrada e saída que foi criada anteriormente.
5. Verifica o estado da Tarefa.
6. Cria um **localizador de transmissão em fluxo**.
7. Cria os URLs de transmissão.

Para obter uma explicação sobre o que faz cada função no exemplo, examine o código e observe os comentários [neste ficheiro de origem](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Quando executar a aplicação, os URLs que podem ser utilizados para reproduzir o vídeo são apresentados através de protocolos diferentes. 

1. Prima Ctrl + F5 para executar a aplicação *EncodeAndStreamFiles*.
2. Escolha o protocolo **HLS** da Apple (termina com *manifest(format=m3u8-aapl)*) e copie o URL de transmissão da consola.

![Saída](./media/stream-files-tutorial-with-api/output.png)

No [código de origem](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) do exemplo, pode ver como é criado o URL. Para criar o código, terá de concatenar o nome do anfitrião do ponto final da transmissão e o caminho de localizador da transmissão.  

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

## <a name="examine-the-code"></a>Examinar o código

Para obter uma explicação sobre o que faz cada função no exemplo, examine o código e observe os comentários [neste ficheiro de origem](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

O tutorial [Upload, encode, and stream files](stream-files-tutorial-with-api.md) (Carregar, codificar e transmitir ficheiros) dá-lhe um exemplo mais avançado de transmissão com explicações detalhadas. 

### <a name="job-error-codes"></a>Códigos de erro da tarefa

Ver [códigos de erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="multithreading"></a>Multithreading

Os SDKs dos Serviços de Multimédia do Azure v3 não são seguros para threads. Ao trabalhar com aplicações com vários threads, deve gerar um novo objeto AzureMediaServicesClient por thread.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: upload, encode, and stream files](stream-files-tutorial-with-api.md) (Tutorial: carregar, codificar e transmitir ficheiros)
