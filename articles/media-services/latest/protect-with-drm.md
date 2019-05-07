---
title: Utilizar DRM dinâmico licença e de encriptação de serviço de entrega de serviços de multimédia do Azure | Documentos da Microsoft
description: Pode utilizar os Serviços de Multimédia do Azure para entregar transmissões em fluxo encriptadas com licenças do Microsoft PlayReady, Google Widevine ou Apple FairPlay.
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
ms.date: 05/02/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 24ea6b2b44518b4cf75389585caf42ff6bc6722f
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65191075"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Tutorial: Utilizar a encriptação dinâmica e o serviço de entrega de licenças do DRM

Pode utilizar os Serviços de Multimédia do Azure para entregar transmissões em fluxo encriptadas com licenças do Microsoft PlayReady, Google Widevine ou Apple FairPlay. Para uma explicação detalhada, consulte [proteção com a encriptação dinâmica de conteúdo](content-protection-overview.md).

Além disso, os Serviços de Multimédia fornecem um serviço para entregar licenças DRM do PlayReady, Widevine e FairPlay. Quando um utilizador pede um conteúdo protegido por DRM, a aplicação de leitor pede uma licença ao serviço de licenças do Serviço de Multimédia. Se a aplicação de leitor tiver autorização, o serviço de licenças dos Serviços de Multimédia emite uma licença para o leitor. Uma licença contém a chave de desencriptação que pode ser utilizada pelo leitor de cliente para desencriptar e transmitir em fluxo o conteúdo.

Este artigo baseia-se no exemplo [Encriptar com o DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM). 

O exemplo descrito neste artigo produz o seguinte resultado:

![AMS com vídeo DRM protegido](./media/protect-with-drm/ams_player.png)

Este tutorial mostrar-lhe como:    

> [!div class="checklist"]
> * Criar uma transformação de codificação
> * Definir a chave de assinatura utilizada para a verificação de seu token
> * Definir os requisitos na política de chave de conteúdo
> * Criar um StreamingLocator à política de transmissão em fluxo especificada
> * Criar um URL utilizado para reproduzir seu arquivo

## <a name="prerequisites"></a>Pré-requisitos

O seguinte é necessário para concluir o tutorial.

* Reveja o artigo [Descrição Geral da Proteção de Conteúdo](content-protection-overview.md).
* Reveja o [crie o sistema de proteção de conteúdo multi-DRM com controlo de acesso](design-multi-drm-system-with-access-control.md)
* Instalar o Visual Studio Code ou o Visual Studio
* Crie uma nova conta dos Serviços de Multimédia do Azure, conforme descrito [neste início rápido](create-account-cli-quickstart.md).
* Obter as credenciais necessárias para utilizar as APIs dos Serviços de Multimédia, ao seguir [APIs de Acesso](access-api-cli-how-to.md)
* Defina os valores adequados no ficheiro de configuração da aplicação (appsettings.json).

## <a name="download-code"></a>Transferir código

