---
title: Criptografar vídeo com o AES-128
titleSuffix: Azure Media Services
description: Saiba como criptografar vídeo com criptografia AES de 128 bits e como usar o serviço de distribuição de chaves nos serviços de mídia do Azure.
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
ms.openlocfilehash: 3b56747d9bc8c8ae5884d4fb654c20d49527fed5
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186081"
---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>Tutorial: criptografar vídeo com o AES-128 e usar o serviço de distribuição de chaves

> [!NOTE]
> Embora o tutorial use os exemplos do [SDK do .net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) , as etapas gerais são as mesmas para a [API REST](https://docs.microsoft.com/rest/api/media/liveevents), a [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)ou outros [SDKs](media-services-apis-overview.md#sdks)com suporte.

Você pode usar os serviços de mídia para fornecer HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming criptografados com o AES usando chaves de criptografia de 128 bits. Os serviços de mídia também fornecem o serviço de distribuição de chaves que fornece chaves de criptografia para usuários autorizados. Se você quiser que os serviços de mídia criptografem dinamicamente o vídeo, associe a chave de criptografia a um localizador de streaming e também configure a política de chave de conteúdo. Quando um fluxo é solicitado por um player, os serviços de mídia usam a chave especificada para criptografar dinamicamente o conteúdo com o AES-128. Para desencriptar a transmissão em fluxo, o leitor solicita a chave ao serviço de entrega de chaves. Para determinar se o utilizador está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que especificou para a chave.

Pode encriptar cada elemento com vários tipos de encriptação (AES-128, PlayReady, Widevine, FairPlay). Veja [Protocolos de transmissão em fluxo e tipos de encriptação](content-protection-overview.md#streaming-protocols-and-encryption-types), para ver o que faz sentido combinar. Além disso, consulte [como proteger com DRM](protect-with-drm.md).

A saída do exemplo este artigo inclui uma URL para o Player de Mídia do Azure, a URL do manifesto e o token AES necessários para reproduzir o conteúdo. O exemplo define a expiração do token JSON Web token (JWT) para 1 hora. Você pode abrir um navegador e colar a URL resultante para iniciar a página de demonstração de Player de Mídia do Azure com a URL e o token preenchidos para você já no seguinte formato: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Baixe o exemplo de [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) descrito no artigo.
> * Comece a usar as APIs dos serviços de mídia com o SDK do .NET.
> * Criar um ativo de saída.
> * Criar uma transformação de codificação.
> * Envie um trabalho.
> * Aguarde a conclusão do trabalho.
> * Criar uma política de chave de conteúdo
> * Configure a política para usar a restrição de token JWT.
> * Crie um localizador de streaming.
> * Configure o localizador de streaming para criptografar o vídeo com AES (ClearKey).
> * Obter um token de teste.
> * Crie uma URL de streaming.
> * Limpar recursos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

O seguinte é necessário para concluir o tutorial.

* Reveja o artigo [Descrição Geral da Proteção de Conteúdo](content-protection-overview.md).
* Instale o Visual Studio Code ou o Visual Studio.
* [Criar uma conta de Media Services](create-account-cli-quickstart.md).
* Obtenha as credenciais necessárias para usar as APIs dos serviços de mídia seguindo as [APIs de acesso](access-api-cli-how-to.md).

## <a name="download-code"></a>Transferir código

Clone um repositório do GitHub que contenha o exemplo completo de .NET abordado neste artigo para o seu computador, com o seguinte comando:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

O exemplo "criptografar com AES-128" está localizado na pasta [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES)

> [!NOTE]
> O exemplo cria recursos exclusivos toda vez que você executa o aplicativo. Normalmente, você reutilizará recursos existentes, como transformações e políticas (se o recurso existente tiver as configurações necessárias).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a utilizar as APIs dos Serviços de Multimédia com o SDK .NET

Para começar a usar as APIs dos serviços de mídia com o .NET, crie um objeto **AzureMediaServicesClient** . Para criar o objeto, tem de fornecer as credenciais necessárias para o cliente se ligar ao Azure com o Azure AD. No código que clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials, com base nas credenciais fornecidas no ficheiro de configuração local.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Criar um elemento de saída  

A saída [Asset](https://docs.microsoft.com/rest/api/media/assets) armazena o resultado da tarefa de codificação.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Obter ou criar uma Transformação de codificação

Ao criar uma nova instância [Transformar](https://docs.microsoft.com/rest/api/media/transforms), tem de especificar a saída resultante que pretende. O parâmetro necessário é um objeto **TransformOutput**, conforme apresentado no código abaixo. Cada **TransformOutput** contém uma **Predefinição**. A **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que estão a ser utilizadas para gerir o **TransformOutput** pretendido. O exemplo descrito neste artigo utiliza uma Predefinição incorporada chamada **AdaptiveStreaming**. A predefinição codifica o vídeo de entrada em uma escada de taxa de bits gerada automaticamente (pares de resolução de taxa de bits) com base na resolução de entrada e na taxa de bits e, em seguida, produz arquivos de MP4 ISO com vídeo H. 264 e áudio AAC correspondentes a cada par de resolução de taxa de bits.

Antes de criar uma nova [transformação](https://docs.microsoft.com/rest/api/media/transforms), primeiro verifique se já existe um usando o método **Get** , conforme mostrado no código a seguir. Nos Serviços de Multimédia v3, os métodos **Get** nas entidades devolverão um valor **nulo** se a entidade não existir (uma verificação não sensível a maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Submeter Tarefa

Conforme mencionado acima, o objeto [Transformação](https://docs.microsoft.com/rest/api/media/transforms) é a receita e uma [Tarefa](https://docs.microsoft.com/rest/api/media/jobs) é o pedido real para os Serviços de Multimédia aplicarem essa **Transformação** a um determinado conteúdo de áudio ou vídeo de entrada. O **trabalho** especifica informações como o local do vídeo de entrada e o local para a saída.

Neste tutorial, criamos a entrada do trabalho com base em um arquivo que é ingerido diretamente de uma [URL de origem https](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Aguardar a conclusão da Tarefa

O trabalho leva algum tempo para ser concluído. Quando isso ocorrer, você deseja ser notificado. O exemplo de código abaixo mostra como consultar o serviço para saber o estado da [Tarefa](https://docs.microsoft.com/rest/api/media/jobs). A sondagem não é uma prática recomendada para aplicativos de produção devido à possível latência. A consulta poderá ser limitada se for sobreutilizada numa conta. Em alternativa, os programadores devem utilizar o Event Grid. Para obter mais informações, consulte [rotear eventos para um ponto de extremidade da Web personalizado](job-state-events-cli-how-to.md).

Normalmente, a **Tarefa** passa pelos seguintes estados: **Agendada**, **Em fila**, **Em processamento**, **Concluída** (o estado final). Se o trabalho tiver se originado em um erro, você obterá o estado de **erro** . Se o trabalho estiver no processo de ser cancelado, você será **cancelado e** **cancelado** quando terminar.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Criar uma política de chave de conteúdo

Uma chave de conteúdo fornece acesso seguro aos seus Elementos. Você precisa criar uma **política de chave de conteúdo** que configure como a chave de conteúdo é entregue aos clientes finais. A chave de conteúdo está associada ao **localizador de streaming**. Os serviços de mídia também fornecem o serviço de distribuição de chaves que fornece chaves de criptografia para usuários autorizados.

Quando um fluxo é solicitado por um player, os serviços de mídia usam a chave especificada para criptografar dinamicamente o conteúdo (nesse caso, usando a criptografia AES). Para descriptografar o fluxo, o Player solicita a chave do serviço de distribuição de chaves. Para determinar se o utilizador está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que especificou para a chave.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Criar um localizador de streaming

Depois de concluída a codificação e de a política de chave de conteúdo estar definida, o passo seguinte consiste em disponibilizar o vídeo no Elemento de saída para reprodução pelos clientes. Você disponibiliza o vídeo em duas etapas:

1. Crie um [localizador de streaming](https://docs.microsoft.com/rest/api/media/streaminglocators).
2. Crie os URLs de transmissão em fluxo que os clientes podem utilizar.

O processo de criação do **localizador de streaming** é chamado publicação. Por padrão, o **localizador de streaming** é válido imediatamente depois que você faz as chamadas à API. Ela dura até ser excluída, a menos que você configure as horas de início e de término opcionais.

Ao criar um [localizador de streaming](https://docs.microsoft.com/rest/api/media/streaminglocators), você precisará especificar o **StreamingPolicyName**desejado. Neste tutorial, estamos usando um dos PredefinedStreamingPolicies, que informa aos serviços de mídia do Azure como publicar o conteúdo para streaming. Neste exemplo, a criptografia de envelope AES é aplicada (essa criptografia também é conhecida como criptografia ClearKey porque a chave é entregue ao cliente de reprodução via HTTPS e não uma licença DRM).

> [!IMPORTANT]
> Ao usar um [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies)personalizado, você deve criar um conjunto limitado de tais políticas para sua conta de serviço de mídia e reutilizá-las para seus localizadores de streaming sempre que os mesmos protocolos e opções de criptografia forem necessários. A conta dos Serviços de Multimédia tem uma quota para o número de entradas de StreamingPolicy. Você não deve criar um novo StreamingPolicy para cada localizador de streaming.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Obter um token de teste

Neste tutorial, vamos especificar que a política de chave de conteúdo tem uma restrição de token. A política de token restrito tem de ser acompanhada por um token emitido por um serviço de tokens seguro (STS). Os serviços de mídia dão suporte a tokens no formato [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) e é isso que configuramos no exemplo.

O ContentKeyIdentifierClaim é usado na **política de chave de conteúdo**, o que significa que o token apresentado ao serviço de distribuição de chaves deve ter o identificador da chave de conteúdo. No exemplo, não especificamos uma chave de conteúdo ao criar o localizador de streaming, o sistema criou um aleatório para nós. Para gerar o token de teste, devemos obter o ContentKeyId para colocar na declaração ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Criar um URL de transmissão em fluxo DASH

Agora que o [localizador de streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) foi criado, você pode obter as URLs de streaming. Para criar uma URL, você precisa concatenar o nome do host [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) e o caminho do **localizador de streaming** . Neste exemplo, o **ponto de extremidade de streaming** *padrão* é usado. Quando você cria uma conta de serviço de mídia pela primeira vez, esse **ponto de extremidade de streaming** *padrão* estará em um estado parado, portanto, você precisará chamar **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Limpar os recursos na conta dos Serviços de Multimédia

Em geral, você deve limpar tudo, exceto os objetos que pretende reutilizar (normalmente, você reutilizará transformações, localizadores de streaming e assim por diante). Se desejar que sua conta seja limpa após o teste, exclua os recursos que você não planeja reutilizar. Por exemplo, o código a seguir exclui trabalhos:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisar de nenhum dos recursos presentes no seu grupo de recursos, incluindo a conta dos Serviços de Multimédia e a conta de armazenamento que criou para este tutorial, elimine o grupo de recursos anteriormente criado.

Execute o seguinte comando da CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, envie comentários, obtenha atualizações

Confira o artigo [da Comunidade dos serviços de mídia do Azure](media-services-community.md) para ver diferentes maneiras que você pode fazer perguntas, fornecer comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Proteger com o DRM](protect-with-drm.md)
