---
title: Defina um perfil técnico SAML numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico SAML numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4f2b4d6da8d4d69a44ca3aabd755c72fd87aaa8e
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92054904"
---
# <a name="define-a-saml-identity-provider-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico do fornecedor de identidade SAML numa política personalizada do Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) presta apoio ao fornecedor de identidade SAML 2.0. Este artigo descreve as especificidades de um perfil técnico para interagir com um fornecedor de sinistros que suporta este protocolo padronizado. Com um perfil técnico SAML pode federar com um fornecedor de identidade baseado em SAML, como [a ADFS](identity-provider-adfs2016-custom.md) e [a Salesforce.](identity-provider-salesforce-custom.md) Esta federação permite que os seus utilizadores assinem o seu contrato com as suas identidades sociais ou empresariais existentes.

## <a name="metadata-exchange"></a>Troca de metadados

Metadados é informação utilizada no protocolo SAML para expor a configuração de uma parte SAML, como um prestador de serviços ou um fornecedor de identidade. Os metadados definem a localização dos serviços, tais como a inscrição e a assinatura, certificados, método de inscrição e muito mais. O fornecedor de identidade utiliza os metadados para saber comunicar com a Azure AD B2C. Os metadados são configurados em formato XML e podem ser assinados com uma assinatura digital para que a outra parte possa validar a integridade dos metadados. Quando o Azure AD B2C federa com um fornecedor de identidade SAML, atua como prestador de serviços que inicia um pedido DEL e aguarda uma resposta DAL. E, em alguns casos, aceita a autenticação NÃO solicitada da SAML, que também é conhecida como fornecedor de identidade iniciada.

Os metadados podem ser configurados em ambas as partes como "Metadados Estáticos" ou "Metadados Dinâmicos". Em modo estático, copia-se todos os metadados de uma parte e define-os na outra parte. No modo dinâmico, define o URL para os metadados enquanto a outra parte lê a configuração dinamicamente. Os princípios são os mesmos, define os metadados do perfil técnico Azure AD B2C no seu fornecedor de identidade e define os metadados do fornecedor de identidade em Azure AD B2C.

Cada fornecedor de identidade SAML tem diferentes passos para expor e definir o prestador de serviços, neste caso Azure AD B2C, e definir os metadados Azure AD B2C no fornecedor de identidade. Veja a documentação do seu fornecedor de identidade para obter orientações sobre como fazê-lo.

O exemplo a seguir mostra um endereço URL para os metadados SAML de um perfil técnico AZure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Substitua os seguintes valores:

- **seu nome de inquilino** com o seu nome de inquilino, como fabrikam.b2clogin.com.
- **sua política** com o seu nome de política. Utilize a política em que configura o perfil técnico do fornecedor SAML, ou uma política que herda dessa política.
- **seu perfil técnico** com o nome de perfil técnico do seu fornecedor de identidade SAML.

## <a name="digital-signing-certificates-exchange"></a>Troca de certificados de assinatura digital

Para criar uma confiança entre o Azure AD B2C e o seu fornecedor de identidade SAML, precisa de fornecer um certificado X509 válido com a chave privada. Faça o upload do certificado com a chave privada (ficheiro.pfx) para a loja de chaves Azure AD B2C. A Azure AD B2C assina digitalmente o pedido de inscrição SAML utilizando o certificado que fornece.

O certificado é utilizado das seguintes formas:

- Azure AD B2C gera e assina um pedido DEL, utilizando a chave privada Azure AD B2C do certificado. O pedido DA SAML é enviado ao fornecedor de identidade, que valida o pedido utilizando a chave pública do certificado Azure AD B2C. O certificado público Azure AD B2C é acessível através de metadados de perfil técnico. Em alternativa, pode enviar manualmente o ficheiro .cer para o seu fornecedor de identidade SAML.
- O fornecedor de identidade assina os dados enviados para a Azure AD B2C utilizando a chave privada do certificado do fornecedor de identidade. O Azure AD B2C valida os dados utilizando o certificado público do fornecedor de identidade. Cada fornecedor de identidade tem diferentes passos para configurar, olhe para a documentação do seu fornecedor de identidade para obter orientações sobre como fazê-lo. No Azure AD B2C, a sua política necessita de acesso à chave pública do certificado utilizando os metadados do fornecedor de identidade.