Clone um repositório do GitHub que contenha o exemplo completo de .NET abordado neste artigo para o seu computador, com o seguinte comando:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
O exemplo "Encrypt with DRM" está localizado na pasta [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> O exemplo cria recursos exclusivos, sempre que executar a aplicação. Normalmente, irá reutilizar os recursos existentes, como transformações e políticas (se os recursos existentes tiverem as configurações necessárias). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a utilizar as APIs dos Serviços de Multimédia com o SDK .NET

Para começar a utilizar as APIs dos Serviços de Multimédia com o .NET, tem de criar um objeto **AzureMediaServicesClient**. Para criar o objeto, tem de fornecer as credenciais necessárias para o cliente se ligar ao Azure com o Azure AD. No código que clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials, com base nas credenciais fornecidas no ficheiro de configuração local. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Criar um elemento de saída  

A saída [Asset](https://docs.microsoft.com/rest/api/media/assets) armazena o resultado da tarefa de codificação.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Obter ou criar uma Transformação de codificação

Ao criar uma nova instância [Transformação](https://docs.microsoft.com/rest/api/media/transforms), tem de especificar o que pretende produzir como uma saída. O parâmetro necessário é um objeto **TransformOutput**, conforme apresentado no código abaixo. Cada **TransformOutput** contém uma **Predefinição**. A **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que estão a ser utilizadas para gerir o **TransformOutput** pretendido. O exemplo descrito neste artigo utiliza uma Predefinição incorporada chamada **AdaptiveStreaming**. A Predefinição codifica o vídeo de entrada para uma escala de bits gerada automaticamente (pares de resolução/velocidade de transmissão) com base na resolução e velocidade de transmissão de entrada e produz ficheiros ISO MP4 com vídeo H.264 e áudio AAC correspondente a cada par de resolução/velocidade de transmissão. 

Antes de criar uma nova [Transformação](https://docs.microsoft.com/rest/api/media/transforms), em primeiro lugar deve verificar se já existe uma com o método **Get**, conforme mostrado no código que se segue.  Nos Serviços de Multimédia v3, os métodos **Get** nas entidades devolverão um valor **nulo** se a entidade não existir (uma verificação não sensível a maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Submeter Tarefa

Conforme mencionado acima, o objeto [Transformação](https://docs.microsoft.com/rest/api/media/transforms) é a receita e uma [Tarefa](https://docs.microsoft.com/rest/api/media/jobs) é o pedido real para os Serviços de Multimédia aplicarem essa **Transformação** a um determinado conteúdo de áudio ou vídeo de entrada. A **Tarefa** especifica informações como a localização do vídeo de entrada e a localização da saída.

Neste tutorial, vamos criar a entrada da tarefa, com base num ficheiro que é ingerido diretamente a partir de um [URL de origem HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Aguardar a conclusão da Tarefa

A tarefa demora algum tempo a terminar, por isso irá querer receber uma notificação quando for concluída. O exemplo de código abaixo mostra como consultar o serviço para saber o estado da [Tarefa](https://docs.microsoft.com/rest/api/media/jobs). Utilizar uma consulta não é uma prática recomendada para produzir aplicações devido à potencial latência. A consulta poderá ser limitada se for sobreutilizada numa conta. Em alternativa, os programadores devem utilizar o Event Grid. Veja [Route events to a custom web endpoint](job-state-events-cli-how-to.md) (Encaminhar eventos para um ponto final de Web personalizado).

O **tarefa** normalmente atravessa os seguintes Estados: **Agendado**, **em fila**, **processamento**, **concluído** (o estado final). Se a tarefa encontrar um erro, obterá um estado de **Erro**. Se a tarefa estiver prestes a ser cancelada, obterá **A cancelar** e **Cancelada** quando terminar.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkeypolicy"></a>Criar um ContentKeyPolicy

Uma chave de conteúdo fornece acesso seguro aos seus Elementos. Terá de criar uma política de chave de conteúdo que configura a forma como a chave de conteúdo é entregue aos clientes finais. A chave de conteúdo está associada ao StreamingLocator. Os Serviços de Multimédia também fornecem o serviço de entrega de chaves que é responsável pelo fornecimento de chaves e licenças de encriptação aos utilizadores autorizados. 

Tem de definir os requisitos (restrições) na política de chave de conteúdo que têm de ser cumpridos para fornecer as chaves com a configuração especificada. Neste exemplo, definimos as configurações e os requisitos seguintes:

* Configuração 

    As licenças do [PlayReady](playready-license-template-overview.md) e [Widevine](widevine-license-template-overview.md) estão configuradas para que possam ser fornecidas pelo serviço de entrega de licenças dos Serviços de Multimédia. Embora esta aplicação de exemplo não configure a licença do [FairPlay](fairplay-license-overview.md), contém um método que pode utilizar para configurar o FairPlay. Pode adicionar a configuração do FairPlay como outra opção.

* Restrição

    A aplicação define uma restrição do tipo de token JWT na política.

Quando uma transmissão em fluxo é pedida por um leitor, os Serviços de Multimédia utilizam a chave especificada para encriptar dinamicamente o seu conteúdo. Para desencriptar a transmissão em fluxo, o leitor solicita a chave ao serviço de entrega de chaves. Para determinar se o utilizador está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que especificou para a chave.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaminglocator"></a>Criar um StreamingLocator

Depois de concluída a codificação e de a política de chave de conteúdo estar definida, o passo seguinte consiste em disponibilizar o vídeo no Elemento de saída para reprodução pelos clientes. Isto pode ser conseguido em dois passos: 

1. Criar um [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)
2. Crie os URLs de transmissão em fluxo que os clientes podem utilizar. 

O processo de criação do **StreamingLocator** denomina-se publicação. Por predefinição, o **StreamingLocator** é válido imediatamente depois de efetuar as chamadas de API e dura até serem eliminadas, a menos que configure as horas de início e de fim opcionais. 

Ao criar um [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), tem de especificar o **StreamingPolicyName** pretendido. Neste tutorial, estamos a utilizar uma das StreamingPolicies predefinidas, que diz aos Serviços de Multimédia do Azure como publicar o conteúdo para transmissão em fluxo. Neste exemplo, definimos StreamingLocator.StreamingPolicyName como a política "Predefined_MultiDrmCencStreaming". Esta política indica que pretende que sejam geradas e definidas no localizador duas chaves de conteúdo (envelope e CENC). Por conseguinte, são aplicadas as encriptação de envelope, do PlayReady e do Widevine (a chave é entregue ao cliente para reprodução, com base nas licenças DRM configuradas). Se também quiser encriptar a sua transmissão em fluxo com CBCS (FairPlay), utilize "Predefined_MultiDrmStreaming". 

> [!IMPORTANT]
> Quando utilizar uma [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) personalizada, deve conceber um conjunto limitado dessas políticas para a sua conta dos Serviços de Multimédia e utilizá-las novamente para os StreamingLocators sempre que são necessárias as mesmas opções de encriptação e os mesmos protocolos. A conta dos Serviços de Multimédia tem uma quota para o número de entradas de StreamingPolicy. Não deve criar uma nova StreamingPolicy para cada StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Obter um token de teste
        
Neste tutorial, vamos especificar que a política de chave de conteúdo tem uma restrição de token. A política de token restrito tem de ser acompanhada por um token emitido por um serviço de tokens seguro (STS). Os Serviços de Multimédia suportam tokens nos formatos [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) e é o que vamos configurar no exemplo.

O ContentKeyIdentifierClaim é utilizado no ContentKeyPolicy, o que significa que o token apresentado ao serviço de entrega de chaves tem de conter o identificador do ContentKey. No exemplo, não especificamos uma chave de conteúdo ao criar o StreamingLocator, pois o sistema cria uma chave aleatória. Para gerar o token de teste, temos de obter o ContentKeyId a colocar na afirmação ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Compilar um URL de transmissão em fluxo

Agora que o [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) foi criado, pode obter os URLs de transmissão. Para criar um URL, terá de concatenar o nome do anfitrião do [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) e o caminho do **StreamingLocator**. Neste exemplo, é utilizado o **StreamingEndpoint** *predefinido*. Quando cria uma conta dos Serviços de Multimédia pela primeira vez, este **StreamingEndpoint** *predefinido* vai estar no estado parado, por isso terá de chamar **Iniciar**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

Quando executar a aplicação, consulte o seguinte:

![Proteger com o DRM](./media/protect-with-drm/playready_encrypted_url.png)

Pode abrir um browser e colar o URL resultante para iniciar a página de demonstração do Leitor de Multimédia do Azure com o URL e o token previamente preenchidos. 
 
## <a name="clean-up-resources-in-your-media-services-account"></a>Limpar os recursos na conta dos Serviços de Multimédia

Em geral, deve limpar tudo exceto os objetos que está a planear reutilizar (normalmente, reutiliza as Transformações e mantém os StreamingLocators, etc.). Se quiser que a sua conta seja limpa após fazer experiências, deverá eliminar os recursos que não planeia reutilizar.  Por exemplo, o seguinte código elimina as Tarefas.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisar de nenhum dos recursos presentes no seu grupo de recursos, incluindo a conta dos Serviços de Multimédia e a conta de armazenamento que criou para este tutorial, elimine o grupo de recursos anteriormente criado. 

Execute o seguinte comando da CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, comentários, obter atualizações

Veja a [Comunidade dos serviços de multimédia do Azure](media-services-community.md) artigo para ver formas diferentes, pode fazer perguntas, comentários e obter atualizações sobre os serviços de multimédia.

## <a name="next-steps"></a>Passos Seguintes

Veja

> [!div class="nextstepaction"]
> [Proteger com AES-128](protect-with-aes128.md)

