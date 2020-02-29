---
title: Conta social reclama exemplos de transformação para políticas personalizadas
titleSuffix: Azure AD B2C
description: Conta Social reclama exemplos de transformação para o Quadro de Experiência de Identidade (IEF) do Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cb713651aca266ab2546ff26c3cd0175a4cbc289
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183759"
---
# <a name="social-accounts-claims-transformations"></a>Contas sociais reclamam transformações

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

No Azure Ative Directory B2C (Azure AD B2C), as identidades da conta social são armazenadas num atributo `userIdentities` de um tipo de reclamação **alternativoSecurityIdCollection.** Cada item na **alternativaSecurityIdCollection** especifica o emitente (nome do fornecedor de identidade, como facebook.com) e o `issuerUserId`, que é um identificador único do utilizador para o emitente.

```JSON
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

Este artigo fornece exemplos para a utilização da conta social alega transformações do Quadro de Experiência de Identidade em Azure AD B2C. Para mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

Cria uma representação JSON da propriedade alternativa SecurityId do utilizador que pode ser usada nas chamadas para O Diretório Ativo Azure. Para mais informações, consulte o esquema [AlternativeSecurityId.](https://docs.microsoft.com/graph/api/resources/alternativesecurityid)

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | key | string | O ClaimType que especifica o identificador único do utilizador utilizado pelo fornecedor de identidade social. |
| inputClaim | identityProvider | string | O ClaimType que especifica o nome do fornecedor de identidade da conta social, como facebook.com. |
| OutputClaim | alternativeSecurityId | string | O ClaimType que é produzido após a invocação da Transformação de Reclamações. Contém informações sobre a identidade de um utilizador de conta social. O **emitente** é o valor da reivindicação `identityProvider`. O **emitenteUserId** é o valor da reclamação `key` no formato base64. |

Utilize esta transformação de sinistros para gerar um `alternativeSecurityId` ClaimType. É usado por todos os perfis técnicos do fornecedor de identidade social, como `Facebook-OAUTH`. A seguinte transformação de sinistros recebe o ID da conta social do utilizador e o nome do fornecedor de identidade. A saída deste perfil técnico é um formato de cadeia JSON que pode ser usado em serviços de diretório Azure AD.

```XML
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Créditos de entrada:
    - **chave**: 12334
    - **identidadeFornecedor**: Facebook.com
