---
title: Configure uma política de autorização de chave de conteúdo utilizando o Media Services .NET SDK  Microsoft Docs
description: Saiba como configurar uma política de autorização para uma chave de conteúdo utilizando o Media Services .NET SDK.
services: media-services
documentationcenter: ''
author: mingfeiy
manager: femila
editor: ''
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 58d52cd194ca4391c61f2477189984273df1198a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78366893"
---
# <a name="configure-a-content-key-authorization-policy"></a>Configurar uma política de autorização de chave de conteúdo

[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Descrição geral
 Pode utilizar os Serviços De Mídia Azure para fornecer streams MPEG-DASH, Smooth Streaming e HTTP Live Streaming (HLS) protegidos com o Advanced Encryption Standard (AES) utilizando chaves de encriptação de 128 bits ou gestão de [direitos digitais PlayReady (DRM)](https://www.microsoft.com/playready/overview/). Com os Serviços de Media, também pode entregar streams DASH encriptados com DrM Widevine. Tanto o PlayReady, como o Widevine, são encriptados de acordo com a especificação de encriptação comum (ISO/IEC 23001 7 CENC).

A Media Services também fornece um serviço de entrega de chaves/licenças a partir do qual os clientes podem obter chaves AES ou licenças PlayReady/Widevine para reproduzir o conteúdo encriptado.

Se pretender que os Serviços de Media criptografem um ativo, precisa de associar uma chave de encriptação (CommonEncryption ou EnvelopeEncryption) ao ativo. Para mais informações, consulte [Create ContentKeys com .NET](media-services-dotnet-create-contentkey.md). Também precisa de configurar políticas de autorização para a chave (como descrito neste artigo).

Quando um stream é solicitado por um leitor, o Media Services utiliza a chave especificada para encriptar dinamicamente o seu conteúdo utilizando encriptação AES ou DRM. Para desencriptar a transmissão em fluxo, o leitor solicita a chave ao serviço de entrega de chaves. Para determinar se o utilizador está autorizado a obter a chave, o serviço avalia as políticas de autorização que especificou para a chave.

Os Media Services suportam várias formas de autenticar utilizadores que efetuam pedidos de chave. A política de autorização chave de conteúdo pode ter uma ou mais restrições de autorização. As opções são abertas ou restrições simbólicas. A política de token restrito tem de ser acompanhada por um token emitido por um serviço de tokens seguro (STS). Os Serviços de Media suportam tokens no formato simples web token[(SWT)](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)e no formato JSON Web Token[(JWT).](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)

A Media Services não fornece STS. Pode criar um STS personalizado ou utilizar o Serviço de Controlo de Acesso Azure para emitir fichas. O STS deve ser configurado para criar um símbolo assinado com a chave especificada e emitir alegações que especificaste na configuração da restrição simbólica (conforme descrito neste artigo). Se o token for válido e as reclamações no token corresponderem às configuradas para a chave de conteúdo, o serviço de entrega da chave Media Services devolve a chave de encriptação ao cliente.

Para obter mais informações, veja os artigos seguintes:

- [Autenticação simbólica jWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Integrar a aplicação baseada em Azure Media Services OWIN MVC com o Azure Ative Directory e restringir a entrega da chave de conteúdo com base em alegações da JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Algumas considerações aplicam-se
* Quando a conta dos Serviços de Multimédia é criada, é adicionado um ponto final de transmissão em fluxo predefinido à mesma, no estado "Parado". Para começar a transmitir o seu conteúdo e tirar partido de embalagens dinâmicas e encriptação dinâmica, o seu ponto final de streaming deve estar no estado "Running". 
* O seu ativo deve conter um conjunto de MP4s bitrate adaptativos ou ficheiros adaptativos de streaming liso de bitrate. Para mais informações, consulte [encode um ativo](media-services-encode-asset.md).
* Faça upload e codificar os seus ativos utilizando a opção AssetCreationOptions.StorageEncrypted.
* Se planeia ter várias teclas de conteúdo que requerem a mesma configuração de política, recomendamos que crie uma única política de autorização e reutilize-a com várias teclas de conteúdo.
* O serviço de entrega chave caches ContentKeyAuthorizationPolicy e seus objetos relacionados (opções de política e restrições) durante 15 minutos. Pode criar contentKeyAuthorizationPolicy e especificar usar uma restrição simbólica, testá-la e, em seguida, atualizar a política para a restrição aberta. Este processo demora cerca de 15 minutos até que a política mude para a versão aberta da política.
* Se adicionar ou atualizar a sua política de entrega de elementos, tem de eliminar qualquer localizador existente e criar um novo.
* Atualmente, não é possível encriptar downloads progressivos.
* Um ponto final de streaming dos Media Services define o valor do cabeçalho CORS 'Access-Control-Allow-Origin' em resposta pré-voo como o wildcard '\*'. Este valor funciona bem com a maioria dos jogadores, incluindo Azure Media Player, Roku e JWPlayer, entre outros. No entanto, alguns jogadores que usam dashjs não funcionam porque, com o modo de credenciais definido para "incluir", xMLHttpRequest nos seus traços não permite que o wildcard "\*" como o valor de 'Access-Control-Allow-Origin'. Como suposições para esta limitação em dashjs, se hospedar o seu cliente a partir de um único domínio, a Media Services pode especificar esse domínio no cabeçalho de resposta pré-voo. Para assistência, abra um bilhete de apoio através do portal Azure.

## <a name="aes-128-dynamic-encryption"></a>Encriptação dinâmica AES-128
### <a name="open-restriction"></a>Restrição aberta
Restrição aberta significa que o sistema fornece a chave a quem fizer um pedido chave. Esta restrição pode ser útil para efeitos de teste.

O exemplo seguinte cria uma política de autorização aberta e adiciona-a à chave de conteúdo:
```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {
        // Create ContentKeyAuthorizationPolicy with Open restrictions
        // and create authorization policy
        IContentKeyAuthorizationPolicy policy = _context.
        ContentKeyAuthorizationPolicies.
        CreateAsync("Open Authorization Policy").Result;
        
        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
            new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
                Name = "HLS Open Authorization Policy",
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                Requirements = null // no requirements needed for HLS
            };

        restrictions.Add(restriction);

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "policy", 
            ContentKeyDeliveryType.BaselineHttp, 
            restrictions, 
            "");

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }
```

### <a name="token-restriction"></a>Restrição simbólica
Esta secção descreve como criar uma política de autorização de chave de conteúdo e associá-la à chave de conteúdo. A política de autorização descreve quais os requisitos de autorização que devem ser cumpridos para determinar se o utilizador está autorizado a receber a chave. Por exemplo, a lista de chaves de verificação contém a chave com a que o símbolo foi assinado?

Para configurar a opção de restrição simbólica, é necessário utilizar um XML para descrever os requisitos de autorização do token. A configuração de restrição simbólica XML deve estar em conformidade com o seguinte esquema XML:
```csharp
#### Token restriction schema
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>
```
Quando configurar a política restrita de fichas, deve especificar a chave de verificação primária, o emitente e os parâmetros do público. A chave de verificação primária contém a chave de que o token foi assinado com. O emitente é o STS que emite o símbolo. O público (às vezes chamado de âmbito) descreve a intenção do símbolo ou o recurso a que o símbolo autoriza o acesso. O serviço de entrega de chave de serviços de multimédia valida que estes valores no token correspondem aos valores no modelo.

Quando utilizar o SDK dos Serviços de Media para .NET, pode utilizar a classe TokenRestrictionTemplate para gerar o símbolo de restrição.
O exemplo seguinte cria uma política de autorização com uma restrição simbólica. Neste exemplo, o cliente deve apresentar um símbolo que contenha uma chave de assinatura (VerificationKey), um emitente simbólico e reclamações necessárias.
```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString
                };

        restrictions.Add(restriction);

        //You could have multiple options 
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
                "Token option for HLS",
                ContentKeyDeliveryType.BaselineHttp,
                restrictions,
                null  // no key delivery data is needed for HLS
                );

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {
        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();

        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    }
```
#### <a name="test-token"></a>Ficha de teste
Para obter um símbolo de teste com base na restrição simbólica utilizada para a política de autorização chave, faça o seguinte:
```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);

    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();
```

## <a name="playready-dynamic-encryption"></a>Encriptação dinâmica PlayReady
Pode utilizar os Serviços de Media para configurar os direitos e restrições que pretende que o tempo de execução do Reto PlayReady para fazer cumprir quando um utilizador tenta reproduzir conteúdo protegido. 

Quando protege o seu conteúdo com o PlayReady, uma das coisas que precisa de especificar na sua política de autorização é uma cadeia XML que define o modelo de [licença PlayReady](media-services-playready-license-template-overview.md). No SDK de Serviços de Mídia para .NET, as classes PlayReadyLicenseResponseTemplate e PlayReadyLicenseTemplate ajudam-no a definir o modelo de licença PlayReady.

Para aprender a encriptar o seu conteúdo com playReady e Widevine, consulte [Use PlayReady e/ou Widevine dynamic common encryption](media-services-protect-with-playready-widevine.md).

### <a name="open-restriction"></a>Restrição aberta
Restrição aberta significa que o sistema fornece a chave a quem fizer um pedido chave. Esta restrição pode ser útil para efeitos de teste.

O exemplo seguinte cria uma política de autorização aberta e adiciona-a à chave de conteúdo:

```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {

        // Create ContentKeyAuthorizationPolicy with Open restrictions 
        // and create authorization policy          

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Open", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
                Requirements = null
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;


        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }
```

### <a name="token-restriction"></a>Restrição simbólica
Para configurar a opção de restrição simbólica, é necessário utilizar um XML para descrever os requisitos de autorização do token. A configuração de restrição simbólica XML deve estar em conformidade com o esquema XML mostrado na secção "Esquema de restrição token".

```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {

        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();


        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    } 

    static private string ConfigurePlayReadyLicenseTemplate()
    {
        // The following code configures PlayReady License Template using .NET classes
        // and returns the XML string.

        //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
        //It contains a field for a custom data string between the license server and the application 
        //(may be useful for custom app logic) as well as a list of one or more license templates.
        PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

        // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
        // to be returned to the end users. 
        //It contains the data on the content key in the license and any rights or restrictions to be 
        //enforced by the PlayReady DRM runtime when using the content key.
        PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
        //Configure whether the license is persistent (saved in persistent storage on the client) 
        //or non-persistent (only held in memory while the player is using the license).  
        licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

        // AllowTestDevices controls whether test devices can use the license or not.  
        // If true, the MinimumSecurityLevel property of the license
        // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
        licenseTemplate.AllowTestDevices = true;


        // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
        // It grants the user the ability to play back the content subject to the zero or more restrictions 
        // configured in the license and on the PlayRight itself (for playback specific policy). 
        // Much of the policy on the PlayRight has to do with output restrictions 
        // which control the types of outputs that the content can be played over and 
        // any restrictions that must be put in place when using a given output.
        // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
        //then the DRM runtime will only allow the video to be displayed over digital outputs 
        //(analog video outputs won’t be allowed to pass the content).

        //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
        // If the output protections are configured too restrictive, 
        // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

        // For example:
        //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

        responseTemplate.LicenseTemplates.Add(licenseTemplate);

        return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
    }
```

Para obter um símbolo de teste baseado na restrição simbólica que foi usada para a política de autorização chave, consulte a secção "[Test token](#test-token)". 

## <a id="types"></a>Tipos utilizados quando define contentKeyAuthorizationPolicy
### <a id="ContentKeyRestrictionType"></a>Tipo de restrição de chaves de conteúdo

```csharp
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }
```

### <a id="ContentKeyDeliveryType"></a>Tipo de entrega de chaves de conteúdo

```csharp 
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }
```

### <a id="TokenType"></a>TokenType

```csharp
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }
```

## <a name="additional-notes"></a>Notas adicionais

* A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos seguintes
Agora que configuraa a política de autorização da chave de conteúdo, consulte [configurar uma política](media-services-dotnet-configure-asset-delivery-policy.md)de entrega de ativos.

