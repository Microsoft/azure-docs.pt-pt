---
title: BuildingBlocks - Azure Active Directory B2C | Documentos da Microsoft
description: Especifique o elemento de BuildingBlocks de uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 1a7a5463d24ba15b7bd2b514b8c7bce3799f3191
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64688886"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O **BuildingBlocks** elemento é adicionado no interior da [TrustFrameworkPolicy](trustframeworkpolicy.md) elemento.

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">

  <BuildingBlocks>
    <ClaimsSchema>
      ...
    </ClaimsSchema>
    <Predicates>
    ...
    </Predicates>
    <PredicateValidations>
    ...
    </PredicateValidations>
    <ClaimsTransformations>
      ...
    </ClaimsTransformations>
    <ContentDefinitions>
      ...
    </ContentDefinitions>
    <Localization>
      ...
    </Localization>
 </BuildingBlocks>
```

O **BuildingBlocks** elemento contém os elementos seguintes que tem de ser especificados na ordem definida:

- [ClaimsSchema](claimsschema.md) -define os tipos de afirmações que podem ser referenciados como parte da política. O esquema de declarações é o lugar onde declarar seus tipos de afirmação. Um tipo de afirmação é semelhante a uma variável em várias linguagens de programação. Pode utilizar o tipo de afirmação para recolher dados de utilizador da sua aplicação, receber declarações de fornecedores de identidade social, enviar e receber dados de uma API de REST personalizada ou armazenar quaisquer dados internos utilizados pela sua política personalizada. 

- [Predicados e PredicateValidationsInput](predicates.md) -permite-lhe executar um processo de validação para garantir que apenas os dados corretamente formados são introduzidos numa afirmação.
 
- [ClaimsTransformations](claimstransformations.md) -contém uma lista de transformações de afirmações que podem ser utilizadas na sua política.  Uma transformação de declarações converte uma afirmação em outro. Na transformação de declarações, que especifica um método de transformação, tal como: 
    - Alterar o caso de uma afirmação de cadeia de caracteres para um serviço especificado. Por exemplo, alterando uma cadeia de caracteres de minúscula em maiúscula.
    - Comparar duas afirmações e retornar uma afirmação com true indicando que as afirmações coincidirem, caso contrário FALSO.
    - Criar uma afirmação de cadeia de caracteres do parâmetro fornecido na política.
    - A criação de uma cadeia de caracteres aleatória usando o gerador de números aleatórios.
    - Formatação de uma afirmação, de acordo com a cadeia de formato fornecido. Essa transformação utiliza c# `String.Format` método.

- [ContentDefinitions](contentdefinitions.md) -contém URLs para os modelos de HTML5 para utilizar no seu percurso do utilizador. Numa política personalizada, uma definição de conteúdo define a página de HTML5 URI que é utilizado para um passo especificado no percurso do utilizador. Por exemplo, a reposição de palavra-passe de início de sessão ou Inscreva-se, ou páginas de erro. Pode modificar o aspeto e funcionalidade substituindo LoadUri para o ficheiro do HTML5. Ou pode criar novas definições de conteúdo, de acordo com suas necessidades. Este elemento pode conter uma referência de recursos localizados com um ID de localização.

- [Localização](localization.md) -permite-lhe dar suporte a vários idiomas. O suporte de localização nas políticas permite configurar a lista de idiomas suportados numa política e escolha um idioma padrão. Cadeias de caracteres específicas de idiomas e as coleções também são suportadas.


