---
title: Atributos de perfil do utilizador no Azure Ative Directory B2C
description: Saiba mais sobre os atributos do tipo de recursos do utilizador que são suportados pelo perfil de utilizador do diretório Azure AD B2C. Descubra sobre atributos incorporados, extensões e como atribui o mapa ao Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7dfad71d05a882e3a3941a96e12489adb5fb3234
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500534"
---
# <a name="user-profile-attributes"></a>Atributos de perfil de utilizador

O seu perfil de utilizador de diretório B2C (Azure AD) B2C vem com um conjunto incorporado de atributos, tais como nome, apelido, cidade, código postal e número de telefone. Pode alargar o perfil do utilizador com os seus próprios dados de aplicação sem necessitar de uma loja de dados externa. 

A maioria dos atributos que podem ser usados com perfis de utilizador Azure AD B2C também são suportados pelo Microsoft Graph. Este artigo descreve atributos suportados do perfil de utilizador Azure AD B2C. Também nota os atributos que não são suportados pelo Microsoft Graph, bem como os atributos microsoft Graph que não devem ser usados com Azure AD B2C.

> [!IMPORTANT]
> Não deve utilizar atributos incorporados ou de extensão para armazenar dados pessoais sensíveis, tais como credenciais de conta, números de identificação do governo, dados de titulares de cartões, dados de contas financeiras, informações sobre cuidados de saúde ou informações de fundo sensíveis.

Também pode integrar-se com sistemas externos. Por exemplo, pode utilizar o Azure AD B2C para autenticação, mas delegar numa gestão externa da relação com o cliente (CRM) ou na base de dados de fidelização do cliente como fonte autoritária de dados do cliente. Para mais informações, consulte a solução [de perfil remoto.](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile)

## <a name="azure-ad-user-resource-type"></a>Tipo de recurso de utilizador Azure AD

A tabela abaixo lista os atributos [do tipo de recurso do utilizador](/graph/api/resources/user) que são suportados pelo perfil de utilizador do diretório Azure AD B2C. Fornece as seguintes informações sobre cada atributo:

- Nome de atributo utilizado por Azure AD B2C (seguido pelo nome do Microsoft Graph em parênteses, se diferente)
- Tipo de dados de atributo
- Descrição do atributo
- Se o atributo estiver disponível no portal Azure
- Se o atributo puder ser utilizado num fluxo de utilizador
- Se o atributo puder ser utilizado numa política [personalizada, o perfil técnico Azure AD](active-directory-technical-profile.md) e em que secção &lt; (InputClaims, &gt; &lt; OutputClaims &gt; ou &lt; PersistedClaims) &gt;

