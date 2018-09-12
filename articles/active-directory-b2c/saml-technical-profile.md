---
title: Definir um perfil técnico SAML numa política personalizada no Azure Active Directory B2C | Documentos da Microsoft
description: Defina um perfil técnico SAML numa política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6dcc1abdf06df2de951bb851c8b1abe93b71a69e
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44381495"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico de SAML em políticas personalizadas do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory (Azure AD) B2C fornece suporte para o fornecedor de identidade de SAML 2.0. Este artigo descreve as especificações de um perfil técnico da interagir com um fornecedor de afirmações que suporte esse protocolo padronizado. Com o SAML perfil técnico, que pode federar com um SAML, com base em fornecedor de identidade, como o AD FS e o Salesforce, que lhe permite aos utilizadores iniciar sessão com existente social ou as identidades de empresa.

## <a name="metadata-exchange"></a>Troca de metadados

Os metadados são informações utilizadas no protocolo SAML para expor a configuração de uma parte SAML, como um fornecedor de serviços ou o fornecedor de identidade. Metadados definem a localização de serviços, como o início de sessão e fim de sessão, certificados, o método de início de sessão e muito mais. O fornecedor de identidade utiliza os metadados para saber como se comunicar com o Azure AD B2C. Os metadados é configurado no formato XML e podem ser assinado com uma assinatura digital para que a outra parte pode validar a integridade dos metadados. Quando o Azure AD B2C federa com um fornecedor de identidade, atua como um fornecedor de serviços, iniciar um pedido SAML e esperando uma resposta SAML. E, em alguns casos, excepts autenticação SAML não solicitada, o que também é conhecido como fornecedor de identidade iniciada. 

Os metadados podem ser configurados em ambas as partes como "Metadados estático" ou "Metadados dinâmico". No modo estático, copiar os metadados de inteiro de uma parte e defini-lo em outra parte. No modo dinâmico, definir o URL para os metadados enquanto a outra parte lê a configuração dinamicamente. Os princípios são os mesmos, defina os metadados do perfil técnico do Azure AD B2C no seu fornecedor de identidade e definir os metadados do fornecedor de identidade no Azure AD B2C.

Cada fornecedor de identidade SAML tem passos diferentes para expor e definir o fornecedor de serviço, no caso do Azure AD B2C e defina os metadados do Azure AD B2C no fornecedor de identidade. Examinar a documentação do fornecedor de identidade para obter orientações sobre como fazer isso.

O exemplo seguinte mostra um endereço de URL para os metadados SAML de um perfil técnico do Azure AD B2C:

```
https://login.microsoftonline.com/te/your-tenant/your-policy/samlp/metadata?idptp=your-technical-profile
```

Substitua os valores seguintes:

- **o inquilino** com o nome de inquilino, como seu tenant.onmicrosoft.com
- **sua política** com o nome da sua política. Utilize a política de onde configurar o perfil técnico do fornecedor SAML ou uma política que herda dessa política.
- **seu perfil técnico** com o seu nome de perfil técnico de fornecedor de identidade SAML

## <a name="digital-signing-certificates-exchange"></a>Exchange de certificados de assinatura digital

Para criar uma confiança entre o Azure AD B2C e o fornecedor de identidade SAML, tem de fornecer um válido X509 certificado com a chave privada. Carregue o certificado com a chave privada (ficheiro. pfx) para o armazenamento de chaves de política do Azure AD B2C. O Azure AD B2C assina digitalmente o início de sessão pedido SAML com o certificado que fornecer. 

O certificado é utilizado da seguinte maneira:

- O Azure AD B2C gera e assina um pedido SAML, com a chave privada do Azure AD B2C do certificado. O pedido SAML é enviado para o fornecedor de identidade, o que valida a solicitação usando a chave pública do Azure AD B2C do certificado. O certificado público do Azure AD B2C está acessível por meio de metadados do perfil técnico. Em alternativa, pode carregar manualmente o ficheiro. cer para o seu fornecedor de identidade SAML.
- O fornecedor de identidade assina os dados enviados para o Azure AD B2C com a chave privada do fornecedor de identidade do certificado. O Azure AD B2C valida os dados de utilização de certificado público do fornecedor de identidade. Cada fornecedor de identidade tem diferentes etapas para a configuração, examinar a documentação de seus fornecedores de identidade para obter orientações sobre como fazer isso. No Azure AD B2C, a política de precisa de aceder à chave pública do certificado utilizando os metadados do fornecedor de identidade.

Um certificado autoassinado é aceitável para a maioria dos cenários. Para ambientes de produção, é recomendado utilizar uma X509 certificado emitido por uma autoridade de certificação. Além disso, conforme descrito posteriormente neste documento, para um ambiente de não produção pode desativar o assinatura em ambos os lados SAML.

