---
title: Configurar uma política de autorização da chave de conteúdo com o SDK .NET dos Media Services | Documentos da Microsoft
description: Saiba como configurar uma política de autorização para uma chave de conteúdo utilizando o SDK .NET dos Media Services.
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
ms.date: 02/10/2019
ms.author: juliako;mingfeiy
ms.openlocfilehash: d949bf02e9b629b9c525f488f9692ad09dcede4a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57846910"
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>Encriptação dinâmica: Configurar uma política de autorização da chave de conteúdo
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Descrição geral
 Pode utilizar os serviços de multimédia do Azure para entregar transmissões em fluxo MPEG-DASH, Smooth Streaming e HTTP Live Streaming (HLS) protegidas com a encriptação AES (Advanced Standard) utilizando as chaves de encriptação de 128 bits ou [gestão de direitos digitais do PlayReady (DRM) ](https://www.microsoft.com/playready/overview/). Com os Media Services, também pode entregar transmissões em fluxo DASH encriptados com Widevine DRM. Tanto o PlayReady, como o Widevine, são encriptados de acordo com a especificação de encriptação comum (ISO/IEC 23001 7 CENC).

Serviços de multimédia também fornecem um serviço de entrega de chaves/licenças partir do qual os clientes podem obter chaves AES ou licenças do PlayReady/Widevine para reproduzir o conteúdo encriptado.

Se pretender que os serviços de multimédia para encriptar um elemento, terá de associar uma chave de encriptação (CommonEncryption ou EnvelopeEncryption) com o elemento. Para obter mais informações, consulte [criar ContentKeys com .NET](media-services-dotnet-create-contentkey.md). Terá também de configurar as políticas de autorização da chave (conforme descrito neste artigo).

Quando um fluxo é solicitado por um jogador, serviços de multimédia utiliza a chave especificada para encriptar dinamicamente o seu conteúdo através de encriptação AES ou o DRM. Para desencriptar a transmissão em fluxo, o leitor solicita a chave ao serviço de entrega de chaves. Para determinar se o utilizador está autorizado a obter a chave, o serviço avalia as políticas de autorização que especificou para a chave.

Os Media Services suportam várias formas de autenticar utilizadores que efetuam pedidos de chave. A política de autorização da chave de conteúdo pode ter um ou mais restrições de autorização. As opções são aberta ou token de restrição. A política de token restrito tem de ser acompanhada por um token emitido por um serviço de tokens seguro (STS). Serviços de multimédia suportam tokens no token web simples ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) formato e JSON Web Token ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)) formato.

Serviços de multimédia não fornece o STS. Pode criar um STS personalizado ou utilizar o serviço de controle de acesso do Azure para emitir tokens. O STS deve ser configurado para criar um token assinado com as declarações de chave e o problema especificadas que especificou na configuração de restrição de token (conforme descrito neste artigo). Se o token é válido e as afirmações no token corresponderem às configuradas para a chave de conteúdo, o serviço de entrega de chave de serviços de multimédia devolve a chave de encriptação para o cliente.

Para obter mais informações, veja os artigos seguintes:

- [Autenticação de token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Integrar aplicações com base em MVC de OWIN de serviços de multimédia do Azure com o Azure Active Directory e restringir a entrega de chave de conteúdo com base em declarações do JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>São aplicáveis algumas considerações
* Quando a conta dos Serviços de Multimédia é criada, é adicionado um ponto final de transmissão em fluxo predefinido à mesma, no estado "Parado". Para iniciar o seu conteúdo de transmissão em fluxo e tirar partido do empacotamento e encriptação dinâmica, tem de ser o ponto final de transmissão em fluxo no estado "Em execução". 
* O elemento tem de conter um conjunto de MP4s de velocidade de transmissão adaptável ou ficheiros de transmissão em fluxo uniforme de velocidade de transmissão adaptável. Para obter mais informações, consulte [codificar um elemento](media-services-encode-asset.md).
* Carregar e codificar seus ativos através da opção de AssetCreationOptions.StorageEncrypted.
* Se planeia ter várias chaves de conteúdo que exigem a mesma configuração de política, recomendamos que crie uma política de autorização único e reutilizá-lo com várias chaves de conteúdo.
* O serviço de entrega de chave coloca em cache ContentKeyAuthorizationPolicy e seus objetos relacionados (opções de política e restrições) durante 15 minutos. Pode criar ContentKeyAuthorizationPolicy e especificar para utilizar uma restrição de token, testá-lo e, em seguida, atualizar a política para a restrição aberta. Este processo demora cerca de 15 minutos antes dos comutadores de política para a versão aberta da política.
* Se adicionar ou atualizar a sua política de entrega de elementos, tem de eliminar qualquer localizador existente e criar um novo.
* Atualmente, não é possível encriptar transferências progressivas.
* Um ponto final de transmissão em fluxo dos serviços de multimédia define o valor do cabeçalho "Access-Control-Allow-Origin" do CORS na resposta de simulação como o caráter universal "\*'. Este valor funciona bem com a maioria dos leitores, incluindo o leitor de multimédia do Azure, Roku e JWPlayer e outros. No entanto, alguns leitores que utilizam dashjs não funcionam porque, com o modo de credenciais definido como "include", XMLHttpRequest no respetivo dashjs não permite o caráter universal "\*" como o valor de "Access-Control-Allow-Origin". Como solução para essa limitação no dashjs, se alojar o seu cliente a partir de um único domínio, serviços de multimédia pode especificar que o domínio no cabeçalho de resposta de simulação. Para obter ajuda, abra um pedido de suporte através do portal do Azure.

## <a name="aes-128-dynamic-encryption"></a>Encriptação dinâmica AES-128
### <a name="open-restriction"></a>Restrição aberta
Restrição aberta significa que o sistema fornece a chave para qualquer pessoa que faz uma solicitação de chave. Esta restrição poderá ser útil para fins de teste.

O exemplo seguinte cria uma política de autorização de aberto e adiciona-o para a chave de conteúdo:
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
Esta secção descreve como criar uma política de autorização da chave de conteúdo e associe-a com a chave de conteúdo. A política de autorização descreve os requisitos de autorização devem ser cumpridos para determinar se o utilizador tem autorização para receber a chave. Por exemplo, o que a lista de chaves de verificação contém a chave de que o token foi assinado com?

Para configurar a opção de restrição de token, terá de usar um XML para descrever os requisitos de autorização do token. O XML de configuração de restrição de token tem de estar em conformidade com o esquema XML seguinte:
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
Quando configurar a política de token restrito, tem de especificar a chave de verificação primária, emissor e parâmetros de público-alvo. A chave de verificação primária contém a chave de que o token foi assinado com. O emissor é o STS que emite o token. O público-alvo (por vezes denominado âmbito) descreve a intenção do token ou o recurso o token de acesso a autoriza. O serviço de entrega de chave de serviços de multimédia valida que estes valores no token correspondem aos valores no modelo.

Quando utiliza o SDK de Media Services para .NET, pode usar a classe de TokenRestrictionTemplate para gerar o token de restrição.
O exemplo seguinte cria uma política de autorização com uma restrição de token. Neste exemplo, o cliente deve apresentar um token que contém uma chave de assinatura (VerificationKey), um emissor de tokens e afirmações necessárias.
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
Para obter um token de teste com base na restrição de token que foi utilizada para a política de autorização da chave, faça o seguinte:
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
Pode utilizar os serviços de multimédia para configurar os direitos e restrições que pretende que o tempo de execução de PlayReady DRM a impor quando um usuário tentar reproduzir os conteúdos protegidos. 

Quando proteger o seu conteúdo com o PlayReady, uma das coisas que precisa especificar na sua política de autorização é uma cadeia de caracteres XML que define a [modelo de licença PlayReady](media-services-playready-license-template-overview.md). No SDK de serviços de multimédia para .NET, as classes PlayReadyLicenseResponseTemplate e PlayReadyLicenseTemplate ajudá-lo a definir o modelo de licença PlayReady.

Para saber como encriptar o seu conteúdo com PlayReady e Widevine, veja [utilização PlayReady e/ou Widevine encriptação comum dinâmica](media-services-protect-with-playready-widevine.md).

### <a name="open-restriction"></a>Restrição aberta
Restrição aberta significa que o sistema fornece a chave para qualquer pessoa que faz uma solicitação de chave. Esta restrição poderá ser útil para fins de teste.

O exemplo seguinte cria uma política de autorização de aberto e adiciona-o para a chave de conteúdo:

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
Para configurar a opção de restrição de token, terá de usar um XML para descrever os requisitos de autorização do token. O XML de configuração de restrição de token tem de estar em conformidade com o esquema XML mostrado na secção "Esquema de restrição de Token".

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

Para obter um token de teste com base na restrição de token que foi utilizada para a política de autorização da chave, consulte o "[token de teste](#test-token)" secção. 

## <a id="types"></a>Tipos utilizados quando define ContentKeyAuthorizationPolicy
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

## <a name="next-steps"></a>Passos Seguintes
Agora que configurou a política de autorização da chave de conteúdo, consulte [configurar uma política de entrega de elementos](media-services-dotnet-configure-asset-delivery-policy.md).