|Nome     |Tipo     |Description|Portal do Azure|Fluxos do utilizador|Política personalizada|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Booleano|Se a conta de utilizador está ativada ou desativada: **verdadeira** se a conta estiver ativada, caso contrário, **é falsa**.|Yes|No|Persistido, saída|
|ageGroup        |String|A faixa etária do utilizador. Valores possíveis: nulo, indefinido, menor, adulto, não-adufer.|Yes|No|Persistido, saída|
|AlternativeSecurityId[(Identidades)](#identities-attribute)|String|Uma única identidade de utilizador do fornecedor de identidade externa.|No|No|Entrada, Persistência, Saída|
|AlternativeSecurityIds[(Identidades)](#identities-attribute)|coleção alternativa securityId|Uma recolha de identidades de utilizadores de fornecedores de identidade externos.|No|No|Persistido, saída|
|city            |String|A cidade em que o utilizador está localizado. Comprimento máximo 128.|Yes|Yes|Persistido, saída|
|consentProvidedForMinor|String|Se o consentimento foi dado a um menor. Valores permitidos: nulos, concedidos, negados ou não ressaltados.|Yes|No|Persistido, saída|
|país         |String|O país/região em que o utilizador se encontra. Exemplo: "EUA" ou "Reino Unido". Comprimento máximo 128.|Yes|Yes|Persistido, saída|
|createdDateTime|DateTime|A data em que o objeto do utilizador foi criado. Leia apenas.|No|No|Persistido, saída|
|criaçãoType    |String|Se a conta de utilizador foi criada como uma conta local para um inquilino Azure Ative Directory B2C, o valor é LocalAccount ou nomeCoexistence. Leia apenas.|No|No|Persistido, saída|
|dataSSbirth     |Data|Data de nascimento.|No|No|Persistido, saída|
|departamento      |String|O nome do departamento em que o utilizador trabalha. Comprimento máximo 64.|Yes|No|Persistido, saída|
|displayName     |String|O nome de exibição do utilizador. Comprimento máximo 256.|Yes|Yes|Persistido, saída|
|facsimileTelephoneNumber<sup>1</sup>|String|O número de telefone do fax de negócios do utilizador.|Yes|No|Persistido, saída|
|nomeDado       |String|O nome próprio (primeiro nome) do utilizador. Comprimento máximo 64.|Yes|Yes|Persistido, saída|
|jobTitle        |String|O título de trabalho do utilizador. Comprimento máximo 128.|Yes|Yes|Persistido, saída|
|imutávelId     |String|Um identificador que é normalmente utilizado para utilizadores migrados do Ative Directory no local.|No|No|Persistido, saída|
|legalAgeGroupClassification|String|Classificação legal de grupo etário. Apenas de leitura e calculado com base no AgeGroup e consentProvidedForMinor propriedades. Valores permitidos: nulo, menorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult, e adulto.|Yes|No|Persistido, saída|
|legalCountry<sup>1</sup>  |String|País/Região para fins legais.|No|No|Persistido, saída|
|correio            |String|O endereço SMTP para o utilizador, por exemplo, bob@contoso.com ". Só para ler.|No|No|Persistido, saída|
|mailNickName    |String|O pseudónimo do e-mail para o utilizador. Comprimento máximo 64.|No|No|Persistido, saída|
|mobile (mobilePhone) |String|O número de telefone celular primário para o utilizador. Comprimento máximo 64.|Yes|No|Persistido, saída|
|netId           |String|Identificação da rede.|No|No|Persistido, saída|
|objectId        |String|Um identificador globalmente único (GUID) que é o identificador único para o utilizador. Exemplo: 12345678-9abc-def0-1234-56789abcde. Leia apenas, imutável.|Só de leitura|Yes|Entrada, Persistência, Saída|
|outras Mensagens      |Coleção de cordas|Uma lista de outros endereços de e-mail para o utilizador. Exemplo: bob@contoso.com [" Robert@fabrikam.com "".].|Sim (E-mail alternativo)|No|Persistido, saída|
|palavra-passe        |String|A palavra-passe para a conta local durante a criação do utilizador.|No|No|Persistiu|
|passwordPolícias     |String|Política da senha. É uma corda composta por diferentes nomes políticos separados por vírgula. Por exemplo, "DisablePasswordExpiration, DisableStrongPassword".|No|No|Persistido, saída|
|physicalDeliveryOfficeName (officeLocation)|String|A localização do escritório no local de negócio do utilizador. Comprimento máximo 128.|Yes|No|Persistido, saída|
|postalCode      |String|O código postal do endereço postal do utilizador. O código postal é específico do país/região do utilizador. Nos Estados Unidos da América, este atributo contém o código POSTAL. Comprimento máximo 40.|Yes|No|Persistido, saída|
|preferiuLanguage    |String|O idioma preferido para o utilizador. O formato linguístico preferido baseia-se no RFC 4646. O nome é uma combinação de um código de cultura inferior ISO 639 de duas letras associado à língua, e um código de subcultura superior ISO 3166 de duas letras associado ao país ou região. Exemplo: "en-US", ou "es-ES".|No|No|Persistido, saída|
|refreshTokensValidFromDateTime (signInSessionsValidFromDateTime)|DateTime|Quaisquer fichas de atualização emitidas antes deste tempo são inválidas, e as aplicações terão um erro ao usar um token de atualização inválido para adquirir um novo token de acesso. Se isso acontecer, o pedido terá de adquirir um novo token de atualização, fazendo um pedido ao ponto final autorizado. Só para ler.|No|No|Saída|
|signInNames[(Identidades)](#identities-attribute) |String|O nome único de inscrição do utilizador da conta local de qualquer tipo no diretório. Utilize este atributo para obter um utilizador com valor de inscrição sem especificar o tipo de conta local.|No|No|Entrada|
|signInNames.userName[(Identidades)](#identities-attribute) |String|O nome de utilizador único do utilizador da conta local no diretório. Utilize este atributo para criar ou obter um utilizador com um nome de utilizador de inscrição específica. Especificar isto apenas em PersistedClaims durante a operação Patch removerá outros tipos de signInNames. Se quiser adicionar um novo tipo de sinalInNamames, também precisa persistir no signInNames existente.|No|No|Entrada, Persistência, Saída|
|signInNames.phoneNumber[(Identidades)](#identities-attribute) |String|O número de telefone único do utilizador da conta local no diretório. Utilize este atributo para criar ou obter um utilizador com um número de telefone de inscrição específico. Especificar este atributo apenas em PersistedClaims durante a operação Patch removerá outros tipos de signInNames. Se quiser adicionar um novo tipo de sinalInNamames, também precisa persistir no signInNames existente.|No|No|Entrada, Persistência, Saída|
|signInNames.emailAddress[(Identidades)](#identities-attribute)|String|O endereço de e-mail único do utilizador da conta local no diretório. Utilize isto para criar ou obter um utilizador com um endereço de e-mail específico de inscrição. Especificar este atributo apenas em PersistedClaims durante a operação Patch removerá outros tipos de signInNames. Se quiser adicionar um novo tipo de sinalInNamames, também precisa persistir no signInNames existente.|No|No|Entrada, Persistência, Saída|
|state           |String|O estado ou província no endereço do utilizador. Comprimento máximo 128.|Yes|Yes|Persistido, saída|
|streetAddress   |String|O endereço de rua do local de negócios do utilizador. Comprimento máximo 1024.|Yes|Yes|Persistido, saída|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|String|O número de telefone secundário do utilizador, utilizado para a autenticação de vários fatores.|Yes|No|Persistido, saída|
|strongAuthenticationEmailAddress<sup>1</sup>|String|O endereço SMTP para o utilizador. Exemplo: " bob@contoso.com " Este atributo é usado para iniciar s início com a política do nome de utilizador, para armazenar o endereço de e-mail do utilizador. O endereço de e-mail é então utilizado num fluxo de reset de palavra-passe.|Yes|No|Persistido, saída|
|strongAuthenticationPhoneNumber<sup>2</sup>|String|O número de telefone primário do utilizador, utilizado para a autenticação de vários fatores.|Yes|No|Persistido, saída|
|surname         |String|Sobrenome do utilizador (nome de família ou apelido). Comprimento máximo 64.|Yes|Yes|Persistido, saída|
|número de telefones (primeira entrada de businessPhones)|String|O número de telefone primário do local de negócio do utilizador.|Yes|No|Persistido, saída|
|userPrincipalName    |String|O nome principal de utilizador (UPN). O UPN é um nome de login estilo Internet para o utilizador com base na norma de Internet RFC 822. O domínio deve estar presente na coleção de domínios verificados pelo arrendatário. Esta propriedade é necessária quando uma conta é criada. Imutável.|No|No|Entrada, Persistência, Saída|
|usageLocation   |String|Requerido para os utilizadores que serão atribuídas licenças devido à obrigação legal de verificar a disponibilidade de serviços em países/regiões. Não é nulo. Um código país/região de duas letras (norma ISO 3166). Exemplos: "US", "JP" e "GB".|Yes|No|Persistido, saída|
|userType        |String|Um valor de cadeia que pode ser usado para classificar os tipos de utilizadores no seu diretório. O valor deve ser membro. Só para ler.|Só de leitura|No|Persistido, saída|
|estado de utilizador (externalUserState)<sup>3</sup>|String|Apenas para a conta Azure AD B2B, indica se o convite é pendente ou aceite.|No|No|Persistido, saída|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|Mostra a data de tempo para a última alteração à propriedade UserState.|No|No|Persistido, saída|

<sup>1 </sup> Não suportado pelo Microsoft Graph<br><sup>2 </sup> Para mais informações, consulte [o atributo de número de telefone MFA](#mfa-phone-number-attribute)<br><sup>3 </sup> Não deve ser usado com Azure AD B2C

## <a name="display-name-attribute"></a>Atributo de nome de exibição

`displayName`É o nome a apresentar na gestão do utilizador do portal Azure para o utilizador, e no token AD B2C regressa à aplicação. Esta propriedade é necessária.

## <a name="identities-attribute"></a>Atributo de identidades

Uma conta de cliente, que pode ser um consumidor, parceiro ou cidadão, pode ser associada a estes tipos de identidade:

- **Identidade local** - O nome de utilizador e a palavra-passe são armazenados localmente no diretório Azure AD B2C. Muitas vezes nos referimos a estas identidades como "contas locais".
- Identidade **federada** - Também conhecida como contas *sociais* ou *empresariais,* a identidade do utilizador é gerida por um fornecedor de identidade federado como Facebook, Microsoft, ADFS ou Salesforce.

Um utilizador com uma conta de cliente pode iniciar súmuta com múltiplas identidades. Por exemplo, nome de utilizador, e-mail, identificação de funcionários, ID do governo, e outros. Uma única conta pode ter múltiplas identidades, tanto locais como sociais, com a mesma senha. 

Na API do Gráfico da Microsoft, as identidades locais e federadas são armazenadas no atributo do `identities` utilizador, que é de tipo [objetoIdentity](/graph/api/resources/objectidentity). A `identities` coleção representa um conjunto de identidades usadas para iniciar scontabilidade de um utilizador. Esta recolha permite ao utilizador iniciar seduções na conta do utilizador com qualquer uma das suas identidades associadas. As identidades atribuídas podem conter até dez objetos [objectIdentity.](/graph/api/resources/objectidentity) Cada objeto contém as seguintes propriedades:

| Nome   | Tipo |Description|
|:---------------|:--------|:----------|
|signInType|string| Especifica os tipos de inscrição do utilizador no seu diretório. Para a conta local:  `emailAddress` , , , , ou qualquer outro tipo que você `emailAddress1` `emailAddress2` `emailAddress3`  `userName` goste. A conta social deve ser definida para  `federated` .|
|issuer|string|Especifica o emitente da identidade. Para contas locais (onde **o signInType** não `federated` está), esta propriedade é o nome de domínio padrão do inquilino B2C local, por `contoso.onmicrosoft.com` exemplo. Para a identidade social (onde **o signInType**  `federated` é) o valor é o nome do emitente, por exemplo `facebook.com`|
|emitenteAssignedId|string|Especifica o identificador único atribuído ao utilizador pelo emitente. A combinação de **emitente** e **emitente AssignedId** deve ser única dentro do seu inquilino. Para a conta local, quando **o signInType** está definido para `emailAddress` `userName` ou, representa o nome de inscrição para o utilizador.<br>Quando **o signInType** estiver definido para: <ul><li>`emailAddress` (ou começa com `emailAddress` como `emailAddress1` ) **emitenteAssignedId** deve ser um endereço de e-mail válido</li><li>`userName` (ou qualquer outro valor), **emitenteAssignedId** deve ser uma parte local válida [de um endereço de e-mail](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`, **emitenteAssignedId** representa o identificador único da conta federada</li></ul>|

As **seguintes identidades** atribuem, com uma identidade de conta local com nome de inscrição, um endereço de e-mail como sindução, e com uma identidade social. 

 ```json
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

Para identidades federadas, dependendo do fornecedor de identidade, o **emitenteAssignedId** é um valor único para um dado utilizador por aplicação ou conta de desenvolvimento. Configure a política Azure AD B2C com o mesmo ID de aplicação que foi previamente atribuído pelo prestador social ou outro pedido dentro da mesma conta de desenvolvimento. 

## <a name="password-profile-property"></a>Propriedade de perfil de senha

Para uma identidade local, o atributo **passwordProfile** é necessário e contém a palavra-passe do utilizador. O `forceChangePasswordNextSignIn` atributo indica se um utilizador deve redefinir a palavra-passe no próximo início de sposição. Para lidar com um reset de palavra-passe forçada, [configurar o fluxo de reset de palavra-passe forçada](force-password-reset.md).

Para uma identidade federada (social), o atributo **passwordProfile** não é necessário.

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

## <a name="password-policy-attribute"></a>Atributo de política de palavra-passe

A política de senha Azure AD B2C (para contas locais) baseia-se na política [de força de senha forte](../active-directory/authentication/concept-sspr-policy.md) do Azure Ative Directory. As políticas de inscrição ou de início de sposição do Azure AD B2C ou de início de sposição e de redefinição de palavras-passe requerem esta forte força de senha e não expiram palavras-passe.

Nos cenários de migração do utilizador, se as contas que pretende migrar tiverem uma força de senha mais fraca do que a [forte força](../active-directory/authentication/concept-sspr-policy.md) de senha aplicada pelo Azure AD B2C, pode desativar o requisito de senha forte. Para alterar a política de senha padrão, desafine o `passwordPolicies` atributo para `DisableStrongPassword` . Por exemplo, pode modificar o pedido de criação do utilizador da seguinte forma:

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="mfa-phone-number-attribute"></a>Atributo de número de telefone MFA

Ao utilizar um telefone para autenticação multi-factor (MFA), o telemóvel é utilizado para verificar a identidade do utilizador. Para [adicionar](/graph/api/authentication-post-phonemethods) um novo número de telefone programaticamente, [atualize,](/graph/api/b2cauthenticationmethodspolicy-update) [obtenha,](/graph/api/b2cauthenticationmethodspolicy-get)ou [elimine](/graph/api/phoneauthenticationmethod-delete) o número de telefone, utilize o [método de autenticação do telefone](/graph/api/resources/phoneauthenticationmethod)MS Graph API .

Nas [políticas personalizadas](custom-policy-overview.md)Azure AD B2C, o número de telefone está disponível através do `strongAuthenticationPhoneNumber` tipo de reclamação.

## <a name="extension-attributes"></a>Atributos de extensão

Cada aplicação virada para o cliente tem requisitos únicos para que a informação seja recolhida. O seu inquilino Azure AD B2C vem com um conjunto incorporado de informações armazenadas em propriedades, tais como Nome Dado, Apelido e Código Postal. Com o Azure AD B2C, pode alargar o conjunto de propriedades armazenadas em cada conta de cliente. Para obter mais informações, consulte [Adicionar atributos do utilizador e personalizar a entrada do utilizador no Azure Ative Directory B2C](configure-user-input.md)

Os atributos de extensão [estendem o esquema](/graph/extensibility-overview#schema-extensions) dos objetos do utilizador no diretório. Os atributos de extensão só podem ser registados num objeto de aplicação, mesmo que possam conter dados para um utilizador. O atributo de extensão é anexado à aplicação denominada `b2c-extensions-app` . Não modifique esta aplicação, uma vez que é utilizada pelo Azure AD B2C para armazenar dados do utilizador. Pode encontrar esta aplicação nos registos da Azure Ative Directory App.

> [!NOTE]
> - Até 100 atributos de extensão podem ser escritos em qualquer conta de utilizador.
> - Se a aplicação de aplicações de extensões b2c for eliminada, esses atributos de extensão são removidos de todos os utilizadores, juntamente com quaisquer dados que contenham.
> - Se um atributo de extensão for eliminado pela aplicação, é removido de todas as contas do utilizador e os valores são eliminados.

Os atributos de extensão na API do Gráfico são nomeados através da `extension_ApplicationClientID_AttributeName` convenção, onde é o `ApplicationClientID` **ID da aplicação (cliente)** da `b2c-extensions-app` aplicação (encontrado nos **registos da App** Todas as  >  **Aplicações** no portal Azure). Note que o **ID de Aplicação (cliente)** tal como está representado no nome do atributo de extensão não inclui hífenes. Por exemplo:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

Os seguintes tipos de dados são suportados ao definir um atributo numa extensão de esquema:

|Tipo |Observações  |
|--------------|---------|
|Booleano    | Valores possíveis: **verdadeiros** ou **falsos.** |
|DateTime   | Deve ser especificado no formato ISO 8601. Será armazenado na UTC.   |
|Número inteiro    | Valor de 32 bits.               |
|String     | 256 caracteres no máximo.     |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os atributos de extensão:

- [Extensões de esquema](/graph/extensibility-overview#schema-extensions)
- [Definir atributos personalizados](user-flow-custom-attributes.md)
