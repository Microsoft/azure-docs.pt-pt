---
title: Analisar vídeos com Serviços de Media v3
description: Saiba como analisar vídeos usando o Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: f9c52544160e52c29b2d0e53aa957b40017dc8df
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870488"
---
# <a name="tutorial-analyze-videos-with-media-services-v3"></a>Tutorial: Analisar vídeos com Serviços de Comunicação Social v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este tutorial mostra-lhe como analisar vídeos com os Serviços de Multimédia do Microsoft Azure. Existem muitos cenários em que pode querer obter mais informações sobre conteúdos de áudio ou vídeos gravados. Por exemplo, para alcançar uma maior satisfação do cliente, as organizações podem executar o processamento de voz em texto para converter gravações de suporte de clientes num catálogo pesquisável, com índices e dashboards.

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Descarregue a aplicação de amostra descrita no tópico.
> * Examine o código que analisa o vídeo especificado.
> * Execute a aplicação.
> * Examine o resultado.
> * Limpe os recursos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Conformidade, Privacidade e Segurança
 
Como um lembrete importante, deve cumprir todas as leis aplicáveis na sua utilização do Video Indexer. Não deve utilizar o Video Indexer ou qualquer outro serviço Azure de forma a violar os direitos dos outros. Antes de enviar quaisquer vídeos, incluindo quaisquer dados biométricos, para o serviço de Indexer de Vídeo para processamento e armazenamento, deve ter todos os direitos adequados, incluindo todos os consentimentos adequados, dos indivíduos no vídeo. Para conhecer a conformidade, privacidade e segurança no Video Indexer, os Termos dos [Serviços Cognitivos](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)Azure . Para as obrigações de privacidade da Microsoft e tratamento dos seus dados, reveja a Declaração de [Privacidade](https://privacy.microsoft.com/PrivacyStatement)da Microsoft, os [Termos dos Serviços Online](https://www.microsoft.com/licensing/product-licensing/products) (OST) e a [adenda ao processamento de dados](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Mais informações sobre privacidade, incluindo sobre a retenção de dados, eliminação/destruição, estão disponíveis no OST e [aqui.](../video-indexer/faq.md) Ao utilizar o Video Indexer, concorda em ficar vinculado pelos Termos dos Serviços Cognitivos, o OST, o DPA e a Declaração de Privacidade.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver o Visual Studio instalado, obtenha [o Visual Studio Community 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social.<br/>Lembre-se dos valores que utilizou para o nome do grupo de recursos e nome da conta dos Media Services.
- Siga os passos na [Access Azure Media Services API com o Azure CLI](./access-api-howto.md) e guarde as credenciais. Terá de usá-los para aceder à API.

## <a name="download-and-configure-the-sample"></a>Descarregue e configuure a amostra

Clone o repositório do GitHub que contém o exemplo de .NET para o seu computador com o seguinte comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

O exemplo está localizado na pasta [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos).

Abra [appsettings.jsno](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/appsettings.json) seu projeto descarregado. Substitua os valores pelas credenciais que obteve de [aceder às APIs.](./access-api-howto.md)

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>Examinar o código que analisa o vídeo especificado

Esta secção examina funções definidas no ficheiro [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) do projeto *AnalyzeVideos*.

A amostra completa as seguintes ações:

1. Cria um **Transform** and a **Job** que analisa o seu vídeo.
2. Cria um **Ativo** de entrada e envia o vídeo para dentro dele. O elemento é utilizado como entrada da tarefa.
3. Cria um elemento de saída que armazena a saída da tarefa.
4. Submete a tarefa.
5. Verifica o estado da tarefa.
6. Transfere os ficheiros que resultaram da execução da tarefa.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a utilizar APIs dos Serviços de Multimédia com SDK do .NET

Para começar a utilizar as APIs dos Serviços de Multimédia com o .NET, tem de criar um objeto **AzureMediaServicesClient**. Para criar o objeto, tem de fornecer as credenciais necessárias para o cliente se ligar ao Azure com o Azure AD. No código que clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no ficheiro de configuração local. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Criar um elemento de entrada e carregar um ficheiro local para ele 

A função **CreateInputAsset** cria um novo [Elemento](/rest/api/media/assets) de entrada e carrega o ficheiro de vídeo local especificado para ele. Este Elemento é utilizado como entrada para a Tarefa de codificação. Nos Serviços de Multimédia v3, a entrada para uma Tarefa pode ser um Elemento ou pode ser conteúdo que tenha disponibilizado na sua conta dos Serviços de Multimédia através de URLs HTTPS. Para aprender a codificar a partir de um URL HTTPS, consulte [este](job-input-from-http-how-to.md) artigo.  

Nos Serviços de Multimédia v3, deverá utilizar as APIs do Armazenamento do Azure para carregar os ficheiros. O seguinte fragmento de .NET mostra como.

A seguinte função completa estas ações:

* Cria um Ativo.
* Obtém um [URL SAS](../../storage/common/storage-sas-overview.md) writable para o contentor do Ativo [no armazenamento](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container).

    Se utilizar a função [ListContainerSas](/rest/api/media/assets/listcontainersas) do ativo para obter URLs SAS, note que a função devolve vários URLs SAS, uma vez que existem duas chaves de conta de armazenamento para cada conta de armazenamento. Uma conta de armazenamento tem duas teclas porque permite uma rotação perfeita das chaves da conta de armazenamento (por exemplo, altere uma enquanto utiliza a outra e, em seguida, comece a usar a nova chave e rode a outra tecla). O 1º URL SAS representa a chave de armazenamento1 e a segunda chave de armazenamento 2.
* Envia o ficheiro para o contentor armazenado utilizando o URL SAS.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>Criar um elemento de saída para armazenar o resultado da tarefa

O [Elemento](/rest/api/media/assets) de saída armazena o resultado da sua tarefa. O projeto define a função **DownloadResults** que transfere os resultados deste elemento de saída para a pasta "saída", para que possa ver o que recebeu.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Criar uma transformação e uma tarefa que analisa vídeos

Ao codificar ou processar conteúdos em Media Services, é um padrão comum configurar as definições de codificação como receita. Em seguida, deverá submeter uma **Tarefa** para aplicar essa receita a um vídeo. Ao submeter novos Jobs para cada novo vídeo, está a aplicar essa receita em todos os vídeos da sua biblioteca. Uma receita nos Serviços de Media chama-se **Transform.** Para mais informações, consulte [Transforms e jobs.](./transforms-jobs-concept.md) O exemplo descrito neste tutorial define uma receita que analisa o vídeo especificado.

#### <a name="transform"></a>Transformação

Ao criar uma nova instância [Transformação](/rest/api/media/transforms), tem de especificar o que pretende produzir como uma saída. **TransformOutput**  é um parâmetro necessário. Cada **TransformOutput** contém uma **Predefinição**. A **Predefinição** descreve instruções passo a passo das operações de processamento de áudio e/ou vídeo que estão a ser utilizadas para gerir o **TransformOutput** pretendido. Neste exemplo, é utilizada a predefinição **do VideoAnalyzerPreset** e a língua ("en-US") é passada para o seu construtor `new VideoAnalyzerPreset("en-US")` . Esta predefinição permite-lhe extrair múltiplas informações de vídeo e áudio a partir de um vídeo. Pode utilizar a predefinição **AudioAnalyzerPreset** se precisar de extrair múltiplas informações de áudio a partir de um vídeo.

Ao criar um **Transform,** verifique primeiro se já existe um utilizando o método **Get,** como mostra o código que se segue. Nos Serviços de Multimédia v3, os métodos **Get** nas entidades devolverão um valor **nulo** se a entidade não existir (uma verificação não sensível a maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Tarefa

Conforme mencionado acima, o objeto [Transformação](/rest/api/media/transforms) é a receita e uma [Tarefa](/rest/api/media/jobs) é o pedido real para os Serviços de Multimédia aplicarem essa **Transformação** a um determinado conteúdo de áudio ou vídeo de entrada. O **Trabalho** especifica informações como a localização do vídeo de entrada e a localização para a saída. Pode especificar a localização do seu vídeo com: URLs HTTPS, URLs SAS ou Recursos que estão na sua conta dos Serviços de Multimédia.

Neste exemplo, a entrada da tarefa é um vídeo local.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Aguarde até que a tarefa termine

O trabalho leva algum tempo para ser concluído. Quando acontecer, quer ser notificado. Existem algumas opções diferentes para ser notificado sobre a conclusão da [Tarefa](/rest/api/media/jobs). A opção mais simples (que é mostrada aqui) é usar as sondagens.

As sondagens não são uma das melhores práticas recomendadas para aplicações de produção devido à latência potencial. A consulta poderá ser limitada se for sobreutilizada numa conta. Em alternativa, os programadores devem utilizar o Event Grid.

O Event Grid foi concebido para ter uma elevada disponibilidade, um desempenho consistente e um dimensionamento dinâmico. Com o Event Grid, as aplicações podem escutar e reagir a eventos a partir de praticamente todos os serviços do Azure, bem como de origens personalizadas. O processamento de eventos simples, reativo e baseado em HTTP ajuda-o a criar soluções eficientes através da filtragem e do encaminhamento inteligente de eventos. Para obter mais informações, consulte [os eventos da Rota para um ponto final personalizado da web.](monitoring/job-state-events-cli-how-to.md)

Normalmente, a **Tarefa** passa pelos seguintes estados: **Agendada**, **Em fila**, **Em processamento**, **Concluída** (o estado final). Se o trabalho tiver tido um erro, obtém-se o estado **de Erro.** Se o trabalho está em vias de ser cancelado, você é **cancelado** e depois **cancelado** quando estiver feito.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Códigos de erro das tarefas

Ver [códigos de erro](/rest/api/media/jobs/get#joberrorcode).

### <a name="download-the-result-of-the-job"></a>Transferir o resultado da tarefa

A seguinte função descarrega os resultados da saída [O Ativo](/rest/api/media/assets) para a pasta "saída" para que possa examinar os resultados do trabalho.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Limpar os recursos na conta dos Serviços de Multimédia

[!INCLUDE [clean-up-warning](includes/clean-up-warning.md)]

Geralmente, deve limpar tudo, exceto objetos que planeia reutilizar (normalmente, reutilizará Transformações e persistirá streamingLocators). Se quiser que a sua conta esteja limpa após a experiência, elimine os recursos que não pretende reutilizar. Por exemplo, o seguinte código elimina o ativo de trabalho e de saída:

### <a name="delete-resources-with-code"></a>Eliminar recursos com código

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

Também pode utilizar o CLI.

[!INCLUDE [clean-up-resources-cli](includes/clean-up-resources-cli.md)]

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Prima Ctrl+F5 para executar a aplicação *AnalyzeVideos.*

Quando executa o programa, a tarefa produz miniaturas para cada rosto que encontra no vídeo. Também produz o ficheiro insights.json.

## <a name="examine-the-output"></a>Examinar o ficheiro de saída

O ficheiro de saída para analisar vídeos chama-se insights.json. Este ficheiro contém informações sobre o seu vídeo. Encontrará a descrição dos elementos contidos no ficheiro json no artigo [Informações de multimédia](./analyzing-video-audio-files-concept.md).

## <a name="multithreading"></a>Multithreading

> [!WARNING]
> Os Azure Media Services v3 SDKs não são seguros. Ao trabalhar com uma aplicação multi-roscadas, deverá gerar um novo objeto AzureMediaServicesClient por fio.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: upload, encode, and stream files](stream-files-tutorial-with-api.md) (Tutorial: carregar, codificar e transmitir ficheiros)
