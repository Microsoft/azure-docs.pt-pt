---
title: "Utilizar a encriptação dinâmica AES-128 e o serviço de entrega de chave | Microsoft Docs"
description: "Serviços de suporte de dados do Microsoft Azure permitem entregar o conteúdo encriptado com chaves de encriptação AES de 128 bits. Os Media Services também fornecem o serviço de entrega de chave que fornece as chaves de encriptação para os utilizadores autorizados. Este tópico mostra como encriptar com AES-128 e utilizar o serviço de entrega de chave dinamicamente."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: a441e76fae0bda829cb112d307b3b436809b9c9b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-aes-128-dynamic-encryption-and-key-delivery-service"></a>Utilizar a encriptação dinâmica AES-128 e o serviço de entrega de chave
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Descrição geral
> [!NOTE]
> Consulte [isto](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption) vídeo para uma descrição geral de como proteger o suporte de dados de conteúdo com encriptação AES.
> 
> 

Serviços de suporte de dados do Microsoft Azure permitem entregar Http-Live-Streaming (HLS) e fluxos uniforme encriptados com AES Advanced Encryption Standard () (utilizando as chaves de encriptação de 128 bits). Os Media Services também fornecem o serviço de entrega de chave que fornece as chaves de encriptação para os utilizadores autorizados. Se pretender que para os serviços de suporte de dados encriptar um recurso, tem de associar uma chave de encriptação com o elemento e também configurar políticas de autorização da chave. Quando um fluxo é solicitado por um leitor, os Media Services utiliza a chave especificada para encriptar o conteúdo através da encriptação AES de forma dinâmica. Para desencriptar o fluxo, o leitor solicitará a chave do serviço de entrega de chave. Para decidir se pretende ou não o utilizador está autorizado para obter a chave, o serviço avalia as políticas de autorização que especificou para a chave.