Um certificado auto-assinado é aceitável para a maioria dos cenários. Para ambientes de produção, recomenda-se a utilização de um certificado X509 emitido por uma autoridade de certificados. Além disso, como descrito mais tarde neste documento, para um ambiente de não produção pode desativar a assinatura SAML de ambos os lados.

O diagrama a seguir mostra os metadados e a troca de certificados:

![metadados e troca de certificados](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)

## <a name="digital-encryption"></a>Encriptação digital

Para encriptar a afirmação de resposta SAML, o fornecedor de identidade usa sempre uma chave pública de um certificado de encriptação num perfil técnico Azure AD B2C. Quando o Azure AD B2C precisa de desencriptar os dados, utiliza a parte privada do certificado de encriptação.

Para encriptar a afirmação de resposta SAML:

1. Faça o upload de um certificado X509 válido com a chave privada (ficheiro.pfx) para a loja de chaves Azure AD B2C.
2. Adicione um elemento **CryptographicKey** com um identificador da `SamlAssertionDecryption` coleção **CryptographicKeys** de perfil técnico. Desaprote o **StorageReferenceId** para o nome da chave de política que criou no passo 1.
3. Desafie os metadados de perfil técnico **Quer Configurações encriptadas** para `true` .
4. Atualize o fornecedor de identidade com os novos metadados de perfil técnico Azure AD B2C. Deverá consultar o **KeyDescriptor** com a propriedade **de utilização** definida para `encryption` conter a chave pública do seu certificado.

O exemplo a seguir mostra a secção de descritor chave dos metadados SAML utilizados para encriptação:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="protocol"></a>Protocolo

O **atributo nome** do elemento Protocolo tem de ser definido para `SAML2` .

## <a name="output-claims"></a>Reclamações de saída

O elemento **OutputClaims** contém uma lista de reclamações devolvidas pelo fornecedor de identidade SAML na `AttributeStatement` secção. Poderá ser necessário mapear o nome da reclamação definida na sua política para o nome definido no fornecedor de identidade. Também pode incluir reclamações que não são devolvidas pelo fornecedor de identidade desde que desemque o `DefaultValue` atributo.

### <a name="subject-name-output-claim"></a>Reclamação de produção de nome de sujeito

Para ler a afirmação SAML **NameId** no **Sujeito** como uma reclamação normalizada, desajuste o Parceiro de **ReclamaçãoClaimType** ao valor do `SPNameQualifier` atributo. Se o `SPNameQualifier` atributo não for apresentado, desavenda o Parceiro de ReclamaçãoClaimType ao valor do **PartnerClaimType** `NameQualifier` atributo. 


Afirmação SAML: 

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
    <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
      <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

Reclamação de saída:

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

Se ambos `SPNameQualifier` ou `NameQualifier` atributos não forem apresentados na afirmação SAML, desa um conjunto de parceiros de reclamaçãoClaimType para **PartnerClaimType** `assertionSubjectName` . Certifique-se de que o **NameId** é o primeiro valor em afirmação XML. Quando define mais do que uma afirmação, a Azure AD B2C escolhe o valor do sujeito a partir da última afirmação.

O exemplo a seguir mostra as reclamações devolvidas por um fornecedor de identidade SAML:

- A **alegação do emitenteUserId** está mapeada para a **afirmaçãoSubjectName** claim.
- A **first_name** alegação está mapeada para a **reclamação dada pelo Nome.**
- A **last_name** alegação está mapeada para a alegação do **sobrenome.**
- A **alegação do nome de exibição** está mapeada para a reclamação do **nome.**
- A reclamação **de e-mail** sem mapeamento de nome.

O perfil técnico também devolve alegações que não são devolvidas pelo fornecedor de identidade:

