---
title: Utilize encriptação dinâmica AES-128 e o serviço de entrega chave | Microsoft Docs
description: Este tópico mostra como encriptar dinamicamente com o AES-128 e utilizar o serviço de entrega de chaves.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 7dacc01b37fb91f02fe67f76fe79c0bfa9375ea7
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009543"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Utilizar a encriptação dinâmica de AES-128 e o serviço de entrega de chave

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>  

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Pode utilizar os Serviços de Comunicação social para fornecer HTTP Live Streaming (HLS) e Smooth Streaming encriptados com o AES utilizando chaves de encriptação de 128 bits. Os Serviços de Comunicação Social também fornecem o serviço de entrega chave que fornece chaves de encriptação aos utilizadores autorizados. Se pretender que os Media Services encriptem um ativo, associe uma chave de encriptação ao ativo e também configurá políticas de autorização para a chave. Quando um stream é solicitado por um jogador, os Media Services utilizam a chave especificada para encriptar dinamicamente o seu conteúdo utilizando a encriptação AES. Para desencriptar a transmissão em fluxo, o leitor solicita a chave ao serviço de entrega de chaves. Para determinar se o utilizador está autorizado a obter a chave, o serviço avalia as políticas de autorização que especificou para a chave.

Os Media Services suportam várias formas de autenticar utilizadores que efetuam pedidos de chave. A política de autorização da chave de conteúdo pode ter uma ou mais restrições de autorização, quer sejam restrições abertas ou de token. A política de token restrito tem de ser acompanhada por um token emitido por um serviço de tokens seguro (STS). Os Serviços de Multimédia suportam tokens no formato [simple web tokens](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2) (SWT) e [JSON Web Token](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) (JWT). Para obter mais informações, veja [Configure the content key's authorization policy](media-services-protect-with-aes128.md#configure_key_auth_policy) (Configurar a política de autorização da chave de conteúdo).

Para tirar partido da encriptação dinâmica, tem de ter um elemento que contenha um conjunto de ficheiros MP4 com velocidade de transmissão múltipla ou ficheiros de origem de Transmissão em Fluxo Uniforme de múltipla transmissão. Também é necessário configurar a política de entrega do ativo (descrito mais tarde neste artigo). Em seguida, com base no formato especificado no URL de transmissão em fluxo, o servidor de transmissão em fluxo a pedido irá garantir que a transmissão é entregue no protocolo que selecionou. Como resultado, precisa de armazenar e pagar apenas pelos ficheiros em formato único de armazenamento. Os Serviços de Multimédia criam e entregam a resposta adequada com base nos pedidos de um cliente.

Este artigo é útil para os desenvolvedores que trabalham em aplicações que fornecem meios protegidos. O artigo mostra-lhe como configurar o serviço de entrega chave com políticas de autorização para que apenas os clientes autorizados possam receber chaves de encriptação. Também mostra como usar encriptação dinâmica.

Para obter informações sobre como encriptar conteúdo com a Norma Avançada de Encriptação (AES) para entrega no Safari no macOS, consulte [este post de blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).
Para obter uma visão geral de como proteger o seu conteúdo de mídia com encriptação AES, consulte [este vídeo](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption).


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Encriptação dinâmica AES-128 e fluxo de trabalho do serviço de entrega chave

Execute os seguintes passos gerais quando encriptar os seus ativos com a AES utilizando o serviço de entrega de chaves dos Serviços de Comunicação Social e também utilizando encriptação dinâmica:

1. [Crie um ativo e faça o upload de ficheiros para o ativo.](media-services-protect-with-aes128.md#create_asset)

2. [Codificar o ativo que contém o ficheiro para o conjunto de BITRATE adaptativo MP4](media-services-protect-with-aes128.md#encode_asset).

3. [Crie uma chave de conteúdo e associe-a ao ativo codificado.](media-services-protect-with-aes128.md#create_contentkey) Nos Serviços de Multimédia, a chave de conteúdo contém a chave de encriptação do elemento.

4. [Configure a política de autorização da chave de conteúdo.](media-services-protect-with-aes128.md#configure_key_auth_policy) Tem de configurar a política de autorização da chave de conteúdo. O cliente tem de satisfazer a política antes de a chave de conteúdo lhe ser entregue.

5. [Configure a política de entrega de um ativo](media-services-protect-with-aes128.md#configure_asset_delivery_policy). A configuração da política de entrega inclui o URL de aquisição chave e um vetor de inicialização (IV). (AES-128 requer o mesmo IV para encriptação e desencriptação.) A configuração também inclui o protocolo de entrega (por exemplo, MPEG-DASH, HLS, Smooth Streaming, ou todos) e o tipo de encriptação dinâmica (por exemplo, envelope ou nenhuma encriptação dinâmica).

    Pode aplicar uma política diferente para cada protocolo no mesmo elemento. Por exemplo, pode aplicar a encriptação do PlayReady a Smooth/DASH e envelope AES a HLS. Quaisquer protocolos que não estejam definidos numa política de entrega estão bloqueados do streaming. (Um exemplo é se adicionarmos uma única política que especifique apenas o HLS como o protocolo.) A exceção é se não tiver nenhuma política de entrega de ativos definida. Aí, todos os protocolos serão permitidos.

6. [Crie um localizador OnDemand](media-services-protect-with-aes128.md#create_locator) para obter um URL de streaming.

O artigo também mostra [como uma aplicação do cliente pode solicitar uma chave do serviço de entrega chave](media-services-protect-with-aes128.md#client_request).

Pode encontrar um [exemplo completo .NET](media-services-protect-with-aes128.md#example) no final do artigo.

A imagem seguinte demonstra o fluxo de trabalho descrito anteriormente. Aqui, o token é utilizado para autenticação.

![Proteger com AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

O restante deste artigo fornece explicações, exemplos de código e ligações a tópicos que lhe mostram como realizar as tarefas anteriormente descritas.

## <a name="current-limitations"></a>Limitações atuais
Se adicionar ou atualizar a sua política de entrega de elementos, tem de eliminar qualquer localizador existente e criar um novo.

## <a name="create-an-asset-and-upload-files-into-the-asset"></a><a id="create_asset"></a>Criar um ativo e enviar ficheiros para o ativo
Para gerir, codificar e transmitir em fluxo os seus vídeos, primeiro tem de carregar o conteúdo para os Serviços de Multimédia. Quando estiver carregado, o seu conteúdo é armazenado em segurança na cloud para processamento adicional e transmissão em fluxo. 

Para obter mais informações, veja [Upload files into a Media Services account](media-services-dotnet-upload-files.md) (Carregar ficheiros para uma conta dos Serviços de Multimédia).

## <a name="encode-the-asset-that-contains-the-file-to-the-adaptive-bitrate-mp4-set"></a><a id="encode_asset"></a>Codifique o elemento que contém o ficheiro para o MP4 de velocidade de transmissão adaptável definido.
Com a encriptação dinâmica, vai criar um elemento que contenha um conjunto de ficheiros MP4 com velocidade de transmissão múltipla ou ficheiros de origem de Smooth Streaming de transmissão múltipla. Em seguida, com base no formato especificado no pedido manifesto ou fragmento, o servidor de streaming a pedido garante que recebe o fluxo no protocolo selecionado. Em seguida, basta armazenar e pagar os ficheiros em formato único de armazenamento. Os Serviços de Multimédia criam e entregam a resposta adequada com base nos pedidos de um cliente. Para obter mais informações, veja [Dynamic packaging overview](media-services-dynamic-packaging-overview.md) (Descrição geral do empacotamento dinâmico).

>[!NOTE]
>Quando a conta dos Serviços de Multimédia é criada, é adicionado um ponto final de transmissão em fluxo predefinido à mesma, no estado "Parado". Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e da encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado "Em execução”. 
>
>Além disso, para utilizar embalagens dinâmicas e encriptação dinâmica, o seu ativo deve conter um conjunto de mp4s bitrate adaptativos ou ficheiros de streaming suaves adaptáveis.

Para obter instruções sobre como codificar, veja [Encode an asset by using Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) (Utilizar o Media Encoder Standard para codificar elementos).

## <a name="create-a-content-key-and-associate-it-with-the-encoded-asset"></a><a id="create_contentkey"></a>Crie uma chave de conteúdo e associe-a ao ativo codificado
Nos Media Services, a chave de conteúdo contém a chave na qual pretende encriptar um elemento.

Para obter mais informações, veja [Create a content key](media-services-dotnet-create-contentkey.md) (Criar chaves de conteúdo).

## <a name="configure-the-content-keys-authorization-policy"></a><a id="configure_key_auth_policy"></a>Configurar a política de autorização da chave de conteúdo
Os Media Services suportam várias formas de autenticar utilizadores que efetuam pedidos de chave. Tem de configurar a política de autorização da chave de conteúdo. O cliente (jogador) deve cumprir a apólice antes de a chave poder ser entregue ao cliente. A política de autorização de chave de conteúdo pode ter uma ou mais restrições de autorização, quer abertas, restrições simbólicas ou restrições de IP.

Para obter mais informações, veja [Configure a content key authorization policy](media-services-dotnet-configure-content-key-auth-policy.md) (Configurar uma política de autorização de chave de conteúdo).

## <a name="configure-an-asset-delivery-policy"></a><a id="configure_asset_delivery_policy"></a>Configurar uma política de entrega de elementos
Configure a política de entrega para o seu elemento. Alguns dos aspetos da configuração da política de entrega de elementos incluem:

* O URL de aquisição chave. 
* O vetor de inicialização (IV) para usar para a encriptação do envelope. O AES-128 requer o mesmo IV para encriptação e desencriptação. 
* O protocolo de entrega de elementos, (por exemplo, MPEG DASH, HLS, Smooth Streaming ou todos).
* O tipo de encriptação dinâmica (por exemplo, envelope AES) ou nenhuma encriptação dinâmica. 

Para obter mais informações, veja [Configure asset delivery policy](media-services-dotnet-configure-asset-delivery-policy.md) (Configurar a política de entrega de elementos).

## <a name="create-an-ondemand-streaming-locator-to-get-a-streaming-url"></a><a id="create_locator"></a>Criar um localizador de transmissão em fluxo OnDemand para obter um URL de transmissão em fluxo
Tem de indicar aos seus utilizadores o URL de transmissão de Smooth Streaming, DASH ou HLS.

> [!NOTE]
> Se adicionar ou atualizar a sua política de entrega de elementos, tem de eliminar qualquer localizador existente e criar um novo.
> 
> 

Para obter instruções sobre como publicar um elemento e compilar um URL de transmissão em fluxo, consulte [Compilar um URL de transmissão em fluxo](media-services-deliver-streaming-content.md).

## <a name="get-a-test-token"></a>Obter um token de teste
Obtenha um token de teste baseado na restrição de token que foi utilizada para a política de autorização de chave.

```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word "Bearer" in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

Pode utilizar o [Leitor dos Serviços de Multimédia do Azure](https://aka.ms/azuremediaplayer) para testar a sua transmissão em fluxo.

## <a name="how-can-your-client-request-a-key-from-the-key-delivery-service"></a><a id="client_request"></a>Como pode o seu cliente solicitar uma chave do serviço de entrega chave?
No passo anterior, construiu o URL que aponta para um ficheiro manifesto. O seu cliente precisa de extrair as informações necessárias dos ficheiros manifestos de streaming para fazer um pedido ao serviço de entrega chave.

### <a name="manifest-files"></a>Ficheiros manifestos
O cliente precisa extrair o url (que também contém o valor de ID [criança] da chave de conteúdo do ficheiro manifesto. O cliente tenta então obter a chave de encriptação do serviço de entrega de chaves. O cliente também precisa extrair o valor IV e usá-lo para desencriptar o fluxo. O seguinte corte mostra o `<Protection>` elemento do manifesto de Streaming Suave:

```xml
    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>
```

No caso do HLS, o manifesto raiz é dividido em ficheiros de segmento. 

Por exemplo, o manifesto de raiz é: http: \/ /test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest (formato=m3u8-aapl). Contém uma lista de nomes de ficheiros de segmento.

```text
. . . 
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
…
```

Se abrir um dos ficheiros de segmento num editor de texto (por exemplo, http: \/ /test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifesto (vídeo,formato=m3u8-aapl), contém #EXT-X-X, KEY o que indica que o ficheiro está encriptado.

```text
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:9
#EXT-X-KEY:METHOD=AES-128,
URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
        kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
        IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
#EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
#EXTINF:8.425708,no-desc
Fragments(video=0,format=m3u8-aapl)
#EXT-X-ENDLIST
```

>[!NOTE] 
>Se planeia jogar um HLS encriptado com AES no Safari, consulte [este blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Solicite a chave do serviço de entrega chave

O código que se segue mostra como enviar um pedido para o serviço de entrega de chaves dos Serviços de Comunicação, utilizando uma entrega chave Uri (que foi extraída do manifesto) e um símbolo. (Este artigo não explica como obter SWTs de uma STS.)

```csharp
    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }
```

## <a name="protect-your-content-with-aes-128-by-using-net"></a>Proteja o seu conteúdo com a AES-128 utilizando .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

1. Configurar o seu ambiente de desenvolvimento e povoar o ficheiro app.config com informações de ligação, conforme descrito no [desenvolvimento dos Serviços de Comunicação Social com .NET](media-services-dotnet-how-to-use.md).

2. Adicione os seguintes elementos a appSettings, conforme definido no seu ficheiro app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

### <a name="example"></a><a id="example"></a>Exemplo

Substitua o código no seu ficheiro Program.cs com o código mostrado nesta secção.
 
>[!NOTE]
>Existe um limite de 1.000.000 políticas para diferentes políticas de Serviços de Comunicação (por exemplo, para a política de Locator ou ContentKeyAuthorizationPolicy). Utilize o mesmo ID de política se utilizar sempre as mesmas permissões de dia/acesso. Um exemplo são as políticas para os localizadores que se destinam a estar em vigor durante muito tempo (políticas de não carregamento). Para mais informações, consulte a secção "Limitar as políticas de acesso" em [Gerir ativos e entidades relacionadas com os Serviços de Comunicação Social .NET SDK](media-services-dotnet-manage-entities.md#limit-access-policies).

Certifique-se de que atualiza as variáveis para apontar para as pastas onde se encontram os seus ficheiros de entrada.

[!code-csharp[Main](../../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
