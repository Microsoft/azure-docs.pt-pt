---
title: ContentDefinitions
titleSuffix: Azure AD B2C
description: Especificar o elemento ContentDefinitions de uma política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 62bae22b6a4bb06b1e97c18e52ad614fd2439902
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103489326"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Pode personalizar o aspeto e a sensação de qualquer [perfil técnico autoafirmado.](self-asserted-technical-profile.md) O Azure Ative Directory B2C (Azure AD B2C) executa código no navegador do seu cliente e utiliza uma abordagem moderna chamada Partilha de Recursos de Origem Cruzada (CORS).

Para personalizar a interface do utilizador, especifica um URL no elemento **ContentDefinition** com conteúdo HTML personalizado. No perfil técnico autoafirmado ou **OrquestrationStep,** aponta-se para o identificador de definição de conteúdo. A definição de conteúdo pode conter um elemento **LocaledResourcesReferences** que especifica uma lista de recursos localizados para carregar. O Azure AD B2C funde elementos de interface de utilizador com o conteúdo HTML que é carregado a partir do seu URL e, em seguida, exibe a página para o utilizador.

O elemento **ContentDefinitions** contém URLs a modelos HTML5 que podem ser usados numa viagem de utilizador. O HTML5 page URI é utilizado para um passo de interface de utilizador especificado. Por exemplo, a inscrição ou inscrição, o reset da palavra-passe ou as páginas de erro. Pode modificar o aspeto e a sensação, sobrepor-se ao LoadUri para o ficheiro HTML5. Pode criar novas definições de conteúdo de acordo com as suas necessidades. Este elemento pode conter uma referência de recursos localizados ao identificador de localização especificado no elemento [Localização.](localization.md)

O exemplo a seguir mostra o identificador de definição de conteúdo e a definição de recursos localizados:

```xml
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

Os metadados do perfil técnico autoafirmado **localAccountSignUpWithLogonEmail** contém o identificador de definição de **conteúdoDefinitionReferenceId** definido para `api.localaccountsignup`

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    ...
  </Metadata>
  ...
```

## <a name="contentdefinition"></a>Definição de conteúdo

O elemento **ContentDefinition** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | Yes | Um identificador para uma definição de conteúdo. O valor é especificado na secção **IDs** de definição de conteúdo mais tarde nesta página. |

O elemento **ContentDefinition** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | Uma cadeia que contém o URL da página HTML5 para a definição de conteúdo. |
| RecoveryUri | 1:1 | Uma cadeia que contém o URL da página HTML para exibir um erro relacionado com a definição de conteúdo. Não atualmente utilizado, o valor deve `~/common/default_page_error.html` ser. |
| DataUri | 1:1 | Uma cadeia que contém o URL relativo de um ficheiro HTML que fornece a experiência do utilizador para invocar para o passo. |
| Metadados | 0:1 | Uma coleção de pares chave/valor que contém os metadados utilizados pela definição de conteúdo. |
| Referências de Recursos Recursos Localizados | 0:1 | Uma coleção de referências de recursos localizados. Utilize este elemento para personalizar a localização de uma interface de utilizador e atributo de reclamações. |

### <a name="datauri"></a>DataUri

O elemento **DataUri** é utilizado para especificar o identificador de página. Azure AD B2C utiliza o identificador de página para carregar e iniciar elementos de UI e javaScript do lado do cliente. O formato do valor `urn:com:microsoft:aad:b2c:elements:page-name:version` é. A tabela que se segue lista os identificadores de página que pode utilizar.

| Identificador de página | Description |
| ----- | ----------- |
| `globalexception` | Apresenta uma página de erro quando se encontra uma exceção ou um erro. |
| `providerselection`, `idpselection` | Lista os fornecedores de identidade que os utilizadores podem escolher durante a sin-in.  |
| `unifiedssp` | Apresenta um formulário para iniciar sessão com uma conta local que se baseia num endereço de e-mail ou num nome de utilizador. Este valor também fornece a funcionalidade "mantenha-me em funções" e "Esqueci-me da sua palavra-passe?" . |
| `unifiedssd` | Apresenta um formulário para iniciar sessão com uma conta local que se baseia num endereço de e-mail ou num nome de utilizador. |
| `multifactor` | Verifique os números de telefone utilizando texto ou voz durante a inscrição ou o registo. |
| `selfasserted` | Apresenta um formulário para recolher dados de um utilizador. Por exemplo, permite que os utilizadores criem ou atualizem o seu perfil. |

### <a name="select-a-page-layout"></a>Selecione um layout de página

Pode ativar o [código do lado do cliente JavaScript](javascript-and-page-layout.md) inserindo entre o tipo de `contract` `elements` página. Por exemplo, `urn:com:microsoft:aad:b2c:elements:contract:page-name:version`.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

A parte da [versão](page-layout.md) especifica o pacote de conteúdo que `DataUri` contém HTML, CSS e JavaScript para os elementos de interface do utilizador na sua política. Se pretende ativar o código do lado do cliente JavaScript, os elementos em que baseia o JavaScript devem ser imutáveis. Se não forem imutáveis, quaisquer alterações podem causar comportamentos inesperados nas suas páginas de utilizador. Para prevenir estes problemas, imponha o uso de um layout de página e especifique uma versão de layout de página. Ao fazê-lo, todas as definições de conteúdo em que baseou o JavaScript são imutáveis. Mesmo que não pretenda ativar o JavaScript, ainda precisa de especificar a versão de layout da página para as suas páginas.

O exemplo a seguir mostra o **DataUri** da `selfasserted` `1.2.0` versão:

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