O diagrama seguinte mostra a troca de metadados e o certificado:

![exchange de metadados e certificado](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)


## <a name="digital-encryption"></a>Encriptação digital

Para encriptar a asserção de resposta SAML, o fornecedor de identidade utiliza sempre uma chave pública de um certificado de encriptação num perfil técnico do Azure AD B2C. Quando precisa do Azure AD B2C descriptografar os dados, ele usa a parte privada do certificado de encriptação.

Para encriptar a asserção de resposta SAML:

1. Carregar um válido X509 certificado com a chave privada (ficheiro. pfx) para o armazenamento de chaves de política do Azure AD B2C.
2. Adicionar uma **CryptographicKey** elemento com um identificador de `SamlAssertionDecryption` para o perfil técnico **CryptographicKeys** coleção. Definir o **StorageReferenceId** para o nome da chave de política que criou no passo 1.
3. Definir os metadados do perfil técnico **WantsEncryptedAssertions** para `true`.
4. Atualize o fornecedor de identidade com os novos metadados de perfil técnico do Azure AD B2C. Deverá ver o **KeyDescriptor** com o **utilizar** propriedade definida como `encryption` que contém a chave pública do certificado.

O exemplo seguinte mostra a seção de criptografia do perfil técnico do Azure AD B2C dos metadados:

