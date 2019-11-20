---
title: Analisar vídeos com os serviços de mídia v3
titleSuffix: Azure Media Services
description: Saiba como analisar vídeos usando os serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: f803bcafb1966e32e894b4caeaa8fafb5f73e8e7
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186289"
---
# <a name="tutorial-analyze-videos-with-media-services-v3"></a>Tutorial: analisar vídeos com os serviços de mídia v3

> [!NOTE]
> Embora este tutorial use os exemplos do [SDK do .net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) , as etapas gerais são as mesmas para a [API REST](https://docs.microsoft.com/rest/api/media/liveevents), a [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)ou outros [SDKs](media-services-apis-overview.md#sdks)com suporte.

Este tutorial mostra-lhe como analisar vídeos com os Serviços de Multimédia do Microsoft Azure. Existem muitos cenários em que pode querer obter mais informações sobre conteúdos de áudio ou vídeos gravados. Por exemplo, para alcançar uma maior satisfação do cliente, as organizações podem executar o processamento de voz em texto para converter gravações de suporte de clientes num catálogo pesquisável, com índices e dashboards. Em seguida, eles podem obter informações sobre seus negócios. Essas informações incluem uma lista de reclamações comuns, fontes de tais reclamações e outros informativos úteis.

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Baixe o aplicativo de exemplo descrito no tópico.
> * Examine o código que analisa o vídeo especificado.
> * Execute o aplicativo.
> * Examine a saída.
> * Limpar recursos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver o Visual Studio instalado, obtenha o [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Criar uma conta de Media Services](create-account-cli-how-to.md).<br/>Lembre-se de lembrar os valores que você usou para o nome do grupo de recursos e o nome da conta dos serviços de mídia.
- Siga as etapas em [acessar a API dos serviços de mídia do Azure com o CLI do Azure](access-api-cli-how-to.md) e salve as credenciais. Você precisará usá-las para acessar a API.

## <a name="download-and-configure-the-sample"></a>Baixar e configurar o exemplo

Clone o repositório do GitHub que contém o exemplo de .NET para o seu computador com o seguinte comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

O exemplo está localizado na pasta [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos).

Abra [appSettings. JSON](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/appsettings.json) em seu projeto baixado. Substitua os valores pelas credenciais obtidas em [acessando APIs](access-api-cli-how-to.md).

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>Examinar o código que analisa o vídeo especificado

Esta secção examina funções definidas no ficheiro [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) do projeto *AnalyzeVideos*.

O exemplo conclui as seguintes ações:

1. Cria uma **transformação** e um **trabalho** que analisa seu vídeo.
2. Cria um **ativo** de entrada e carrega o vídeo nele. O elemento é utilizado como entrada da tarefa.
3. Cria um elemento de saída que armazena a saída da tarefa.
4. Submete a tarefa.
5. Verifica o estado da tarefa.
6. Transfere os ficheiros que resultaram da execução da tarefa.

> [!NOTE]
> Quando utilizar uma predefinição do Analisador de Vídeo ou Áudio, utilize o portal do Azure para definir a sua conta para ter 10 Unidades Reservadas de Multimédia S3. Para obter mais informações, veja [Dimensionar o processamento de multimédia](media-reserved-units-cli-how-to.md).

### <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a utilizar as APIs dos Serviços de Multimédia com o SDK .NET

Para começar a utilizar as APIs dos Serviços de Multimédia com o .NET, tem de criar um objeto **AzureMediaServicesClient**. Para criar o objeto, tem de fornecer as credenciais necessárias para o cliente se ligar ao Azure com o Azure AD. No código que clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no ficheiro de configuração local. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Criar um elemento de entrada e carregar um ficheiro local para ele 

A função **CreateInputAsset** cria um novo [Elemento](https://docs.microsoft.com/rest/api/media/assets) de entrada e carrega o ficheiro de vídeo local especificado para ele. Este Elemento é utilizado como entrada para a Tarefa de codificação. Nos Serviços de Multimédia v3, a entrada para uma Tarefa pode ser um Elemento ou pode ser conteúdo que tenha disponibilizado na sua conta dos Serviços de Multimédia através de URLs HTTPS. Para saber como codificar a partir de uma URL HTTPS, consulte [este](job-input-from-http-how-to.md) artigo.  

Nos Serviços de Multimédia v3, deverá utilizar as APIs do Armazenamento do Azure para carregar os ficheiros. O seguinte fragmento de .NET mostra como.

A função a seguir conclui estas ações:

* Cria um ativo.
* Obtém uma [URL SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) gravável para o contêiner do ativo [no armazenamento](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container).
* Carrega o arquivo no contêiner no armazenamento usando a URL SAS.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>Criar um elemento de saída para armazenar o resultado da tarefa

O [Elemento](https://docs.microsoft.com/rest/api/media/assets) de saída armazena o resultado da sua tarefa. O projeto define a função **DownloadResults**, que transfere os resultados deste elemento de saída para a pasta “saída”, para que possa ver o que obteve.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Criar uma transformação e uma tarefa que analisa vídeos

Ao codificar ou processar conteúdo nos serviços de mídia, é um padrão comum definir as configurações de codificação como uma receita. Em seguida, deverá submeter uma **Tarefa** para aplicar essa receita a um vídeo. Ao enviar novos trabalhos para cada novo vídeo, você está aplicando essa receita a todos os vídeos em sua biblioteca. Uma receita nos serviços de mídia é chamada de **transformação**. Para obter mais informações, veja [Transforms and jobs](transform-concept.md) (Transformações e tarefas). O exemplo descrito neste tutorial define uma receita que analisa o vídeo especificado.

#### <a name="transform"></a>Transformar

Ao criar uma nova instância [Transformar](https://docs.microsoft.com/rest/api/media/transforms), tem de especificar a saída resultante que pretende. **TransformOutput** é um parâmetro obrigatório. Cada **TransformOutput** contém uma **Predefinição**. A **Predefinição** descreve instruções passo a passo das operações de processamento de áudio e/ou vídeo que estão a ser utilizadas para gerir o **TransformOutput** pretendido. Neste exemplo, a predefinição **VideoAnalyzerPreset** é usada e a linguagem ("en-US") é passada para seu construtor (`new VideoAnalyzerPreset("en-US")`). Esta predefinição permite-lhe extrair múltiplas informações de vídeo e áudio a partir de um vídeo. Pode utilizar a predefinição **AudioAnalyzerPreset** se precisar de extrair múltiplas informações de áudio a partir de um vídeo.

Ao criar uma **transformação**, marque primeiro se já existir um usando o método **Get** , conforme mostrado no código a seguir. Nos Serviços de Multimédia v3, os métodos **Get** nas entidades devolverão um valor **nulo** se a entidade não existir (uma verificação não sensível a maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Tarefa

Conforme mencionado acima, o objeto [Transformação](https://docs.microsoft.com/rest/api/media/transforms) é a receita e uma [Tarefa](https://docs.microsoft.com/rest/api/media/jobs) é o pedido real para os Serviços de Multimédia aplicarem essa **Transformação** a um determinado conteúdo de áudio ou vídeo de entrada. O **trabalho** especifica informações como o local do vídeo de entrada e o local para a saída. Pode especificar a localização do seu vídeo com: URLs HTTPS, URLs SAS ou Recursos que estão na sua conta dos Serviços de Multimédia.

Neste exemplo, a entrada da tarefa é um vídeo local.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Aguarde até que a tarefa termine

O trabalho leva algum tempo para ser concluído. Quando isso ocorrer, você deseja ser notificado. Existem algumas opções diferentes para ser notificado sobre a conclusão da [Tarefa](https://docs.microsoft.com/rest/api/media/jobs). A opção mais simples (que é mostrada aqui) é usar a sondagem.

A sondagem não é uma prática recomendada para aplicativos de produção devido à possível latência. A consulta poderá ser limitada se for sobreutilizada numa conta. Em alternativa, os programadores devem utilizar o Event Grid.

O Event Grid foi concebido para ter uma elevada disponibilidade, um desempenho consistente e um dimensionamento dinâmico. Com o Event Grid, as aplicações podem escutar e reagir a eventos a partir de praticamente todos os serviços do Azure, bem como de origens personalizadas. O processamento de eventos simples, reativo e baseado em HTTP ajuda-o a criar soluções eficientes através da filtragem e do encaminhamento inteligente de eventos. Para obter mais informações, consulte [rotear eventos para um ponto de extremidade da Web personalizado](job-state-events-cli-how-to.md).

Normalmente, a **Tarefa** passa pelos seguintes estados: **Agendada**, **Em fila**, **Em processamento**, **Concluída** (o estado final). Se o trabalho tiver se originado em um erro, você obterá o estado de **erro** . Se o trabalho estiver no processo de cancelado, você será **cancelado** e **cancelado** quando terminar.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Códigos de erro das tarefas

Consulte [códigos de erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

### <a name="download-the-result-of-the-job"></a>Transferir o resultado da tarefa

A função a seguir baixa os resultados do [ativo](https://docs.microsoft.com/rest/api/media/assets) de saída na pasta "output" para que você possa examinar os resultados do trabalho.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Limpar os recursos na conta dos Serviços de Multimédia

Em geral, você deve limpar tudo, exceto os objetos que pretende reutilizar (normalmente, você reutilizará transformações e persistirá StreamingLocators). Se desejar que sua conta seja limpa após o teste, exclua os recursos que você não planeja reutilizar. Por exemplo, o código a seguir exclui trabalhos:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Pressione CTRL + F5 para executar o aplicativo *AnalyzeVideos* .

Quando executa o programa, a tarefa produz miniaturas para cada rosto que encontra no vídeo. Também produz o ficheiro insights.json.

## <a name="examine-the-output"></a>Examinar o ficheiro de saída

O ficheiro de saída para analisar vídeos chama-se insights.json. Este ficheiro contém informações sobre o seu vídeo. Encontrará a descrição dos elementos contidos no ficheiro json no artigo [Informações de multimédia](intelligence-concept.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisar de nenhum dos recursos presentes no seu grupo de recursos, incluindo a conta dos Serviços de Multimédia e a conta de armazenamento que criou para este tutorial, elimine o grupo de recursos anteriormente criado.

Execute o seguinte comando da CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Os SDKs dos serviços de mídia do Azure v3 não são thread-safe. Ao trabalhar com um aplicativo multi-threaded, você deve gerar um novo objeto AzureMediaServicesClient por thread.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, envie comentários, obtenha atualizações

Confira o artigo [da Comunidade dos serviços de mídia do Azure](media-services-community.md) para ver diferentes maneiras que você pode fazer perguntas, fornecer comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: upload, encode, and stream files](stream-files-tutorial-with-api.md) (Tutorial: carregar, codificar e transmitir ficheiros)