Os Media Services suportam várias formas de autenticar utilizadores que efetuam pedidos de chave. A política de autorização da chave de conteúdo pode ter uma ou mais restrições de autorização: aberto ou token restrito. A política de token restrito tem de ser acompanhada por um token emitido por um Serviço de Token Seguro (STS). Os Media Services suportam tokens no formato [Web Tokens Simples](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) e [Web Token JSON](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Para obter mais informações, consulte [configurar política de autorização da chave de conteúdo](media-services-protect-with-aes128.md#configure_key_auth_policy).

Para tirar partido da encriptação dinâmica, tem de ter um elemento que contenha um conjunto de ficheiros MP4 com velocidade de transmissão múltipla ou ficheiros de origem de Transmissão em Fluxo Uniforme de múltipla transmissão. Terá também de configurar a política de entrega para o elemento (descrito mais à frente neste tópico). Em seguida, com base no formato especificado no URL de transmissão em fluxo, o servidor de Transmissão em Fluxo a Pedido irá garantir que a transmissão é entregue no protocolo que escolheu. Como resultado, só tem de armazenar e pagar pelos ficheiros num único formato de armazenamento e os Media Services irão compilar e disponibilizar a resposta adequada com base nos pedidos de um cliente.

Este tópico seria útil para programadores que funcionam em aplicações que entregam multimédia protegida. O tópico mostra como configurar o serviço de entrega de chave com políticas de autorização para que apenas os clientes autorizados conseguiram receber as chaves de encriptação. Também mostra como utilizar a encriptação dinâmica.


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Encriptação dinâmica AES-128 e fluxo de trabalho de serviço de entrega de chave

Seguem-se passos gerais que precisa para efetuar a encriptar os seus elementos com AES, utilizando o serviço de entrega de chave de Media Services e também da encriptação dinâmica.

1. [Criar um elemento e carregar ficheiros para o elemento](media-services-protect-with-aes128.md#create_asset).
2. [Codificar o elemento que contém o ficheiro para o conjunto de MP4 de velocidade de transmissão adaptável](media-services-protect-with-aes128.md#encode_asset).
3. [Criar uma chave de conteúdo e associe-a com elemento codificado](media-services-protect-with-aes128.md#create_contentkey). Nos Media Services, a chave de conteúdo contém a chave de encriptação do elemento.
4. [Configurar a política de autorização da chave de conteúdo](media-services-protect-with-aes128.md#configure_key_auth_policy). A política de autorização da chave de conteúdo tem de ser configurada por si e cumprida pelo cliente para que a chave de conteúdo seja entregue ao cliente.
5. [Configurar a política de entrega para um recurso](media-services-protect-with-aes128.md#configure_asset_delivery_policy). A configuração de política de entrega inclui: a chave de URL de aquisição e o Vetor de inicialização (IV) (AES 128 requer o mesmo IV indicar quando a encriptação e desencriptação), protocolo entrega (por exemplo, MPEG DASH, HLS, transmissão em fluxo uniforme ou todos), o tipo de encriptação dinâmica (por exemplo, envelope ou sem encriptação dinâmica).

    Pode aplicar uma política diferente para cada protocolo no mesmo elemento. Por exemplo, pode aplicar encriptação PlayReady para Uniforme/DASH e Envelope AES para HLS. Quaisquer protocolos que não estão definidos numa política de entrega (por exemplo, adicionar uma única política que especifica apenas HLS como o protocolo) serão bloqueados da transmissão em fluxo. A exceção é quando não há qualquer política de entrega de elemento definida. Em seguida, todos os protocolos serão permitidos.

6. [Criar um localizador OnDemand](media-services-protect-with-aes128.md#create_locator) para obter um URL de transmissão em fluxo.

O tópico também mostra [como uma aplicação cliente pode pedir uma chave do serviço de entrega de chave](media-services-protect-with-aes128.md#client_request).

Vai encontrar uma .NET concluída [exemplo](media-services-protect-with-aes128.md#example) no final do tópico.

A imagem seguinte demonstra o fluxo de trabalho descrito acima. Aqui, o token é utilizado para autenticação.

![Proteger com AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

O resto deste tópico fornece explicações detalhadas, exemplos de códigos e ligações para tópicos que mostram como atingir as tarefas descritas acima.

## <a name="current-limitations"></a>Limitações atuais
Se adicionar ou atualizar a sua política de entrega de elementos, tem de eliminar um localizador existente (se aplicável) e criar um novo localizador.

## <a id="create_asset"></a>Criar um elemento e carregar ficheiros para o elemento
Para poder gerir, codificar e transmitir em fluxo os seus vídeos, primeiro tem de carregar o conteúdo para os Media Services do Microsoft Azure. Assim que seja carregado, o seu conteúdo é armazenado em segurança na nuvem para processamento adicional e a transmissão em fluxo. 

Para obter informações detalhadas, consulte [Carregar Ficheiros para uma conta de Media Services](media-services-dotnet-upload-files.md).

## <a id="encode_asset"></a>Codificar o elemento que contém o ficheiro para o definir MP4 de velocidade de transmissão adaptável
Com a encriptação dinâmica, apenas tem de criar um elemento que contenha um conjunto de ficheiros MP4 com velocidade de transmissão múltipla ou ficheiros de origem de Transmissão em Fluxo Uniforme de múltipla transmissão. Em seguida, com base no formato especificado no pedido de manifesto ou fragmento, o On-a pedido de transmissão em fluxo servidor irá garantir que recebe o fluxo no protocolo que escolheu. Como resultado, só tem de armazenar e pagar pelos ficheiros num único formato de armazenamento e os Media Services irão compilar e disponibilizar a resposta adequada com base nos pedidos de um cliente. Para obter mais informações, consulte o tópico [Descrição Geral de Empacotamento Dinâmico](media-services-dynamic-packaging-overview.md).

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 
>
>Além disso, para poder utilizar o empacotamento dinâmico e a encriptação dinâmica seu elemento tem de conter um conjunto de MP4s de velocidade de transmissão adaptável ou ficheiros de transmissão em fluxo uniforme de velocidade de transmissão adaptável.

Para obter instruções sobre como codificar, consulte [Como codificar um elemento utilizando um Codificador de Multimédia Standard](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a id="create_contentkey"></a>Criar uma chave de conteúdo e associe-a com elemento codificado
Nos Media Services, a chave de conteúdo contém a chave na qual pretende encriptar um elemento.

Para obter informações detalhadas, consulte [Criar chave de conteúdo](media-services-dotnet-create-contentkey.md).

## <a id="configure_key_auth_policy"></a>Configure a política de autorização da chave de conteúdo
Os Media Services suportam várias formas de autenticar utilizadores que efetuam pedidos de chave. A política de autorização da chave de conteúdo tem de ser configurada por si e cumprida pelo cliente (leitor) para que a chave seja entregue ao cliente. A política de autorização da chave de conteúdo pode ter um ou mais restrições de autorização: abra, restrição ou restrição de IP do token.

Para obter informações detalhadas, consulte [Configurar a Política de Autorização da Chave de Conteúdo](media-services-dotnet-configure-content-key-auth-policy.md).

## <a id="configure_asset_delivery_policy"></a>Configurar a política de entrega de elemento
Configure a política de entrega para o seu elemento. Alguns dos aspetos da configuração de política de entrega de elementos incluem:

* O URL de aquisição de chave. 
* A inicialização Vetor (IV) a utilizar para a encriptação de envelope. AES 128 requer o mesmo IV indicar quando a encriptação e desencriptação. 
* O protocolo de entrega do elemento, (por exemplo, MPEG DASH, HLS, Smooth Streaming ou todos).
* O tipo de encriptação dinâmica (por exemplo, envelope AES) ou sem encriptação dinâmica. 

Para obter informações detalhadas, consulte [Configurar a política de entrega de elemento](media-services-rest-configure-asset-delivery-policy.md).

## <a id="create_locator"></a>Criar um localizador de transmissão em fluxo OnDemand para obter um URL de transmissão em fluxo
Terá de fornecer aos seus utilizadores o URL de transmissão em fluxo Uniforme, DASH ou HLS.

> [!NOTE]
> Se adicionar ou atualizar a sua política de entrega de elementos, tem de eliminar um localizador existente (se aplicável) e criar um novo localizador.
> 
> 

Para obter instruções sobre como publicar um elemento e compilar um URL de transmissão em fluxo, consulte [Compilar um URL de transmissão em fluxo](media-services-deliver-streaming-content.md).

## <a name="get-a-test-token"></a>Obter um token de teste
Obtenha um token de teste baseado na restrição de token que foi utilizada para a política de autorização de chave.

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

Pode utilizar o [Leitor AMS](http://amsplayer.azurewebsites.net/azuremediaplayer.html) para testar a sua transmissão em fluxo.

## <a id="client_request"></a>Como pode o cliente solicitar uma chave do serviço de entrega de chave?
No passo anterior, construir o URL que aponta para um ficheiro de manifesto. O cliente tem de extrair as informações necessárias a partir dos ficheiros de manifesto de transmissão em fluxo para efetuar um pedido para o serviço de entrega de chave.

### <a name="manifest-files"></a>Ficheiros de manifesto
O cliente tem de extrair o URL (que também contém a chave de conteúdo Id (kid)) valor a partir do ficheiro de manifesto. O cliente irá, em seguida, tentar obter a chave de encriptação do serviço de entrega de chave. O cliente também precisa extrair o valor de IV e utilize-a desencriptar o fluxo. O fragmento seguinte mostra o <Protection> elemento do manifesto de transmissão em fluxo uniforme.

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

No caso de HLS, o manifesto de raiz é dividido em ficheiros de segmento. 

Por exemplo, o manifesto de raiz é: http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl) contém uma lista de nomes de ficheiro de segmento.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Se abrir um dos ficheiros de segmento no editor de texto (por exemplo, http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl), it should contain #EXT X-chave que indica que o ficheiro está encriptado.

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
>Se estiver a planear reproduzir uma AES encriptados HLS no Safari, consulte [este blogue](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Solicitar a chave do serviço de entrega de chave

O código seguinte mostra como enviar um pedido para o serviço de entrega de chave de Media Services utilizando uma chave entrega Uri (que foi extraído do manifesto) e um token (Este tópico não falar sobre como obter Web Tokens simples a partir do serviço de Token seguro).

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

## <a name="protect-your-content-with-aes-128-using-net"></a>Proteger os seus conteúdos com AES-128 através do .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

1. Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET). 
2. Adicione os elementos seguintes a **appSettings** definidos no ficheiro app.config:

        <add key="Issuer" value="http://testacs.com"/>
        <add key="Audience" value="urn:test"/>

### <a id="example"></a>Exemplo

Substitua o código no seu ficheiro Program.cs com o código mostrado nesta secção.
 
>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Deve utilizar o mesmo ID de política se estiver a utilizar sempre os mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que pretendam permanecem no local durante muito tempo (políticas de não carregamento). Para obter mais informações, veja [este](media-services-dotnet-manage-entities.md#limit-access-policies) tópico.

Certifique-se de que atualiza as variáveis para apontar para as pastas onde se encontram os seus ficheiros de entrada.

[!code-csharp[Main](../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

