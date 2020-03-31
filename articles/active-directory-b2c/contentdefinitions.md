---
title: ContentDefinitions
titleSuffix: Azure AD B2C
description: Especifique o elemento ContentDefinitions de uma política personalizada no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 074a0a39090e22a29f778fc1c99060848c6bfd99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051500"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Pode personalizar o aspeto e a sensação de qualquer [perfil técnico autoafirmado.](self-asserted-technical-profile.md) O Azure Ative Directory B2C (Azure AD B2C) executa código no navegador do seu cliente e utiliza uma abordagem moderna chamada Cross-Origin Resource Sharing (CORS).

Para personalizar a interface do utilizador, especifica um URL no elemento **ContentDefinition** com conteúdo HTML personalizado. No perfil técnico autoafirmado ou **OrquestrationStep,** aponta para esse identificador de definição de conteúdo. A definição de conteúdo pode conter um elemento **LocalizadoResourcesReferences** que especifica uma lista de recursos localizados para carregar. O Azure AD B2C combina elementos de interface de utilizador com o conteúdo HTML que é carregado a partir do seu URL e, em seguida, exibe a página para o utilizador.

O elemento **ContentDefinitions** contém URLs a modelos HTML5 que podem ser usados numa viagem de utilizador. O URI da página HTML5 é utilizado para um passo de interface de utilizador especificado. Por exemplo, o início ou inscrição, o reset da palavra-passe ou as páginas de erro. Pode modificar a aparência e sentir substituindo o LoadUri para o ficheiro HTML5. Pode criar novas definições de conteúdo de acordo com as suas necessidades. Este elemento pode conter uma referência de recursos localizados ao identificador de localização especificado no elemento [Localização.](localization.md)

O exemplo que se segue mostra o identificador de definição de conteúdo e a definição de recursos localizados:

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

Os metadados do perfil técnico autoafirmado **LocalAccountSignUpWithLogonEmail** contém o conjunto de definição de conteúdo **ContentDefinitionReferenceId** para`api.localaccountsignup`

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

## <a name="contentdefinition"></a>Definição de Conteúdo

O elemento **Definição** de Conteúdo contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador para uma definição de conteúdo. O valor é especificado na secção ids de **definição** de conteúdo mais tarde nesta página. |

O elemento **Definição** de Conteúdo contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Rio Loaduri | 1:1 | Uma cadeia que contém o URL da página HTML5 para a definição de conteúdo. |
| Recoveryuri | 1:1 | Uma cadeia que contém o URL da página HTML para exibir um erro relacionado com a definição de conteúdo. Não utilizado atualmente, o `~/common/default_page_error.html`valor deve ser . |
| Datauri | 1:1 | Uma cadeia que contém o URL relativo de um ficheiro HTML que fornece a experiência do utilizador para invocar para o passo. |
| Metadados | 0:1 | Uma coleção de pares chave/valor que contém os metadados utilizados pela definição de conteúdo. |
| Referências de Recursos Localizados | 0:1 | Uma coleção de referências de recursos localizados. Utilize este elemento para personalizar a localização de uma interface de utilizador e atribuições de reclamações. |

### <a name="datauri"></a>Datauri

O elemento **DataUri** é utilizado para especificar o identificador da página. O Azure AD B2C utiliza o identificador de página para carregar e iniciar elementos ui e javaScript do lado do cliente. O formato do `urn:com:microsoft:aad:b2c:elements:page-name:version`valor é . A tabela seguinte lista os identificadores da página que pode utilizar.

| Identificador de página | Descrição |
| ----- | ----------- |
| `globalexception` | Apresenta uma página de erro quando uma exceção ou um erro é encontrado. |
| `providerselection`, `idpselection` | Lista os fornecedores de identidade que os utilizadores podem escolher durante o sessão.  |
| `unifiedssp` | Apresenta um formulário para iniciar sessão com uma conta local baseada num endereço de e-mail ou num nome de utilizador. Este valor também fornece a funcionalidade "mantenha-me em sessão" e "Esqueci-me da sua palavra-passe?" link. |
| `unifiedssd` | Apresenta um formulário para iniciar sessão com uma conta local baseada num endereço de e-mail ou num nome de utilizador. |
| `multifactor` | Verifica os números de telefone utilizando texto ou voz durante o registo ou inscrição. |
| `selfasserted` | Apresenta um formulário para recolher dados de um utilizador. Por exemplo, permite que os utilizadores criem ou atualizem o seu perfil. |

### <a name="select-a-page-layout"></a>Selecione um layout de página

Pode ativar o código do lado `contract` `elements` do [cliente JavaScript](javascript-samples.md) inserindo entre e o tipo de página. Por exemplo, `urn:com:microsoft:aad:b2c:elements:contract:page-name:version`.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

A [version](page-layout.md) versão parte `DataUri` da versão especifica o pacote de conteúdo que contém HTML, CSS e JavaScript para os elementos da interface do utilizador na sua política. Se pretender ativar o código do lado do cliente JavaScript, os elementos em que baseia o seu JavaScript devem ser imutáveis. Se não forem imutáveis, quaisquer alterações podem causar comportamentos inesperados nas suas páginas de utilizador. Para prevenir estes problemas, aplique o uso de um layout de página e especifique uma versão de layout da página. Ao fazê-lo, garante que todas as definições de conteúdo em que baseou o seu JavaScript são imutáveis. Mesmo que não pretenda ativar o JavaScript, ainda precisa especificar a versão de layout da página para as suas páginas.

