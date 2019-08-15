---
title: Configurar uma política de autorização de chave de conteúdo usando o SDK do .NET dos serviços de mídia | Microsoft Docs
description: Saiba como configurar uma política de autorização para uma chave de conteúdo usando o SDK do .NET dos serviços de mídia.
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
ms.openlocfilehash: a2d978a68f6f654e3bdeea07c931cd7103f5850c
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "69015535"
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>Criptografia dinâmica: Configurar uma política de autorização de chave de conteúdo
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Descrição geral
 Você pode usar os serviços de mídia do Azure para fornecer fluxos MPEG-DASH, Smooth Streaming e HTTP Live Streaming (HLS) protegidos com o criptografia AES (AES) usando chaves de criptografia de 128 bits ou o [DRM (gerenciamento de direitos digitais) do PlayReady](https://www.microsoft.com/playready/overview/). Com os serviços de mídia, você também pode fornecer fluxos de traço criptografados com o Widevine DRM. Tanto o PlayReady, como o Widevine, são encriptados de acordo com a especificação de encriptação comum (ISO/IEC 23001 7 CENC).

Os serviços de mídia também fornecem um serviço de entrega de chave/licença do qual os clientes podem obter chaves AES ou licenças PlayReady/Widevine para reproduzir o conteúdo criptografado.

Se desejar que os serviços de mídia criptografem um ativo, você precisará associar uma chave de criptografia (CommonEncryption ou EnvelopeEncryption) ao ativo. Para obter mais informações, consulte [criar ContentKeys com .net](media-services-dotnet-create-contentkey.md). Você também precisa configurar políticas de autorização para a chave (conforme descrito neste artigo).

Quando um fluxo é solicitado por um player, os serviços de mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando criptografia AES ou DRM. Para desencriptar a transmissão em fluxo, o leitor solicita a chave ao serviço de entrega de chaves. Para determinar se o usuário está autorizado a obter a chave, o serviço avalia as políticas de autorização que você especificou para a chave.

Os Media Services suportam várias formas de autenticar utilizadores que efetuam pedidos de chave. A política de autorização de chave de conteúdo pode ter uma ou mais restrições de autorização. As opções são de restrição aberta ou de token. A política de token restrito tem de ser acompanhada por um token emitido por um serviço de tokens seguro (STS). Os serviços de mídia dão suporte a tokens no formato Simple Web token ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) e no formato JSON Web token ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)).

Os serviços de mídia não fornecem STS. Você pode criar um STS personalizado ou usar o serviço de controle de acesso do Azure para emitir tokens. O STS deve ser configurado para criar um token assinado com a chave especificada e as declarações de emissão que você especificou na configuração de restrição de token (conforme descrito neste artigo). Se o token for válido e as declarações no token corresponderem àquelas configuradas para a chave de conteúdo, o serviço de distribuição de chaves dos serviços de mídia retornará a chave de criptografia para o cliente.

Para obter mais informações, veja os artigos seguintes:

- [Autenticação de token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Integrar o aplicativo baseado em MVC do Azure Media Services OWIN com Azure Active Directory e restringir a entrega de chave de conteúdo com base em declarações JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Algumas considerações se aplicam
* Quando a conta dos Serviços de Multimédia é criada, é adicionado um ponto final de transmissão em fluxo predefinido à mesma, no estado "Parado". Para começar a transmitir seu conteúdo e aproveitar o empacotamento dinâmico e a criptografia dinâmica, o ponto de extremidade de streaming deve estar no estado "em execução". 
* Seu ativo deve conter um conjunto de MP4s de taxa de bits adaptável ou arquivos Smooth Streaming de taxa de bits adaptável. Para obter mais informações, consulte [codificar um ativo](media-services-encode-asset.md).
* Carregue e Codifique seus ativos usando a opção AssetCreationOptions. StorageEncrypted.
* Se você planeja ter várias chaves de conteúdo que exigem a mesma configuração de política, é recomendável criar uma única política de autorização e reutilizá-la com várias chaves de conteúdo.
* O serviço de distribuição de chaves armazena em cache ContentKeyAuthorizationPolicy e seus objetos relacionados (opções e restrições de política) por 15 minutos. Você pode criar ContentKeyAuthorizationPolicy e especificar para usar uma restrição de token, testá-la e, em seguida, atualizar a política para a restrição aberta. Esse processo leva aproximadamente 15 minutos antes que a política alterne para a versão aberta da política.
* Se adicionar ou atualizar a sua política de entrega de elementos, tem de eliminar qualquer localizador existente e criar um novo.
* No momento, não é possível criptografar downloads progressivos.
* Um ponto de extremidade de streaming dos serviços de mídia define o valor do cabeçalho ' Access-Control-Allow-Origin ' do CORS na resposta de\*simulação como o curinga ' '. Esse valor funciona bem com a maioria dos players, incluindo Player de Mídia do Azure, Roku e JWPlayer, entre outros. No entanto, alguns jogadores que usam dashjs não funcionam porque, com o modo de credenciais definido como "include", XMLHttpRequest em seu dashjs não permite\*o curinga "" como o valor de "Access-Control-Allow-Origin". Como uma solução alternativa para essa limitação no dashjs, se você hospedar o cliente de um único domínio, os serviços de mídia poderão especificar esse domínio no cabeçalho de resposta de simulação. Para obter assistência, abra um tíquete de suporte por meio do portal do Azure.

## <a name="aes-128-dynamic-encryption"></a>Criptografia dinâmica AES-128
### <a name="open-restriction"></a>Restrição aberta
Restrição aberta significa que o sistema entrega a chave para qualquer pessoa que faça uma solicitação de chave. Essa restrição pode ser útil para fins de teste.

O exemplo a seguir cria uma política de autorização aberta e a adiciona à chave de conteúdo:
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

### <a name="token-restriction"></a>Restrição de token
Esta seção descreve como criar uma política de autorização de chave de conteúdo e associá-la à chave de conteúdo. A política de autorização descreve quais requisitos de autorização devem ser atendidos para determinar se o usuário está autorizado a receber a chave. Por exemplo, a lista de chaves de verificação contém a chave com a qual o token foi assinado?

Para configurar a opção de restrição de token, você precisa usar um XML para descrever os requisitos de autorização do token. O XML de configuração de restrição de token deve estar de acordo com o seguinte esquema XML:
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
Ao configurar a política restrita por token, você deve especificar os parâmetros de chave de verificação primária, emissor e público. A chave de verificação primária contém a chave de que o token foi assinado com. O emissor é o STS que emite o token. O público (às vezes chamado de escopo) descreve a intenção do token ou o recurso ao qual o token autoriza o acesso. O serviço de entrega de chave de serviços de multimédia valida que estes valores no token correspondem aos valores no modelo.

Ao usar o SDK dos serviços de mídia para .NET, você pode usar a classe TokenRestrictionTemplate para gerar o token de restrição.
O exemplo a seguir cria uma política de autorização com uma restrição de token. Neste exemplo, o cliente deve apresentar um token que contém uma chave de assinatura (VerificationKey), um emissor de token e declarações necessárias.
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
#### <a name="test-token"></a>Token de teste
Para obter um token de teste com base na restrição de token que foi usada para a política de autorização de chave, faça o seguinte:
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

## <a name="playready-dynamic-encryption"></a>Criptografia dinâmica do PlayReady
Você pode usar os serviços de mídia para configurar os direitos e as restrições que você deseja que o tempo de execução do PlayReady DRM aplique quando um usuário tentar reproduzir conteúdo protegido. 

Quando você protege seu conteúdo com o PlayReady, uma das coisas que você precisa especificar em sua política de autorização é uma cadeia de caracteres XML que define o [modelo de licença do PlayReady](media-services-playready-license-template-overview.md). No SDK dos serviços de mídia para .NET, as classes PlayReadyLicenseResponseTemplate e PlayReadyLicenseTemplate ajudam a definir o modelo de licença do PlayReady.

Para saber como criptografar seu conteúdo com o PlayReady e o Widevine, confira [usar a criptografia comum dinâmica do PlayReady e/ou do Widevine](media-services-protect-with-playready-widevine.md).

### <a name="open-restriction"></a>Restrição aberta
Restrição aberta significa que o sistema entrega a chave para qualquer pessoa que faça uma solicitação de chave. Essa restrição pode ser útil para fins de teste.

O exemplo a seguir cria uma política de autorização aberta e a adiciona à chave de conteúdo:

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

### <a name="token-restriction"></a>Restrição de token
Para configurar a opção de restrição de token, você precisa usar um XML para descrever os requisitos de autorização do token. O XML de configuração de restrição de token deve estar em conformidade com o esquema XML mostrado na seção "esquema de restrição de token".

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

Para obter um token de teste com base na restrição de token que foi usada para a política de autorização de chave, consulte a seção "[token de teste](#test-token)". 

## <a id="types"></a>Tipos usados quando você define ContentKeyAuthorizationPolicy
### <a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

```csharp
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }
```

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

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

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos seguintes
Agora que você configurou a política de autorização da chave de conteúdo, consulte [Configurar uma política de entrega de ativos](media-services-dotnet-configure-asset-delivery-policy.md).

