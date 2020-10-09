---
title: Encrypt vídeo com AES-128
titleSuffix: Azure Media Services
description: Saiba como encriptar o vídeo com encriptação AES de 128 bits e como utilizar o serviço de entrega chave nos Serviços Azure Media.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 5347479d32dc9f4909483dc63891e8057fd7ff86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89289348"
---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>Tutorial: Criptografe o vídeo com a AES-128 e use o serviço de entrega de chaves

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Mesmo que o tutorial utilize os exemplos [.NET SDK,](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) os passos gerais são os mesmos para [REST API,](/rest/api/media/liveevents) [CLI,](/cli/azure/ams/live-event?view=azure-cli-latest)ou [outros SDKs suportados.](media-services-apis-overview.md#sdks)

Pode utilizar os Serviços de Comunicação social para fornecer HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming encriptados com o AES utilizando chaves de encriptação de 128 bits. Os Serviços de Comunicação Social também fornecem o serviço de entrega chave que fornece chaves de encriptação aos utilizadores autorizados. Se pretender que os Serviços de Media criptografem dinamicamente o seu vídeo, associa a chave de encriptação a um Localizador de Streaming e também configura a política de chave de conteúdo. Quando um stream é solicitado por um jogador, os Media Services utilizam a chave especificada para encriptar dinamicamente o seu conteúdo com a AES-128. Para desencriptar a transmissão em fluxo, o leitor solicita a chave ao serviço de entrega de chaves. Para determinar se o utilizador está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que especificou para a chave.

Pode encriptar cada elemento com vários tipos de encriptação (AES-128, PlayReady, Widevine, FairPlay). Veja [Protocolos de transmissão em fluxo e tipos de encriptação](content-protection-overview.md#streaming-protocols-and-encryption-types), para ver o que faz sentido combinar. Além disso, veja [como proteger com DRM.](protect-with-drm.md)

A saída da amostra deste artigo inclui um URL para o Azure Media Player, URL manifesto, e o símbolo AES necessário para reproduzir o conteúdo. A amostra define a expiração do token web JSON (JWT) para 1 hora. Pode abrir um browser e colar o URL resultante para lançar a página de demonstração do Azure Media Player com o URL e o token preenchido para si já no seguinte formato: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}``` .

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Descarregue a amostra [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) descrita no artigo.
> * Comece a utilizar APIs de Serviços de Mídia com .NET SDK.
> * Criar um ativo de saída.
> * Crie uma transformação codificadora.
> * Submeter um Trabalho.
> * Espere que o Trabalho termine.
> * Criar uma política de chave de conteúdo
> * Configure a política de utilização da restrição simbólica JWT.
> * Crie um localizador de streaming.
> * Configure o Localizador de Streaming para encriptar o vídeo com AES (ClearKey).
> * Faça um teste.
> * Construa uma URL de streaming.
> * Limpe os recursos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

O seguinte é necessário para concluir o tutorial.

* Reveja o artigo [Descrição Geral da Proteção de Conteúdo](content-protection-overview.md).
* Instale o Visual Studio Code ou o Visual Studio.
* [Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social.
* Obtenha credenciais necessárias para utilizar APIs de Serviços de Mídia seguindo [APIs de acesso](./access-api-howto.md).

## <a name="download-code"></a>Transferir código

Clone um repositório do GitHub que contenha o exemplo completo de .NET abordado neste artigo para o seu computador, com o seguinte comando:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

A amostra "Criptografe com AES-128" está localizada na pasta [EncryptWithAES.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES)

> [!NOTE]
> A amostra cria recursos únicos sempre que executa a aplicação. Normalmente, reutilizará os recursos existentes como transformações e políticas (se o recurso existente tiver configurações necessárias).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a utilizar APIs dos Serviços de Multimédia com SDK do .NET

Para começar a utilizar APIs de Serviços de Mídia com .NET, crie um objeto **AzureMediaServicesClient.** Para criar o objeto, tem de fornecer as credenciais necessárias para o cliente se ligar ao Azure com o Azure AD. No código que clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials, com base nas credenciais fornecidas no ficheiro de configuração local.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Criar um elemento de saída  

A saída [Asset](/rest/api/media/assets) armazena o resultado da tarefa de codificação.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Obter ou criar uma Transformação de codificação

Ao criar uma nova instância [Transformação](/rest/api/media/transforms), tem de especificar o que pretende produzir como uma saída. O parâmetro necessário é um objeto **TransformOutput**, conforme apresentado no código abaixo. Cada **TransformOutput** contém uma **Predefinição**. A **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que estão a ser utilizadas para gerir o **TransformOutput** pretendido. O exemplo descrito neste artigo utiliza uma Predefinição incorporada chamada **AdaptiveStreaming**. O Predefinição codifica o vídeo de entrada numa escada bitrate autogerada (pares de resolução bitrate) com base na resolução de entrada e bitrate, e depois produz ficheiros MP4 ISO com vídeo H.264 e áudio AAC correspondentes a cada par de resolução de bitrate.

Antes de criar um novo [Transform](/rest/api/media/transforms), verifique primeiro se já existe um utilizando o método **Get,** como mostra o código que se segue. Nos Serviços de Multimédia v3, os métodos **Get** nas entidades devolverão um valor **nulo** se a entidade não existir (uma verificação não sensível a maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Submeter Tarefa

Conforme mencionado acima, o objeto [Transformação](/rest/api/media/transforms) é a receita e uma [Tarefa](/rest/api/media/jobs) é o pedido real para os Serviços de Multimédia aplicarem essa **Transformação** a um determinado conteúdo de áudio ou vídeo de entrada. O **Trabalho** especifica informações como a localização do vídeo de entrada e a localização para a saída.

Neste tutorial, criamos a entrada do trabalho com base num ficheiro que é ingerido diretamente a partir de um [URL de origem HTTPs.](job-input-from-http-how-to.md)

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Aguardar a conclusão da Tarefa

O trabalho leva algum tempo para ser concluído. Quando acontecer, quer ser notificado. O exemplo de código abaixo mostra como consultar o serviço para saber o estado da [Tarefa](/rest/api/media/jobs). As sondagens não são uma das melhores práticas recomendadas para aplicações de produção devido à latência potencial. A consulta poderá ser limitada se for sobreutilizada numa conta. Em alternativa, os programadores devem utilizar o Event Grid. Para obter mais informações, consulte [os eventos da Rota para um ponto final personalizado da web.](job-state-events-cli-how-to.md)

Normalmente, a **Tarefa** passa pelos seguintes estados: **Agendada**, **Em fila**, **Em processamento**, **Concluída** (o estado final). Se o trabalho tiver tido um erro, obtém-se o estado **de Erro.** Se o trabalho está em vias de ser cancelado, você é **cancelado** e **cancelado** quando estiver feito.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Criar uma política de chave de conteúdo

Uma chave de conteúdo fornece acesso seguro aos seus Elementos. É necessário criar uma **Política de Chave de Conteúdo** que configura a forma como a chave de conteúdo é entregue aos clientes finais. A chave de conteúdo está associada ao **Localizador de Streaming.** Os Serviços de Comunicação Social também fornecem o serviço de entrega chave que fornece chaves de encriptação aos utilizadores autorizados.

Quando um stream é solicitado por um leitor, os Media Services utilizam a chave especificada para encriptar dinamicamente o seu conteúdo (neste caso, utilizando a encriptação AES.) Para desencriptar o fluxo, o leitor solicita a chave do serviço de entrega da chave. Para determinar se o utilizador está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que especificou para a chave.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Criar um localizador de streaming

Depois de concluída a codificação e de a política de chave de conteúdo estar definida, o passo seguinte consiste em disponibilizar o vídeo no Elemento de saída para reprodução pelos clientes. Disponibiliza o vídeo em dois passos:

1. Criar um [localizador de streaming.](/rest/api/media/streaminglocators)
2. Crie os URLs de transmissão em fluxo que os clientes podem utilizar.

O processo de criação do **Localizador de Streaming** chama-se publicação. Por predefinição, o **Localizador de Streaming** é válido imediatamente após a edição da API. Dura até ser apagado, a não ser que configuure os tempos de início e fim opcionais.

Ao criar um [localizador de streaming,](/rest/api/media/streaminglocators)terá de especificar o **nome streamingPolicyName**desejado. Neste tutorial, estamos a usar uma das PredefinedStreamingPolicies, que diz à Azure Media Services como publicar os conteúdos para streaming. Neste exemplo, é aplicada a encriptação AES Envelope (esta encriptação também é conhecida como encriptação ClearKey porque a chave é entregue ao cliente de reprodução através de HTTPS e não de uma licença DRM).

> [!IMPORTANT]
> Ao utilizar um [StreamingPolicy](/rest/api/media/streamingpolicies)personalizado, deverá conceber um conjunto limitado de tais políticas para a sua conta De Serviço de Media e reutilizá-las para os seus Localizadores de Streaming sempre que forem necessárias as mesmas opções e protocolos de encriptação. A conta dos Serviços de Multimédia tem uma quota para o número de entradas de StreamingPolicy. Não devias estar a criar um novo StreamingPolicy para cada Localizador de Streaming.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Obter um token de teste

Neste tutorial, vamos especificar que a política de chave de conteúdo tem uma restrição de token. A política de token restrito tem de ser acompanhada por um token emitido por um serviço de tokens seguro (STS). Os Serviços de Comunicação Social suportam fichas no formato [JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) e é isso que configuramos na amostra.

O ContentKeyIdentifierClaim é utilizado na **Política de Chave de Conteúdo,** o que significa que o símbolo apresentado ao serviço de Entrega de Chaves deve ter o identificador da chave de conteúdo nele. Na amostra, não especificámos uma chave de conteúdo ao criar o Localizador de Streaming, o sistema criou uma aleatória para nós. Para gerar o token de teste, temos de fazer com que o ContentKeyId coloque na alegação ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Criar um URL de transmissão em fluxo DASH

Agora que o [Localizador de Streaming](/rest/api/media/streaminglocators) foi criado, pode obter os URLs de streaming. Para construir um URL, é necessário concatenar o nome de anfitrião [streamingEndpoint](/rest/api/media/streamingendpoints) e o caminho **do localizador de streaming.** Nesta amostra, é utilizado o ponto final de streaming *predefinido.* **Streaming Endpoint** Quando criar uma conta de Serviço de Mídia, este **ponto final de streaming** *predefinido* estará num estado parado, pelo que tem de ligar para **o Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Limpar os recursos na conta dos Serviços de Multimédia

Geralmente, deve limpar tudo, exceto objetos que planeia reutilizar (normalmente, reutilizará Transformas, Localizadores de Streaming, e assim por diante). Se quiser que a sua conta esteja limpa após a experiência, elimine os recursos que não pretende reutilizar. Por exemplo, o seguinte código elimina o trabalho, cria ativos e política de chave de conteúdo:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Limpar os recursos

Se já não precisa de nenhum dos recursos presentes no seu grupo de recursos, incluindo as contas de armazenamento que criou e os Serviços de Multimédia que carregou neste tutorial, elimine o grupo de recursos que criou anteriormente.

Execute o seguinte comando CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Consulte o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="additional-notes"></a>Notas adicionais

* Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Proteger com DRM](protect-with-drm.md)