O exemplo seguinte mostra `selfasserted` os `1.2.0` **DataUri** da versão:

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

O formato do valor `contract`deve conter a palavra : _urn:com:microsoft:aad:b2c:elementos:**contrair**:pnome de idade:versão_. Para especificar um layout de página nas suas políticas personalizadas que usam um valor **DataUri** antigo, use a seguinte tabela para migrar para o novo formato.

| Valor DataUri antigo | Novo valor DataUri |
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


### <a name="metadata"></a>Metadados

Um elemento **metadados** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Item | 0:n | Os metadados que se relacionam com a definição de conteúdo. |

O elemento **Item** do elemento **Metadados** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Chave | Sim | A chave dos metadados.  |

#### <a name="metadata-keys"></a>Chaves de metadados

A definição de conteúdo suporta os seguintes itens de metadados:

| Chave | Necessário | Descrição |
| --------- | -------- | ----------- |
| DisplayName | Não | Uma cadeia que contém o nome da definição de conteúdo. |

### <a name="localizedresourcesreferences"></a>Referências de Recursos Localizados

O elemento **LocalizadoResourcesReferences** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Referência de Recursos Localizados | 1:n | Uma lista de referências de recursos localizados para a definição de conteúdo. |

O elemento **LocalizadoResourcesReference** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Idioma | Sim | Uma cadeia que contém uma linguagem apoiada para a política por RFC 5646 - Tags para identificar idiomas. |
| Recursos LocalizadosReferenceId | Sim | O identificador do elemento **Recursos Localizados.** |

O exemplo seguinte mostra uma definição de conteúdo de inscrição ou inscrição com referência à localização para inglês, francês e espanhol:

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

O atributo ID do elemento **ContentDefinition** especifica o tipo de página que diz respeito à definição de conteúdo. O elemento define o contexto que um modelo personalizado HTML5/CSS vai aplicar. A tabela seguinte descreve o conjunto de IDs de definição de conteúdo que é reconhecido pelo Quadro de Experiência de Identidade, e os tipos de página que se relacionam com eles. Pode criar as suas próprias definições de conteúdo com uma identificação arbitrária.

| ID | Modelo padrão | Descrição |
| -- | ---------------- | ----------- |
| **api.error** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Página de erro** - Exibe uma página de erro quando uma exceção ou um erro é encontrado. |
| **api.idpselections** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Página de seleção** do fornecedor de identidade - Lista fornecedores de identidade que os utilizadores podem escolher durante o sessão. As opções são geralmente fornecedores de identidade empresarial, fornecedores de identidade social como Facebook e Google+, ou contas locais. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Seleção do fornecedor de identidade para inscrição** - Lista fornecedores de identidade que os utilizadores podem escolher durante o seu inscrição. As opções são geralmente fornecedores de identidade empresarial, fornecedores de identidade social como Facebook e Google+, ou contas locais. |
| **api.localaccountpasswordreset** | [autoafirmou.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de palavra-passe esquecida** - Mostra um formulário que os utilizadores devem preencher para iniciar um reset de palavra-passe. |
| **api.localaccountsignin** | [autoafirmou.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de sessão de conta local** - Exibe um formulário para iniciar sessão com uma conta local baseada num endereço de e-mail ou num nome de utilizador. O formulário pode conter uma caixa de entrada de texto e uma caixa de entrada de senha. |
| **api.localaccountsignup** | [autoafirmou.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição** de conta local - Exibe um formulário para se inscrever numa conta local baseada num endereço de e-mail ou num nome de utilizador. O formulário pode conter vários controlos de entrada, tais como: uma caixa de entrada de texto, uma caixa de entrada de senha, um botão de rádio, caixas de entrega únicas e caixas de verificação multi-selecionadas. |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Página de autenticação de vários fatores** - Verifica os números de telefone, utilizando texto ou voz, durante o registo ou inscrição. |
| **api.autoafirmou** | [autoafirmou.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição** na conta social - Apresenta um formulário que os utilizadores devem preencher quando se inscrevem utilizando uma conta existente de um fornecedor de identidade social. Esta página é semelhante à página de inscrição da conta social anterior, com exceção dos campos de entrada de palavras-passe. |
| **api.self-asserted.profileupdate** | [updateprofile.cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Página de atualização** de perfis - Apresenta um formulário a que os utilizadores podem aceder à atualização do seu perfil. Esta página é semelhante à página de inscrição da conta social, com exceção dos campos de entrada de palavras-passe. |
| **api.signuporsignina** | [unificado.cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | Página de **inscrição ou inscrição unificada** - Trata do processo de inscrição e de inscrição do utilizador. Os utilizadores podem utilizar fornecedores de identidade empresarial, fornecedores de identidade social, como facebook ou Google+, ou contas locais. |

## <a name="next-steps"></a>Passos seguintes

Para um exemplo de personalização da interface do utilizador utilizando definições de conteúdo, consulte:

[Personalize a interface de utilizador da sua aplicação usando uma política personalizada](custom-policy-ui-customization.md)
