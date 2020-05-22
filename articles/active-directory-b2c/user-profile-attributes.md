---
title: Atributos de perfil de utilizador no Diretório Ativo Azure B2C
description: Conheça os atributos do tipo de recurso do utilizador que são suportados pelo perfil de utilizador do diretório Azure AD B2C. Descubra sobre atributos incorporados, extensões e como atribui o mapa ao Microsoft Graph.
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
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738562"
---
# <a name="user-profile-attributes"></a>Atributos de perfil de utilizador

O seu perfil de utilizador do Diretório Ativo Azure (Azure AD) B2C vem com um conjunto incorporado de atributos, tais como nome, nome, cidade, código postal e número de telefone. Pode estender o perfil do utilizador com os dados da sua própria aplicação sem necessitar de uma loja de dados externa. A maioria dos atributos que podem ser usados com perfis de utilizador Azure AD B2C também são suportados pelo Microsoft Graph. Este artigo descreve atributos de perfil de utilizador Do Azure AD B2C suportados. Também nota os atributos que não são suportados pelo Microsoft Graph, bem como atributos do Microsoft Graph que não devem ser usados com O Azure AD B2C.

> [!IMPORTANT]
> Não deve utilizar atributos incorporados ou de extensão para armazenar dados pessoais sensíveis, tais como credenciais de conta, números de identificação do governo, dados do titular do cartão, dados de contas financeiras, informações de cuidados de saúde ou informações confidenciais de antecedentes.

Também pode integrar-se com sistemas externos. Por exemplo, pode utilizar o Azure AD B2C para autenticação, mas delegar uma gestão externa da relação com o cliente (CRM) ou uma base de dados de fidelização de clientes como fonte autoritária de dados dos clientes. Para mais informações, consulte a solução de [perfil remoto.](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile)

A tabela abaixo lista os atributos do tipo de recurso do [utilizador](https://docs.microsoft.com/graph/api/resources/user) que são suportados pelo perfil de utilizador do diretório Azure AD B2C. Dá as seguintes informações sobre cada atributo:

- Nome de atributo utilizado pelo Azure AD B2C (seguido do nome Microsoft Graph em parênteses, se diferente)
- Tipo de dados de atributo
- Descrição do atributo
- Se o atributo estiver disponível no portal Azure
- Se o atributo puder ser utilizado num fluxo de utilizador
- Se o atributo puder ser utilizado numa política personalizada [do perfil técnico Azure AD](active-directory-technical-profile.md) e em que secção ( &lt; InputClaims, &gt; &lt; &gt; OutputClaims, or &lt; PersistedClaims &gt; )

|Name     |Tipo     |Descrição|Portal do Azure|Fluxos do utilizador|Política personalizada|
|---------|---------|----------|------------|----------|-------------|
|contaEnabled  |Booleano|Se a conta de utilizador está ativada ou desativada: **verdadeira** se a conta estiver ativada, de outra forma **falsa**.|Sim|Não|Persistida, Saída|
|ageGroup        |String|A faixa etária do utilizador. Valores possíveis: nulo, indefinido, menor, adulto, não adulto.|Sim|Não|Persistida, Saída|
|alternativaSecurityId[(Identidades)](manage-user-accounts-graph-api.md#identities-property)|String|Uma única identidade de utilizador do fornecedor de identidade externa.|Não|Não|Entrada, Persistida, Saída|
|alternativaSIds de Segurança[(Identidades)](manage-user-accounts-graph-api.md#identities-property)|coleção de id de segurança alternativa|Uma coleção de identidades de utilizadores de fornecedores de identidade externos.|Não|Não|Persistida, Saída|
|city            |String|A cidade onde o utilizador está localizado. Comprimento máximo 128.|Sim|Sim|Persistida, Saída|
|consentProvidedForMinor|String|Se o consentimento foi dado para um menor. Valores permitidos: nulo, concedido, negado ou não Exigido.|Sim|Não|Persistida, Saída|
|país         |String|O país/região em que o utilizador se encontra. Exemplo: "EUA" ou "Reino Unido". Comprimento máximo 128.|Sim|Sim|Persistida, Saída|
|criadoDateTime|DateTime|A data em que o objeto de utilizador foi criado. Leia apenas.|Não|Não|Persistida, Saída|
|criaçãoTipo    |String|Se a conta de utilizador foi criada como uma conta local para um inquilino Azure Ative Directory B2C, o valor é LocalAccount ou nomeCoexistência. Leia apenas.|Não|Não|Persistida, Saída|
|dataofbirth     |Data|Data de nascimento.|Não|Não|Persistida, Saída|
|departamento      |String|O nome para o departamento em que o utilizador trabalha. Comprimento máximo 64.|Sim|Não|Persistida, Saída|
|displayName     |String|O nome do visor para o utilizador. Comprimento máximo 256.|Sim|Sim|Persistida, Saída|
|facsimileTelefoneNúmero<sup>1</sup>|String|O número de telefone do fax de negócios do utilizador.|Sim|Não|Persistida, Saída|
|nomeDado       |String|O nome dado (primeiro nome) do utilizador. Comprimento máximo 64.|Sim|Sim|Persistida, Saída|
|empregoTítulo        |String|O cargo de utilizador. Comprimento máximo 128.|Sim|Sim|Persistida, Saída|
|imutávelId     |String|Um identificador que é normalmente utilizado para utilizadores migrados do diretório ativo no local.|Não|Não|Persistida, Saída|
|legalAgeGroupClassification|String|Classificação de grupo etário legal. Apenas de leitura e calculado com base nas propriedades do AgeGroup e do consentProvidedForMinor. Valores permitidos: nulo, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, não Adulto e Adulto.|Sim|Não|Persistida, Saída|
|legalPaís<sup>1</sup>  |String|País/Região para fins legais.|Não|Não|Persistida, Saída|
|correio            |String|O endereço SMTP para o utilizador, por exemplo, bob@contoso.com ". Só de leitura.|Não|Não|Persistida, Saída|
|mailNickName    |String|O pseudónimo do correio para o utilizador. Comprimento máximo 64.|Não|Não|Persistida, Saída|
|móvel (telemóvelPhone) |String|O número de telefone celular primário para o utilizador. Comprimento máximo 64.|Sim|Não|Persistida, Saída|
|netId           |String|Identificação da rede.|Não|Não|Persistida, Saída|
|objectId        |String|Um identificador globalmente único (GUID) que é o identificador único para o utilizador. Exemplo: 12345678-9abc-def0-1234-56789abcde. Leia apenas, imutável.|Só de leitura|Sim|Entrada, Persistida, Saída|
|outros Mails      |Coleção de cordas|Uma lista de endereços de e-mail adicionais para o utilizador. Exemplo: [" bob@contoso.com ", Robert@fabrikam.com "].|Sim (e-mail alternativo)|Não|Persistida, Saída|
|palavra-passe        |String|A palavra-passe para a conta local durante a criação do utilizador.|Não|Não|Persistido|
|passwordPolíticas     |String|Política da senha. É uma corda composta por diferentes nomes políticos separados pela vírem. ou é. "Desativar a validade da palavra-passe, desativar a palavra-passe".|Não|Não|Persistida, Saída|
|physicalDeliveryOfficeName (officeLocation)|String|A localização do escritório no local de negóciodo do utilizador. Comprimento máximo 128.|Sim|Não|Persistida, Saída|
|Código postal      |String|O código postal para o endereço postal do utilizador. O código postal é específico do país/região do utilizador. Nos Estados Unidos da América, este atributo contém o código POSTAL. Comprimento máximo 40.|Sim|Não|Persistida, Saída|
|língua preferida    |String|O idioma preferido para o utilizador. Deve seguir o código ISO 639-1. Exemplo: "en-US".|Não|Não|Persistida, Saída|
|refreshTokensValidFromDateTime|DateTime|Quaisquer fichas de atualização emitidas antes deste tempo são inválidas, e as aplicações terão um erro ao utilizar um token de atualização inválido para adquirir um novo sinal de acesso. Se isso acontecer, o pedido terá de adquirir um novo token de atualização, fazendo um pedido ao ponto final de autorização. Só de leitura.|Não|Não|Saída|
|signInNames[(Identidades)](manage-user-accounts-graph-api.md#identities-property) |String|O nome único de inscrição do utilizador da conta local de qualquer tipo no diretório. Utilize isto para obter um utilizador com valor de início de sessão sem especificar o tipo de conta local.|Não|Não|Input|
|signNames.userName[(Identidades)](manage-user-accounts-graph-api.md#identities-property) |String|O nome de utilizador único do utilizador da conta local no diretório. Utilize isto para criar ou obter um utilizador com um nome de utilizador específico. Especificando isto apenas em PersistedClaims durante a operação Patch removerá outros tipos de signInNames. Se quiser adicionar um novo tipo de signNames, também precisa de persistir no signNames existente.|Não|Não|Entrada, Persistida, Saída|
|signNames.phoneNumber[(Identidades)](manage-user-accounts-graph-api.md#identities-property) |String|O número de telefone único do utilizador da conta local no diretório. Utilize isto para criar ou obter um utilizador com um número de telefone específico. Especificando isto apenas em PersistedClaims durante a operação Patch removerá outros tipos de signInNames. Se quiser adicionar um novo tipo de signNames, também precisa de persistir no signNames existente.|Não|Não|Entrada, Persistida, Saída|
|signNames.emailAddress[(Identidades)](manage-user-accounts-graph-api.md#identities-property)|String|O endereço de e-mail único do utilizador da conta local no diretório. Utilize isto para criar ou obter um utilizador com um endereço de e-mail específico. Especificando isto apenas em PersistedClaims durante a operação Patch removerá outros tipos de signInNames. Se quiser adicionar um novo tipo de signNames, também precisa de persistir no signNames existente.|Não|Não|Entrada, Persistida, Saída|
|state           |String|O estado ou província no endereço do utilizador. Comprimento máximo 128.|Sim|Sim|Persistida, Saída|
|streetAddress   |String|O endereço de rua do local de negóciodo do utilizador. Comprimento máximo 1024.|Sim|Sim|Persistida, Saída|
|strongAutenticação AlternativaTelefoneNumber<sup>1</sup>|String|O número de telefone secundário do utilizador, utilizado para a autenticação de vários fatores.|Sim|Não|Persistida, Saída|
|strongAutenticaçãoEmailAddress<sup>1</sup>|String|O endereço SMTP para o utilizador. Exemplo: bob@contoso.com " Este atributo é usado para iniciar sessão com a política de nome de utilizador, para armazenar o endereço de e-mail do utilizador. O endereço de e-mail utilizado num fluxo de redefinição de palavra-passe.|Sim|Não|Persistida, Saída|
|strongAuthenticationPhoneNumber<sup>1</sup>|String|O número de telefone primário do utilizador, utilizado para a autenticação de vários fatores.|Sim|Não|Persistida, Saída|
|surname         |String|Apelido do utilizador (nome de família ou apelido). Comprimento máximo 64.|Sim|Sim|Persistida, Saída|
|telefoneNumber (primeira entrada de businessPhones)|String|O número de telefone primário do local de trabalho do utilizador.|Sim|Não|Persistida, Saída|
|userPrincipalName    |String|O nome principal de utilizador (UPN). A UPN é um nome de login ao estilo da Internet para o utilizador com base no RFC 822 padrão da Internet. O domínio deve estar presente na coleção de domínios verificados pelo arrendatário. Este imóvel é necessário quando uma conta é criada. Imutável.|Não|Não|Entrada, Persistida, Saída|
|utilizaçãoLocalização   |String|Necessáriopara os utilizadores que serão atribuídos licenças devido à obrigação legal de verificar a disponibilidade de serviços em países/regiões. Não é nulidade. Um código de duas letras país/região (norma ISO 3166). Exemplos: "EUA", "JP" e "GB".|Sim|Não|Persistida, Saída|
|userType        |String|Um valor de cadeia que pode ser usado para classificar os tipos de utilizador no seu diretório. O valor deve ser membro. Só de leitura.|Só de leitura|Não|Persistida, Saída|
|userState (externalUserState)<sup>2</sup>|String|Apenas para a conta Azure AD B2B, indica se o convite é Pendente aceitaou ou aceito.|Não|Não|Persistida, Saída|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|Mostra a marca de tempo para a mais recente alteração na propriedade userState.|Não|Não|Persistida, Saída|
|<sup>1</sup> Não suportado pelo Microsoft Graph<br><sup>2</sup> Não deve ser utilizado com Azure AD B2C||||||


## <a name="extension-attributes"></a>Atributos de extensão

Muitas vezes terá de criar os seus próprios atributos, como nos seguintes casos:

- Uma aplicação virada para o cliente precisa de persistir para um atributo como **LoyaltyNumber**.
- Um fornecedor de identidade tem um identificador de utilizador único, como **o UserGUID único,** que deve ser guardado.
- Uma viagem personalizada de utilizador precisa de persistir para um estado de utilizador, como **a migraçãoStatus**.

O Azure AD B2C alarga o conjunto de atributos armazenados em cada conta de utilizador. Os atributos de extensão [alargam o esquema](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions) dos objetos do utilizador no diretório. Os atributos de extensão só podem ser registados num objeto de aplicação, mesmo que possam conter dados para um utilizador. O atributo de extensão está anexado à aplicação chamada b2c-extensions-app. Não modifique esta aplicação, uma vez que é utilizada pelo Azure AD B2C para armazenar dados dos utilizadores. Pode encontrar esta aplicação nos registos da App de Diretório Ativo Do Azure.

> [!NOTE]
> - Até 100 atributos de extensão podem ser escritos em qualquer conta de utilizador.
> - Se a aplicação b2c-extensions-app for eliminada, esses atributos de extensão são removidos de todos os utilizadores juntamente com quaisquer dados que contenham.
> - Se um atributo de extensão for eliminado pela aplicação, é removido de todas as contas do utilizador e os valores são eliminados.
> - O nome subjacente do atributo de extensão é gerado no formato "Extension_" + ID da aplicação + "_" + nome do atributo. Por exemplo, se criar um atributo de extensão LoyaltyNumber, e o ID de aplicação de aplicação de aplicação b2c-extensões é 831374b3-bd50-41bf-aa54-263ec9e050fc, o nome de atributo de extensão subjacente será: extension_831374b3bd5041bfaa54263ec9e050fc_LoyaltyNumber. Usa o nome subjacente quando executa consultas de API de gráfico para criar ou atualizar contas de utilizador.

Os seguintes tipos de dados são suportados ao definir uma propriedade numa extensão de esquema:

|Tipo de propriedade |Observações  |
|--------------|---------|
|Booleano    | Valores possíveis: **verdadeiros** ou **falsos.** |
|DateTime   | Deve ser especificado no formato ISO 8601. Será armazenado na UTC.   |
|Número inteiro    | Valor de 32 bits.               |
|String     | 256 caracteres no máximo.     |

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre atributos de extensão:
- [Extensões de esquema](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [Defina atributos personalizados com fluxo do utilizador](user-flow-custom-attributes.md)
- [Definir atributos personalizados com política personalizada](custom-policy-custom-attributes.md)