- Alegações de saída:
    - **alternativaSecurityId**: { "emitente": "facebook.com", "emitenteUserId": "MTA4MTTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Acrescenta um `AlternativeSecurityId` a uma reivindicação `alternativeSecurityIdCollection`.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | item | string | O ClaimType a adicionar à reivindicação de saída. |
| inputClaim | coleção | alternativeSecurityIdCollection | Os Tipos de Reclamação que são utilizados pela transformação de sinistros se disponíveis na política. Se for fornecida, a transformação de sinistros adiciona o `item` no final da coleção. |
| OutputClaim | coleção | alternativeSecurityIdCollection | Os Tipos de Reclamação que são produzidos após esta Transformação de Reclamações foi invocado. A nova coleção que contém tanto os itens da entrada `collection` como `item`. |

O exemplo seguinte liga uma nova identidade social a uma conta existente. Para ligar uma nova identidade social:
1. Nos perfis técnicos **AAD-UserReadUsingAlternativeSecurityId** e **AAD-UserReadObjectId,** fora a alegação alternativa de **SegurançaIds** do utilizador.
1. Peça ao utilizador que assine sessão com um dos fornecedores de identidade que não esteja associado a este utilizador.
1. Utilizando a transformação de reivindicações **CreateAlternativeSecurityId,** crie um novo tipo de reivindicação **alternativaSecurityId** com um nome de `AlternativeSecurityId2`
1. Ligue para o **AddItemToAlternativeSecurityIdCollection** alega a transformação para adicionar a alegação **AlternativeSecurityId2** à reivindicação **alternativaSecurityIds** existente.
1. Persistir as **alternativasSecurityIds** reivindicam na conta de utilizador

```XML
<ClaimsTransformation Id="AddAnotherAlternativeSecurityId" TransformationMethod="AddItemToAlternativeSecurityIdCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId2" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Créditos de entrada:
    - **artigo**: {"emitente": "facebook.com", "emitenteUserId": "MTIzNDU=" }
    - **coleção**: [ {"emitente": "live.com", "emitenteUserId": "MTA4MTTQ2MDgyOTI3MDUyNTYzMjcw" } }
- Alegações de saída:
    - **coleção**: { {"emitente": "live.com", "emitenteUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "emitente": "facebook.com", "emitenteUserId": "MTTIzNDU=" } } }

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

Lista de emitentes da **alternativaSecurityIdCollection** reclama uma nova reclamação de **stringCollection.**

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | O ClaimType a utilizar para obter a lista de fornecedores de identidade (emitente). |
| OutputClaim | identityProvidersCollection | stringCollection | Os Tipos de Reclamação que são produzidos após esta Transformação de Reclamações foi invocado. Lista de fornecedores de identidade associados à alegação de entrada alternativaSecurityIdCollection |

A seguinte transformação de sinistros lê a reivindicação alternativa do **utilizadorSecurityIds** e extrai a lista de nomes de fornecedores de identidade associados a essa conta. Utilize a identidade de **saídaProvidersCollection** para mostrar ao utilizador a lista de fornecedores de identidade associados à conta. Ou, na página de seleção do fornecedor de identidade, filtrar a lista de fornecedores de identidade com base na alegação de identidade de **saídaProvidersCollection.** Assim, o utilizador pode selecionar para ligar uma nova identidade social que ainda não está associada à conta.

```XML
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- Créditos de entrada:
    - **alternativaSecurityIdCollection**: { "emitente": "google.com", "emitenteUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "emitente": "facebook.com", "emitenteUserId": "MTIzNDU=" } } }
- Alegações de saída:
    - **identidadeProvidersCollection**: [ "facebook.com", "google.com" ]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

Remove um **Id De Segurança Alternativa** de uma reivindicação **alternativaSecurityIdCollection.**

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | identityProvider | string | O ClaimType que contém o nome do fornecedor de identidade a ser removido da coleção. |
| inputClaim | coleção | alternativeSecurityIdCollection | Os Tipos de Reclamação que são utilizados pela transformação de sinistros. A transformação de sinistros retira a identidadeFornecedor da coleção. |
| OutputClaim | coleção | alternativeSecurityIdCollection | Os Tipos de Reclamação que são produzidos após esta Transformação de Reclamações foi invocado. A nova coleção, após a identidade Fornecedor removida da coleção. |

O exemplo que se segue desliga uma das identidades sociais com uma conta existente. Para desvincular uma identidade social:
1. Nos perfis técnicos **AAD-UserReadUsingAlternativeSecurityId** e **AAD-UserReadObjectId,** fora a alegação alternativa de **SegurançaIds** do utilizador.
2. Peça ao utilizador que selecione qual a conta social a remover dos fornecedores de identidade da lista que estão associados a este utilizador.
3. Ligue para um perfil técnico de transformação de sinistros que chama a transformação de reivindicações **removendo O RemoveAlternativeSecurityIdByIdentityProvider,** que removeu a identidade social selecionada, utilizando o nome do fornecedor de identidade.
4. Persistir as **alternativasSecurityIds** reivindicam na conta de utilizador.

```XML
<ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="secondIdentityProvider" TransformationClaimType="identityProvider" />
        <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="example"></a>Exemplo

- Créditos de entrada:
    - **fornecedor de identidade**: facebook.com
    - **coleção**: { {"emitente": "live.com", "emitenteUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "emitente": "facebook.com", "emitenteUserId": "MTTIzNDU=" } } }
- Alegações de saída:
    - **coleção**: [ {"emitente": "live.com", "emitenteUserId": "MTA4MTTQ2MDgyOTI3MDUyNTYzMjcw" } }
