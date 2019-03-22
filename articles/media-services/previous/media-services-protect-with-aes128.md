---
title: Utilizar a encriptação dinâmica de AES-128 e o serviço de entrega de chave | Documentos da Microsoft
description: Distribuir os seus conteúdos encriptado com chaves de encriptação AES de 128 bits com os serviços de multimédia do Microsoft Azure. Serviços de multimédia também fornecem o serviço de entrega de chave que fornece as chaves de encriptação para os utilizadores autorizados. Este tópico mostra como encriptar com AES-128 e utilizar o serviço de entrega de chave dinamicamente.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 7ff2e89c116ee74665c0e3a74505476972af5d9c
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317158"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Utilizar a encriptação dinâmica de AES-128 e o serviço de entrega de chave
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>  

> [!NOTE]
> Para obter a versão mais recente do Java SDK e começar a programar com o Java, veja [Introdução ao SDK de cliente Java para os Serviços de Multimédia](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Para transferir o SDK mais recente do PHP para os Serviços de Multimédia, procure a versão 0.5.7 do pacote Microsoft/WindowsAzure no [repositório do Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="overview"></a>Descrição geral
> [!NOTE]
> Para obter informações sobre como encriptar o conteúdo com o AES Advanced Encryption Standard () para entrega para o Safari no macOS, veja [nesta mensagem de blogue](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).
> Para uma descrição geral de como proteger o seu conteúdo multimédia com encriptação AES, consulte [este vídeo](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption).
> 
> 

 Pode utilizar os serviços de multimédia para entregar HTTP Live Streaming (HLS) e transmissão em fluxo uniforme encriptado com o AES com chaves de encriptação de 128 bits. Serviços de multimédia também fornecem o serviço de entrega de chave que fornece as chaves de encriptação para os utilizadores autorizados. Se pretender que os serviços de multimédia para encriptar um elemento, associe a uma chave de encriptação com o elemento e também configurar políticas de autorização da chave. Quando um fluxo é solicitado por um jogador, serviços de multimédia utiliza a chave especificada para encriptar dinamicamente o seu conteúdo através de encriptação AES. Para desencriptar a transmissão em fluxo, o leitor solicita a chave ao serviço de entrega de chaves. Para determinar se o utilizador está autorizado a obter a chave, o serviço avalia as políticas de autorização que especificou para a chave.

Os Media Services suportam várias formas de autenticar utilizadores que efetuam pedidos de chave. A política de autorização da chave de conteúdo pode ter uma ou mais restrições de autorização, quer sejam restrições abertas ou de token. A política de token restrito tem de ser acompanhada por um token emitido por um serviço de tokens seguro (STS). Os Serviços de Multimédia suportam tokens no formato [simple web tokens](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) e [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Para obter mais informações, veja [Configure the content key's authorization policy](media-services-protect-with-aes128.md#configure_key_auth_policy) (Configurar a política de autorização da chave de conteúdo).

Para tirar partido da encriptação dinâmica, tem de ter um elemento que contenha um conjunto de ficheiros MP4 com velocidade de transmissão múltipla ou ficheiros de origem de Transmissão em Fluxo Uniforme de múltipla transmissão. Terá também de configurar a política de entrega para o elemento (descrito mais adiante neste artigo). Em seguida, com base no formato especificado no URL de transmissão em fluxo, o servidor de transmissão em fluxo a pedido irá garantir que a transmissão é entregue no protocolo que selecionou. Como resultado, precisa armazenar e pagar apenas pelos ficheiros num único formato de armazenamento. Os Serviços de Multimédia criam e entregam a resposta adequada com base nos pedidos de um cliente.

Este artigo é útil para os programadores que trabalham com aplicações que entregam multimédia protegida. O artigo mostra-lhe como configurar o serviço de entrega de chave com políticas de autorização para que apenas os clientes autorizados podem receber as chaves de encriptação. Ele também mostra como utilizar a encriptação dinâmica.


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>A encriptação dinâmica AES-128 e fluxo de trabalho de serviço de entrega de chave

Quando criptografa seus ativos com AES com o serviço de entrega de chave de serviços de multimédia e também ao utilizar a encriptação dinâmica, execute os seguintes passos gerais:

1. [Criar um elemento e carregar ficheiros para o elemento](media-services-protect-with-aes128.md#create_asset).

2. [Codifique o elemento que contém o ficheiro para o conjunto de MP4 de velocidade de transmissão adaptável](media-services-protect-with-aes128.md#encode_asset).

3. [Criar uma chave de conteúdo e associe-a com elemento codificado](media-services-protect-with-aes128.md#create_contentkey). Nos Serviços de Multimédia, a chave de conteúdo contém a chave de encriptação do elemento.

4. [Configurar a política de autorização da chave de conteúdo](media-services-protect-with-aes128.md#configure_key_auth_policy). Tem de configurar a política de autorização da chave de conteúdo. O cliente tem de satisfazer a política antes de a chave de conteúdo lhe ser entregue.

5. [Configurar a política de entrega para um recurso](media-services-protect-with-aes128.md#configure_asset_delivery_policy). A configuração de política de entrega inclui o URL de aquisição de chave e um vetor de inicialização (IV). (AES-128 requer o mesmo IV para encriptação e desencriptação.) A configuração também inclui o protocolo de entrega (por exemplo, MPEG-DASH, HLS, Smooth Streaming ou todos) e o tipo de encriptação dinâmica (por exemplo, envelope ou sem encriptação dinâmica).

    Pode aplicar uma política diferente para cada protocolo no mesmo elemento. Por exemplo, pode aplicar a encriptação do PlayReady a Smooth/DASH e envelope AES a HLS. Quaisquer protocolos que não estão definidos numa política de entrega estão bloqueados da transmissão em fluxo. (Um exemplo é se adicionar uma única política que especifica apenas HLS como o protocolo). A exceção é se não tiver nenhuma política de entrega de elementos definida. Aí, todos os protocolos serão permitidos.

6. [Criar um localizador OnDemand](media-services-protect-with-aes128.md#create_locator) para obter um URL de transmissão em fluxo.

O artigo também mostra [como uma aplicação cliente pode pedir uma chave do serviço de entrega de chave](media-services-protect-with-aes128.md#client_request).

Pode encontrar uma completa [exemplo de .NET](media-services-protect-with-aes128.md#example) no final do artigo.

A imagem seguinte demonstra o fluxo de trabalho descrito anteriormente. Aqui, o token é utilizado para autenticação.

![Proteger com AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

O restante deste artigo apresenta explicações, exemplos de código e ligações para tópicos que mostram como atingir as tarefas descritas anteriormente.

## <a name="current-limitations"></a>Limitações atuais
Se adicionar ou atualizar a sua política de entrega de elementos, tem de eliminar qualquer localizador existente e criar um novo.

## <a id="create_asset"></a>Criar um elemento e carregar ficheiros para o elemento
Para gerir, codificar e transmitir em fluxo os seus vídeos, primeiro tem de carregar o conteúdo para os Serviços de Multimédia. Quando estiver carregado, o seu conteúdo é armazenado em segurança na cloud para processamento adicional e transmissão em fluxo. 

Para obter mais informações, veja [Upload files into a Media Services account](media-services-dotnet-upload-files.md) (Carregar ficheiros para uma conta dos Serviços de Multimédia).

## <a id="encode_asset"></a>Codifique o elemento que contém o ficheiro para o definir MP4 de velocidade de transmissão adaptável
Com a encriptação dinâmica, vai criar um elemento que contenha um conjunto de ficheiros MP4 com velocidade de transmissão múltipla ou ficheiros de origem de Smooth Streaming de transmissão múltipla. Em seguida, com base no formato especificado no pedido de manifesto ou fragmento, o servidor de transmissão em fluxo a pedido garante que recebe o fluxo no protocolo que selecionou. Em seguida, só precisa armazenar e pagar pelos ficheiros num único formato de armazenamento. Os Serviços de Multimédia criam e entregam a resposta adequada com base nos pedidos de um cliente. Para obter mais informações, veja [Dynamic packaging overview](media-services-dynamic-packaging-overview.md) (Descrição geral do empacotamento dinâmico).

>[!NOTE]
>Quando a conta dos Serviços de Multimédia é criada, é adicionado um ponto final de transmissão em fluxo predefinido à mesma, no estado "Parado". Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e da encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado "Em execução”. 
>
>Além disso, para utilizar o empacotamento dinâmico e a encriptação dinâmica, o elemento tem de conter um conjunto de MP4s de velocidade de transmissão adaptável ou ficheiros de transmissão em fluxo uniforme de velocidade de transmissão adaptável.

Para obter instruções sobre como codificar, veja [Encode an asset by using Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) (Utilizar o Media Encoder Standard para codificar elementos).

## <a id="create_contentkey"></a>Criar uma chave de conteúdo e associe-a com elemento codificado
Nos Media Services, a chave de conteúdo contém a chave na qual pretende encriptar um elemento.

Para obter mais informações, veja [Create a content key](media-services-dotnet-create-contentkey.md) (Criar chaves de conteúdo).

## <a id="configure_key_auth_policy"></a>Configurar a política de autorização da chave de conteúdo
Os Media Services suportam várias formas de autenticar utilizadores que efetuam pedidos de chave. Tem de configurar a política de autorização da chave de conteúdo. O cliente (leitor) tem de cumprir a política antes da chave pode ser entregue ao cliente. A política de autorização da chave de conteúdo pode ter um ou mais restrições de acesso autorização, um abrir, restrição ou restrição de IP do token.

Para obter mais informações, veja [Configure a content key authorization policy](media-services-dotnet-configure-content-key-auth-policy.md) (Configurar uma política de autorização de chave de conteúdo).

## <a id="configure_asset_delivery_policy"></a>Configurar uma política de entrega de elementos
Configure a política de entrega para o seu elemento. Alguns dos aspetos da configuração da política de entrega de elementos incluem:

* O URL de aquisição de chave. 
* O vetor de inicialização (IV) a utilizar para a encriptação de envelope. AES-128 requer o mesmo IV para encriptação e desencriptação. 
* O protocolo de entrega de elementos, (por exemplo, MPEG DASH, HLS, Smooth Streaming ou todos).
* O tipo de encriptação dinâmica (por exemplo, envelope AES) ou sem encriptação dinâmica. 

Para obter mais informações, veja [Configure asset delivery policy](media-services-dotnet-configure-asset-delivery-policy.md) (Configurar a política de entrega de elementos).

## <a id="create_locator"></a>Criar um localizador de transmissão em fluxo OnDemand para obter um URL de transmissão em fluxo
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

Pode utilizar o [Leitor dos Serviços de Multimédia do Azure](https://amsplayer.azurewebsites.net/azuremediaplayer.html) para testar a sua transmissão em fluxo.

## <a id="client_request"></a>Como pode o cliente pedir uma chave do serviço de entrega de chave?
No passo anterior, construiu a URL que aponta para um arquivo de manifesto. O cliente precise extrair as informações necessárias dos arquivos de manifestos de transmissão em fluxo para fazer um pedido para o serviço de entrega de chave.

### <a name="manifest-files"></a>Ficheiros de manifesto
O cliente precisa extrair o URL (que também contém conteúdo [criança] do ID da chave) valor a partir do ficheiro de manifesto. O cliente tenta, em seguida obter a chave de encriptação do serviço de entrega de chave. O cliente também precisa extrair o valor de IV e usá-la para desencriptar o fluxo. O fragmento seguinte mostra o <Protection> elemento do manifesto de transmissão em fluxo uniforme:

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

No caso de HLS, o manifesto de raiz é dividido em arquivos de segmento. 

Por exemplo, o manifesto de raiz é: http:\//test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl). Ela contém uma lista de nomes de ficheiro do segmento.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Se abrir um dos ficheiros de segmento num editor de texto (por exemplo, http:\//test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl), ela contém #EXT-X-chave, que indica que o ficheiro está encriptado.

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

>[!NOTE] 
>Se pretender reproduzir um HLS encriptado por AES no Safari, consulte [este blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Solicitar a chave do serviço de entrega de chave

O código seguinte mostra como enviar um pedido para o serviço de entrega de chave de serviços de multimédia através de uma entrega de chave de Uri (que foram extraída a partir do manifesto) e um token. (Este artigo não explica como obter SWTs de um STS).

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

## <a name="protect-your-content-with-aes-128-by-using-net"></a>Proteger os seus conteúdos com AES-128 com o .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

1. Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento dos Serviços de Multimédia com .NET).

2. Adicione os seguintes elementos para appSettings, conforme definido no ficheiro App. config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

### <a id="example"></a>Exemplo

Substitua o código no seu ficheiro Program.cs com o código mostrado nesta secção.
 
>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas de serviços de multimédia (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Utilize o mesmo ID de política se utilizar sempre as mesmo dias/permissões de acesso. Um exemplo são as políticas para os localizadores que se destinam a estar em vigor durante muito tempo (políticas de não carregamento). Para obter mais informações, consulte a secção de "Políticas de acesso de limite" em [gerir ativos e entidades relacionadas com o SDK .NET dos Media Services](media-services-dotnet-manage-entities.md#limit-access-policies).

Certifique-se de que atualiza as variáveis para apontar para as pastas onde se encontram os seus ficheiros de entrada.

[!code-csharp[Main](../../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
