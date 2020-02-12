---
title: ContentDefinitions
titleSuffix: Azure AD B2C
description: Especifique o elemento ContentDefinitions de uma política personalizada em Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/10/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 724736bedd81ea45d7472a615fa22cde6916f21c
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77148866"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Pode personalizar o aspeto e a sensação de qualquer [perfil técnico autoafirmado.](self-asserted-technical-profile.md) Azure Active Directory B2C (Azure AD B2C) executa o código no navegador do cliente e usa uma abordagem moderna chamada CORS (compartilhamento de recursos entre origens).

Para personalizar a interface do utilizador, especifica um URL no elemento **ContentDefinition** com conteúdo HTML personalizado. No perfil técnico autoafirmado ou **OrquestrationStep,** aponta para esse identificador de definição de conteúdo. A definição de conteúdo pode conter um elemento **LocalizadoResourcesReferences** que especifica uma lista de recursos localizados para carregar. Azure AD B2C mescla os elementos da interface do usuário com o conteúdo HTML que é carregado de sua URL e, em seguida, exibe a página para o usuário.

O elemento **ContentDefinitions** contém URLs a modelos HTML5 que podem ser usados numa viagem de utilizador. O URI da página HTML5 é usado para uma etapa de interface do usuário especificada. Por exemplo, o início ou inscrição, o reset da palavra-passe ou as páginas de erro. Pode modificar a aparência e sentir substituindo o LoadUri para o ficheiro HTML5. Você pode criar novas definições de conteúdo de acordo com suas necessidades. Este elemento pode conter uma referência de recursos localizados ao identificador de localização especificado no elemento [Localização.](localization.md)

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

Os metadados do perfil técnico autoafirmado **LocalAccountSignUpWithLogonEmail** contém o conjunto de definição de conteúdo **ContentDefinitionReferenceId** para `api.localaccountsignup`

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

O elemento **Definição** de Conteúdo contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador para uma definição de conteúdo. O valor é especificado na secção ids de **definição** de conteúdo mais tarde nesta página. |

O elemento **Definição** de Conteúdo contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | Uma cadeia de caracteres que contém a URL da página HTML5 para a definição de conteúdo. |
| RecoveryUri | 0:1 | Uma cadeia de caracteres que contém a URL da página HTML para exibir um erro relacionado à definição de conteúdo. |
| DataUri | 1:1 | Uma cadeia de caracteres que contém a URL relativa de um arquivo HTML que fornece a experiência do usuário para invocar para a etapa. |
| Metadados | 0:1 | Uma coleção de pares de chave/valor que contém os metadados utilizados pela definição de conteúdo. |
| LocalizedResourcesReferences | 0:1 | Uma coleção de referências de recursos localizados. Use este elemento para personalizar a localização de uma interface do usuário e um atributo de declarações. |

### <a name="datauri"></a>DataUri

O elemento **DataUri** é utilizado para especificar o identificador da página. Azure AD B2C usa o identificador de página para carregar e iniciar elementos de interface do usuário e JavaScript do lado do cliente. O formato do valor é `urn:com:microsoft:aad:b2c:elements:page-name:version`. A tabela seguinte lista os identificadores da página que pode utilizar.

| Identificador de página | Descrição |
| ----- | ----------- |
| `globalexception` | Exibe uma página de erro quando uma exceção ou um erro é encontrado. |
| `providerselection` | Lista os provedores de identidade que os usuários podem escolher durante a entrada. |
| `unifiedssp` | Exibe um formulário para entrar com uma conta local com base em um endereço de email ou um nome de usuário. Esse valor também fornece a "funcionalidade de conexão" manter-me "e" esqueceu sua senha? " criar. |
| `unifiedssp` | Exibe um formulário para entrar com uma conta local com base em um endereço de email ou um nome de usuário. |
| `multifactor` | Verifica os números de telefone usando o texto ou a voz durante a inscrição ou entrada. |
| `selfasserted` | Exibe um formulário que permite aos usuários criar ou atualizar seu perfil. |

## <a name="select-a-page-layout"></a>Selecione um layout de página

Pode ativar o [código do lado do cliente JavaScript](javascript-samples.md) inserindo `contract` entre `elements` e o tipo de página. Por exemplo, `urn:com:microsoft:aad:b2c:elements:contract:page-name:version`.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

A [versão](page-layout.md) parte do `DataUri` especifica o pacote de conteúdo que contém HTML, CSS e JavaScript para os elementos da interface do utilizador na sua política. Se pretender ativar o código do lado do cliente JavaScript, os elementos em que baseia o seu JavaScript devem ser imutáveis. Se não forem imutáveis, quaisquer alterações podem causar comportamentos inesperados nas suas páginas de utilizador. Para prevenir estes problemas, aplique o uso de um layout de página e especifique uma versão de layout da página. Ao fazê-lo, garante que todas as definições de conteúdo em que baseou o seu JavaScript são imutáveis. Mesmo que não pretenda ativar o JavaScript, ainda precisa especificar a versão de layout da página para as suas páginas.

