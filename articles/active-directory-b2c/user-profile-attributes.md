---
title: Atributos de perfil do utilizador no Azure Ative Directory B2C
description: Saiba mais sobre os atributos do tipo de recursos do utilizador que são suportados pelo perfil de utilizador do diretório Azure AD B2C. Descubra sobre atributos incorporados, extensões e como atribui o mapa ao Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1e6965e15b7482935148ae7fcd2edf0f3cc722b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83738562"
---
# <a name="user-profile-attributes"></a>Atributos de perfil de utilizador

O seu perfil de utilizador de diretório B2C (Azure AD) B2C vem com um conjunto incorporado de atributos, tais como nome, apelido, cidade, código postal e número de telefone. Pode alargar o perfil do utilizador com os seus próprios dados de aplicação sem necessitar de uma loja de dados externa. A maioria dos atributos que podem ser usados com perfis de utilizador Azure AD B2C também são suportados pelo Microsoft Graph. Este artigo descreve atributos suportados do perfil de utilizador Azure AD B2C. Também nota os atributos que não são suportados pelo Microsoft Graph, bem como os atributos microsoft Graph que não devem ser usados com Azure AD B2C.

> [!IMPORTANT]
> Não deve utilizar atributos incorporados ou de extensão para armazenar dados pessoais sensíveis, tais como credenciais de conta, números de identificação do governo, dados de titulares de cartões, dados de contas financeiras, informações sobre cuidados de saúde ou informações de fundo sensíveis.

Também pode integrar-se com sistemas externos. Por exemplo, pode utilizar o Azure AD B2C para autenticação, mas delegar numa gestão externa da relação com o cliente (CRM) ou na base de dados de fidelização do cliente como fonte autoritária de dados do cliente. Para mais informações, consulte a solução [de perfil remoto.](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile)

