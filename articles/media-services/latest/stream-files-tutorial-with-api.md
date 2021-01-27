---
título: Carregar, codificar e transmitir com os Media Services v3 : Descrição do Azure Media Services: Tutorial mostrando como carregar um ficheiro, codificar vídeo e transmitir conteúdo com a Azure Media Services v3.
serviços: media-services documentationcenter: '' autor: IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: ms.topic: tutorial ms.custom: mvc ms.date: 08/31/2020 ms.author: inhenkel
---

# <a name="tutorial-upload-encode-and-stream-videos-with-media-services-v3"></a>Tutorial: Carregar, codificar e transmitir vídeos com o Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Mesmo que este tutorial utilize exemplos [.NET SDK,](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) os passos gerais são os mesmos para [REST API,](/rest/api/media/liveevents) [CLI,](/cli/azure/ams/live-event?view=azure-cli-latest)ou [outros SDKs suportados.](media-services-apis-overview.md#sdks)

O Azure Media Services permite-lhe codificar os seus ficheiros de mídia em formatos que reproduzem uma grande variedade de navegadores e dispositivos. Por exemplo, pode querer transmitir o conteúdo nos formatos HLS ou MPEG DASH da Apple. Antes de transmissão, deve codificar o ficheiro de multimédia digital de alta qualidade. Para obter ajuda na codificação, consulte [o conceito de Codificação.](encoding-concept.md) Este tutorial carrega um ficheiro de vídeo local e codifica o ficheiro carregado. Também pode codificar conteúdo que torna acessível através de um URL HTTPS. Para obter mais informações, veja [Criar uma entrada de tarefa a partir de um URL HTTP(s)](job-input-from-http-how-to.md).

