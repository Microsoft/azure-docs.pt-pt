---
title: Usar a criptografia dinâmica AES-128 e o serviço de distribuição de chaves | Microsoft Docs
description: Este tópico mostra como criptografar dinamicamente com o AES-128 e usar o serviço de distribuição de chaves.
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
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 01153317b49e4543f10faa517bce7bcc01ce22d4
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895837"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Usar a criptografia dinâmica AES-128 e o serviço de distribuição de chaves
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>  

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, consulte [diretrizes de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

Você pode usar os serviços de mídia para fornecer HTTP Live Streaming (HLS) e Smooth Streaming criptografados com o AES usando chaves de criptografia de 128 bits. Os serviços de mídia também fornecem o serviço de distribuição de chaves que fornece chaves de criptografia para usuários autorizados. Se você quiser que os serviços de mídia criptografem um ativo, associe uma chave de criptografia ao ativo e também Configure políticas de autorização para a chave. Quando um fluxo é solicitado por um player, os serviços de mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando a criptografia AES. Para desencriptar a transmissão em fluxo, o leitor solicita a chave ao serviço de entrega de chaves. Para determinar se o usuário está autorizado a obter a chave, o serviço avalia as políticas de autorização que você especificou para a chave.

Os Media Services suportam várias formas de autenticar utilizadores que efetuam pedidos de chave. A política de autorização da chave de conteúdo pode ter uma ou mais restrições de autorização, quer sejam restrições abertas ou de token. A política de token restrito tem de ser acompanhada por um token emitido por um serviço de tokens seguro (STS). Os Serviços de Multimédia suportam tokens no formato [simple web tokens](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) e [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Para obter mais informações, veja [Configure the content key's authorization policy](media-services-protect-with-aes128.md#configure_key_auth_policy) (Configurar a política de autorização da chave de conteúdo).

Para tirar partido da encriptação dinâmica, tem de ter um elemento que contenha um conjunto de ficheiros MP4 com velocidade de transmissão múltipla ou ficheiros de origem de Transmissão em Fluxo Uniforme de múltipla transmissão. Você também precisa configurar a política de entrega para o ativo (descrita mais adiante neste artigo). Em seguida, com base no formato especificado no URL de transmissão em fluxo, o servidor de transmissão em fluxo a pedido irá garantir que a transmissão é entregue no protocolo que selecionou. Como resultado, você precisa armazenar e pagar apenas pelos arquivos em um único formato de armazenamento. Os Serviços de Multimédia criam e entregam a resposta adequada com base nos pedidos de um cliente.

Este artigo é útil para desenvolvedores que trabalham em aplicativos que fornecem mídia protegida. O artigo mostra como configurar o serviço de distribuição de chaves com políticas de autorização para que somente clientes autorizados possam receber chaves de criptografia. Ele também mostra como usar a criptografia dinâmica.

Para obter informações sobre como criptografar conteúdo com o criptografia AES (AES) para entrega para o Safari no macOS, consulte [esta postagem no blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).
Para obter uma visão geral de como proteger o conteúdo de mídia com a criptografia AES, consulte [este vídeo](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption).


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Fluxo de trabalho do serviço de distribuição de chaves e criptografia dinâmica AES-128

Execute as seguintes etapas gerais ao criptografar seus ativos com o AES usando o serviço de distribuição de chaves dos serviços de mídia e também usando a criptografia dinâmica:

1. [Criar um ativo e carregar arquivos no ativo](media-services-protect-with-aes128.md#create_asset).

2. [Codifique o ativo que contém o arquivo para o conjunto de MP4 de taxa de bits adaptável](media-services-protect-with-aes128.md#encode_asset).

3. [Crie uma chave de conteúdo e associe-a ao ativo codificado](media-services-protect-with-aes128.md#create_contentkey). Nos Serviços de Multimédia, a chave de conteúdo contém a chave de encriptação do elemento.

4. [Configure a política de autorização da chave de conteúdo](media-services-protect-with-aes128.md#configure_key_auth_policy). Tem de configurar a política de autorização da chave de conteúdo. O cliente tem de satisfazer a política antes de a chave de conteúdo lhe ser entregue.

5. [Configure a política de entrega para um ativo](media-services-protect-with-aes128.md#configure_asset_delivery_policy). A configuração da política de entrega inclui a URL de aquisição de chave e um vetor de inicialização (IV). (O AES-128 requer o mesmo IV para criptografia e descriptografia.) A configuração também inclui o protocolo de entrega (por exemplo, MPEG-DASH, HLS, Smooth Streaming ou All) e o tipo de criptografia dinâmica (por exemplo, envelope ou sem criptografia dinâmica).

    Pode aplicar uma política diferente para cada protocolo no mesmo elemento. Por exemplo, pode aplicar a encriptação do PlayReady a Smooth/DASH e envelope AES a HLS. Todos os protocolos que não estão definidos em uma política de entrega são bloqueados do streaming. (Um exemplo é se você adicionar uma única política que especifica apenas HLS como o protocolo.) A exceção será se você não tiver nenhuma política de entrega de ativos definida. Aí, todos os protocolos serão permitidos.

6. [Crie um localizador OnDemand](media-services-protect-with-aes128.md#create_locator) para obter uma URL de streaming.

O artigo também mostra [como um aplicativo cliente pode solicitar uma chave do serviço de distribuição de chaves](media-services-protect-with-aes128.md#client_request).

Você pode encontrar um [exemplo .net](media-services-protect-with-aes128.md#example) completo no final do artigo.

A imagem seguinte demonstra o fluxo de trabalho descrito anteriormente. Aqui, o token é utilizado para autenticação.

![Proteger com AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

O restante deste artigo fornece explicações, exemplos de código e links para tópicos que mostram como obter as tarefas descritas anteriormente.

## <a name="current-limitations"></a>Limitações atuais
Se adicionar ou atualizar a sua política de entrega de elementos, tem de eliminar qualquer localizador existente e criar um novo.

## <a id="create_asset"></a>Criar um ativo e carregar arquivos no ativo
Para gerir, codificar e transmitir em fluxo os seus vídeos, primeiro tem de carregar o conteúdo para os Serviços de Multimédia. Quando estiver carregado, o seu conteúdo é armazenado em segurança na cloud para processamento adicional e transmissão em fluxo. 

Para obter mais informações, veja [Upload files into a Media Services account](media-services-dotnet-upload-files.md) (Carregar ficheiros para uma conta dos Serviços de Multimédia).

## <a id="encode_asset"></a>Codificar o ativo que contém o arquivo para o conjunto de MP4 de taxa de bits adaptável
Com a encriptação dinâmica, vai criar um elemento que contenha um conjunto de ficheiros MP4 com velocidade de transmissão múltipla ou ficheiros de origem de Smooth Streaming de transmissão múltipla. Em seguida, com base no formato especificado no manifesto ou na solicitação de fragmento, o servidor de streaming sob demanda garante que você receba o fluxo no protocolo selecionado. Em seguida, você só precisa armazenar e pagar pelos arquivos em um único formato de armazenamento. Os Serviços de Multimédia criam e entregam a resposta adequada com base nos pedidos de um cliente. Para obter mais informações, veja [Dynamic packaging overview](media-services-dynamic-packaging-overview.md) (Descrição geral do empacotamento dinâmico).

>[!NOTE]
>Quando a conta dos Serviços de Multimédia é criada, é adicionado um ponto final de transmissão em fluxo predefinido à mesma, no estado "Parado". Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e da encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado "Em execução”. 
>
>Além disso, para usar o empacotamento dinâmico e a criptografia dinâmica, seu ativo deve conter um conjunto de MP4s de taxa de bits adaptável ou arquivos de Smooth Streaming de taxa de bits adaptável.

Para obter instruções sobre como codificar, veja [Encode an asset by using Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) (Utilizar o Media Encoder Standard para codificar elementos).

## <a id="create_contentkey"></a>Criar uma chave de conteúdo e associe-a com elemento codificado
Nos Media Services, a chave de conteúdo contém a chave na qual pretende encriptar um elemento.

Para obter mais informações, veja [Create a content key](media-services-dotnet-create-contentkey.md) (Criar chaves de conteúdo).

## <a id="configure_key_auth_policy"></a>Configurar a política de autorização da chave de conteúdo
Os Media Services suportam várias formas de autenticar utilizadores que efetuam pedidos de chave. Tem de configurar a política de autorização da chave de conteúdo. O cliente (leitor) tem de cumprir a política antes da chave pode ser entregue ao cliente. A política de autorização de chave de conteúdo pode ter uma ou mais restrições de autorização, abrir, restrição de token ou restrição de IP.

Para obter mais informações, veja [Configure a content key authorization policy](media-services-dotnet-configure-content-key-auth-policy.md) (Configurar uma política de autorização de chave de conteúdo).

## <a id="configure_asset_delivery_policy"></a>Configurar uma política de entrega de elementos
Configure a política de entrega para o seu elemento. Alguns dos aspetos da configuração da política de entrega de elementos incluem:

* A URL de aquisição de chave. 
* O vetor de inicialização (IV) a ser usado para a criptografia de envelope. O AES-128 requer o mesmo IV para criptografia e descriptografia. 
* O protocolo de entrega de elementos, (por exemplo, MPEG DASH, HLS, Smooth Streaming ou todos).
* O tipo de criptografia dinâmica (por exemplo, envelope AES) ou nenhuma criptografia dinâmica. 

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

Pode utilizar o [Leitor dos Serviços de Multimédia do Azure](https://aka.ms/azuremediaplayer) para testar a sua transmissão em fluxo.

## <a id="client_request"></a>Como o cliente pode solicitar uma chave do serviço de distribuição de chaves?
Na etapa anterior, você construiu a URL que aponta para um arquivo de manifesto. O cliente precisa extrair as informações necessárias dos arquivos de manifesto de streaming para fazer uma solicitação ao serviço de distribuição de chaves.

### <a name="manifest-files"></a>Arquivos de manifesto
O cliente precisa extrair a URL (que também contém o valor de ID de chave de conteúdo [Kid]) do arquivo de manifesto. Em seguida, o cliente tenta obter a chave de criptografia do serviço de distribuição de chaves. O cliente também precisa extrair o valor de IV e usá-lo para descriptografar o fluxo. O trecho a seguir mostra o elemento `<Protection>` do manifesto Smooth Streaming:

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

No caso do HLS, o manifesto raiz é dividido em arquivos de segmento. 

Por exemplo, o manifesto raiz é: http:\//test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest (Format = M3U8-AAPL). Ele contém uma lista de nomes de arquivo de segmento.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Se você abrir um dos arquivos de segmento em um editor de texto (por exemplo, http:\//test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels (514369)/manifest (vídeo, Format = M3U8-AAPL), ele conterá #EXT-X-KEY, o que indica que o arquivo está criptografado.

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
>Se você planeja reproduzir um HLS criptografado por AES no Safari, consulte [este blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Solicitar a chave do serviço de distribuição de chaves

O código a seguir mostra como enviar uma solicitação para o serviço de distribuição de chaves dos serviços de mídia usando um URI de entrega de chave (que foi extraído do manifesto) e um token. (Este artigo não explica como obter SWTs de um STS.)

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

## <a name="protect-your-content-with-aes-128-by-using-net"></a>Proteja seu conteúdo com o AES-128 usando o .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

1. Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento dos Serviços de Multimédia com .NET).

2. Adicione os seguintes elementos a appSettings, conforme definido no seu arquivo app. config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

### <a id="example"></a>Exemplo

Substitua o código no seu ficheiro Program.cs com o código mostrado nesta secção.
 
>[!NOTE]
>Há um limite de 1 milhão políticas para diferentes políticas de serviços de mídia (por exemplo, para política de localizador ou ContentKeyAuthorizationPolicy). Use a mesma ID de política se você sempre usar os mesmos dias/permissões de acesso. Um exemplo são as políticas para os localizadores que se destinam a estar em vigor durante muito tempo (políticas de não carregamento). Para obter mais informações, consulte a seção "limitar políticas de acesso" em [gerenciar ativos e entidades relacionadas com o SDK do .net dos serviços de mídia](media-services-dotnet-manage-entities.md#limit-access-policies).

Certifique-se de que atualiza as variáveis para apontar para as pastas onde se encontram os seus ficheiros de entrada.

[!code-csharp[Main](../../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