O formato do valor deve conter a palavra `contract` : _urn:com:microsoft:aad:b2c:elements:**contract**:p age-name:version_. Para especificar um layout de página nas suas políticas personalizadas que usam um valor **DataUri** antigo, use a tabela seguinte para migrar para o novo formato.

| Valor DataUri antigo | Novo valor DataUri |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |

O exemplo a seguir mostra os identificadores de definição de conteúdo e os **DataUri correspondentes** com a versão mais recente da página: 

```xml
<!-- 
<BuildingBlocks> -->
  <ContentDefinitions>
    <ContentDefinition Id="api.error">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.idpselections">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.idpselections.signup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.selfasserted">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.selfasserted.profileupdate">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountsignup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountpasswordreset">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.phonefactor">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.2</DataUri>
    </ContentDefinition>
  </ContentDefinitions>
<!-- 
</BuildingBlocks> -->
```

### <a name="metadata"></a>Metadados

Um elemento **metadados** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Item | 0:n | Os metadados que se relacionam com a definição de conteúdo. |

O elemento **item** do elemento **Metadados** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Chave | Yes | A chave dos metadados.  |

#### <a name="metadata-keys"></a>Chaves de metadados

A definição de conteúdo suporta os seguintes itens de metadados:

| Chave | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| DisplayName | No | Uma cadeia que contém o nome da definição de conteúdo. |

### <a name="localizedresourcesreferences"></a>Referências de Recursos Recursos Localizados

O elemento **LocalresourcesReferences** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Referência de Recursos Fontes Localizadas | 1:n | Uma lista de referências de recursos localizadas para a definição de conteúdo. |

O elemento **LocalresourcesReference** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Linguagem | Yes | Uma cadeia que contém uma linguagem suportada para a política por RFC 5646 - Tags para identificar línguas. |
| Resources LocalizadoreferenceId | Yes | O identificador do elemento **Recursos Locais.** |

O exemplo a seguir mostra uma definição de conteúdo de inscrição ou de inscrição com referência à localização para inglês, francês e espanhol:

```xml
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

Para aprender a adicionar suporte de localização às definições de conteúdo, consulte [a Localização.](localization.md)

## <a name="content-definition-ids"></a>IDs de definição de conteúdo

O atributo ID do elemento **ContentDefinition** especifica o tipo de página que se relaciona com a definição de conteúdo. O elemento define o contexto que um modelo html5/CSS personalizado vai aplicar. A tabela seguinte descreve o conjunto de IDs de definição de conteúdo que é reconhecido pelo Quadro de Experiência de Identidade, e os tipos de página que se relacionam com eles. Pode criar as suas próprias definições de conteúdo com um ID arbitrário.

| ID | Modelo de padrão | Description |
| -- | ---------------- | ----------- |
| **api.erro** | [exceção.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Página de erro** - Apresenta uma página de erro quando uma exceção ou um erro é encontrado. |
| **api.idpselections** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Página de seleção do fornecedor** de identidade - Lista os fornecedores de identidade que os utilizadores podem escolher durante a sindução. As opções são geralmente fornecedores de identidade empresarial, fornecedores de identidade social, como Facebook e Google+, ou contas locais. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Seleção de fornecedores de identidade para inscrição** - Lista os fornecedores de identidade que os utilizadores podem escolher durante a inscrição. As opções são geralmente fornecedores de identidade empresarial, fornecedores de identidade social, como Facebook e Google+, ou contas locais. |
| **api.localaccountpasswordreset** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Esqueceu-se da página da palavra-passe** - Exibe um formulário que os utilizadores devem completar para iniciar uma redefinição de palavra-passe. |
| **api.localaccountsignin** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição de conta local** - Apresenta um formulário para iniciar sessão com uma conta local que se baseia num endereço de e-mail ou num nome de utilizador. O formulário pode conter uma caixa de entrada de texto e uma caixa de entrada de senha. |
| **api.localaccountsignup** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição de conta local** - Apresenta um formulário para se inscrever numa conta local que é baseada num endereço de e-mail ou num nome de utilizador. O formulário pode conter vários controlos de entrada, tais como: uma caixa de entrada de texto, uma caixa de entrada de senha, um botão de rádio, caixas de entrega de uma única seleção e caixas de verificação multi-selecionadas. |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Página de autenticação multi-factor** - Verifique os números de telefone, utilizando texto ou voz, durante a inscrição ou a inscrição. |
| **api.selfasserted** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição na conta social** - Exibe um formulário que os utilizadores devem preencher quando se inscrevem utilizando uma conta existente de um fornecedor de identidade social. Esta página é semelhante à página de inscrição da conta social anterior, exceto para os campos de entrada de senha. |
| **api.selfasserted.profileupdate** | [updateprofile.cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Página de atualização de perfis** - Exibe um formulário que os utilizadores podem aceder para atualizar o seu perfil. Esta página é semelhante à página de inscrição da conta social, exceto para os campos de entrada de senha. |
| **api.signuporsignin** | [unificado.cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Página de inscrição ou inscrição unificada** - Trata do processo de inscrição e inscrição do utilizador. Os utilizadores podem usar fornecedores de identidade empresarial, fornecedores de identidade social, como facebook ou Google+, ou contas locais. |

## <a name="next-steps"></a>Passos seguintes

Para um exemplo de personalização da interface do utilizador utilizando definições de conteúdo, consulte:

[Personalize a interface de utilizador da sua aplicação utilizando uma política personalizada](customize-ui-with-html.md)