![Jogue um vídeo com Azure Media Player](./media/stream-files-tutorial-with-api/final-video.png)

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Descarregue a aplicação de amostra descrita no tópico.
> * Examine o código que carrega, codifica e flui.
> * Execute a aplicação.
> * Teste a URL de streaming.
> * Limpe os recursos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver o Visual Studio instalado, pode obter [Visual Studio Community 2019.](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)
- [Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social.<br/>Lembre-se dos valores que utilizou para o nome do grupo de recursos e nome da conta dos Media Services.
- Siga os passos na [Access Azure Media Services API com o Azure CLI](./access-api-howto.md) e guarde as credenciais. Terá de usá-los para aceder à API.

## <a name="download-and-set-up-the-sample"></a>Faça o download e crie a amostra

Clone um repositório GitHub que tenha a amostra de streaming .NET para a sua máquina utilizando o seguinte comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

O exemplo está localizado na pasta [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles).

Abra [appsettings.jsno](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/appsettings.json) seu projeto descarregado. Substitua os valores por credenciais que obteve ao [aceder às APIs.](./access-api-howto.md)

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>Examinar o código que carrega, codifica e transmite

Esta secção examina as funções definidas no ficheiro [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) do projeto *UploadEncodeAndStreamFiles*.

O exemplo realiza as seguintes ações:

1. Cria uma nova **Transformação** (em primeiro lugar, verifica se existe a Transformação especificada).
2. Cria uma saída **O Ativo** que é usado como a saída de **Job** codificante.
3. Crie um **Ativo** de entrada e carrefique o ficheiro de vídeo local especificado nele. O elemento é utilizado como entrada da tarefa.
4. Submete o trabalho de codificação usando a entrada e saída que foi criada.
5. Verifica o estado da tarefa.
6. Cria um **localizador de streaming.**
7. Cria os URLs de transmissão.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a utilizar APIs dos Serviços de Multimédia com SDK do .NET

Para começar a utilizar as APIs dos Serviços de Multimédia com o .NET, tem de criar um objeto **AzureMediaServicesClient**. Para criar o objeto, deve fornecer credenciais necessárias para que o cliente se conecte ao Azure usando Azure AD. No código que clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no ficheiro de configuração local.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Criar um elemento de entrada e carregar um ficheiro local para ele

A função **CreateInputAsset** cria um novo [Elemento](/rest/api/media/assets) de entrada e carrega o ficheiro de vídeo local especificado para ele. Este **Ativo** é utilizado como entrada para o seu trabalho de codificação. Nos Serviços de Comunicação social v3, a entrada para um **Job** pode ser um **Ativo** ou conteúdo que disponibiliza à sua conta de Media Services através de URLs HTTPS. Para aprender a codificar a partir de um URL HTTPS, consulte [este](job-input-from-http-how-to.md) artigo.

Nos Serviços de Multimédia v3, deverá utilizar as APIs do Armazenamento do Azure para carregar os ficheiros. O seguinte fragmento de .NET mostra como.

A função seguinte executa estes passos:

* Cria um **Ativo**.
* Obtém um [URL SAS](../../storage/common/storage-sas-overview.md) writable para o contentor do ativo [no armazenamento](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container).

    Se utilizar a função [ListContainerSas](/rest/api/media/assets/listcontainersas) do ativo para obter URLs SAS, note que a função devolve vários URLs SAS, uma vez que existem duas chaves de conta de armazenamento para cada conta de armazenamento. Uma conta de armazenamento tem duas teclas porque permite uma rotação perfeita das chaves da conta de armazenamento (por exemplo, altere uma enquanto utiliza a outra e, em seguida, comece a usar a nova chave e rode a outra tecla). O 1º URL SAS representa a chave de armazenamento1 e a segunda chave de armazenamento 2.
* Envia o ficheiro para o contentor armazenado utilizando o URL SAS.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Criar um elemento de saída para armazenar o resultado de uma tarefa

A saída [Asset](/rest/api/media/assets) armazena o resultado da tarefa de codificação. O projeto define a função **DownloadResults** que transfere os resultados deste elemento de saída para a pasta "saída", para que possa ver o que recebeu.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Criar uma Transformação e uma Tarefa que codifica o ficheiro carregado

Ao codificar ou processar conteúdos em Media Services, é um padrão comum configurar as definições de codificação como receita. Em seguida, deverá submeter uma **Tarefa** para aplicar essa receita a um vídeo. Ao apresentar novos empregos para cada novo vídeo, está a aplicar essa receita em todos os vídeos da sua biblioteca. Uma receita nos Serviços de Media chama-se **Transform.** Para obter mais informações, veja [Transforms and Jobs](./transforms-jobs-concept.md) (Transformações e Trabalhos). O exemplo descrito neste tutorial define uma receita que codifica o vídeo para transmiti-lo numa variedade de dispositivos iOS e Android.

#### <a name="transform"></a>Transformação

Ao criar uma nova instância [Transformação](/rest/api/media/transforms), tem de especificar o que pretende produzir como uma saída. O parâmetro necessário é um objeto **TransformOutput**, conforme apresentado no código abaixo. Cada **TransformOutput** contém uma **Predefinição**. A **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que estão a ser utilizadas para gerir o **TransformOutput** pretendido. O exemplo descrito neste artigo utiliza uma Predefinição incorporada chamada **AdaptiveStreaming**. A Predefinição codifica o vídeo de entrada para uma escala de bits gerada automaticamente (pares de resolução/velocidade de transmissão) com base na resolução e velocidade de transmissão de entrada e produz ficheiros ISO MP4 com vídeo H.264 e áudio AAC correspondente a cada par de resolução/velocidade de transmissão. Para obter informações sobre esta Predefinição, veja [Auto-generating bitrate ladder](autogen-bitrate-ladder.md) (Escala de bits gerada automaticamente).

Pode utilizar um EncoderNamedPreset incorporadi ou utilizar as predefinições personalizadas. Para obter mais informações, veja [Como personalizar as predefinições do codificador](customize-encoder-presets-how-to.md).

Ao criar uma [Transformação](/rest/api/media/transforms), primeiro deve verificar se já existe uma com o método **Get**, conforme apresentado no código que se segue. Nos Serviços de Multimédia v3, os métodos **Get** nas entidades devolverão um valor **nulo** se a entidade não existir (uma verificação não sensível a maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Tarefa

Conforme mencionado acima, o objeto [Transformação](/rest/api/media/transforms) é a receita e uma [Tarefa](/rest/api/media/jobs) é o pedido real para os Serviços de Multimédia aplicarem essa **Transformação** a um determinado conteúdo de áudio ou vídeo de entrada. A **Tarefa** especifica informações como a localização do vídeo de entrada e a localização da saída.

Neste exemplo, o vídeo de entrada foi carregado do computador local. Se quiser aprender a codificar a partir de um URL HTTPS, consulte [este](job-input-from-http-how-to.md) artigo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Aguardar a conclusão da Tarefa

A tarefa demora algum tempo a terminar, por isso irá querer receber uma notificação quando for concluída. O exemplo de código abaixo mostra como consultar o serviço para saber o estado da [Tarefa](/rest/api/media/jobs). As sondagens não são uma das melhores práticas recomendadas para aplicações de produção devido à latência potencial. A consulta poderá ser limitada se for sobreutilizada numa conta. Em alternativa, os programadores devem utilizar o Event Grid.

O Event Grid foi concebido para ter uma elevada disponibilidade, um desempenho consistente e um dimensionamento dinâmico. Com o Event Grid, as aplicações podem escutar e reagir a eventos a partir de praticamente todos os serviços do Azure, bem como de origens personalizadas. O processamento de eventos simples, reativo e baseado em HTTP ajuda-o a criar soluções eficientes através da filtragem e do encaminhamento inteligente de eventos.  Veja [Route events to a custom web endpoint](job-state-events-cli-how-to.md) (Encaminhar eventos para um ponto final de Web personalizado).

Normalmente, a **Tarefa** passa pelos seguintes estados: **Agendada**, **Em fila**, **Em processamento**, **Concluída** (o estado final). Se a tarefa encontrar um erro, obterá um estado de **Erro**. Se o trabalho está em vias de ser cancelado, você é **cancelado** e **cancelado** quando estiver feito.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Códigos de erro das tarefas

Ver [códigos de erro](/rest/api/media/jobs/get#joberrorcode).

### <a name="get-a-streaming-locator"></a>Obtenha um localizador de streaming

Depois de concluída a codificação, o passo seguinte consiste em disponibilizar o vídeo no Elemento de saída para reprodução para os clientes. Pode disponibilizá-lo em dois passos: primeiro, criar um [Localizador de Streaming](/rest/api/media/streaminglocators)e, em segundo lugar, construir os URLs de streaming que os clientes podem usar.

O processo de criação de um **Localizador de Streaming** chama-se publicação. Por predefinição, o **Localizador de Streaming** é válido imediatamente após a edição da API e dura até ser eliminado, a menos que configuure os tempos de início e fim opcionais.

Ao criar um [StreamingLocator,](/rest/api/media/streaminglocators)terá de especificar o **nome streamingPolicyName** desejado. Neste exemplo, estará a transmitir conteúdo limpo (ou não encriptado) para que seja utilizada a política de streaming clara **predefinida (PredefinedStreamingPoliingPolicy.ClearStreamingOnly).**

> [!IMPORTANT]
> Ao utilizar uma [Política de Streaming](/rest/api/media/streamingpolicies)personalizada, deverá conceber um conjunto limitado de tais políticas para a sua conta de Media Service e reutilizá-las para os seus StreamingLocators sempre que forem necessárias as mesmas opções e protocolos de encriptação. A sua conta de Media Service tem uma quota para o número de entradas na Política de Streaming. Não devias estar a criar uma nova Política de Streaming para cada Localizador de Streaming.

O seguinte código pressupõe que está a ligar para a função com um nome localizador único.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Enquanto a amostra deste tópico discute o streaming, você pode usar a mesma chamada para criar um Localizador de Streaming para entregar vídeo através de download progressivo.

### <a name="get-streaming-urls"></a>Obter os URLs de transmissão

Agora que o [Localizador de Streaming](/rest/api/media/streaminglocators) foi criado, pode obter os URLs de streaming, como mostrado no **GetStreamingURLs**. Para construir um URL, é necessário concatenar o nome do anfitrião [streaming Endpoint](/rest/api/media/streamingendpoints) e o caminho **do localizador de streaming.** Nesta amostra, é utilizado o ponto final de streaming *predefinido.*  Quando criar uma conta de Serviço de Mídia, este **ponto final de streaming** *predefinido* estará num estado parado, pelo que tem de ligar para **o Start**.

> [!NOTE]
> Neste método, você precisa do nome localizador que foi usado ao criar o **Localizador de Streaming** para o Ativo de saída.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Limpar os recursos na conta dos Serviços de Multimédia

Geralmente, deve limpar tudo, exceto objetos que planeia reutilizar (normalmente, reutilizar Transforms, e persistirá streamingLocators, etc.). Se quiser que a sua conta esteja limpa após a experiência, elimine os recursos que não pretende reutilizar. Por exemplo, o seguinte código elimina o trabalho, cria ativos e política de chave de conteúdo:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

1. Prima Ctrl+F5 para executar a aplicação *EncodeAndStreamFiles.*
2. Copie um dos URLs de transmissão da consola.

Este exemplo apresenta os URLs que podem ser utilizados para reproduzir o vídeo através de protocolos diferentes:

![Produção de exemplo mostrando URLs para Media Services streaming vídeo](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>Testar o URL de transmissão em fluxo

Para testar a transmissão, este artigo utiliza o Leitor de Multimédia do Azure.

> [!NOTE]
> Se um leitor estiver alojado num site de https, confirme que atualiza o URL para “https”.

1. Abra um navegador web e navegue para [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. No **URL:** caixa, cole um dos valores de URL de streaming que obteve quando executou a aplicação.
3. Selecione **Update Player**.

O Azure Media Player pode ser usado para testes, mas não deve ser usado num ambiente de produção.

## <a name="clean-up-resources"></a>Limpar os recursos

Se já não precisa de nenhum dos recursos presentes no seu grupo de recursos, incluindo as contas de armazenamento que criou e os Serviços de Multimédia que carregou neste tutorial, elimine o grupo de recursos que criou anteriormente.

Execute o seguinte comando CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Os Azure Media Services v3 SDKs não são seguros. Ao desenvolver uma aplicação multi-roscada, deve gerar e utilizar um novo objeto AzureMediaServicesClient por fio.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Consulte o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="next-steps"></a>Próximos passos

Agora que sabe como carregar, codificar e transmitir o vídeo, veja o artigo seguinte: 

> [!div class="nextstepaction"]
> [Analisar vídeos](analyze-videos-tutorial-with-api.md)
