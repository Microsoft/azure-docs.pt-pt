---
title: Criptografe vídeo com AES-128
titleSuffix: Azure Media Services
description: Saiba como encriptar vídeos com encriptação a128 bits aEs E como utilizar o serviço de entrega de chaves nos Serviços De Mídia Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 126700e6290650221a9cb9711b22472301409fca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974177"
---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>Tutorial: Criptografe o vídeo com AES-128 e use o serviço de entrega de chaves

> [!NOTE]
> Mesmo que o tutorial utilize os exemplos [.NET SDK,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) os passos gerais são os mesmos para [REST API,](https://docs.microsoft.com/rest/api/media/liveevents) [CLI,](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)ou outros [SDKs](media-services-apis-overview.md#sdks)suportados .

Pode utilizar os Serviços de Media para fornecer http Live Streaming (HLS), MPEG-DASH e Smooth Streaming encriptados com o AES utilizando chaves de encriptação de 128 bits. A Media Services também fornece o serviço de entrega chave que fornece chaves de encriptação a utilizadores autorizados. Se pretender que os Serviços de Media criptografem o seu vídeo de forma dinâmica, associa a chave de encriptação a um Localizador de Streaming e também configura a política de chave de conteúdo. Quando um stream é solicitado por um leitor, o Media Services utiliza a chave especificada para encriptar dinamicamente o seu conteúdo com AES-128. Para desencriptar a transmissão em fluxo, o leitor solicita a chave ao serviço de entrega de chaves. Para determinar se o utilizador está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que especificou para a chave.

Pode encriptar cada elemento com vários tipos de encriptação (AES-128, PlayReady, Widevine, FairPlay). Veja [Protocolos de transmissão em fluxo e tipos de encriptação](content-protection-overview.md#streaming-protocols-and-encryption-types), para ver o que faz sentido combinar. Além disso, consulte [Como proteger com a DRM](protect-with-drm.md).

A saída da amostra deste artigo inclui um URL para o Azure Media Player, url manifesto, e o token AES necessário para reproduzir o conteúdo. A amostra define a expiração do token Web JSON (JWT) para 1 hora. Pode abrir um navegador e colar o URL resultante para lançar a página de demonstração do Azure Media ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```Player com o URL e token preenchidos já no seguinte formato: .

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Descarregue a amostra [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) descrita no artigo.
> * Comece a utilizar APIs de Serviços de Media com .NET SDK.
> * Criar um ativo de saída.
> * Criar uma Transformação de Codificação.
> * Submeta um trabalho.
> * Espere que o Trabalho termine.
> * Criar uma política de chave de conteúdo
> * Configure a política para utilizar a restrição de símbolojt.
> * Crie um Localizador de Streaming.
> * Configure o Localizador de Streaming para encriptar o vídeo com AES (ClearKey).
> * Arranja um símbolo de teste.
> * Construa um URL de streaming.
> * Limpe os recursos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

O seguinte é necessário para concluir o tutorial.

* Reveja o artigo [Descrição Geral da Proteção de Conteúdo](content-protection-overview.md).
* Instale o Código do Estúdio Visual ou o Estúdio Visual.
* [Criar uma conta de Media Services.](create-account-cli-quickstart.md)
* Obtenha credenciais necessárias para utilizar APIs de Serviços de Media seguindo [APIs](access-api-cli-how-to.md)de acesso .

## <a name="download-code"></a>Transferir código

Clone um repositório do GitHub que contenha o exemplo completo de .NET abordado neste artigo para o seu computador, com o seguinte comando:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

A amostra "Encrypt with AES-128" está localizada na pasta [EncryptWithAES.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES)

> [!NOTE]
> A amostra cria recursos únicos sempre que executa a aplicação. Normalmente, você reutilizará os recursos existentes como transformações e políticas (se os recursos existentes tiverem configurações necessárias).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a utilizar APIs dos Serviços de Multimédia com SDK do .NET

Para começar a utilizar APIs de Serviços de Media com .NET, crie um objeto **AzureMediaServicesClient.** Para criar o objeto, tem de fornecer as credenciais necessárias para o cliente se ligar ao Azure com o Azure AD. No código que clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials, com base nas credenciais fornecidas no ficheiro de configuração local.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Criar um elemento de saída  

A saída [Asset](https://docs.microsoft.com/rest/api/media/assets) armazena o resultado da tarefa de codificação.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Obter ou criar uma Transformação de codificação

Ao criar uma nova instância [Transformação](https://docs.microsoft.com/rest/api/media/transforms), tem de especificar o que pretende produzir como uma saída. O parâmetro necessário é um objeto **TransformOutput**, conforme apresentado no código abaixo. Cada **TransformOutput** contém uma **Predefinição**. A **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que estão a ser utilizadas para gerir o **TransformOutput** pretendido. O exemplo descrito neste artigo utiliza uma Predefinição incorporada chamada **AdaptiveStreaming**. O Preset codifica o vídeo de entrada numa escada de bitrate autogerada (pares de resolução de bitrate) com base na resolução de entrada e bitrate, e depois produz ficheiros ISO MP4 com vídeo H.264 e áudio AAC correspondente a cada par de resolução de bitrate.

Antes de criar um novo [Transform](https://docs.microsoft.com/rest/api/media/transforms), verifique primeiro se já existe o método **Get,** como mostra o código que se segue. Nos Serviços de Multimédia v3, os métodos **Get** nas entidades devolverão um valor **nulo** se a entidade não existir (uma verificação não sensível a maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Submeter Tarefa

Conforme mencionado acima, o objeto [Transformação](https://docs.microsoft.com/rest/api/media/transforms) é a receita e uma [Tarefa](https://docs.microsoft.com/rest/api/media/jobs) é o pedido real para os Serviços de Multimédia aplicarem essa **Transformação** a um determinado conteúdo de áudio ou vídeo de entrada. O **Trabalho** especifica informações como a localização do vídeo de entrada e a localização para a saída.

Neste tutorial, criamos a entrada do trabalho com base num ficheiro que é ingerido diretamente de um URL de [origem HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Aguardar a conclusão da Tarefa

O trabalho leva algum tempo para ser concluído. Quando acontecer, quer ser notificado. O exemplo de código abaixo mostra como consultar o serviço para saber o estado da [Tarefa](https://docs.microsoft.com/rest/api/media/jobs). As sondagens não são uma boa prática recomendada para apps de produção devido a uma possível latência. A consulta poderá ser limitada se for sobreutilizada numa conta. Em alternativa, os programadores devem utilizar o Event Grid. Para mais informações, consulte os [eventos da Route para um ponto final personalizado](job-state-events-cli-how-to.md)da web .

Normalmente, a **Tarefa** passa pelos seguintes estados: **Agendada**, **Em fila**, **Em processamento**, **Concluída** (o estado final). Se o trabalho tiver deparar-se com um erro, ficas com o estado **de erro.** Se o trabalho estiver em vias de ser cancelado, será **cancelado** e **cancelado** quando estiver feito.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Criar uma política de chave de conteúdo

Uma chave de conteúdo fornece acesso seguro aos seus Elementos. É necessário criar uma **Política chave** de conteúdo que conselhe como a chave de conteúdo é entregue aos clientes finais. A chave de conteúdo está associada ao Localizador de **Streaming**. A Media Services também fornece o serviço de entrega chave que fornece chaves de encriptação a utilizadores autorizados.

Quando um stream é solicitado por um leitor, o Media Services utiliza a chave especificada para encriptar dinamicamente o seu conteúdo (neste caso, utilizando a encriptação AES.) Para desencriptar o fluxo, o leitor solicita a chave do serviço de entrega da chave. Para determinar se o utilizador está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que especificou para a chave.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Criar um localizador de streaming

Depois de concluída a codificação e de a política de chave de conteúdo estar definida, o passo seguinte consiste em disponibilizar o vídeo no Elemento de saída para reprodução pelos clientes. Disponibiliza o vídeo em dois passos:

1. Crie um Localizador de [Streaming.](https://docs.microsoft.com/rest/api/media/streaminglocators)
2. Crie os URLs de transmissão em fluxo que os clientes podem utilizar.

O processo de criação do Localizador de **Streaming** chama-se publicação. Por predefinição, o Localizador de **Streaming** é válido imediatamente após a efetuação das chamadas API. Dura até ser apagado, a menos que configure os tempos de início e fim opcionais.

Ao criar um Localizador de [Streaming,](https://docs.microsoft.com/rest/api/media/streaminglocators)terá de especificar o nome de Política de **Streaming**desejado . Neste tutorial, estamos a usar uma das Políticas de Streaming Predefinidas, que diz à Azure Media Services como publicar os conteúdos para streaming. Neste exemplo, a encriptação Do Envelope AES é aplicada (esta encriptação também é conhecida como encriptação ClearKey porque a chave é entregue ao cliente de reprodução via HTTPS e não uma licença de DRM).

> [!IMPORTANT]
> Ao utilizar uma Política de [Streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies)personalizada, deve conceber um conjunto limitado de tais políticas para a sua conta de Serviço de Media e reutilizá-las para os seus Localizadores de Streaming sempre que forem necessárias as mesmas opções e protocolos de encriptação. A conta dos Serviços de Multimédia tem uma quota para o número de entradas de StreamingPolicy. Não devias estar a criar uma nova Política de Streaming para cada Localizador de Streaming.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Obter um token de teste

Neste tutorial, vamos especificar que a política de chave de conteúdo tem uma restrição de token. A política de token restrito tem de ser acompanhada por um token emitido por um serviço de tokens seguro (STS). A Media Services suporta tokens no formato [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) e é isso que configuramos na amostra.

O ContentKeyIdentifierClaim é utilizado na Política de Chave de **Conteúdo,** o que significa que o símbolo apresentado ao serviço de entrega de chaves deve ter o identificador da chave de conteúdo no mesmo. Na amostra, não especificámos uma chave de conteúdo na criação do Localizador de Streaming, o sistema criou uma aleatória para nós. Para gerar o token do teste, temos de obter o ContentKeyId para colocar na alegação ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Criar um URL de transmissão em fluxo DASH

Agora que o Localizador de [Streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) foi criado, você pode obter os URLs de streaming. Para construir um URL, você precisa concatenar o nome anfitrião [do StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) e o caminho **do localizador de streaming.** Nesta amostra, é utilizado o **ponto final de streaming** *predefinido.* Quando criar uma conta de Serviço de Media, este **ponto final** de streaming *padrão* estará em estado de paragem, por isso precisa ligar para **Iniciar**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Limpar os recursos na conta dos Serviços de Multimédia

Geralmente, deve limpar tudo, exceto objetos que planeia reutilizar (normalmente, vai reutilizar Transforms, Streaming Locators, e assim por diante). Se quiser que a sua conta esteja limpa após a experimentação, elimine os recursos que não planeia reutilizar. Por exemplo, o seguinte código elimina Jobs:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisa de nenhum dos recursos presentes no seu grupo de recursos, incluindo as contas de armazenamento que criou e os Serviços de Multimédia que carregou neste tutorial, elimine o grupo de recursos que criou anteriormente.

Executar o seguinte comando CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Confira o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="additional-notes"></a>Notas adicionais

* A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Proteger com ADRM](protect-with-drm.md)
