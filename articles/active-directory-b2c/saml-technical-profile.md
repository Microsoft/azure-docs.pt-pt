---
title: Definir um perfil técnico SAML em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico SAML em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 74666b1dc2ba4fac25aff0a56a52d048d746d465
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950940"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico SAML em uma política personalizada Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornece suporte para o provedor de identidade SAML 2,0. Este artigo descreve as especificidades de um perfil técnico para interagir com um provedor de declarações que dá suporte a esse protocolo padronizado. Com um perfil técnico SAML, você pode federar com um provedor de identidade baseado em SAML, como o [ADFS](active-directory-b2c-custom-setup-adfs2016-idp.md) e o [Salesforce](active-directory-b2c-setup-sf-app-custom.md). Essa Federação permite que os usuários entrem com suas identidades sociais ou empresariais existentes.

## <a name="metadata-exchange"></a>Troca de metadados

Metadados são informações usadas no protocolo SAML para expor a configuração de uma entidade SAML, como um provedor de serviços ou provedor de identidade. Os metadados definem o local dos serviços, como entrada e saída, certificados, método de entrada e muito mais. O provedor de identidade usa os metadados para saber como se comunicar com Azure AD B2C. Os metadados são configurados no formato XML e podem ser assinados com uma assinatura digital para que a outra parte possa validar a integridade dos metadados. Quando Azure AD B2C federa um provedor de identidade SAML, ele atua como um provedor de serviços que inicia uma solicitação SAML e aguarda uma resposta SAML. E, em alguns casos, o aceita a autenticação SAML não solicitada, que também é conhecida como provedor de identidade iniciado.

Os metadados podem ser configurados em ambas as partes como "metadados estáticos" ou "metadados dinâmicos". No modo estático, você copia todos os metadados de uma parte e os define na outra parte. No modo dinâmico, você define a URL para os metadados, enquanto a outra parte lê a configuração dinamicamente. Os princípios são os mesmos, você define os metadados do perfil técnico de Azure AD B2C em seu provedor de identidade e define os metadados do provedor de identidade em Azure AD B2C.

Cada provedor de identidade SAML tem etapas diferentes para expor e definir o provedor de serviços, neste caso Azure AD B2C e definir os metadados de Azure AD B2C no provedor de identidade. Examine a documentação do provedor de identidade para obter orientação sobre como fazer isso.

O exemplo a seguir mostra um endereço URL para os metadados SAML de um perfil técnico Azure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Substitua os seguintes valores:

- **seu-Tenant-Name** com o nome do locatário, como fabrikam.b2clogin.com.
- **sua política** com o nome da política. Use a política em que você configura o perfil técnico do provedor SAML ou uma política que herda dessa política.
- **seu perfil técnico** com seu nome de perfil técnico do provedor de identidade SAML.

## <a name="digital-signing-certificates-exchange"></a>Troca de certificados de assinatura digital

Para criar uma relação de confiança entre Azure AD B2C e seu provedor de identidade SAML, você precisa fornecer um certificado X509 válido com a chave privada. Você carrega o certificado com a chave privada (arquivo. pfx) no repositório de chaves de política de Azure AD B2C. Azure AD B2C assina digitalmente a solicitação de entrada do SAML usando o certificado que você fornece.

O certificado é usado das seguintes maneiras:

- Azure AD B2C gera e assina uma solicitação SAML, usando a chave privada Azure AD B2C do certificado. A solicitação SAML é enviada para o provedor de identidade, que valida a solicitação usando Azure AD B2C chave pública do certificado. O certificado público Azure AD B2C é acessível por meio de metadados de perfil técnico. Como alternativa, você pode carregar manualmente o arquivo. cer para seu provedor de identidade SAML.
- O provedor de identidade assina os dados enviados para Azure AD B2C usando a chave privada do provedor de identidade do certificado. Azure AD B2C valida os dados usando o certificado público do provedor de identidade. Cada provedor de identidade tem etapas diferentes para a instalação, examine a documentação do provedor de identidade para obter orientação sobre como fazer isso. No Azure AD B2C, sua política precisa acessar a chave pública do certificado usando os metadados do provedor de identidade.

Um certificado autoassinado é aceitável para a maioria dos cenários. Para ambientes de produção, é recomendável usar um certificado X509 emitido por uma autoridade de certificação. Além disso, conforme descrito posteriormente neste documento, para um ambiente que não seja de produção, você pode desabilitar a assinatura SAML em ambos os lados.

O diagrama a seguir mostra os metadados e a troca de certificados:

![metadados e troca de certificados](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)

## <a name="digital-encryption"></a>Criptografia digital

Para criptografar a declaração de resposta SAML, o provedor de identidade sempre usa uma chave pública de um certificado de criptografia em um perfil técnico de Azure AD B2C. Quando Azure AD B2C precisa descriptografar os dados, ele usa a parte privada do certificado de criptografia.

Para criptografar a declaração de resposta SAML:

