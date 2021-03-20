---
title: Conta social reclama exemplos de transformação para políticas personalizadas
titleSuffix: Azure AD B2C
description: A conta social reclama exemplos de transformação para o esquema do Quadro de Experiência de Identidade (IEF) do Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c60cecb9d6bfeeefc0569a1a57185d13f0c6442f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94953122"
---
# <a name="social-accounts-claims-transformations"></a>Contas sociais reclamam transformações

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

No Azure Ative Directory B2C (Azure AD B2C), as identidades da conta social são armazenadas num `userIdentities` atributo de um tipo de reclamação **alternativaSecurityIdCollection.** Cada item na **alternativaSecurityIdCollection** especifica o emitente (nome do fornecedor de identidade, como facebook.com) e o `issuerUserId` , que é um identificador de utilizador único para o emitente.

```json
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

Este artigo fornece exemplos para a utilização da conta social alega transformações do esquema do Quadro de Experiência de Identidade em Azure AD B2C. Para obter mais informações, consulte [SinistrosTransformações](claimstransformations.md).

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

Cria uma representação JSON da propriedade alternativaSecurityId do utilizador que pode ser usada nas chamadas para o Azure Ative Directory. Para mais informações, consulte o esquema [AlternativeSecurityId.](/graph/api/resources/alternativesecurityid)

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | key | string | O ClaimType que especifica o identificador de utilizador único utilizado pelo fornecedor de identidade social. |
| InputClaim | identidadeProvider | string | O ClaimType que especifica o nome do fornecedor de identidade da conta social, como facebook.com. |
| OutputClaim | AlternativeSecurityId | string | O ClaimType que é produzido após a inserção da ClaimsTransformation. Contém informações sobre a identidade de um utilizador de conta social. O **emitente** é o valor da `identityProvider` reclamação. O **emitenteUSerId** é o valor da `key` reclamação no formato base64. |

Utilize esta transformação de sinistros para gerar um `alternativeSecurityId` ClaimType. É usado por todos os perfis técnicos do fornecedor de identidade social, tais `Facebook-OAUTH` como. A seguinte transformação de sinistros recebe o ID da conta social do utilizador e o nome do fornecedor de identidade. A saída deste perfil técnico é um formato de cadeia JSON que pode ser usado nos serviços de diretório AD Azure.

```xml
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

- Reclamações de entrada:
    - **chave**: 12334
    - **identidadeProvider**: Facebook.com
- Reclamações de saída:
    - **alternativeSecurityId**: { "emitente": "facebook.com", "emitenteUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Acrescenta uma `AlternativeSecurityId` `alternativeSecurityIdCollection` reivindicação.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | string | O ClaimType a ser adicionado à reclamação de saída. |
| InputClaim | coleção | alternativaSecurityIdCollection | Os ClaimTypes que são utilizados pela transformação de sinistros se disponíveis na apólice. Se for fornecida, a transformação de sinistros adiciona `item` o no final da coleção. |
| OutputClaim | coleção | alternativaSecurityIdCollection | Os ClaimTypes que são produzidos após esta ReclamaçãoTransformação foi invocado. A nova coleção que contém os itens da entrada `collection` e `item` . |

O exemplo a seguir liga uma nova identidade social a uma conta existente. Para associar uma nova identidade social:
1. Nos perfis **técnicos AAD-UserReadUsingAlternativeSecurityId** e **AAD-UserReadUsingObjectId,** faça a produção da reivindicação **alternativa deSecurityIds** do utilizador.
1. Peça ao utilizador para iniciar sedução com um dos fornecedores de identidade que não estão associados a este utilizador.
1. Utilizando a transformação de reclamações **CreateAlternativeSecurityId,** crie um novo tipo de reclamação **alternativaSecurityId** com um nome de `AlternativeSecurityId2`
1. Ligue para a **AddItemToAlternativeSecurityIdCollection** alega transformação para adicionar a reivindicação **AlternativaSecurityId2** à reivindicação **de AlternativeSecurityIds** existente.
1. Persistir a **reivindicação alternativa Dessegurança** na conta do utilizador

```xml
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

- Reclamações de entrada:
    - **ponto**: { "emitente": "facebook.com", "emitenteUserId": "MTIzNDU=" }
    - **coleção:**[ {"emitente": "live.com", "emitenteUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }
- Reclamações de saída:
    - **coleção:**[ {"emitente": "live.com", "emitenteUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, {"emitente": "facebook.com", "emitenteUserId": "MTIzNDU=" }

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

Devolução da lista de emitentes da reivindicação **alternativaSecurityIdCollection** para uma nova **alegação de stringCollection.**

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | alternativaSecurityIdCollection | alternativaSecurityIdCollection | O ClaimType a ser usado para obter a lista de fornecedores de identidade (emitente). |
| OutputClaim | identidadeProvidersCollectão | stringCollection | Os ClaimTypes que são produzidos após esta ReclamaçãoTransformação foi invocado. Lista de fornecedores de identidade associados à reivindicação alternativa de entradaSecurityIdCollection |

A seguinte transformação de reclamações lê a reivindicação **alternativa de Segurança do** utilizador e extrai a lista de nomes de fornecedores de identidade associados a essa conta. Utilize identidade de **saídaProvidersCollection** para mostrar ao utilizador a lista de fornecedores de identidade associados à conta. Ou, na página de seleção do fornecedor de identidade, filtrar a lista de fornecedores de identidade com base na alegação de identidade de **saídaProvidersCollection.** Assim, o utilizador pode selecionar para ligar uma nova identidade social que ainda não está associada à conta.

```xml
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- Reclamações de entrada:
    - **alternativaSecurityIdCollection:**[ { "emitente": "google.com", "emitenteUserId": "MTA4MTQ2MDgyOTI3MDYzMjcw" }, { "emitente": "facebook.com", "emitenteUserId": "MTIzNDU=" }
- Reclamações de saída:
    - **identidadeProvidersCollection:**[ "facebook.com", "google.com" ]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

Remove uma **AlternativeSecurityId** de uma reivindicação **alternativaSecurityIdCollection.**

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | identidadeProvider | string | O ClaimType que contém o nome do fornecedor de identidade a ser removido da coleção. |
| InputClaim | coleção | alternativaSecurityIdCollection | Os ClaimTypes que são utilizados pela transformação de sinistros. A transformação de sinistros remove a identidadeProvider da coleção. |
| OutputClaim | coleção | alternativaSecurityIdCollection | Os ClaimTypes que são produzidos após esta ReclamaçãoTransformação foi invocado. A nova coleção, depois da identidadeProvider removida da coleção. |

O exemplo a seguir desvincula uma das identidades sociais com uma conta existente. Para desvincular uma identidade social:
1. Nos perfis **técnicos AAD-UserReadUsingAlternativeSecurityId** e **AAD-UserReadUsingObjectId,** faça a produção da reivindicação **alternativa deSecurityIds** do utilizador.
2. Peça ao utilizador para selecionar qual a conta social a remover dos fornecedores de identidade da lista que estão associados a este utilizador.
3. Chame um perfil técnico de transformação de reclamações que chama **a remoção DeSegurançaIdByIder** removeu a identidade social selecionada, utilizando o nome do fornecedor de identidade.
4. Persistir a **reivindicação alternativa Dessegurança** na conta do utilizador.

```xml
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

- Reclamações de entrada:
    - **identidadeProvider**: facebook.com
    - **coleção:**[ {"emitente": "live.com", "emitenteUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, {"emitente": "facebook.com", "emitenteUserId": "MTIzNDU=" }
- Reclamações de saída:
    - **coleção:**[ {"emitente": "live.com", "emitenteUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }