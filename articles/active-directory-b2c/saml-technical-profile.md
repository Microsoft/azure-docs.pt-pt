---
title: Defina um perfil técnico SAML numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico SAML numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3a3df4d3a955926381a664ab872e03ae987f0839
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77197977"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico SAML numa política personalizada do Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) presta apoio ao fornecedor de identidade SAML 2.0. Este artigo descreve as especificidades de um perfil técnico para interagir com um fornecedor de sinistros que suporta este protocolo padronizado. Com um perfil técnico SAML pode federar-se com um fornecedor de identidade baseado em SAML, como [a ADFS](identity-provider-adfs2016-custom.md) e [a Salesforce](identity-provider-salesforce-custom.md). Esta federação permite que os seus utilizadores inscrevam-se com as identidades sociais ou empresariais existentes.

## <a name="metadata-exchange"></a>Troca de metadados

Metadados são informações utilizadas no protocolo SAML para expor a configuração de uma parte SAML, como um prestador de serviços ou fornecedor de identidade. Os metadados definem a localização dos serviços, tais como inscrição e inscrição, certificados, método de inscrição e muito mais. O fornecedor de identidade utiliza os metadados para saber comunicar com o Azure AD B2C. Os metadados estão configurados em formato XML e podem ser assinados com uma assinatura digital para que a outra parte possa validar a integridade dos metadados. Quando o Azure AD B2C federa com um fornecedor de identidade SAML, atua como prestador de serviços, aquando de um pedido SAML e aguarda ndo uma resposta SAML. E, em alguns casos, aceita a autenticação SAML não solicitada, que também é conhecida como fornecedor de identidade iniciada.

Os metadados podem ser configurados em ambas as partes como "Metadados Estáticos" ou "Metadados Dinâmicos". Em modo estático, copia-se todos os metadados de uma parte e define-os na outra parte. No modo dinâmico, define o URL para os metadados enquanto a outra parte lê a configuração de forma dinâmica. Os princípios são os mesmos, define os metadados do perfil técnico Azure AD B2C no seu fornecedor de identidade e define os metadados do fornecedor de identidade em Azure AD B2C.

Cada fornecedor de identidade SAML tem diferentes passos para expor e definir o prestador de serviços, neste caso Azure AD B2C, e definir os metadados Azure AD AD B2C no fornecedor de identidade. Veja a documentação do seu fornecedor de identidade para obter orientações sobre como fazê-lo.

O exemplo seguinte mostra um endereço URL para os metadados SAML de um perfil técnico Azure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Substitua os seguintes valores:

- **seu nome de inquilino** com o seu nome de inquilino, como fabrikam.b2clogin.com.
- **sua política** com o seu nome de política. Utilize a política em que configura o perfil técnico do fornecedor SAML, ou uma política que herda dessa política.
- **seu perfil técnico** com o nome de perfil técnico do fornecedor de identidade SAML.

## <a name="digital-signing-certificates-exchange"></a>Troca de certificados de assinatura digital

Para construir um fundo entre o Azure AD B2C e o seu fornecedor de identidade SAML, precisa de fornecer um certificado X509 válido com a chave privada. Faça o upload do certificado com a chave privada (ficheiro.pfx) para a loja de chaves Azure AD B2C. O Azure AD B2C assina digitalmente o pedido de entrada da SAML utilizando o certificado que fornece.

O certificado é utilizado das seguintes formas:

- O Azure AD B2C gera e assina um pedido SAML, utilizando a chave privada Azure AD B2C do certificado. O pedido SAML é enviado ao fornecedor de identidade, que valida o pedido utilizando a chave pública Azure AD B2C do certificado. O certificado público Azure AD B2C é acessível através de metadados de perfil técnico. Em alternativa, pode enviar manualmente o ficheiro .cer para o seu fornecedor de identidade SAML.
- O fornecedor de identidade assina os dados enviados ao Azure AD B2C utilizando a chave privada do certificado do fornecedor de identidade. O Azure AD B2C valida os dados utilizando o certificado público do fornecedor de identidade. Cada fornecedor de identidade tem diferentes passos para configuração, veja a documentação do seu fornecedor de identidade para obter orientações sobre como fazê-lo. No Azure AD B2C, a sua política precisa de acesso à chave pública do certificado utilizando os metadados do fornecedor de identidade.

Um certificado auto-assinado é aceitável para a maioria dos cenários. Para ambientes de produção, recomenda-se a utilização de um certificado X509 emitido por uma autoridade de certificados. Além disso, como descrito mais tarde neste documento, para um ambiente de não produção pode desativar a assinatura SAML de ambos os lados.

O diagrama seguinte mostra os metadados e a troca de certificados:

![metadados e troca de certificados](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)

## <a name="digital-encryption"></a>Encriptação digital

Para encriptar a afirmação de resposta SAML, o fornecedor de identidade usa sempre uma chave pública de um certificado de encriptação num perfil técnico Azure AD B2C. Quando o Azure AD B2C precisa de desencriptar os dados, utiliza a parte privada do certificado de encriptação.

Para encriptar a afirmação de resposta SAML:

1. Faça o upload de um certificado X509 válido com a chave privada (ficheiro.pfx) para a loja de chaves Azure AD B2C.
2. Adicione um elemento **CryptographicKey** com um identificador de `SamlAssertionDecryption` à coleção **cryptographicKeys** de perfil técnico. Delineie o **StorageReferenceId** para o nome da chave de política que criou no passo 1.
3. Detete os metadados de perfil técnico **WantEncryptedAssertions** para `true`.
4. Atualize o fornecedor de identidade com os novos metadados de perfil técnico Azure AD B2C. Deverá consultar o **KeyDescriptor** com a propriedade **de utilização** definida para `encryption` contendo a chave pública do seu certificado.

O exemplo seguinte mostra a secção de encriptação de perfil técnico Azure AD B2C dos metadados:

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

O **nome** atributo do elemento protocolo tem de ser definido para `SAML2`.

## <a name="output-claims"></a>Reclamações de produção

O elemento **OutputClaims** contém uma lista de reclamações devolvidas pelo fornecedor de identidade SAML ao abrigo da secção `AttributeStatement`. Poderá ter de mapear o nome da reclamação definida na sua política para o nome definido no fornecedor de identidade. Também pode incluir reclamações que não sejam devolvidas pelo fornecedor de identidade desde que detetete o `DefaultValue` atributo.

Para ler a afirmação SAML **NamedId** in **Subject** como uma reclamação normalizada, detete tea reivindicação **O Type** de reclamação para `assertionSubjectName`. Certifique-se de que o **NameId** é o primeiro valor na afirmação XML. Quando define mais do que uma afirmação, O Azure AD B2C escolhe o valor do assunto a partir da última afirmação.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **outputClaimsTransformation** que são usados para modificar as reclamações de saída ou gerar novos.

O exemplo que se segue mostra as reclamações devolvidas pelo fornecedor de identidade do Facebook:

- A alegação **do emitenteUserId** está mapeada para a alegação **"Nome de nomede-objecto".**
- A **first_name** reclamação está mapeada para a reclamação **do nome dado.**
- A **alegação last_name** está mapeada para a alegação de **sobrenome.**
- A reivindicação **do nome** do ecrã sem mapeamento de nome.
- A reclamação **de e-mail** sem mapeamento de nome.

O perfil técnico também devolve reclamações que não são devolvidas pelo fornecedor de identidade:

- A **alegação do Fornecedor de Identidade** que contém o nome do fornecedor de identidade.
- A **autenticaçãoSource** reclama com um valor predefinido de **socialIdpAuthentication**.

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

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Entidade Parceira | Sim | URL dos metadados do fornecedor de identidade SAML. Copie os metadados do fornecedor de identidade e adicione-os dentro do elemento CDATA `<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | Não | Indica se o perfil técnico requer a assinatura de todos os pedidos de autenticação de saída. Valores possíveis: `true` ou `false`. O valor predefinido é `true`. Quando o valor estiver definido para `true`, a chave criptográfica **SamlMessageSigning** precisa de ser especificada e todos os pedidos de autenticação de saída são assinados. Se o valor for definido para `false`, os parâmetros **SigAlg** e **Signature** (fio de consulta ou parâmetro de post) são omitidos do pedido. Estes metadados também controlam os metadados **AuthnRequestsSigned** atributo, que é a saída nos metadados do perfil técnico Azure AD B2C que é partilhado com o fornecedor de identidade. O Azure AD B2C não assina o pedido se o valor dos **Pedidos de Pedidos de Solicitação** nos metadados de perfil técnico estiver definido para `false` e os metadados do fornecedor de identidade **WantAuthnRequestsSigned** estiverem definidos para `false` ou não especificados. |
| XmlSignatureAlgorithm | Não | O método que o Azure AD B2C utiliza para assinar o pedido SAML. Estes metadados controlam o valor do parâmetro **SigAlg** (cadeia de consulta ou parâmetro de post) no pedido SAML. Valores possíveis: `Sha256`, `Sha384`, `Sha512`ou `Sha1`. Certifique-se de configurar o algoritmo de assinatura em ambos os lados com o mesmo valor. Utilize apenas o algoritmo que o seu certificado suporta. |
| WantsSignedAssertions | Não | Indica se o perfil técnico requer todas as afirmações a chegar para serem assinadas. Valores possíveis: `true` ou `false`. O valor predefinido é `true`. Se o valor for fixado para `true`, todas as afirmações `saml:Assertion` enviadas pelo fornecedor de identidade para o Azure AD B2C devem ser assinadas. Se o valor for definido para `false`, o fornecedor de identidade não deve assinar as afirmações, mas mesmo que o faça, o Azure AD B2C não valida a assinatura. Este metadados também controla a bandeira de metadados **WantsAssertionsSigned,** que é saída nos metadados do perfil técnico Azure AD B2C que é partilhado com o fornecedor de identidade. Se desativar a validação de afirmações, também poderá querer desativar a validação da assinatura de resposta (para mais informações, consulte **ResponsesSigned**). |
| RespostasAssinadas | Não | Valores possíveis: `true` ou `false`. O valor predefinido é `true`. Se o valor for definido para `false`, o fornecedor de identidade não deve assinar a resposta SAML, mas mesmo que o faça, o Azure AD B2C não valida a assinatura. Se o valor for fixado para `true`, a resposta SAML enviada pelo fornecedor de identidade ao Azure AD B2C é assinada e deve ser validada. Se desativar a validação da resposta SAML, também poderá querer desativar a validação da assinatura de afirmação (para mais informações, consulte **WantsSignedAssertions).** |
| WantsEncryptedAssertions | Não | Indica se o perfil técnico requer que todas as afirmações de entrada sejam encriptadas. Valores possíveis: `true` ou `false`. O valor predefinido é `false`. Se o valor for definido para `true`, as afirmações enviadas pelo fornecedor de identidade para o Azure AD B2C devem ser assinadas e a chave **criptográfica SamlAssertionDecryption** tem de ser especificada. Se o valor for definido para `true`, os metadados do perfil técnico Azure AD B2C incluem a secção **de encriptação.** O fornecedor de identidade lê os metadados e encripta a afirmação de resposta SAML com a chave pública que é fornecida nos metadados do perfil técnico Azure AD B2C. Se ativar a encriptação das afirmações, também poderá ser necessário desativar a validação da assinatura de resposta (para mais informações, consulte **ResponsesSigned**). |
| IdpInitiatedProfileEnabled | Não | Indica se um único perfil de sessão de início de sessão está ativado que foi iniciado por um perfil de fornecedor de identidade SAML. Valores possíveis: `true` ou `false`. A predefinição é `false`. No fluxo iniciado pelo fornecedor de identidade, o utilizador é autenticado externamente e uma resposta não solicitada é enviada para o Azure AD B2C, que depois consome o símbolo, executa passos de orquestração e, em seguida, envia uma resposta à aplicação do partido que depende. |
| NameIdPolicyFormat | Não | Especifica constrangimentos no identificador de nome a utilizar para representar o sujeito solicitado. Se omitir, qualquer tipo de identificador apoiado pelo fornecedor de identidade para o sujeito solicitado pode ser utilizado. Por exemplo, `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **NameIdPolicyFormat** pode ser usado com **NameIdPolicyAllowCreate**. Veja a documentação do seu fornecedor de identidade para obter orientações sobre quais as políticas de identificação do nome. |
| NameIdPolicyAllowCreate | Não | Ao utilizar o **NameIdPolicyFormat,** também pode especificar a propriedade `AllowCreate` do **NameIDPolicy**. O valor destes metadados é `true` ou `false` para indicar se o fornecedor de identidade está autorizado a criar uma nova conta durante o fluxo de entrada. Veja a documentação do seu fornecedor de identidade para obter orientações sobre como fazê-lo. |
| AuthenticationRequestExtensions | Não | Elementos de extensão de mensagem de protocolo opcionais que são acordados entre o Azure AD BC e o fornecedor de identidade. A extensão é apresentada em formato XML. Adicione os dados XML dentro do elemento CDATA `<![CDATA[Your IDP metadata]]>`. Verifique a documentação do seu fornecedor de identidade para ver se o elemento de extensão é suportado. |
| IncludeAuthnContextClassReferences | Não | Especifica uma ou mais referências URI identificando aulas de contexto de autenticação. Por exemplo, para permitir que um utilizador assine apenas com o nome de utilizador e a palavra-passe, defino o valor para `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`. Para permitir o sessão através do nome de utilizador e da palavra-passe durante uma sessão protegida (SSL/TLS), especifique `PasswordProtectedTransport`. Veja a documentação do seu fornecedor de identidade para obter orientações sobre as URIs **AuthnContextClassRef** que são suportadas. Especifique vários URIs como uma lista delimitada de vírina. |
| Incluir KeyInfo | Não | Indica se o pedido de autenticação SAML contém a chave pública do certificado quando a ligação está definida para `HTTP-POST`. Valores possíveis: `true` ou `false`. |
| Incluir Requerer Resolução de Reclamações  | Não | Para pedidos de entrada e saída, especifica se a resolução de [sinistros](claim-resolver-overview.md) está incluída no perfil técnico. Valores possíveis: `true`, ou `false` (predefinido). Se pretender utilizar uma reclamação no perfil técnico, desempente-a para `true`. |

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento **CryptographicKeys** contém os seguintes atributos:

| Atributo |Necessário | Descrição |
| --------- | ----------- | ----------- |
| SamlMessageSigning |Sim | O certificado X509 (conjunto de teclas RSA) para utilizar para assinar mensagens SAML. O Azure AD B2C utiliza esta chave para assinar os pedidos e enviá-los para o fornecedor de identidade. |
| SamlAssertionDecryption |Sim | O certificado X509 (conjunto de teclas RSA) para utilizar para desencriptar mensagens SAML. Este certificado deve ser fornecido pelo fornecedor de identidade. O Azure AD B2C utiliza este certificado para desencriptar os dados enviados pelo fornecedor de identidade. |
| MetadadosSigning |Não | O certificado X509 (conjunto de chaves RSA) para usar para assinar metadados SAML. O Azure AD B2C utiliza esta chave para assinar os metadados.  |

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos, por exemplo, de trabalho com fornecedores de identidade SAML no Azure AD B2C:

- [Adicione a ADFS como fornecedor de identidade SAML usando políticas personalizadas](identity-provider-adfs2016-custom.md)
- [Inscreva-se utilizando contas Salesforce via SAML](identity-provider-salesforce-custom.md)
