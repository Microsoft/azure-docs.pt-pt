---
title: Analisar vídeos com media services v3
titleSuffix: Azure Media Services
description: Saiba como analisar vídeos utilizando o Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 7497e226589689497ce572193017dc7fc31042b1
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934531"
---
# <a name="tutorial-analyze-videos-with-media-services-v3"></a>Tutorial: Analisar vídeos com Media Services v3

> [!NOTE]
> Embora este tutorial use os exemplos do [SDK do .net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) , as etapas gerais são as mesmas para a [API REST](https://docs.microsoft.com/rest/api/media/liveevents), a [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)ou outros [SDKs](media-services-apis-overview.md#sdks)com suporte.

Este tutorial mostra-lhe como analisar vídeos com os Serviços de Multimédia do Microsoft Azure. Existem muitos cenários em que pode querer obter mais informações sobre conteúdos de áudio ou vídeos gravados. Por exemplo, para alcançar uma maior satisfação do cliente, as organizações podem executar o processamento de voz em texto para converter gravações de suporte de clientes num catálogo pesquisável, com índices e dashboards. Depois, podem obter informações sobre os seus negócios. Estes conhecimentos incluem uma lista de queixas comuns, fontes de tais queixas e outras informações úteis.

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Descarregue a aplicação de amostra supor no tópico.
> * Examine o código que analisa o vídeo especificado.
> * Executar a aplicação.
> * Examine a saída.
> * Limpe os recursos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Conformidade, Privacidade e Segurança
 
Como um lembrete importante, você deve cumprir todas as leis aplicáveis na sua utilização do Indexer de Vídeo, e você não pode usar O Indexer de Vídeo ou qualquer outro serviço Azure de uma forma que viole os direitos de outros ou pode ser prejudicial para os outros. Antes de enviar quaisquer vídeos, incluindo quaisquer dados biométricos, para o serviço de Indexer de Vídeo para processamento e armazenamento, deve ter todos os direitos adequados, incluindo todos os consentimentos adequados, do(s) indivíduo(s) no vídeo. Para conhecer a conformidade, privacidade e segurança no Indexer de Vídeo, os [Termos dos Serviços Cognitivos](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)da Microsoft . Para as obrigações de privacidade da Microsoft e o tratamento dos seus dados, por favor, consulte a Declaração de [Privacidade](https://privacy.microsoft.com/PrivacyStatement)da Microsoft, os Termos dos [Serviços Online ("OST")](https://www.microsoft.com/licensing/product-licensing/products) e a [Adenda](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) de Processamento de Dados ("DPA"). Informações adicionais sobre a privacidade, incluindo sobre a retenção de dados, eliminação/destruição, estão disponíveis no OST e [aqui](../video-indexer/faq.md). Ao utilizar o Indexer de Vídeo, concorda em ficar vinculado aos Termos dos Serviços Cognitivos, ao OST, dPA e à Declaração de Privacidade.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver o Visual Studio instalado, obtenha [visual studio community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Criar uma conta de Media Services](create-account-cli-how-to.md).<br/>Lembre-se dos valores que utilizou para o nome do grupo de recursos e nome da conta Media Services.
- Siga os passos na API access [Azure Media Services com o Azure CLI](access-api-cli-how-to.md) e guarde as credenciais. Terá de usá-los para aceder à API.

## <a name="download-and-configure-the-sample"></a>Descarregue e configure a amostra

Clone o repositório do GitHub que contém o exemplo de .NET para o seu computador com o seguinte comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

O exemplo está localizado na pasta [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos).

Abra [as aplicações.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/appsettings.json) no seu projeto descarregado. Substitua os valores pelas credenciais que obteve ao [aceder a APIs](access-api-cli-how-to.md).

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>Examinar o código que analisa o vídeo especificado

Esta secção examina funções definidas no ficheiro [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) do projeto *AnalyzeVideos*.

A amostra completa as seguintes ações:

1. Cria uma **Transformação** e um **Trabalho** que analisa o seu vídeo.
2. Cria um **Ativo** de entrada e envia o vídeo para o mesmo. O elemento é utilizado como entrada da tarefa.
3. Cria um elemento de saída que armazena a saída da tarefa.
4. Submete a tarefa.
5. Verifica o estado da tarefa.
6. Transfere os ficheiros que resultaram da execução da tarefa.

> [!NOTE]
> Quando utilizar uma predefinição do Analisador de Vídeo ou Áudio, utilize o portal do Azure para definir a sua conta para ter 10 Unidades Reservadas de Multimédia S3. Para obter mais informações, veja [Dimensionar o processamento de multimédia](media-reserved-units-cli-how-to.md).

### <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a utilizar APIs dos Serviços de Multimédia com SDK do .NET

Para começar a utilizar as APIs dos Serviços de Multimédia com o .NET, tem de criar um objeto **AzureMediaServicesClient**. Para criar o objeto, tem de fornecer as credenciais necessárias para o cliente se ligar ao Azure com o Azure AD. No código que clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no ficheiro de configuração local. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Criar um elemento de entrada e carregar um ficheiro local para ele 

A função **CreateInputAsset** cria um novo [Elemento](https://docs.microsoft.com/rest/api/media/assets) de entrada e carrega o ficheiro de vídeo local especificado para ele. Este Elemento é utilizado como entrada para a Tarefa de codificação. Nos Serviços de Multimédia v3, a entrada para uma Tarefa pode ser um Elemento ou pode ser conteúdo que tenha disponibilizado na sua conta dos Serviços de Multimédia através de URLs HTTPS. Para aprender a codificar a partir de um URL HTTPS, consulte [este](job-input-from-http-how-to.md) artigo.  

Nos Serviços de Multimédia v3, deverá utilizar as APIs do Armazenamento do Azure para carregar os ficheiros. O seguinte fragmento de .NET mostra como.

A seguinte função completa estas ações:

* Cria um Ativo.
* Obtém um [URL SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) writável para o contentor do Ativo [em armazenamento](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container).
* Envia o ficheiro para o recipiente armazenado utilizando o URL SAS.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>Criar um elemento de saída para armazenar o resultado da tarefa

O [Elemento](https://docs.microsoft.com/rest/api/media/assets) de saída armazena o resultado da sua tarefa. O projeto define a função **DownloadResults** que transfere os resultados deste elemento de saída para a pasta "saída", para que possa ver o que recebeu.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Criar uma transformação e uma tarefa que analisa vídeos

Ao codificar ou processar conteúdos em Serviços de Media, é um padrão comum configurar as definições de codificação como uma receita. Em seguida, deverá submeter uma **Tarefa** para aplicar essa receita a um vídeo. Ao submeter novos Jobs para cada novo vídeo, está a aplicar essa receita a todos os vídeos da sua biblioteca. Uma receita em Media Services chama-se **Transform**. Para obter mais informações, veja [Transforms and jobs](transform-concept.md) (Transformações e tarefas). O exemplo descrito neste tutorial define uma receita que analisa o vídeo especificado.

#### <a name="transform"></a>Transformar

Ao criar uma nova instância [Transformar](https://docs.microsoft.com/rest/api/media/transforms), tem de especificar a saída resultante que pretende. **TransformOutput** é um parâmetro necessário. Cada **TransformOutput** contém uma **Predefinição**. A **Predefinição** descreve instruções passo a passo das operações de processamento de áudio e/ou vídeo que estão a ser utilizadas para gerir o **TransformOutput** pretendido. Neste exemplo, o preset **VideoAnalyzerPreset** é usado e a linguagem ("en-US") é passada para o seu construtor (`new VideoAnalyzerPreset("en-US")`). Esta predefinição permite-lhe extrair múltiplas informações de vídeo e áudio a partir de um vídeo. Pode utilizar a predefinição **AudioAnalyzerPreset** se precisar de extrair múltiplas informações de áudio a partir de um vídeo.

Ao criar um **Transform,** verifique primeiro se já existe o método **Get,** como mostra o código que se segue. Nos Serviços de Multimédia v3, os métodos **Get** nas entidades devolverão um valor **nulo** se a entidade não existir (uma verificação não sensível a maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Tarefa

Conforme mencionado acima, o objeto [Transformação](https://docs.microsoft.com/rest/api/media/transforms) é a receita e uma [Tarefa](https://docs.microsoft.com/rest/api/media/jobs) é o pedido real para os Serviços de Multimédia aplicarem essa **Transformação** a um determinado conteúdo de áudio ou vídeo de entrada. O **trabalho** especifica informações como o local do vídeo de entrada e o local para a saída. Pode especificar a localização do seu vídeo com: URLs HTTPS, URLs SAS ou Recursos que estão na sua conta dos Serviços de Multimédia.

Neste exemplo, a entrada da tarefa é um vídeo local.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Aguarde até que a tarefa termine

O trabalho leva algum tempo para ser concluído. Quando isso ocorrer, você deseja ser notificado. Existem algumas opções diferentes para ser notificado sobre a conclusão da [Tarefa](https://docs.microsoft.com/rest/api/media/jobs). A opção mais simples (que é mostrada aqui) é usar as sondagens.

A sondagem não é uma prática recomendada para aplicativos de produção devido à possível latência. A consulta poderá ser limitada se for sobreutilizada numa conta. Em alternativa, os programadores devem utilizar o Event Grid.

O Event Grid foi concebido para ter uma elevada disponibilidade, um desempenho consistente e um dimensionamento dinâmico. Com o Event Grid, as aplicações podem escutar e reagir a eventos a partir de praticamente todos os serviços do Azure, bem como de origens personalizadas. O processamento de eventos simples, reativo e baseado em HTTP ajuda-o a criar soluções eficientes através da filtragem e do encaminhamento inteligente de eventos. Para mais informações, consulte os [eventos da Route para um ponto final personalizado](job-state-events-cli-how-to.md)da web .

Normalmente, a **Tarefa** passa pelos seguintes estados: **Agendada**, **Em fila**, **Em processamento**, **Concluída** (o estado final). Se o trabalho tiver se originado em um erro, você obterá o estado de **erro** . Se o trabalho está em vias de ser cancelado, **é** cancelado e **depois cancelado** quando estiver feito.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Códigos de erro das tarefas

Consulte [códigos de erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

### <a name="download-the-result-of-the-job"></a>Transferir o resultado da tarefa

A função seguinte transfere os resultados do [Ativo](https://docs.microsoft.com/rest/api/media/assets) de saída para a pasta "output" para que possa examinar os resultados do trabalho.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Limpar os recursos na conta dos Serviços de Multimédia

Geralmente, deve limpar tudo, exceto objetos que planeia reutilizar (normalmente, vai reutilizar Transforms e persistir streamingLocators). Se desejar que sua conta seja limpa após o teste, exclua os recursos que você não planeja reutilizar. Por exemplo, o código a seguir exclui trabalhos:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Prima Ctrl+F5 para executar a aplicação *AnalyzeVideos.*

Quando executa o programa, a tarefa produz miniaturas para cada rosto que encontra no vídeo. Também produz o ficheiro insights.json.

## <a name="examine-the-output"></a>Examinar o ficheiro de saída

O ficheiro de saída para analisar vídeos chama-se insights.json. Este ficheiro contém informações sobre o seu vídeo. Encontrará a descrição dos elementos contidos no ficheiro json no artigo [Informações de multimédia](intelligence-concept.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisa de nenhum dos recursos presentes no seu grupo de recursos, incluindo as contas de armazenamento que criou e os Serviços de Multimédia que carregou neste tutorial, elimine o grupo de recursos que criou anteriormente.

Execute o seguinte comando da CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Os Serviços De Mídia Azure v3 SDKs não são seguros. Ao trabalhar com uma aplicação com várias linhas, deverá gerar um novo objeto AzureMediaServicesClient por fio.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, envie comentários, obtenha atualizações

Confira o artigo [da Comunidade dos serviços de mídia do Azure](media-services-community.md) para ver diferentes maneiras que você pode fazer perguntas, fornecer comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: upload, encode, and stream files](stream-files-tutorial-with-api.md) (Tutorial: carregar, codificar e transmitir ficheiros)