- A **identidadeProvider** afirma que contém o nome do fornecedor de identidade.
- A **autenticaçãoProvação com** um valor predefinido de **SocialIdpAuthentication**.

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

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos de **saídaClaimsTransformation** que são utilizados para modificar as alegações de saída ou gerar novos.

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Entidade De Parceiros | Yes | URL dos metadados do fornecedor de identidade SAML. Copie os metadados do fornecedor de identidade e adicione-os dentro do elemento CDATA `<![CDATA[Your IDP metadata]]>` |
| Quer Requesigned | No | Indica se o perfil técnico requer que todos os pedidos de autenticação de saída sejam assinados. Valores possíveis: `true` ou `false` . O valor predefinido é `true`. Quando o valor é `true` definido, a tecla criptográfica **SamlMessageSigning** precisa de ser especificada e todos os pedidos de autenticação de saída são assinados. Se o valor for definido para `false` , os parâmetros **SigAlg** e **Signature** (linha de consulta ou parâmetro pós)são omitidos do pedido. Estes metadados também controlam os metadados **AuthnRequestsSigned** atributo, que é a saída nos metadados do perfil técnico Azure AD B2C que é partilhado com o fornecedor de identidade. O Azure AD B2C não assina o pedido se o valor dos **QuerSignedRequests** nos metadados de perfil técnico for definido `false` e os metadados do fornecedor de identidade **WantAuthnRequestsSigned** forem definidos ou `false` não. |
| XmlSignatureAlgorithm | No | O método que a Azure AD B2C utiliza para assinar o pedido DAL. Estes metadados controlam o valor do parâmetro  **SigAlg** (cadeia de consulta ou parâmetro de pós) no pedido DEL. Valores possíveis: `Sha256` `Sha384` , , ou `Sha512` `Sha1` . Certifique-se de configurar o algoritmo de assinatura em ambos os lados com o mesmo valor. Use apenas o algoritmo que o seu certificado suporta. |
| Quer As SesignedAssertions | No | Indica se o perfil técnico requer que todas as afirmações recebidas sejam assinadas. Valores possíveis: `true` ou `false` . O valor predefinido é `true`. Se o valor for definido para `true` , todas as afirmações `saml:Assertion` enviadas pelo fornecedor de identidade para Azure AD B2C devem ser assinadas. Se o valor for definido para `false` , o fornecedor de identidade não deve assinar as afirmações, mas mesmo que o faça, a Azure AD B2C não validará a assinatura. Estes metadados também controlam a bandeira de metadados **WantAssertionsSigned**, que é a saída nos metadados do perfil técnico Azure AD B2C que é partilhado com o fornecedor de identidade. Se desativar a validação das afirmações, também poderá querer desativar a validação da assinatura de resposta (para mais informações, consulte **RespostasSS Assinadas).** |
| RespostasSSInsignitado | No | Valores possíveis: `true` ou `false` . O valor predefinido é `true`. Se o valor for definido para `false` , o fornecedor de identidade não deve assinar a resposta SAML, mas mesmo que o faça, a Azure AD B2C não validará a assinatura. Se o valor for definido para `true` , a resposta SAML enviada pelo fornecedor de identidade ao Azure AD B2C é assinada e deve ser validada. Se desativar a validação de resposta SAML, também poderá querer desativar a validação da assinatura de afirmação (para mais informações, consulte **QuerSignedAssertions).** |
| Quer AcordosEncripted | No | Indica se o perfil técnico requer que todas as afirmações recebidas sejam encriptadas. Valores possíveis: `true` ou `false` . O valor predefinido é `false`. Se o valor for definido para `true` , as afirmações enviadas pelo fornecedor de identidade para Azure AD B2C devem ser assinadas e a chave criptográfica **SamlAssertionDecryption** deve ser especificada. Se o valor for definido para `true` , os metadados do perfil técnico Azure AD B2C incluem a secção **de encriptação.** O fornecedor de identidade lê os metadados e encripta a afirmação de resposta SAML com a chave pública que é fornecida nos metadados do perfil técnico Azure AD B2C. Se ativar a encriptação das afirmações, também poderá ter de desativar a validação da assinatura de resposta (para mais informações, consulte **RespostasSS Assinadas).** |
| NameIdPolicyFormat | No | Especifica os constrangimentos no identificador de nome a utilizar para representar o sujeito solicitado. Se omitido, qualquer tipo de identificador apoiado pelo fornecedor de identidade para o sujeito solicitado pode ser utilizado. Por exemplo, `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **NameIdPolicyFormat** pode ser usado com **NameIdPolicyAllowCreate**. Veja a documentação do seu fornecedor de identidade para obter orientações sobre quais as políticas de identificação de nomes que são apoiadas. |
| NameIdPolicyAllowCreate | No | Ao utilizar **o NameIdPolicyFormat,** também pode especificar a `AllowCreate` propriedade da **NameIDPolicy**. O valor destes metadados é `true` ou indicar se o fornecedor de identidade é autorizado a criar uma nova conta durante o fluxo de `false` entrada. Veja a documentação do seu fornecedor de identidade para obter orientações sobre como fazê-lo. |
| AutenticaçãoRequestExtensions | No | Elementos de extensão de mensagem de protocolo opcional acordados entre a Azure AD BC e o fornecedor de identidade. A extensão é apresentada em formato XML. Adicione os dados XML dentro do elemento CDATA `<![CDATA[Your IDP metadata]]>` . Consulte a documentação do seu fornecedor de identidade para ver se o elemento de extensão está suportado. |
| Incluir Conferências DeAuthnContextClassReferences | No | Especifica uma ou mais referências URI identificando classes de contexto de autenticação. Por exemplo, para permitir que um utilizador faça seduca apenas com nome de utilizador e senha, descreva o valor para `urn:oasis:names:tc:SAML:2.0:ac:classes:Password` . Para permitir o acesso através do nome de utilizador e da palavra-passe através de uma sessão protegida (SSL/TLS), especifique `PasswordProtectedTransport` . Veja a documentação do seu fornecedor de identidade para obter orientações sobre os URIs **AuthnContextClassRef** que são suportados. Especifique vários URIs como uma lista delimitada por vírgula. |
| IncluirKeyInfo | No | Indica se o pedido de autenticação SAML contém a chave pública do certificado quando a ligação está definida para `HTTP-POST` . Valores possíveis: `true` ou `false` . |
| IncluirClaimResolvingInClaimsHandling  | No | Para pedidos de entradas e saídas, especifica se a [resolução de sinistros](claim-resolver-overview.md) está incluída no perfil técnico. Valores possíveis: `true` , ou `false`   (predefinição). Se pretender utilizar uma reclamação no perfil técnico, desa um pouco `true` para . |

## <a name="cryptographic-keys"></a>Chaves criptográficas

O elemento **CryptographicKeys** contém os seguintes atributos:

| Atributo |Obrigatório | Descrição |
| --------- | ----------- | ----------- |
| SamlMessageSigning |Yes | O certificado X509 (conjunto de teclas RSA) para usar para assinar mensagens SAML. A Azure AD B2C utiliza esta chave para assinar os pedidos e enviá-los para o fornecedor de identidade. |
| SamlAssertionDecryption |No | O certificado X509 (conjunto de teclas RSA). Um fornecedor de identidade SAML utiliza a parte pública do certificado para encriptar a afirmação da resposta SAML. A Azure AD B2C utiliza a parte privada do certificado para desencriptar a afirmação. |
| MetadadosSigning |No | O certificado X509 (conjunto de teclas RSA) para usar para assinar metadados SAML. Azure AD B2C utiliza esta chave para assinar os metadados.  |

## <a name="saml-entityid-customization"></a>Personalização DEID de entidade SAML

Se tiver várias aplicações SAML que dependem de diferentes valores DEID de entidades diferentes, pode sobrepor-se ao `issueruri` valor no seu ficheiro de festa de confiança. Para tal, copie o perfil técnico com o ID "Saml2AssertionIssuer" do ficheiro base e sobreponha o `issueruri` valor.

> [!TIP]
> Copie a `<ClaimsProviders>` secção da base e preserve estes elementos dentro do fornecedor de sinistros: , e `<DisplayName>Token Issuer</DisplayName>` `<TechnicalProfile Id="Saml2AssertionIssuer">` `<DisplayName>Token Issuer</DisplayName>` .
 
Exemplo:

```xml
   <ClaimsProviders>   
    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Saml2AssertionIssuer">
          <DisplayName>Token Issuer</DisplayName>
          <Metadata>
            <Item Key="IssuerUri">customURI</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpInSAML" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2" />
      <Metadata>
     …
```

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para exemplos de trabalhar com fornecedores de identidade SAML em Azure AD B2C:

- [Adicione a ADFS como fornecedor de identidade SAML usando políticas personalizadas](identity-provider-adfs2016-custom.md)
- [Inscreva-se utilizando as contas salesforce via SAML](identity-provider-salesforce-custom.md)
