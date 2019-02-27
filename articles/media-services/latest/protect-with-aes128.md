---
title: Utilizar serviços de multimédia do Azure para encriptar o vídeo com AES-128 | Documentos da Microsoft
description: Distribuir os seus conteúdos encriptado com chaves de encriptação AES de 128 bits com os serviços de multimédia do Microsoft Azure. Serviços de multimédia também fornecem o serviço de entrega de chave que fornece as chaves de encriptação para os utilizadores autorizados. Este tópico mostra como encriptar com AES-128 e utilizar o serviço de entrega de chave dinamicamente.
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
ms.date: 02/25/2019
ms.author: juliako
ms.openlocfilehash: 2216deb7a59dda2a7c3b99c55956ef8541925425
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56877287"
---
# <a name="tutorial-use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Tutorial: Utilizar a encriptação dinâmica de AES-128 e o serviço de entrega de chave

Pode utilizar os serviços de multimédia para entregar HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming encriptado com o AES com chaves de encriptação de 128 bits. Serviços de multimédia também fornecem o serviço de entrega de chave que fornece as chaves de encriptação para os utilizadores autorizados. Se desejar para serviços de multimédia para encriptar dinamicamente o seu vídeo, associar a chave de encriptação de localizador de transmissão em fluxo e também configurar a política de chave de conteúdo. Quando um fluxo é solicitado por um jogador, serviços de multimédia utiliza a chave especificada para encriptar dinamicamente o seu conteúdo com AES-128. Para desencriptar a transmissão em fluxo, o leitor solicita a chave ao serviço de entrega de chaves. Para determinar se o utilizador está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que especificou para a chave.