1. Carregue um certificado X509 válido com a chave privada (arquivo. pfx) no repositório de chaves de política de Azure AD B2C.
2. Adicione um elemento **CryptographicKey** com um identificador de `SamlAssertionDecryption` à coleção **CryptographicKeys** do perfil técnico. Defina **StorageReferenceId** como o nome da chave de política criada na etapa 1.
3. Defina os metadados do perfil técnico **WantsEncryptedAssertions** como `true`.
4. Atualize o provedor de identidade com os novos metadados de perfil técnico de Azure AD B2C. Você deve ver o **descritor** de chaves com a propriedade **use** definida como `encryption` que contém a chave pública do seu certificado.

O exemplo a seguir mostra a seção Azure AD B2C técnica de criptografia de perfil dos metadados:

```XML
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento Protocol precisa ser definido como `SAML2`.

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações retornadas pelo provedor de identidade SAML na seção `AttributeStatement`. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido no provedor de identidade. Você também pode incluir declarações que não são retornadas pelo provedor de identidade contanto que você defina o atributo `DefaultValue`.

Para ler a asserção de SAML **nomeada** na **entidade** como uma declaração normalizada, defina o **PartnerClaimType** de declaração como `assertionSubjectName`. Verifique se **NameID** é o primeiro valor no XML de asserção. Quando você define mais de uma asserção, Azure AD B2C escolhe o valor do assunto da última asserção.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** que são usados para modificar as declarações de saída ou gerar novas.

O exemplo a seguir mostra as declarações retornadas pelo provedor de identidade do Facebook:

- A Declaração **issuerUserId** é mapeada para a Declaração **assertionSubjectName** .
- A declaração de **first_name** é mapeada para a Declaração **fornecida** .
- A declaração de **last_name** é mapeada para a declaração de **sobrenome** .
- A Declaração **DisplayName** sem mapeamento de nome.
- A declaração de **email** sem mapeamento de nome.

O perfil técnico também retorna declarações que não são retornadas pelo provedor de identidade:

- A Declaração **identityprovider** que contém o nome do provedor de identidade.
- A declaração de **authenticationname** com um valor padrão de **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email"  />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| PartnerEntity | Sim | URL dos metadados do provedor de identidade SAML. Copie os metadados do provedor de identidade e adicione-os dentro do elemento CDATA `<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | Não | Indica se o perfil técnico exige que todas as solicitações de autenticação de saída sejam assinadas. Valores possíveis: `true` ou `false`. O valor predefinido é `true`. Quando o valor é definido como `true`, a chave de criptografia **SamlMessageSigning** precisa ser especificada e todas as solicitações de autenticação de saída são assinadas. Se o valor for definido como `false`, os parâmetros de **SigAlg** e de **assinatura** (cadeia de caracteres de consulta ou parâmetro de postagem) serão omitidos da solicitação. Esses metadados também controlam o atributo **AuthnRequestsSigned** de metadados, que é a saída nos metadados do perfil técnico de Azure ad B2C que é compartilhado com o provedor de identidade. Azure AD B2C não assinará a solicitação se o valor de **WantsSignedRequests** nos metadados do perfil técnico estiver definido como `false` e os metadados do provedor de identidade **WantAuthnRequestsSigned** estiverem definidos como `false` ou não especificados. |
| XmlSignatureAlgorithm | Não | O método que Azure AD B2C usa para assinar a solicitação SAML. Esses metadados controlam o valor do parâmetro **SigAlg** (cadeia de caracteres de consulta ou parâmetro de postagem) na solicitação SAML. Valores possíveis: `Sha256`, `Sha384`, `Sha512`ou `Sha1`. Certifique-se de configurar o algoritmo de assinatura em ambos os lados com o mesmo valor. Use apenas o algoritmo ao qual seu certificado dá suporte. |
| WantsSignedAssertions | Não | Indica se o perfil técnico exige que todas as asserções de entrada sejam assinadas. Valores possíveis: `true` ou `false`. O valor predefinido é `true`. Se o valor for definido como `true`, todas as asserções da seção `saml:Assertion` enviadas pelo provedor de identidade para Azure AD B2C devem ser assinadas. Se o valor for definido como `false`, o provedor de identidade não deverá assinar as asserções, mas mesmo que ela tenha, Azure AD B2C não validará a assinatura. Esses metadados também controlam o sinalizador de metadados **WantsAssertionsSigned**, que é a saída nos metadados do perfil técnico de Azure ad B2C que é compartilhado com o provedor de identidade. Se você desabilitar a validação de asserções, você também pode querer desabilitar a validação da assinatura de resposta (para obter mais informações, consulte **ResponsesSigned**). |
| ResponsesSigned | Não | Valores possíveis: `true` ou `false`. O valor predefinido é `true`. Se o valor for definido como `false`, o provedor de identidade não deverá assinar a resposta SAML, mas mesmo que tenha, Azure AD B2C não validará a assinatura. Se o valor for definido como `true`, a resposta SAML enviada pelo provedor de identidade para Azure AD B2C será assinada e deverá ser validada. Se você desabilitar a validação de resposta SAML, também poderá querer desabilitar a validação da assinatura de asserção (para obter mais informações, consulte **WantsSignedAssertions**). |
| WantsEncryptedAssertions | Não | Indica se o perfil técnico exige que todas as asserções de entrada sejam criptografadas. Valores possíveis: `true` ou `false`. O valor predefinido é `false`. Se o valor for definido como `true`, as asserções enviadas pelo provedor de identidade para Azure AD B2C devem ser assinadas e a chave de criptografia **SamlAssertionDecryption** precisará ser especificada. Se o valor for definido como `true`, os metadados do perfil técnico Azure AD B2C incluirão a seção **criptografia** . O provedor de identidade lê os metadados e criptografa a declaração de resposta SAML com a chave pública que é fornecida nos metadados do perfil técnico de Azure AD B2C. Se você habilitar a criptografia de asserções, também poderá ser necessário desabilitar a validação da assinatura de resposta (para obter mais informações, consulte **ResponsesSigned**). |
| IdpInitiatedProfileEnabled | Não | Indica se um perfil de sessão de logon único está habilitado e iniciado por um perfil de provedor de identidade SAML. Valores possíveis: `true` ou `false`. A predefinição é `false`. No fluxo iniciado pelo provedor de identidade, o usuário é autenticado externamente e uma resposta não solicitada é enviada para Azure AD B2C, que então consome o token, executa etapas de orquestração e, em seguida, envia uma resposta para o aplicativo de terceira parte confiável. |
| NameIdPolicyFormat | Não | Especifica restrições sobre o identificador de nome a ser usado para representar o assunto solicitado. Se for omitido, qualquer tipo de identificador suportado pelo provedor de identidade para o assunto solicitado poderá ser usado. Por exemplo, `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **NameIdPolicyFormat** pode ser usado com **NameIdPolicyAllowCreate**. Examine a documentação do provedor de identidade para obter orientação sobre quais políticas de ID de nome têm suporte. |
| NameIdPolicyAllowCreate | Não | Ao usar **NameIdPolicyFormat**, você também pode especificar a propriedade `AllowCreate` de **NameIDPolicy**. O valor desses metadados é `true` ou `false` para indicar se o provedor de identidade tem permissão para criar uma nova conta durante o fluxo de entrada. Examine a documentação do provedor de identidade para obter orientação sobre como fazer isso. |
| AuthenticationRequestExtensions | Não | Elementos opcionais de extensão de mensagem de protocolo que são acordados entre o Azure AD BC e o provedor de identidade. A extensão é apresentada no formato XML. Você adiciona os dados XML dentro do elemento CDATA `<![CDATA[Your IDP metadata]]>`. Verifique a documentação do provedor de identidade para ver se o elemento Extensions tem suporte. |
| IncludeAuthnContextClassReferences | Não | Especifica uma ou mais referências de URI identificando classes de contexto de autenticação. Por exemplo, para permitir que um usuário entre somente com nome de usuário e senha, defina o valor como `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`. Para permitir a entrada por meio de nome de usuário e senha em uma sessão protegida (SSL/TLS), especifique `PasswordProtectedTransport`. Examine a documentação do provedor de identidade para obter diretrizes sobre os URIs **AuthnContextClassRef** com suporte. Especifique vários URIs como uma lista delimitada por vírgula. |
| IncludeKeyInfo | Não | Indica se a solicitação de autenticação SAML contém a chave pública do certificado quando a associação é definida como `HTTP-POST`. Valores possíveis: `true` ou `false`. |

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento **CryptographicKeys** contém os seguintes atributos:

| Atributo |Obrigatório | Descrição |
| --------- | ----------- | ----------- |
| SamlMessageSigning |Sim | O certificado X509 (conjunto de chaves RSA) a ser usado para assinar mensagens SAML. Azure AD B2C usa essa chave para assinar as solicitações e enviá-las ao provedor de identidade. |
| SamlAssertionDecryption |Sim | O certificado X509 (conjunto de chaves RSA) a ser usado para descriptografar mensagens SAML. Esse certificado deve ser fornecido pelo provedor de identidade. Azure AD B2C usa esse certificado para descriptografar os dados enviados pelo provedor de identidade. |
| MetadataSigning |Não | O certificado X509 (conjunto de chaves RSA) a ser usado para assinar metadados SAML. Azure AD B2C usa essa chave para assinar os metadados.  |

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter exemplos de como trabalhar com provedores de identidade SAML no Azure AD B2C:

- [Adicionar o ADFS como um provedor de identidade SAML usando políticas personalizadas](active-directory-b2c-custom-setup-adfs2016-idp.md)
- [Entrar usando contas do Salesforce por meio do SAML](active-directory-b2c-setup-sf-app-custom.md)
