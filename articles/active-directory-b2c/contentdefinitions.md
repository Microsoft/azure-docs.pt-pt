---
title: ContentDefinitions-Azure Active Directory B2C | Microsoft Docs
description: Especifique o elemento ContentDefinitions de uma política personalizada em Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1ce564767fe9664604687d8cbaced58507e6b8b3
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76119657"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Você pode personalizar a aparência de qualquer [perfil técnico autodeclarado](self-asserted-technical-profile.md). Azure Active Directory B2C (Azure AD B2C) executa o código no navegador do cliente e usa uma abordagem moderna chamada CORS (compartilhamento de recursos entre origens).

Para personalizar a interface do usuário, você especifica uma URL no elemento **ContentDefinition** com conteúdo HTML personalizado. No perfil técnico autodeclarado ou **OrchestrationStep**, você aponta para esse identificador de definição de conteúdo. A definição de conteúdo pode conter um elemento **LocalizedResourcesReferences** que especifica uma lista de recursos localizados a serem carregados. Azure AD B2C mescla os elementos da interface do usuário com o conteúdo HTML que é carregado de sua URL e, em seguida, exibe a página para o usuário.

O elemento **ContentDefinitions** contém URLs para modelos HTML5 que podem ser usados em uma jornada do usuário. O URI da página HTML5 é usado para uma etapa de interface do usuário especificada. Por exemplo, as páginas de entrada, de inscrição, de redefinição de senha ou de erro. Você pode modificar a aparência substituindo o LoadUri para o arquivo HTML5. Você pode criar novas definições de conteúdo de acordo com suas necessidades. Esse elemento pode conter uma referência de recursos localizados para o identificador de Localização especificado no elemento de [localização](localization.md) .

O exemplo a seguir mostra o identificador de definição de conteúdo e a definição de recursos localizados:

```XML
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

Os metadados do perfil técnico autodeclarado **LocalAccountSignUpWithLogonEmail** contém o identificador de definição de conteúdo **ContentDefinitionReferenceId** definido como `api.localaccountsignup`

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    ...
  </Metadata>
  ...
```


## <a name="contentdefinition"></a>ContentDefinition

O elemento **ContentDefinition** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador para uma definição de conteúdo. O valor é um especificado na seção **IDs de definição de conteúdo** mais adiante nesta página. |

O elemento **ContentDefinition** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | Uma cadeia de caracteres que contém a URL da página HTML5 para a definição de conteúdo. |
| RecoveryUri | 0:1 | Uma cadeia de caracteres que contém a URL da página HTML para exibir um erro relacionado à definição de conteúdo. |
| DataUri | 1:1 | Uma cadeia de caracteres que contém a URL relativa de um arquivo HTML que fornece a experiência do usuário para invocar para a etapa. |
| Metadados | 1:1 | Uma coleção de pares de chave/valor que contém os metadados utilizados pela definição de conteúdo. |
| LocalizedResourcesReferences | 0:1 | Uma coleção de referências de recursos localizados. Use este elemento para personalizar a localização de uma interface do usuário e um atributo de declarações. |

### <a name="datauri"></a>DataUri

O elemento **DataUri** é usado para especificar o identificador de página. Azure AD B2C usa o identificador de página para carregar e iniciar elementos de interface do usuário e JavaScript do lado do cliente. O formato do valor é `urn:com:microsoft:aad:b2c:elements:page-name:version`.  A tabela a seguir lista os identificadores de página que você pode usar.

| Valor |   Descrição |
| ----- | ----------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | Exibe uma página de erro quando uma exceção ou um erro é encontrado. |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | Lista os provedores de identidade que os usuários podem escolher durante a entrada. |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | Exibe um formulário para entrar com uma conta local com base em um endereço de email ou um nome de usuário. Esse valor também fornece a "funcionalidade de conexão" manter-me "e" esqueceu sua senha? " criar. |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | Exibe um formulário para entrar com uma conta local com base em um endereço de email ou um nome de usuário. |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | Verifica os números de telefone usando o texto ou a voz durante a inscrição ou entrada. |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | Exibe um formulário que permite aos usuários criar ou atualizar seu perfil. |


### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

O elemento **LocalizedResourcesReferences** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1: n | Uma lista de referências de recursos localizadas para a definição de conteúdo. |

O elemento **LocalizedResourcesReferences** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Linguagem | Sim | Uma cadeia de caracteres que contém um idioma com suporte para a política por RFC 5646-marcas para identificar idiomas. |
| LocalizedResourcesReferenceId | Sim | O identificador do elemento **LocalizedResources** . |

O exemplo a seguir mostra uma definição de conteúdo de inscrição ou entrada:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
</ContentDefinition>
```

O exemplo a seguir mostra uma definição de conteúdo de inscrição ou entrada com uma referência à localização para inglês, francês e espanhol:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
    <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="api.signuporsignin.rf" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
</LocalizedResourcesReferences>
</ContentDefinition>
```

Para saber como adicionar suporte à localização para suas definições de conteúdo, consulte [localização](localization.md).

## <a name="content-definition-ids"></a>IDs de definição de conteúdo

O atributo ID do elemento **ContentDefinition** especifica o tipo de página relacionado à definição de conteúdo. O elemento define o contexto que um modelo HTML5/CSS personalizado irá aplicar. A tabela a seguir descreve o conjunto de IDs de definição de conteúdo que é reconhecido pela estrutura de experiência de identidade e os tipos de página relacionados a eles. Você pode criar suas próprias definições de conteúdo com uma ID arbitrária.

| ID | Modelo padrão | Descrição |
| -- | ---------------- | ----------- |
| **api.error** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Página de erro** – exibe uma página de erro quando uma exceção ou um erro é encontrado. |
| **api.idpselections** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Página de seleção do provedor de identidade** – lista os provedores de identidade que os usuários podem escolher durante a entrada. As opções geralmente são provedores de identidade empresarial, provedores de identidade social, como Facebook e Google +, ou contas locais. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Seleção de provedor de identidade para lista de inscrição** de provedores de identidade que os usuários podem escolher durante a inscrição. As opções geralmente são provedores de identidade empresarial, provedores de identidade social, como Facebook e Google +, ou contas locais. |
| **api.localaccountpasswordreset** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página esqueceu a senha** – exibe um formulário que os usuários devem concluir para iniciar uma redefinição de senha. |
| **api.localaccountsignin** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de entrada da conta local** – exibe um formulário para entrar com uma conta local baseada em um endereço de email ou um nome de usuário. O formulário pode conter uma caixa de entrada de texto e uma caixa de entrada de senha. |
| **api.localaccountsignup** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição da conta local** – exibe um formulário para se inscrever em uma conta local baseada em um endereço de email ou um nome de usuário. O formulário pode conter vários controles de entrada, como: uma caixa de entrada de texto, uma caixa de entrada de senha, um botão de opção, caixas suspensas de seleção única e caixas de seleção de várias seleções. |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Página autenticação multifator** – verifica os números de telefone usando o texto ou a voz, durante a inscrição ou entrada. |
| **api.selfasserted** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição de conta social** – exibe um formulário que os usuários devem concluir ao se inscreverem usando uma conta existente de um provedor de identidade social. Esta página é semelhante à página de inscrição de conta social anterior, com exceção dos campos de entrada de senha. |
| **api.selfasserted.profileupdate** | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Página de atualização de perfil** – exibe um formulário que os usuários podem acessar para atualizar seu perfil. Esta página é semelhante à página de inscrição de conta social, com exceção dos campos de entrada de senha. |
| **api.signuporsignin** | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Página de inscrição ou entrada unificada** -lida com o processo de inscrição e entrada do usuário. Os usuários podem usar provedores de identidade corporativa, provedores de identidade social, como Facebook ou Google +, ou contas locais. |

