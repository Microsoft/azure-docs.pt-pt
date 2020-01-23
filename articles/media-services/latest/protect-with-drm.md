---
title: Serviço de entrega de licença e criptografia DRM dos serviços de mídia do Azure
titleSuffix: Azure Media Services
description: Saiba como usar a criptografia dinâmica de DRM e o serviço de entrega de licença para entregar fluxos criptografados com licenças do Microsoft PlayReady, Google Widevine ou Apple FairPlay.
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
ms.openlocfilehash: bd5c026da27c68e249532b70629ba01afea655fe
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513070"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Tutorial: usar o serviço de entrega de licença e criptografia dinâmica de DRM

> [!NOTE]
> Embora este tutorial use os exemplos do [SDK do .net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) , as etapas gerais são as mesmas para a [API REST](https://docs.microsoft.com/rest/api/media/liveevents), a [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)ou outros [SDKs](media-services-apis-overview.md#sdks)com suporte.

Pode utilizar os Serviços de Multimédia do Azure para entregar transmissões em fluxo encriptadas com licenças do Microsoft PlayReady, Google Widevine ou Apple FairPlay. Para obter uma explicação detalhada, consulte [proteção de conteúdo com criptografia dinâmica](content-protection-overview.md).

Os serviços de mídia também fornecem um serviço para fornecer licenças DRM do PlayReady, do Widevine e do FairPlay. Quando um usuário solicita conteúdo protegido por DRM, o aplicativo de Player solicita uma licença do serviço de licença dos serviços de mídia. Se o aplicativo de player for autorizado, o serviço de licença dos serviços de mídia emitirá uma licença para o Player. Uma licença contém a chave de desencriptação que pode ser utilizada pelo leitor de cliente para desencriptar e transmitir em fluxo o conteúdo.

Este artigo baseia-se no exemplo [Encriptar com o DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

O exemplo descrito neste artigo produz o seguinte resultado:

![AMS com vídeo protegido por DRM no Player de Mídia do Azure](./media/protect-with-drm/ams_player.png)

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Criar uma transformação de codificação.
> * Definir a chave de assinatura utilizada para a verificação do token.
> * Defina os requisitos na política de chave de conteúdo.
> * Crie um StreamingLocator com a política de streaming especificada.
> * Crie uma URL usada para reproduzir o arquivo.

## <a name="prerequisites"></a>Pré-requisitos

São necessários os itens seguintes para concluir o tutorial:

* Reveja o artigo [Descrição Geral da Proteção de Conteúdo](content-protection-overview.md).
* Examine o [design do sistema de proteção de conteúdo de vários DRM com controle de acesso](design-multi-drm-system-with-access-control.md).
* Instale o Visual Studio Code ou o Visual Studio.
* Crie uma nova conta dos Serviços de Multimédia do Azure, conforme descrito [neste início rápido](create-account-cli-quickstart.md).
* Obter as credenciais necessárias para utilizar as APIs dos Serviços de Multimédia, ao seguir [APIs de Acesso](access-api-cli-how-to.md)
* Defina os valores apropriados no arquivo de configuração de aplicativo (appSettings. JSON).

## <a name="download-code"></a>Transferir código

Clone um repositório do GitHub que contenha o exemplo completo de .NET abordado neste artigo para o seu computador, com o seguinte comando:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
O exemplo "Encrypt with DRM" está localizado na pasta [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> O exemplo cria recursos exclusivos toda vez que você executa o aplicativo. Normalmente, você reutilizará recursos existentes, como transformações e políticas (se o recurso existente tiver as configurações necessárias).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a utilizar as APIs dos Serviços de Multimédia com o SDK .NET

Para começar a usar as APIs dos serviços de mídia com o .NET, crie um objeto **AzureMediaServicesClient** . Para criar o objeto, tem de fornecer as credenciais necessárias para o cliente se ligar ao Azure com o Azure AD. No código que clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials, com base nas credenciais fornecidas no ficheiro de configuração local.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Criar um elemento de saída  

A saída [Asset](assets-concept.md) armazena o resultado da tarefa de codificação.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Obter ou criar uma Transformação de codificação

Ao criar uma nova instância [Transformar](transforms-jobs-concept.md), tem de especificar a saída resultante que pretende. O parâmetro Required é um objeto `transformOutput`, conforme mostrado no código abaixo. Cada TransformOutput contém uma **predefinição**. Predefinição descreve as instruções detalhadas de operações de processamento de vídeo e/ou áudio que devem ser usadas para gerar o TransformOutput desejado. O exemplo descrito neste artigo utiliza uma Predefinição incorporada chamada **AdaptiveStreaming**. A predefinição codifica o vídeo de entrada em uma escada de taxa de bits gerada automaticamente (pares de resolução de taxa de bits) com base na resolução de entrada e na taxa de bits, e produz arquivos ISO MP4 com o vídeo H. 264 e áudio AAC correspondentes a cada par de resolução de taxa de bits. 

Antes de criar uma nova **Transformação**, em primeiro lugar deve verificar se já existe uma com o método **Get**, conforme mostrado no código que se segue.  Nos Serviços de Multimédia v3, os métodos **Get** nas entidades devolverão um valor **nulo** se a entidade não existir (uma verificação não sensível a maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Submeter Tarefa

Conforme mencionado acima, o objeto **Transformação** é a receita e uma [Tarefa](transforms-jobs-concept.md) é o pedido real para os Serviços de Multimédia aplicarem essa **Transformação** a um determinado conteúdo de áudio ou vídeo de entrada. O **trabalho** especifica informações como o local do vídeo de entrada e o local para a saída.

Neste tutorial, criamos a entrada do trabalho com base em um arquivo que é ingerido diretamente de uma [URL de origem https](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Aguardar a conclusão da Tarefa

O trabalho leva algum tempo para ser concluído. Quando isso ocorrer, você deseja ser notificado. O exemplo de código abaixo mostra como consultar o serviço para saber o estado da **Tarefa**. A sondagem não é uma prática recomendada para aplicativos de produção devido à possível latência. A consulta poderá ser limitada se for sobreutilizada numa conta. Em alternativa, os programadores devem utilizar o Event Grid. Veja [Route events to a custom web endpoint](job-state-events-cli-how-to.md) (Encaminhar eventos para um ponto final de Web personalizado).

Normalmente, a **Tarefa** passa pelos seguintes estados: **Agendada**, **Em fila**, **Em processamento**, **Concluída** (o estado final). Se o trabalho tiver se originado em um erro, você obterá o estado de **erro** . Se o trabalho estiver no processo de ser cancelado, você será **cancelado e** **cancelado** quando terminar.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Criar uma política de chave de conteúdo

Uma chave de conteúdo fornece acesso seguro aos seus Elementos. Você precisa criar uma [política de chave de conteúdo](content-key-policy-concept.md) ao criptografar o conteúdo com um DRM. A política configura como a chave de conteúdo é entregue aos clientes finais. A chave de conteúdo está associada a um localizador de streaming. Os Serviços de Multimédia também fornecem o serviço de entrega de chaves que é responsável pelo fornecimento de chaves e licenças de encriptação aos utilizadores autorizados.

Você precisa definir os requisitos (restrições) na política de **chave de conteúdo** que deve ser atendida para fornecer chaves com a configuração especificada. Neste exemplo, definimos as configurações e os requisitos seguintes:

* Configuração

    As licenças do [PlayReady](playready-license-template-overview.md) e [Widevine](widevine-license-template-overview.md) estão configuradas para que possam ser fornecidas pelo serviço de entrega de licenças dos Serviços de Multimédia. Embora este aplicativo de exemplo não configure a licença [Fairplay](fairplay-license-overview.md) , ele contém um método que você pode usar para configurar o Fairplay. Você pode adicionar a configuração FairPlay como outra opção.

* Restrição

    O aplicativo define uma restrição de tipo de token JWT (token Web JSON) na política.

Quando uma transmissão em fluxo é pedida por um leitor, os Serviços de Multimédia utilizam a chave especificada para encriptar dinamicamente o seu conteúdo. Para desencriptar a transmissão em fluxo, o leitor solicita a chave ao serviço de entrega de chaves. Para determinar se o utilizador está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que especificou para a chave.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Criar um localizador de streaming

Depois de concluída a codificação e de a política de chave de conteúdo estar definida, o passo seguinte consiste em disponibilizar o vídeo no Elemento de saída para reprodução pelos clientes. Você disponibiliza o vídeo em duas etapas:

1. Crie um [localizador de streaming](streaming-locators-concept.md).
2. Crie os URLs de transmissão em fluxo que os clientes podem utilizar.

O processo de criação do **localizador de streaming** é chamado publicação. Por padrão, o **localizador de streaming** é válido imediatamente depois que você faz as chamadas à API. Ela dura até ser excluída, a menos que você configure as horas de início e de término opcionais.

Ao criar um **localizador de streaming**, você precisa especificar o `StreamingPolicyName`desejado. Neste tutorial, estamos usando uma das políticas de streaming predefinidas, que informa aos serviços de mídia do Azure como publicar o conteúdo para streaming. Neste exemplo, definimos StreamingLocator.StreamingPolicyName como a política "Predefined_MultiDrmCencStreaming". As criptografias PlayReady e Widevine são aplicadas e a chave é entregue ao cliente de reprodução com base nas licenças DRM configuradas. Se também quiser encriptar a sua transmissão em fluxo com CBCS (FairPlay), utilize "Predefined_MultiDrmStreaming".

> [!IMPORTANT]
> Ao usar uma [política de streaming](streaming-policy-concept.md)personalizada, você deve criar um conjunto limitado de tais políticas para sua conta de serviço de mídia e reutilizá-las para o StreamingLocators sempre que forem necessárias as mesmas opções de criptografia e protocolos. A conta dos Serviços de Multimédia tem uma quota para o número de entradas de StreamingPolicy. Você não deve criar um novo StreamingPolicy para cada StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Obter um token de teste

Neste tutorial, vamos especificar que a política de chave de conteúdo tem uma restrição de token. A política de token restrito tem de ser acompanhada por um token emitido por um serviço de tokens seguro (STS). Os serviços de mídia oferecem suporte a tokens nos formatos [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) e é isso que configuramos no exemplo.

O ContentKeyIdentifierClaim é utilizado no ContentKeyPolicy, o que significa que o token apresentado ao serviço de entrega de chaves tem de conter o identificador do ContentKey. No exemplo, não especificamos uma chave de conteúdo ao criar o localizador de streaming, o sistema cria um aleatório para nós. Para gerar o token de teste, devemos obter o ContentKeyId para colocar na declaração ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Criar uma URL de streaming

Agora que o [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) foi criado, pode obter os URLs de transmissão. Para criar uma URL, você precisa concatenar o nome do host [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) e o caminho do **localizador de streaming** . Neste exemplo, o **ponto de extremidade de streaming** *padrão* é usado. Quando você cria uma conta de serviço de mídia pela primeira vez, esse **ponto de extremidade de streaming** *padrão* estará em um estado parado, portanto, você precisará chamar **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

Ao executar o aplicativo, você verá a tela a seguir:

![Proteger com DRM](./media/protect-with-drm/playready_encrypted_url.png)

Pode abrir um browser e colar o URL resultante para iniciar a página de demonstração do Leitor de Multimédia do Azure com o URL e o token previamente preenchidos.

## <a name="clean-up-resources-in-your-media-services-account"></a>Limpar os recursos na conta dos Serviços de Multimédia

Em geral, você deve limpar tudo, exceto os objetos que pretende reutilizar (normalmente, você reutilizará transformações, StreamingLocators e assim por diante). Se desejar que sua conta seja limpa após o teste, exclua os recursos que você não planeja reutilizar. Por exemplo, o código a seguir exclui trabalhos:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisar de nenhum dos recursos presentes no seu grupo de recursos, incluindo a conta dos Serviços de Multimédia e a conta de armazenamento que criou para este tutorial, elimine o grupo de recursos anteriormente criado.

Execute o seguinte comando da CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>Notas adicionais

* O Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, envie comentários, obtenha atualizações

Confira o artigo [da Comunidade dos serviços de mídia do Azure](media-services-community.md) para ver diferentes maneiras que você pode fazer perguntas, fornecer comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Passos seguintes

Veja

> [!div class="nextstepaction"]
> [Proteger com AES-128](protect-with-aes128.md)
