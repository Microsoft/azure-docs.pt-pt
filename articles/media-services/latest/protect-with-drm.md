---
title: Serviço de encriptação DRM e entrega de licenças da Azure Media Services
titleSuffix: Azure Media Services
description: Saiba como utilizar o serviço de encriptação dinâmica DRM e entrega de licenças para fornecer streams encriptados com licenças Microsoft PlayReady, Google Widevine ou Apple FairPlay.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 14ba5f270138db22a76fd697b264046e22577427
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086725"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Tutorial: Utilize encriptação dinâmica DRM e serviço de entrega de licenças

> [!NOTE]
> Mesmo que este tutorial utilize os exemplos [.NET SDK,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) os passos gerais são os mesmos para [REST API,](https://docs.microsoft.com/rest/api/media/liveevents) [CLI,](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)ou outros [SDKs](media-services-apis-overview.md#sdks)suportados .

Pode utilizar os Serviços de Multimédia do Azure para entregar transmissões em fluxo encriptadas com licenças do Microsoft PlayReady, Google Widevine ou Apple FairPlay. Para uma explicação aprofundada, consulte [a proteção do conteúdo com encriptação dinâmica](content-protection-overview.md).

A Media Services também fornece um serviço para a entrega de licenças PlayReady, Widevine e FairPlay DRM. Quando um utilizador solicita conteúdo protegido pela DRM, a aplicação do jogador solicita uma licença do serviço de licença de Serviços de Media. Se a aplicação do jogador for autorizada, o serviço de licença de Serviços de Media emite uma licença ao jogador. Uma licença contém a chave de desencriptação que pode ser utilizada pelo leitor de cliente para desencriptar e transmitir em fluxo o conteúdo.

Este artigo baseia-se no exemplo [Encriptar com o DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

O exemplo descrito neste artigo produz o seguinte resultado:

![AMS com vídeo protegido da DRM no Azure Media Player](./media/protect-with-drm/ams_player.png)

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Criar uma Transformação de Codificação.
> * Definir a chave de assinatura utilizada para a verificação do token.
> * Detete requisitos na política de chave de conteúdo.
> * Crie um Localizador de Streaming com a política de streaming especificada.
> * Crie um URL usado para reproduzir o seu ficheiro.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

São necessários os itens seguintes para concluir o tutorial:

* Reveja o artigo [Descrição Geral da Proteção de Conteúdo](content-protection-overview.md).
* Reveja o sistema de proteção de [conteúdos multi-DRM do Design com controlo de acesso](design-multi-drm-system-with-access-control.md).
* Instale o Código do Estúdio Visual ou o Estúdio Visual.
* Crie uma nova conta dos Serviços de Multimédia do Azure, conforme descrito [neste início rápido](create-account-cli-quickstart.md).
* Obter as credenciais necessárias para utilizar as APIs dos Serviços de Multimédia, ao seguir [APIs de Acesso](access-api-cli-how-to.md)
* Detete os valores apropriados no ficheiro de configuração da aplicação (appsettings.json).

## <a name="download-code"></a>Transferir código

Clone um repositório do GitHub que contenha o exemplo completo de .NET abordado neste artigo para o seu computador, com o seguinte comando:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
O exemplo "Encrypt with DRM" está localizado na pasta [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> A amostra cria recursos únicos sempre que executa a aplicação. Normalmente, você reutilizará os recursos existentes como transformações e políticas (se os recursos existentes tiverem configurações necessárias).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a utilizar as APIs dos Serviços de Multimédia com o SDK .NET

Para começar a utilizar APIs de Serviços de Media com .NET, crie um objeto **AzureMediaServicesClient.** Para criar o objeto, tem de fornecer as credenciais necessárias para o cliente se ligar ao Azure com o Azure AD. No código que clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials, com base nas credenciais fornecidas no ficheiro de configuração local.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Criar um elemento de saída  

A saída [Asset](assets-concept.md) armazena o resultado da tarefa de codificação.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Obter ou criar uma Transformação de codificação

Ao criar uma nova instância [Transformar](transforms-jobs-concept.md), tem de especificar a saída resultante que pretende. O parâmetro necessário é um objeto `transformOutput`, como indicado no código abaixo. Cada TransformOutput contém um **Preset**. Predefinição descreve as instruções passo-a-passo das operações de processamento de vídeo e/ou áudio que devem ser usadas para gerar a TransformOutput desejada. O exemplo descrito neste artigo utiliza uma Predefinição incorporada chamada **AdaptiveStreaming**. O Preset codifica o vídeo de entrada numa escada de bitrate autogerada (pares de resolução de bitrate) com base na resolução de entrada e bitrate, e produz ficheiros ISO MP4 com vídeo H.264 e áudio AAC correspondente a cada par de resolução de bitrate. 

Antes de criar uma nova **Transformação**, em primeiro lugar deve verificar se já existe uma com o método **Get**, conforme mostrado no código que se segue.  Nos Serviços de Multimédia v3, os métodos **Get** nas entidades devolverão um valor **nulo** se a entidade não existir (uma verificação não sensível a maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Submeter Tarefa

Conforme mencionado acima, o objeto **Transformação** é a receita e uma [Tarefa](transforms-jobs-concept.md) é o pedido real para os Serviços de Multimédia aplicarem essa **Transformação** a um determinado conteúdo de áudio ou vídeo de entrada. O **Trabalho** especifica informações como a localização do vídeo de entrada e a localização para a saída.

Neste tutorial, criamos a entrada do trabalho com base num ficheiro que é ingerido diretamente de um URL de [origem HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Aguardar a conclusão da Tarefa

O trabalho leva algum tempo para ser concluído. Quando acontecer, quer ser notificado. O exemplo de código abaixo mostra como consultar o serviço para saber o estado da **Tarefa**. As sondagens não são uma boa prática recomendada para apps de produção devido a uma possível latência. A consulta poderá ser limitada se for sobreutilizada numa conta. Em alternativa, os programadores devem utilizar o Event Grid. Veja [Route events to a custom web endpoint](job-state-events-cli-how-to.md) (Encaminhar eventos para um ponto final de Web personalizado).

Normalmente, a **Tarefa** passa pelos seguintes estados: **Agendada**, **Em fila**, **Em processamento**, **Concluída** (o estado final). Se o trabalho tiver deparar-se com um erro, ficas com o estado **de erro.** Se o trabalho estiver em vias de ser cancelado, será **cancelado** e **cancelado** quando estiver feito.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Criar uma política de chave de conteúdo

Uma chave de conteúdo fornece acesso seguro aos seus Elementos. É necessário criar uma [Política de Chave](content-key-policy-concept.md) de Conteúdo ao encriptar o seu conteúdo com uma DRM. A política confunde como a chave de conteúdo é entregue aos clientes finais. A chave de conteúdo está associada a um Localizador de Streaming. Os Serviços de Multimédia também fornecem o serviço de entrega de chaves que é responsável pelo fornecimento de chaves e licenças de encriptação aos utilizadores autorizados.

É necessário definir os requisitos (restrições) na **Política de Chave** de Conteúdo que devem ser satisfeitas para entregar as teclas com a configuração especificada. Neste exemplo, definimos as configurações e os requisitos seguintes:

* Configuração

    As licenças do [PlayReady](playready-license-template-overview.md) e [Widevine](widevine-license-template-overview.md) estão configuradas para que possam ser fornecidas pelo serviço de entrega de licenças dos Serviços de Multimédia. Mesmo que esta aplicação de amostras não configure a licença [FairPlay,](fairplay-license-overview.md) contém um método que pode usar para configurar fairPlay. Pode adicionar a configuração FairPlay como outra opção.

* Restrição

    A aplicação define uma restrição do tipo de token da JSON Web Token (JWT) na apólice.

Quando uma transmissão em fluxo é pedida por um leitor, os Serviços de Multimédia utilizam a chave especificada para encriptar dinamicamente o seu conteúdo. Para desencriptar a transmissão em fluxo, o leitor solicita a chave ao serviço de entrega de chaves. Para determinar se o utilizador está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que especificou para a chave.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Criar um localizador de streaming

Depois de concluída a codificação e de a política de chave de conteúdo estar definida, o passo seguinte consiste em disponibilizar o vídeo no Elemento de saída para reprodução pelos clientes. Disponibiliza o vídeo em dois passos:

1. Crie um Localizador de [Streaming.](streaming-locators-concept.md)
2. Crie os URLs de transmissão em fluxo que os clientes podem utilizar.

O processo de criação do Localizador de **Streaming** chama-se publicação. Por predefinição, o Localizador de **Streaming** é válido imediatamente após a efetuação das chamadas API. Dura até ser apagado, a menos que configure os tempos de início e fim opcionais.

Ao criar um Localizador de **Streaming,** precisa especificar o `StreamingPolicyName`desejado . Neste tutorial, estamos a usar uma das Políticas de Streaming predefinidas, que diz à Azure Media Services como publicar os conteúdos para streaming. Neste exemplo, definimos StreamingLocator.StreamingPolicyName como a política "Predefined_MultiDrmCencStreaming". As encriptações PlayReady e Widevine são aplicadas e a chave é entregue ao cliente de reprodução com base nas licenças de DRM configuradas. Se também quiser encriptar a sua transmissão em fluxo com CBCS (FairPlay), utilize "Predefined_MultiDrmStreaming".

> [!IMPORTANT]
> Ao utilizar uma Política de [Streaming](streaming-policy-concept.md)personalizada, deve conceber um conjunto limitado de tais políticas para a sua conta de Serviço de Media e reutilizá-las para os seus Localizadores de Streaming sempre que forem necessárias as mesmas opções e protocolos de encriptação. A conta dos Serviços de Multimédia tem uma quota para o número de entradas de StreamingPolicy. Não devias estar a criar uma nova Política de Streaming para cada StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Obter um token de teste

Neste tutorial, vamos especificar que a política de chave de conteúdo tem uma restrição de token. A política de token restrito tem de ser acompanhada por um token emitido por um serviço de tokens seguro (STS). A Media Services suporta tokens nos formatos [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) e é isso que configuramos na amostra.

O ContentKeyIdentifierClaim é utilizado no ContentKeyPolicy, o que significa que o token apresentado ao serviço de entrega de chaves tem de conter o identificador do ContentKey. Na amostra, não especificamos uma chave de conteúdo na criação do Localizador de Streaming, o sistema cria uma aleatória para nós. Para gerar o token do teste, temos de obter o ContentKeyId para colocar na alegação ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Construa um URL de streaming

Agora que o [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) foi criado, pode obter os URLs de transmissão. Para construir um URL, você precisa concatenar o nome anfitrião [do StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) e o caminho **do localizador de streaming.** Nesta amostra, é utilizado o **ponto final de streaming** *predefinido.* Quando criar uma conta de Serviço de Media, este **ponto final** de streaming *padrão* estará em estado de paragem, por isso precisa ligar para **Iniciar**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

Ao executar a aplicação, vê o seguinte ecrã:

![Proteger com ADRM](./media/protect-with-drm/playready_encrypted_url.png)

Pode abrir um browser e colar o URL resultante para iniciar a página de demonstração do Leitor de Multimédia do Azure com o URL e o token previamente preenchidos.

## <a name="clean-up-resources-in-your-media-services-account"></a>Limpar os recursos na conta dos Serviços de Multimédia

Geralmente, deve limpar tudo, exceto objetos que planeia reutilizar (normalmente, vai reutilizar Transforms, StreamingLocators, e assim por diante). Se quiser que a sua conta esteja limpa após a experimentação, elimine os recursos que não planeia reutilizar. Por exemplo, o seguinte código elimina Jobs:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisar de nenhum dos recursos presentes no seu grupo de recursos, incluindo a conta dos Serviços de Multimédia e a conta de armazenamento que criou para este tutorial, elimine o grupo de recursos anteriormente criado.

Executar o seguinte comando CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>Notas adicionais

* A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Confira o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="next-steps"></a>Passos seguintes

Fazer Check-out

> [!div class="nextstepaction"]
> [Proteja com AES-128](protect-with-aes128.md)