A tabela abaixo lista os atributos [do tipo de recurso do utilizador](https://docs.microsoft.com/graph/api/resources/user) que são suportados pelo perfil de utilizador do diretório Azure AD B2C. Fornece as seguintes informações sobre cada atributo:

- Nome de atributo utilizado por Azure AD B2C (seguido pelo nome do Microsoft Graph em parênteses, se diferente)
- Tipo de dados de atributo
- Descrição do atributo
- Se o atributo estiver disponível no portal Azure
- Se o atributo puder ser utilizado num fluxo de utilizador
- Se o atributo puder ser utilizado numa política [personalizada, o perfil técnico Azure AD](active-directory-technical-profile.md) e em que secção &lt; (InputClaims, &gt; &lt; OutputClaims &gt; ou &lt; PersistedClaims) &gt;

|Nome     |Tipo     |Descrição|Portal do Azure|Fluxos do utilizador|Política personalizada|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Booleano|Se a conta de utilizador está ativada ou desativada: **verdadeira** se a conta estiver ativada, caso contrário, **é falsa**.|Sim|Não|Persistido, saída|
|ageGroup        |Cadeia|A faixa etária do utilizador. Valores possíveis: nulo, indefinido, menor, adulto, não-adufer.|Sim|Não|Persistido, saída|
|AlternativeSecurityId[(Identidades)](manage-user-accounts-graph-api.md#identities-property)|Cadeia|Uma única identidade de utilizador do fornecedor de identidade externa.|Não|Não|Entrada, Persistência, Saída|
|AlternativeSecurityIds[(Identidades)](manage-user-accounts-graph-api.md#identities-property)|coleção alternativa securityId|Uma recolha de identidades de utilizadores de fornecedores de identidade externos.|Não|Não|Persistido, saída|
|city            |Cadeia|A cidade em que o utilizador está localizado. Comprimento máximo 128.|Sim|Sim|Persistido, saída|
|consentimentoProvidedForMinor|Cadeia|Se o consentimento foi dado a um menor. Valores permitidos: nulos, concedidos, negados ou não ressaltados.|Sim|Não|Persistido, saída|
|país         |Cadeia|O país/região em que o utilizador se encontra. Exemplo: "EUA" ou "Reino Unido". Comprimento máximo 128.|Sim|Sim|Persistido, saída|
|createdDateTime|DateTime|A data em que o objeto do utilizador foi criado. Leia apenas.|Não|Não|Persistido, saída|
|criaçãoType    |Cadeia|Se a conta de utilizador foi criada como uma conta local para um inquilino Azure Ative Directory B2C, o valor é LocalAccount ou nomeCoexistence. Leia apenas.|Não|Não|Persistido, saída|
|dataSSbirth     |Date|Data de nascimento.|Não|Não|Persistido, saída|
|departamento      |Cadeia|O nome do departamento em que o utilizador trabalha. Comprimento máximo 64.|Sim|Não|Persistido, saída|
|displayName     |String|O nome de exibição do utilizador. Comprimento máximo 256.|Sim|Sim|Persistido, saída|
|facsimileTelephoneNumber<sup>1</sup>|Cadeia|O número de telefone do fax de negócios do utilizador.|Sim|Não|Persistido, saída|
|nomeDado       |Cadeia|O nome próprio (primeiro nome) do utilizador. Comprimento máximo 64.|Sim|Sim|Persistido, saída|
|jobTitle        |Cadeia|O título de trabalho do utilizador. Comprimento máximo 128.|Sim|Sim|Persistido, saída|
|imutávelId     |Cadeia|Um identificador que é normalmente utilizado para utilizadores migrado do Ative Directory no local.|Não|Não|Persistido, saída|
|legalAgeGroupClassificação|Cadeia|Classificação legal de grupo etário. Apenas de leitura e calculado com base no AgeGroup e consentProvidedForMinor propriedades. Valores permitidos: nulo, menorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult e adulto.|Sim|Não|Persistido, saída|
|legalCountry<sup>1</sup>  |Cadeia|País/Região para fins legais.|Não|Não|Persistido, saída|
|correio            |Cadeia|O endereço SMTP para o utilizador, por exemplo, bob@contoso.com ". Só para ler.|Não|Não|Persistido, saída|
|mailNickName    |Cadeia|O pseudónimo do e-mail para o utilizador. Comprimento máximo 64.|Não|Não|Persistido, saída|
|mobile (mobilePhone) |Cadeia|O número de telefone celular primário para o utilizador. Comprimento máximo 64.|Sim|Não|Persistido, saída|
|netId           |Cadeia|Identificação da rede.|Não|Não|Persistido, saída|
|objectId        |Cadeia|Um identificador globalmente único (GUID) que é o identificador único para o utilizador. Exemplo: 12345678-9abc-def0-1234-56789abcde. Leia apenas, imutável.|Só de leitura|Sim|Entrada, Persistência, Saída|
|outras Mensagens      |Coleção de cordas|Uma lista de endereços de e-mail adicionais para o utilizador. Exemplo: bob@contoso.com [" Robert@fabrikam.com "".].|Sim (E-mail alternativo)|Não|Persistido, saída|
|palavra-passe        |Cadeia|A palavra-passe para a conta local durante a criação do utilizador.|Não|Não|Persistiu|
|passwordPolícias     |Cadeia|Política da senha. É uma corda composta por diferentes nomes políticos separados por vírgula. ou seja, "DisablePasswordExpiration, DisableStrongPassword".|Não|Não|Persistido, saída|
|physicalDeliveryOfficeName (officeLocation)|Cadeia|A localização do escritório no local de negócio do utilizador. Comprimento máximo 128.|Sim|Não|Persistido, saída|
|código postal      |Cadeia|O código postal do endereço postal do utilizador. O código postal é específico do país/região do utilizador. Nos Estados Unidos da América, este atributo contém o código POSTAL. Comprimento máximo 40.|Sim|Não|Persistido, saída|
|preferiuLanguage    |Cadeia|O idioma preferido para o utilizador. Deve seguir o Código ISO 639-1. Exemplo: "en-US".|Não|Não|Persistido, saída|
|refreshTokensValidFromDateTime|DateTime|Quaisquer fichas de atualização emitidas antes deste tempo são inválidas, e as aplicações terão um erro ao usar um token de atualização inválido para adquirir um novo token de acesso. Se isso acontecer, o pedido terá de adquirir um novo token de atualização, fazendo um pedido ao ponto final autorizado. Só para ler.|Não|Não|Saída|
|signInNames[(Identidades)](manage-user-accounts-graph-api.md#identities-property) |Cadeia|O nome único de inscrição do utilizador da conta local de qualquer tipo no diretório. Utilize isto para obter um utilizador com valor de inscrição sem especificar o tipo de conta local.|Não|Não|Input|
|signInNames.userName[(Identidades)](manage-user-accounts-graph-api.md#identities-property) |Cadeia|O nome de utilizador único do utilizador da conta local no diretório. Utilize isto para criar ou obter um utilizador com um nome de utilizador específico de inscrição. Especificar isto apenas em PersistedClaims durante a operação Patch removerá outros tipos de signInNames. Se quiser adicionar um novo tipo de sinalInNamames, também precisa persistir no signInNames existente.|Não|Não|Entrada, Persistência, Saída|
|signInNames.phoneNumber[(Identidades)](manage-user-accounts-graph-api.md#identities-property) |Cadeia|O número de telefone único do utilizador da conta local no diretório. Utilize isto para criar ou obter um utilizador com um número de telefone de inscrição específico. Especificar isto apenas em PersistedClaims durante a operação Patch removerá outros tipos de signInNames. Se quiser adicionar um novo tipo de sinalInNamames, também precisa persistir no signInNames existente.|Não|Não|Entrada, Persistência, Saída|
|signInNames.emailAddress[(Identidades)](manage-user-accounts-graph-api.md#identities-property)|Cadeia|O endereço de e-mail único do utilizador da conta local no diretório. Utilize isto para criar ou obter um utilizador com um endereço de e-mail específico de inscrição. Especificar isto apenas em PersistedClaims durante a operação Patch removerá outros tipos de signInNames. Se quiser adicionar um novo tipo de sinalInNamames, também precisa persistir no signInNames existente.|Não|Não|Entrada, Persistência, Saída|
|state           |Cadeia|O estado ou província no endereço do utilizador. Comprimento máximo 128.|Sim|Sim|Persistido, saída|
|streetAddress   |Cadeia|O endereço de rua do local de negócios do utilizador. Comprimento máximo 1024.|Sim|Sim|Persistido, saída|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|Cadeia|O número de telefone secundário do utilizador, utilizado para a autenticação de vários fatores.|Sim|Não|Persistido, saída|
|strongAuthenticationEmailAddress<sup>1</sup>|Cadeia|O endereço SMTP para o utilizador. Exemplo: " bob@contoso.com " Este atributo é usado para iniciar s início com a política do nome de utilizador, para armazenar o endereço de e-mail do utilizador. O endereço de e-mail é então utilizado num fluxo de reset de palavra-passe.|Sim|Não|Persistido, saída|
|strongAuthenticationPhoneNumber<sup>1</sup>|Cadeia|O número de telefone primário do utilizador, utilizado para a autenticação de vários fatores.|Sim|Não|Persistido, saída|
|surname         |Cadeia|Sobrenome do utilizador (nome de família ou apelido). Comprimento máximo 64.|Sim|Sim|Persistido, saída|
|número de telefones (primeira entrada de businessPhones)|Cadeia|O número de telefone primário do local de negócio do utilizador.|Sim|Não|Persistido, saída|
|userPrincipalName    |Cadeia|O nome principal de utilizador (UPN). O UPN é um nome de login estilo Internet para o utilizador com base na norma de Internet RFC 822. O domínio deve estar presente na coleção de domínios verificados pelo arrendatário. Esta propriedade é necessária quando uma conta é criada. Imutável.|Não|Não|Entrada, Persistência, Saída|
|utilizaçãoLocalização   |Cadeia|Requerido para os utilizadores que serão atribuídas licenças devido à obrigação legal de verificar a disponibilidade de serviços em países/regiões. Não é nulo. Um código país/região de duas letras (norma ISO 3166). Exemplos: "US", "JP" e "GB".|Sim|Não|Persistido, saída|
|userType        |String|Um valor de cadeia que pode ser usado para classificar os tipos de utilizadores no seu diretório. O valor deve ser membro. Só para ler.|Só de leitura|Não|Persistido, saída|
|estado de utilizador (externalUserState)<sup>2</sup>|Cadeia|Apenas para a conta Azure AD B2B, indica se o convite é pendente ou aceite.|Não|Não|Persistido, saída|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|Mostra a data de tempo para a última alteração à propriedade UserState.|Não|Não|Persistido, saída|
|<sup>1 </sup> Não suportado pelo Microsoft Graph<br><sup>2 </sup> Não deve ser usado com Azure AD B2C||||||


## <a name="extension-attributes"></a>Atributos de extensão

Muitas vezes terá de criar os seus próprios atributos, como nos seguintes casos:

- Uma aplicação voltada para o cliente tem de persistir para um atributo como **o LoyaltyNumber**.
- Um fornecedor de identidade tem um identificador de utilizador único, como **o UniqueUserGUID,** que deve ser salvo.
- Uma viagem personalizada de utilizador precisa de persistir para um estado de utilizador, como **a migraçãoStatus**.

Azure AD B2C alarga o conjunto de atributos armazenados em cada conta de utilizador. Os atributos de extensão [estendem o esquema](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions) dos objetos do utilizador no diretório. Os atributos de extensão só podem ser registados num objeto de aplicação, mesmo que possam conter dados para um utilizador. O atributo de extensão é anexado à aplicação chamada b2c-extensions-app. Não modifique esta aplicação, uma vez que é utilizada pelo Azure AD B2C para armazenar dados do utilizador. Pode encontrar esta aplicação nos registos da Azure Ative Directory App.

> [!NOTE]
> - Até 100 atributos de extensão podem ser escritos em qualquer conta de utilizador.
> - Se a aplicação de aplicações de extensões b2c for eliminada, esses atributos de extensão são removidos de todos os utilizadores, juntamente com quaisquer dados que contenham.
> - Se um atributo de extensão for eliminado pela aplicação, é removido de todas as contas do utilizador e os valores são eliminados.
> - O nome subjacente do atributo de extensão é gerado no formato "Extension_" + ID de aplicação + "_" + Nome do atributo. Por exemplo, se criar um atributo de extensão LoyaltyNumber, e o ID da aplicação de extensões b2c for 831374b3-bd50-41bf-aa54-263ec9e050fc, o nome do atributo de extensão subjacente será: extension_831374b3bd5041bfaa54263ec9e050fc_LoyaltyNumber. Utiliza o nome subjacente quando executa consultas de API de gráficos para criar ou atualizar contas de utilizador.

Os seguintes tipos de dados são suportados ao definir uma propriedade numa extensão de esquema:

|Tipo de propriedade |Observações  |
|--------------|---------|
|Booleano    | Valores possíveis: **verdadeiros** ou **falsos.** |
|DateTime   | Deve ser especificado no formato ISO 8601. Será armazenado na UTC.   |
|Número inteiro    | Valor de 32 bits.               |
|Cadeia     | 256 caracteres no máximo.     |

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre os atributos de extensão:
- [Extensões de esquema](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [Defina atributos personalizados com fluxo de utilizador](user-flow-custom-attributes.md)
- [Defina atributos personalizados com política personalizada](custom-policy-custom-attributes.md)