O exemplo que se segue mostra os **DataUri** da versão `selfasserted` `1.2.0`:

```xml
<ContentDefinition Id="api.localaccountpasswordreset">
<LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
<RecoveryUri>~/common/default_page_error.html</RecoveryUri>
<DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
<Metadata>
    <Item Key="DisplayName">Local account change password page</Item>
</Metadata>
</ContentDefinition>
```

#### <a name="migrating-to-page-layout"></a>Migrando para o layout da página

O formato do valor deve conter a palavra `contract`: _urn:com:microsoft:aad:b2c:elementos:**contrair**:pnome de idade:versão_. Para especificar um layout de página nas suas políticas personalizadas que usam um valor **DataUri** antigo, use a seguinte tabela para migrar para o novo formato.

| Valor do URI de dados antigo | Novo valor de URI de dados |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0` |

### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

O elemento **LocalizadoResourcesReferences** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1: n | Uma lista de referências de recursos localizadas para a definição de conteúdo. |

O elemento **LocalizadoResourcesReferences** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Idioma | Sim | Uma cadeia de caracteres que contém um idioma com suporte para a política por RFC 5646-marcas para identificar idiomas. |
| LocalizedResourcesReferenceId | Sim | O identificador do elemento **Recursos Localizados.** |

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

Para aprender a adicionar suporte de localização às definições de conteúdo, consulte [Localização](localization.md).

## <a name="content-definition-ids"></a>IDs de definição de conteúdo

O atributo ID do elemento **ContentDefinition** especifica o tipo de página que diz respeito à definição de conteúdo. O elemento define o contexto que um modelo HTML5/CSS personalizado irá aplicar. A tabela a seguir descreve o conjunto de IDs de definição de conteúdo que é reconhecido pela estrutura de experiência de identidade e os tipos de página relacionados a eles. Você pode criar suas próprias definições de conteúdo com uma ID arbitrária.

| ID | Modelo padrão | Descrição |
| -- | ---------------- | ----------- |
| **api.error** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Página de erro** - Exibe uma página de erro quando uma exceção ou um erro é encontrado. |
| **api.idpselections** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Página de seleção** do fornecedor de identidade - Lista fornecedores de identidade que os utilizadores podem escolher durante o sessão. As opções geralmente são provedores de identidade empresarial, provedores de identidade social, como Facebook e Google +, ou contas locais. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Seleção do fornecedor de identidade para inscrição** - Lista fornecedores de identidade que os utilizadores podem escolher durante o seu inscrição. As opções geralmente são provedores de identidade empresarial, provedores de identidade social, como Facebook e Google +, ou contas locais. |
| **api.localaccountpasswordreset** | [autoafirmou.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de palavra-passe esquecida** - Mostra um formulário que os utilizadores devem preencher para iniciar um reset de palavra-passe. |
| **api.localaccountsignin** | [autoafirmou.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de sessão de conta local** - Exibe um formulário para iniciar sessão com uma conta local baseada num endereço de e-mail ou num nome de utilizador. O formulário pode conter uma caixa de entrada de texto e uma caixa de entrada de senha. |
| **api.localaccountsignup** | [autoafirmou.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição** de conta local - Exibe um formulário para se inscrever numa conta local baseada num endereço de e-mail ou num nome de utilizador. O formulário pode conter vários controles de entrada, como: uma caixa de entrada de texto, uma caixa de entrada de senha, um botão de opção, caixas suspensas de seleção única e caixas de seleção de várias seleções. |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Página de autenticação de vários fatores** - Verifica os números de telefone, utilizando texto ou voz, durante o registo ou inscrição. |
| **api.autoafirmou** | [autoafirmou.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição** na conta social - Apresenta um formulário que os utilizadores devem preencher quando se inscrevem utilizando uma conta existente de um fornecedor de identidade social. Esta página é semelhante à página de inscrição de conta social anterior, com exceção dos campos de entrada de senha. |
| **api.self-asserted.profileupdate** | [updateprofile.cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Página de atualização** de perfis - Apresenta um formulário a que os utilizadores podem aceder à atualização do seu perfil. Esta página é semelhante à página de inscrição de conta social, com exceção dos campos de entrada de senha. |
| **api.signuporsignina** | [unificado.cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | Página de **inscrição ou inscrição unificada** - Trata do processo de inscrição e de inscrição do utilizador. Os usuários podem usar provedores de identidade corporativa, provedores de identidade social, como Facebook ou Google +, ou contas locais. |

## <a name="next-steps"></a>Passos seguintes

Para um exemplo de personalização da interface do utilizador utilizando definições de conteúdo, consulte:

[Personalize a interface de utilizador da sua aplicação usando uma política personalizada](custom-policy-ui-customization.md)