```XML
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="identity-provider-initiated-flow"></a>Fluxo de iniciadas do fornecedor de identidade

Numa única início de sessão em sessão (pedido não solicitada) iniciada por IDP, uma resposta SAML não solicitada é enviada ao Provedor de serviços, neste caso, um perfil técnico do Azure AD B2C. Neste fluxo, o utilizador não passa pela primeira vez pelo aplicativo web, mas é direcionado para o fornecedor de identidade. Quando a solicitação é enviada, uma página de autenticação é fornecida ao usuário pelo fornecedor de identidade. O utilizador concluir o início de sessão e, em seguida, o pedido é redirecionado para o Azure AD B2C com uma resposta SAML que contenha as asserções. O Azure AD B2C lê as asserções e emite um novo token SAML e, em seguida, redireciona o utilizador para a aplicação da entidade confiadora de terceiros. Os redirecionamentos são efetuados pelos **AssertionConsumerService** do elemento **localização** propriedade.


![IDP SAML iniciado](media/saml-technical-profile/technical-profile-idp-saml-idp-initiated.png) 

Ao criar um fornecedor de identidade iniciada pelo flow, considere os seguintes requisitos de política:

- O primeiro passo de orquestração tem de ser que um único exchange que aponta para um perfil técnico SAML de afirmações.
- O perfil técnico tem de ter uma item com o nome de metadados **IdpInitiatedProfileEnabled** definido como `true`.
- A política da entidade confiadora de terceiros tem de ser um entidade confiadora de SAML.
- A política da entidade confiadora de terceiros tem de ter uma item com o nome de metadados **IdpInitiatedProfileEnabled** definido como `true`.
- A resposta não solicitada precisa ser enviada para o `/your-tenant/your-policy/samlp/sso/assertionconsumer` ponto final. Qualquer Estado de reencaminhamento incluído com a resposta é encaminhado para a entidade confiadora. Substitua os valores seguintes: **seu inquilino** com o nome do seu inquilino. **sua política** pelo seu nome de política de terceiros entidade confiadora.
    
## <a name="protocol"></a>Protocolo

O **Name** atributo do elemento de protocolo tem de ser definido como `SAML2`. 

## <a name="metadata"></a>Metadados

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| PartnerEntity | Sim | URL dos metadados do fornecedor de identidade SAML. Copie os metadados do fornecedor de identidade e adicioná-lo dentro do elemento CDATA `<![CDATA[Your IDP metadata]]>` |
| IssuerUri | Não | Controla o valor de **entityID** da **EntityDescriptor** elemento nos metadados do perfil técnico do Azure AD B2C. O **entityID** atributo é o identificador exclusivo do fornecedor de serviços, neste caso, o perfil técnico do Azure AD B2C. O valor predefinido da **entityID** é `https://login.microsoftonline.com/te/your-tenant.onmicrosoft.com/your-base-policy-name` |
| WantsSignedRequests | Não | Indica se o perfil técnico necessita que todos os pedidos de autenticação de saída sejam assinados. Valores possíveis: `true` ou `false`. O valor predefinido é `true`. Quando o valor é definido como `true`, o **SamlMessageSigning** chave criptográfica tem de ser especificado e todos os pedidos de autenticação de saída tem sessão iniciados. Se o valor é definido como `false`, o **SigAlg** e **assinatura** parâmetros (cadeia de consulta ou postar parâmetro) são omitidos do pedido. Estes metadados também controla os metadados **AuthnRequestsSigned** atributo, que é a saída nos metadados do perfil técnico do Azure AD B2C que é partilhado com o fornecedor de identidade. |
| XmlSignatureAlgorithm | Não | O método que utiliza o Azure AD B2C para assinar o pedido SAML. Estes metadados controla o valor do **SigAlg** parâmetro (cadeia de consulta ou postar parâmetro) no pedido de SAML. Valores possíveis: `Sha256`, `Sha384`, `Sha512`, ou `Sha1`. Certifique-se de que configurar o algoritmo de assinatura em ambos os lados com o mesmo valor. Utilize apenas o algoritmo que suporta o seu certificado. | 
| WantsSignedAssertions | Não | Indica se o perfil técnico necessita que todas as declarações de entrada sejam assinados. Valores possíveis: `true` ou `false`. O valor predefinido é `true`. Se o valor é definido como `true`, todos os secção de asserções `saml:Assertion` enviadas pela identidade do fornecedor para o Azure AD B2C deve ser assinado. Se o valor é definido como `false`, o fornecedor de identidade não deve assinar as asserções, mas mesmo se falhar, o Azure AD B2C não validar a assinatura. Estes metadados também controla o sinalizador de metadados **WantsAssertionsSigned**, que é de saída nos metadados do perfil técnico do Azure AD B2C que é partilhado com o fornecedor de identidade. Se desativar a validação de asserções, pode também querer desativar a validação de assinatura de resposta (para obter mais informações, consulte **ResponsesSigned**). |
| ResponsesSigned | Não | Valores possíveis: `true` ou `false`. O valor predefinido é `true`. Se o valor é definido como `false`, o fornecedor de identidade não deve assinar resposta SAML, mas mesmo se falhar, o Azure AD B2C não validar a assinatura. Se o valor é definido como `true`, a resposta SAML enviada pelo fornecedor de identidade para o Azure AD B2C está assinada e têm de ser validada. Se desativar a validação de resposta SAML, pode também querer desativar a validação de assinatura de asserção (para obter mais informações, consulte **WantsSignedAssertions**). |
| WantsEncryptedAssertions | Não | Indica se o perfil técnico necessita que todas as declarações de entrada sejam encriptados. Valores possíveis: `true` ou `false`. O valor predefinido é `false`. Se o valor é definido como `true`, tem de ser assinadas asserções enviadas pelo fornecedor de identidade para o Azure AD B2C e o **SamlAssertionDecryption** chave criptográfica tem de ser especificado. Se o valor é definido como `true`, os metadados do perfil técnico do Azure AD B2C incluem o **encriptação** secção. O fornecedor de identidade lê os metadados e encripta a asserção de resposta SAML com a chave pública fornecida nos metadados do perfil técnico do Azure AD B2C. Se ativar a encriptação de asserções, também poderá ter de desativar a validação de assinatura de resposta (para obter mais informações, consulte **ResponsesSigned**). | 
| IdpInitiatedProfileEnabled | Não |Indica se um perfil de sessão de início de sessão único está ativado que foi iniciada por um perfil de fornecedor de identidade SAML. Valores possíveis: `true` ou `false`. A predefinição é `false`. No fluxo iniciado pelo fornecedor de identidade, o utilizador é autenticado externamente e uma resposta não solicitada é enviada para o Azure AD B2C, que, em seguida, consome o token, executa etapas da orquestração e, em seguida, envia uma resposta para a aplicação da entidade confiadora de terceiros. |

## <a name="cryptographic-keys"></a>Chaves criptográficas

O **CryptographicKeys** elemento contém os seguintes atributos:

| Atributo |Necessário | Descrição |
| --------- | ----------- | ----------- |
| SamlMessageSigning |Sim | O X509 certificado (conjunto de chaves RSA) a ser usado para assinar mensagens SAML. O Azure AD B2C utiliza esta chave para assinar as solicitações e enviá-los para o fornecedor de identidade. |
| SamlAssertionDecryption |Sim | O X509 certificado (conjunto de chaves RSA) a utilizar para desencriptar mensagens SAML. Este certificado deve ser fornecido pelo fornecedor de identidade. O Azure AD B2C utiliza este certificado para desencriptar os dados enviados pelo fornecedor de identidade. |
| MetadataSigning |Não | O X509 certificado (conjunto de chaves RSA) para utilizar para iniciar os metadados SAML. O Azure AD B2C utiliza esta chave para assinar os metadados.  |

## <a name="examples"></a>Exemplos

- [Adicionar o ADFS como um fornecedor de identidade com as políticas personalizadas](active-directory-b2c-custom-setup-adfs2016-idp.md)
- [Inicie sessão com contas do Salesforce através de SAML](active-directory-b2c-setup-sf-app-custom.md)













