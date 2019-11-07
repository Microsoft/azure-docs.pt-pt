---
title: Carregar, codificar e transmitir com os serviços de mídia v3
titleSuffix: Azure Media Services
description: Tutorial mostrando como carregar um arquivo, codificar vídeo e transmitir conteúdo com os serviços de mídia do Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/22/2019
ms.author: juliako
ms.openlocfilehash: f8ff3dc71727abf9e276cccc951c4d1143f4200d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73583104"
---
# <a name="tutorial-upload-encode-and-stream-videos-with-media-services-v3"></a>Tutorial: carregar, codificar e transmitir vídeos com os serviços de mídia v3

> [!NOTE]
> Embora este tutorial use exemplos do [SDK do .net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) , as etapas gerais são as mesmas para [API REST](https://docs.microsoft.com/rest/api/media/liveevents), [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)ou outros [SDKs](media-services-apis-overview.md#sdks)com suporte.

Os serviços de mídia do Azure permitem codificar seus arquivos de mídia em formatos que são executados em uma ampla variedade de navegadores e dispositivos. Por exemplo, pode querer transmitir o conteúdo nos formatos HLS ou MPEG DASH da Apple. Antes de transmissão, deve codificar o ficheiro de multimédia digital de alta qualidade. Para obter ajuda com a codificação, consulte [conceito de codificação](encoding-concept.md). Este tutorial carrega um ficheiro de vídeo local e codifica o ficheiro carregado. Você também pode codificar o conteúdo que você torna acessível por meio de uma URL HTTPS. Para obter mais informações, veja [Criar uma entrada de tarefa a partir de um URL HTTP(s)](job-input-from-http-how-to.md).

![Reproduzir um vídeo com Player de Mídia do Azure](./media/stream-files-tutorial-with-api/final-video.png)

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Baixe o aplicativo de exemplo descrito no tópico.
> * Examine o código que carrega, codifica e transmite.
> * Execute o aplicativo.
> * Teste a URL de streaming.
> * Limpar recursos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver o Visual Studio instalado, poderá obter o [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Crie uma conta dos serviços de mídia](create-account-cli-how-to.md).<br/>Lembre-se de lembrar os valores que você usou para o nome do grupo de recursos e o nome da conta dos serviços de mídia.
- Siga as etapas em [acessar a API dos serviços de mídia do Azure com o CLI do Azure](access-api-cli-how-to.md) e salve as credenciais. Você precisará usá-las para acessar a API.

## <a name="download-and-set-up-the-sample"></a>Baixar e configurar o exemplo

Clone um repositório GitHub que tem o exemplo streaming .NET em seu computador usando o seguinte comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

O exemplo está localizado na pasta [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles).

Abra [appSettings. JSON](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/appsettings.json) em seu projeto baixado. Substitua os valores por credenciais obtidas de [acessar APIs](access-api-cli-how-to.md).

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>Examinar o código que carrega, codifica e transmite

Esta secção examina as funções definidas no ficheiro [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) do projeto *UploadEncodeAndStreamFiles*.

O exemplo realiza as seguintes ações:

1. Cria uma nova **transformação** (primeiro, verifica se a transformação especificada existe).
2. Cria um **ativo** de saída que é usado como a saída do **trabalho**de codificação.
3. Crie um **ativo** de entrada e carregue o arquivo de vídeo local especificado nele. O elemento é utilizado como entrada da tarefa.
4. Envia o trabalho de codificação usando a entrada e a saída que foram criadas.
5. Verifica o estado da tarefa.
6. Cria um **localizador de streaming**.
7. Cria os URLs de transmissão.

### <a name="a-idstart_using_dotnet-start-using-media-services-apis-with-net-sdk"></a><a id="start_using_dotnet" />começar a usar as APIs dos serviços de mídia com o SDK do .NET

Para começar a utilizar as APIs dos Serviços de Multimédia com o .NET, tem de criar um objeto **AzureMediaServicesClient**. Para criar o objeto, você deve fornecer as credenciais necessárias para o cliente se conectar ao Azure usando o Azure AD. No código que clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no ficheiro de configuração local.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Criar um elemento de entrada e carregar um ficheiro local para ele

A função **CreateInputAsset** cria um novo [Elemento](https://docs.microsoft.com/rest/api/media/assets) de entrada e carrega o ficheiro de vídeo local especificado para ele. Esse **ativo** é usado como a entrada para seu trabalho de codificação. Nos serviços de mídia v3, a entrada para um **trabalho** pode ser um **ativo** ou conteúdo que você disponibiliza para sua conta de serviços de mídia por meio de URLs HTTPS. Para saber como codificar a partir de uma URL HTTPS, consulte [este](job-input-from-http-how-to.md) artigo.

Nos Serviços de Multimédia v3, deverá utilizar as APIs do Armazenamento do Azure para carregar os ficheiros. O seguinte fragmento de .NET mostra como.

A função seguinte executa estes passos:

* Cria um **ativo**.
* Obtém uma [URL SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) gravável para o contêiner do ativo [no armazenamento](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container).
* Carrega o arquivo no contêiner no armazenamento usando a URL SAS.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Criar um elemento de saída para armazenar o resultado de uma tarefa

A saída [Asset](https://docs.microsoft.com/rest/api/media/assets) armazena o resultado da tarefa de codificação. O projeto define a função **DownloadResults** que transfere os resultados deste elemento de saída para a pasta "saída", para que possa ver o que recebeu.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Criar uma Transformação e uma Tarefa que codifica o ficheiro carregado

Ao codificar ou processar conteúdo nos serviços de mídia, é um padrão comum definir as configurações de codificação como uma receita. Em seguida, deverá submeter uma **Tarefa** para aplicar essa receita a um vídeo. Ao enviar novos trabalhos para cada novo vídeo, você está aplicando essa receita a todos os vídeos em sua biblioteca. Uma receita nos serviços de mídia é chamada de **transformação**. Para obter mais informações, veja [Transforms and Jobs](transform-concept.md) (Transformações e Trabalhos). O exemplo descrito neste tutorial define uma receita que codifica o vídeo para transmiti-lo numa variedade de dispositivos iOS e Android.

#### <a name="transform"></a>Transformação

Ao criar uma nova instância [Transformar](https://docs.microsoft.com/rest/api/media/transforms), tem de especificar a saída resultante que pretende. O parâmetro necessário é um objeto **TransformOutput**, conforme apresentado no código abaixo. Cada **TransformOutput** contém uma **Predefinição**. A **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que estão a ser utilizadas para gerir o **TransformOutput** pretendido. O exemplo descrito neste artigo utiliza uma Predefinição incorporada chamada **AdaptiveStreaming**. A Predefinição codifica o vídeo de entrada para uma escala de bits gerada automaticamente (pares de resolução/velocidade de transmissão) com base na resolução e velocidade de transmissão de entrada e produz ficheiros ISO MP4 com vídeo H.264 e áudio AAC correspondente a cada par de resolução/velocidade de transmissão. Para obter informações sobre esta Predefinição, veja [Auto-generating bitrate ladder](autogen-bitrate-ladder.md) (Escala de bits gerada automaticamente).

Pode utilizar um EncoderNamedPreset incorporadi ou utilizar as predefinições personalizadas. Para obter mais informações, veja [Como personalizar as predefinições do codificador](customize-encoder-presets-how-to.md).

Ao criar uma [Transformação](https://docs.microsoft.com/rest/api/media/transforms), primeiro deve verificar se já existe uma com o método **Get**, conforme apresentado no código que se segue. Nos Serviços de Multimédia v3, os métodos **Get** nas entidades devolverão um valor **nulo** se a entidade não existir (uma verificação não sensível a maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Tarefa

Conforme mencionado acima, o objeto [Transformação](https://docs.microsoft.com/rest/api/media/transforms) é a receita e uma [Tarefa](https://docs.microsoft.com/rest/api/media/jobs) é o pedido real para os Serviços de Multimédia aplicarem essa **Transformação** a um determinado conteúdo de áudio ou vídeo de entrada. A **Tarefa** especifica informações como a localização do vídeo de entrada e a localização da saída.

Neste exemplo, o vídeo de entrada foi carregado do computador local. Se você quiser saber como codificar a partir de uma URL HTTPS, consulte [este](job-input-from-http-how-to.md) artigo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Aguardar a conclusão da Tarefa

A tarefa demora algum tempo a terminar, por isso irá querer receber uma notificação quando for concluída. O exemplo de código abaixo mostra como consultar o serviço para saber o estado da [Tarefa](https://docs.microsoft.com/rest/api/media/jobs). A sondagem não é uma prática recomendada para aplicativos de produção devido à possível latência. A consulta poderá ser limitada se for sobreutilizada numa conta. Em alternativa, os programadores devem utilizar o Event Grid.

O Event Grid foi concebido para ter uma elevada disponibilidade, um desempenho consistente e um dimensionamento dinâmico. Com o Event Grid, as aplicações podem escutar e reagir a eventos a partir de praticamente todos os serviços do Azure, bem como de origens personalizadas. O processamento de eventos simples, reativo e baseado em HTTP ajuda-o a criar soluções eficientes através da filtragem e do encaminhamento inteligente de eventos.  Veja [Route events to a custom web endpoint](job-state-events-cli-how-to.md) (Encaminhar eventos para um ponto final de Web personalizado).

Normalmente, a **Tarefa** passa pelos seguintes estados: **Agendada**, **Em fila**, **Em processamento**, **Concluída** (o estado final). Se a tarefa encontrar um erro, obterá um estado de **Erro**. Se o trabalho estiver no processo de ser cancelado, você será **cancelado e** **cancelado** quando terminar.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Códigos de erro das tarefas

Consulte [códigos de erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

### <a name="get-a-streaming-locator"></a>Obter um localizador de streaming

Depois de concluída a codificação, o passo seguinte consiste em disponibilizar o vídeo no Elemento de saída para reprodução para os clientes. Você pode torná-lo disponível em duas etapas: primeiro, criar um [localizador de streaming](https://docs.microsoft.com/rest/api/media/streaminglocators)e, em segundo lugar, criar as URLs de streaming que os clientes podem usar.

O processo de criação de um **localizador de streaming** é chamado publicação. Por padrão, o **localizador de streaming** é válido imediatamente depois que você faz as chamadas à API e dura até que ela seja excluída, a menos que você configure as horas de início e de término opcionais.

Ao criar um [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), você precisará especificar o **StreamingPolicyName**desejado. Neste exemplo, você passará por streaming (ou conteúdo não criptografado) para que a política de streaming de limpeza predefinida (**PredefinedStreamingPolicy. ClearStreamingOnly**) seja usada.

> [!IMPORTANT]
> Ao usar uma [política de streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies)personalizada, você deve criar um conjunto limitado de tais políticas para sua conta de serviço de mídia e reutilizá-las para o StreamingLocators sempre que forem necessárias as mesmas opções de criptografia e protocolos. Sua conta de serviço de mídia tem uma cota para o número de entradas de política de streaming. Você não deve criar uma nova política de streaming para cada localizador de streaming.

O código a seguir pressupõe que você está chamando a função com um locatorname exclusivo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Embora o exemplo neste tópico discuta o streaming, você pode usar a mesma chamada para criar um localizador de streaming para entregar vídeo por meio de download progressivo.

### <a name="get-streaming-urls"></a>Obter os URLs de transmissão

Agora que o [localizador de streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) foi criado, você pode obter as URLs de streaming, conforme mostrado em **GetStreamingURLs**. Para criar uma URL, você precisa concatenar o nome do host do [ponto de extremidade de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) e o caminho do **localizador de streaming** . Neste exemplo, o **ponto de extremidade de streaming** *padrão* é usado. Quando você cria uma conta de serviço de mídia pela primeira vez, esse **ponto de extremidade de streaming** *padrão* estará em um estado parado, portanto, você precisará chamar **Start**.

> [!NOTE]
> Nesse método, você precisa do locatorname que foi usado ao criar o **localizador de streaming** para o ativo de saída.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Limpar os recursos na conta dos Serviços de Multimédia

Em geral, você deve limpar tudo, exceto os objetos que pretende reutilizar (normalmente, você reutilizará transformações e persistirá StreamingLocators, etc.). Se você quiser que sua conta seja limpa após o teste, exclua os recursos que você não planeja reutilizar. Por exemplo, o código a seguir exclui trabalhos:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

1. Pressione CTRL + F5 para executar o aplicativo *EncodeAndStreamFiles* .
2. Copie um dos URLs de transmissão da consola.

Este exemplo apresenta os URLs que podem ser utilizados para reproduzir o vídeo através de protocolos diferentes:

![Exemplo de saída mostrando URLs para vídeo de streaming dos serviços de mídia](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>Testar o URL de transmissão em fluxo

Para testar a transmissão, este artigo utiliza o Leitor de Multimédia do Azure.

> [!NOTE]
> Se um leitor estiver alojado num site de https, confirme que atualiza o URL para “https”.

1. Abra um browser e navegue para [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Na caixa **URL:** , Cole um dos valores de URL de streaming que você obteve ao executar o aplicativo.
3. Selecione **Atualizar Player**.

Player de Mídia do Azure pode ser usado para teste, mas não deve ser usado em um ambiente de produção.

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisa de nenhum dos recursos presentes no seu grupo de recursos, incluindo as contas de armazenamento que criou e os Serviços de Multimédia que carregou neste tutorial, elimine o grupo de recursos que criou anteriormente.

Execute o seguinte comando da CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Os SDKs dos serviços de mídia do Azure v3 não são thread-safe. Ao desenvolver um aplicativo multi-threaded, você deve gerar e usar um novo objeto AzureMediaServicesClient por thread.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, envie comentários, obtenha atualizações

Confira o artigo [da Comunidade dos serviços de mídia do Azure](media-services-community.md) para ver diferentes maneiras que você pode fazer perguntas, fornecer comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Passos seguintes

Agora que sabe como carregar, codificar e transmitir o vídeo, veja o artigo seguinte: 

> [!div class="nextstepaction"]
> [Analisar vídeos](analyze-videos-tutorial-with-api.md)