Pode encriptar cada elemento com vários tipos de encriptação (AES-128, PlayReady, Widevine, FairPlay). Veja [Protocolos de transmissão em fluxo e tipos de encriptação](content-protection-overview.md#streaming-protocols-and-encryption-types), para ver o que faz sentido combinar. Além disso, veja [como proteger com o DRM](protect-with-drm.md).

A saída do exemplo deste artigo inclui um URL para o leitor de multimédia do Azure, o URL do manifesto e o token AES necessário para reproduzir o conteúdo. O exemplo define a expiração do JWT token para 1 hora. Pode abrir um browser e cole o URL resultante para iniciar a página de demonstração do leitor de multimédia do Azure com o URL e o token preenchida para si já no seguinte formato: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.

Este tutorial mostrar-lhe como:    

> [!div class="checklist"]
> * Transfira o [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) exemplo descrito no artigo
> * Começar a utilizar as APIs dos Serviços de Multimédia com o SDK .NET
> * Criar um elemento de saída
> * Criar uma transformação de codificação
> * Submeter uma tarefa
> * Aguardar a conclusão da Tarefa
> * Criar uma política de chave de conteúdo
> * Configurar a política a utilizar a restrição de token JWT 
> * Criar um localizador de transmissão em fluxo
> * Configurar o localizador de transmissão em fluxo de mensagens em fila para encriptar o vídeo com o AES (ClearKey)
> * Obter um token de teste
> * Compilar um URL de transmissão em fluxo
> * Limpar recursos

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

O seguinte é necessário para concluir o tutorial.

* Reveja os [descrição geral da proteção de conteúdo](content-protection-overview.md) artigo
* Instalar o Visual Studio Code ou o Visual Studio
* [Criar uma conta de serviços de multimédia](create-account-cli-quickstart.md)
* Obter as credenciais necessárias para utilizar as APIs dos Serviços de Multimédia, ao seguir [APIs de Acesso](access-api-cli-how-to.md)

## <a name="download-code"></a>Transferir código

Clone um repositório do GitHub que contenha o exemplo completo de .NET abordado neste artigo para o seu computador, com o seguinte comando:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
O exemplo de "encriptar com AES-128" está localizado no [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) pasta.

> [!NOTE]
> O exemplo cria recursos exclusivos, sempre que executar a aplicação. Normalmente, irá reutilizar os recursos existentes, como transformações e políticas (se os recursos existentes tiverem as configurações necessárias). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a utilizar as APIs dos Serviços de Multimédia com o SDK .NET

Para começar a utilizar as APIs dos Serviços de Multimédia com o .NET, tem de criar um objeto **AzureMediaServicesClient**. Para criar o objeto, tem de fornecer as credenciais necessárias para o cliente se ligar ao Azure com o Azure AD. No código que clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials, com base nas credenciais fornecidas no ficheiro de configuração local. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Criar um elemento de saída  

A saída [Asset](https://docs.microsoft.com/rest/api/media/assets) armazena o resultado da tarefa de codificação.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Obter ou criar uma Transformação de codificação

Ao criar uma nova instância [Transformação](https://docs.microsoft.com/rest/api/media/transforms), tem de especificar o que pretende produzir como uma saída. O parâmetro necessário é um objeto **TransformOutput**, conforme apresentado no código abaixo. Cada **TransformOutput** contém uma **Predefinição**. A **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que estão a ser utilizadas para gerir o **TransformOutput** pretendido. O exemplo descrito neste artigo utiliza uma Predefinição incorporada chamada **AdaptiveStreaming**. A Predefinição codifica o vídeo de entrada para uma escala de bits gerada automaticamente (pares de resolução/velocidade de transmissão) com base na resolução e velocidade de transmissão de entrada e produz ficheiros ISO MP4 com vídeo H.264 e áudio AAC correspondente a cada par de resolução/velocidade de transmissão. 

Antes de criar uma nova [Transformação](https://docs.microsoft.com/rest/api/media/transforms), em primeiro lugar deve verificar se já existe uma com o método **Get**, conforme mostrado no código que se segue.  Nos Serviços de Multimédia v3, os métodos **Get** nas entidades devolverão um valor **nulo** se a entidade não existir (uma verificação não sensível a maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Submeter Tarefa

Conforme mencionado acima, o objeto [Transformação](https://docs.microsoft.com/rest/api/media/transforms) é a receita e uma [Tarefa](https://docs.microsoft.com/rest/api/media/jobs) é o pedido real para os Serviços de Multimédia aplicarem essa **Transformação** a um determinado conteúdo de áudio ou vídeo de entrada. A **Tarefa** especifica informações como a localização do vídeo de entrada e a localização da saída.

Neste tutorial, vamos criar a entrada da tarefa, com base num ficheiro que é ingerido diretamente a partir de um [URL de origem HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Aguardar a conclusão da Tarefa

A tarefa demora algum tempo a terminar, por isso irá querer receber uma notificação quando for concluída. O exemplo de código abaixo mostra como consultar o serviço para saber o estado da [Tarefa](https://docs.microsoft.com/rest/api/media/jobs). Utilizar uma consulta não é uma prática recomendada para produzir aplicações devido à potencial latência. A consulta poderá ser limitada se for sobreutilizada numa conta. Em alternativa, os programadores devem utilizar o Event Grid. Veja [Route events to a custom web endpoint](job-state-events-cli-how-to.md) (Encaminhar eventos para um ponto final de Web personalizado).

O **tarefa** normalmente atravessa os seguintes Estados: **Agendado**, **em fila**, **processamento**, **concluído** (o estado final). Se a tarefa encontrar um erro, obterá um estado de **Erro**. Se a tarefa estiver prestes a ser cancelada, obterá **A cancelar** e **Cancelada** quando terminar.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Criar uma política de chave de conteúdo

Uma chave de conteúdo fornece acesso seguro aos seus Elementos. Tem de criar uma **política de chave de conteúdo** que configura a forma como a chave de conteúdo é entregue para clientes finais. A chave de conteúdo está associada **localizador de transmissão em fluxo**. Serviços de multimédia também fornecem o serviço de entrega de chave que fornece as chaves de encriptação para os utilizadores autorizados. 

Quando um fluxo é solicitado por um jogador, serviços de multimédia utiliza a chave especificada para encriptar dinamicamente o seu conteúdo (neste caso, ao utilizar a encriptação AES.) Para desencriptar a transmissão em fluxo, o leitor solicita a chave ao serviço de entrega de chaves. Para determinar se o utilizador está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que especificou para a chave.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Criar um localizador de transmissão em fluxo

Depois de concluída a codificação e de a política de chave de conteúdo estar definida, o passo seguinte consiste em disponibilizar o vídeo no Elemento de saída para reprodução pelos clientes. Isto pode ser conseguido em dois passos: 

1. Criar um [localizador de transmissão em fluxo](https://docs.microsoft.com/rest/api/media/streaminglocators)
2. Crie os URLs de transmissão em fluxo que os clientes podem utilizar. 

O processo de criação da **localizador de transmissão em fluxo** é chamado de publicação. Por predefinição, o **localizador de transmissão em fluxo** é válido, imediatamente após fazer as chamadas à API e dura até serem eliminada, a menos que configure o início opcional e de horas de fim. 

Ao criar um [localizador de transmissão em fluxo](https://docs.microsoft.com/rest/api/media/streaminglocators), terá de especificar o pretendido **StreamingPolicyName**. Neste tutorial, estamos a utilizar um dos PredefinedStreamingPolicies, que diz aos Serviços de Multimédia do Azure como publicar o conteúdo para transmissão em fluxo. Neste exemplo, a encriptação AES Envelope é aplicada (também denominado ClearKey encriptação como a chave é enviada para o cliente de reprodução através de HTTPS e não uma licença DRM).

> [!IMPORTANT]
> Quando utilizar um personalizado [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies), deve criar um conjunto limitado dessas políticas para a sua conta de serviço de suporte de dados e utilizá-los novamente para os localizadores de transmissão em fluxo sempre que as mesmas opções de encriptação e os protocolos são necessários. A conta dos Serviços de Multimédia tem uma quota para o número de entradas de StreamingPolicy. Devem não ser a criar um novo StreamingPolicy para cada localizador de transmissão em fluxo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Obter um token de teste
        
Neste tutorial, vamos especificar que a política de chave de conteúdo tem uma restrição de token. A política de token restrito tem de ser acompanhada por um token emitido por um serviço de tokens seguro (STS). Os Serviços de Multimédia suportam tokens nos formatos [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) e é o que vamos configurar no exemplo.

O ContentKeyIdentifierClaim é utilizada na **política de chave de conteúdo**, que significa que o token apresentado para o serviço de entrega de chave tem de ter o identificador da chave de conteúdo no mesmo. No exemplo, não especificamos um conteúdo chaves ao criar o localizador de transmissão em fluxo, o sistema criou uma aleatória para nós. Para gerar o token de teste, temos de obter o ContentKeyId a colocar na afirmação ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Criar um URL de transmissão em fluxo DASH

Agora que o [localizador de transmissão em fluxo](https://docs.microsoft.com/rest/api/media/streaminglocators) tiver sido criado, pode obter os URLs de transmissão em fluxo. Para compilar um URL, terá de concatenar os [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) nome de anfitrião e o **localizador de transmissão em fluxo** caminho. Neste exemplo, o *predefinição* **ponto final de transmissão em fluxo** é utilizado. Quando cria pela primeira vez uma conta de serviço de suporte de dados, isso *predefinição* **ponto final de transmissão em fluxo** estará num estado parado, por isso terá de chamar **iniciar**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Limpar os recursos na conta dos Serviços de Multimédia

Em geral, deve limpar tudo, exceto os objetos que pretende reutilizar (normalmente, irá reutilizar transformações e serão mantidos os localizadores de transmissão em fluxo, etc.). Se quiser que a sua conta seja limpa após fazer experiências, deverá eliminar os recursos que não planeia reutilizar.  Por exemplo, o seguinte código elimina as Tarefas.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisar de nenhum dos recursos presentes no seu grupo de recursos, incluindo a conta dos Serviços de Multimédia e a conta de armazenamento que criou para este tutorial, elimine o grupo de recursos anteriormente criado. 

Execute o seguinte comando da CLI:

```azurecli
az group delete --name amsResourceGroup
```
## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Proteger com o DRM](protect-with-drm.md)
