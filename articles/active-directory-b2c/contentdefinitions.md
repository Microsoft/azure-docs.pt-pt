---
title: ContentDefinitions - Azure Active Directory B2C | Documentos da Microsoft
description: Especifique o elemento de ContentDefinitions de uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d82785a0f833afb6a9c675fc7022ed19e96c7fc0
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511319"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Pode personalizar o aspeto e funcionalidade de qualquer [autodeclarativas perfil técnico](self-asserted-technical-profile.md). O Azure Active Directory (Azure AD) B2C executa o código no browser do seu cliente e utiliza uma abordagem moderna chamada Cross-Origin Resource Sharing (CORS). 

Para personalizar a interface do usuário, tem de especificar um URL no **ContentDefinition** elemento com conteúdo HTML personalizado. No perfil técnico de declaração própria ou **OrchestrationStep**, aponta para esse identificador de definição de conteúdo. A definição de conteúdo pode conter uma **LocalizedResourcesReferences** localizados de elemento que especifica uma lista de recursos para carregar. O Azure AD B2C mescla os elementos de interface do usuário com o conteúdo HTML que são carregados a partir do URL e, em seguida, exibe a página para o usuário.

O **ContentDefinitions** elemento contenha URLs para os modelos de HTML5 que podem ser usados num percurso do utilizador. O URI da página HTML5 é utilizado para um passo de interface de utilizador especificado. Por exemplo, a reposição de palavra-passe de início de sessão ou Inscreva-se, ou páginas de erro. Pode modificar o aspeto e funcionalidade substituindo LoadUri para o ficheiro do HTML5. Pode criar novas definições de conteúdo, de acordo com suas necessidades. Este elemento pode conter uma referência de recursos localizados, para o identificador de localização especificada na [localização](localization.md) elemento.

O exemplo seguinte mostra o identificador de definição de conteúdo e a definição de recursos localizados:

```XML
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LoalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

Os metadados do **LocalAccountSignUpWithLogonEmail** autodeclarativas perfil técnico contém o identificador de definição de conteúdo **ContentDefinitionReferenceId** definido como `api.localaccountsignup`

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

O **ContentDefinition** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador para uma definição de conteúdo. O valor é especificada no **IDs de definição de conteúdo** seção mais adiante nesta página. |

O **ContentDefinition** elemento contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | Uma cadeia que contém o URL da página HTML5 para a definição de conteúdo. |
| RecoveryUri | 0:1 | Uma cadeia que contém o URL da página HTML para exibir um erro relacionadas com a definição de conteúdo. | 
| DataUri | 1:1 | Uma cadeia que contém o URL relativo de um arquivo HTML que fornece a experiência do usuário para invocar para o passo. |  
| Metadados | 1:1 | Uma coleção de pares chave/valor que contenha os metadados utilizados pela definição do conteúdo. | 
| LocalizedResourcesReferences | 0:1 | Uma coleção de referências de recursos localizados. Utilize este elemento para personalizar a localização de um atributo de interface e afirmações de utilizador. |

### <a name="datauri"></a>DataUri

O **URI de dados** elemento é utilizado para especificar o identificador de página. O Azure AD B2C utiliza o identificador de página para carregar e iniciar os elementos de interface do Usuário e de JavaScript do lado do cliente. O formato do valor é `urn:com:microsoft:aad:b2c:elements:page-name:version`.  A tabela seguinte apresenta os identificadores de página que pode utilizar.

| Value |   Descrição |
| ----- | ----------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | Apresenta uma página de erro quando é encontrado uma exceção ou um erro. |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | Lista os fornecedores de identidade que os utilizadores escolherem durante o início de sessão. | 
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | Exibe um formulário para iniciar sessão com uma conta local que se baseia num endereço de e-mail ou um nome de utilizador. Este valor também fornece o "manter a funcionalidade de início de sessão" e "Esqueceu-se a palavra-passe?" ligação. | 
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | Exibe um formulário para iniciar sessão com uma conta local que se baseia num endereço de e-mail ou um nome de utilizador. |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | Verifica se os números de telefone com o texto ou de voz durante a inscrição ou início de sessão. |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | Exibe um formulário que permite aos utilizadores criar ou atualizar o respetivo perfil. | 


### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

O **LocalizedResourcesReferences** elemento contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1:n | Uma lista de referências de recurso localizado para a definição de conteúdo. | 

O **LocalizedResourcesReferences** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Idioma | Sim | Uma cadeia que contém um idioma suportado para a política de por RFC 5646 - as etiquetas para identificar idiomas. |
| LocalizedResourcesReferenceId | Sim | O identificador do **LocalizedResources** elemento. |

O exemplo seguinte mostra uma definição de conteúdo de inscrição ou início de sessão:

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

O exemplo seguinte mostra uma definição de conteúdo inscrição ou início de sessão com uma referência para a localização para inglês, francês e espanhol:

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

Para saber como adicionar suporte de localização para as definições de seu conteúdas, consulte [localização](localization.md).

## <a name="content-definition-ids"></a>IDs de definição de conteúdo

O atributo ID dos **ContentDefinition** elemento Especifica o tipo de página que está relacionado com a definição de conteúdo. O elemento define o contexto no qual vai aplicar um modelo personalizado do HTML5/CSS. A tabela seguinte descreve o conjunto de definição de conteúdo IDs que é reconhecida pelo Framework de experiência de identidade e os tipos de página relacionados. Pode criar suas próprias definições de conteúdo com um ID de arbitrário.

| ID | Modelo predefinido | Descrição | 
| -- | ---------------- | ----------- |
| **api.error** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Página de erro** - a página de erro apresentada quando uma exceção ou for encontrado um erro. |
| **api.idpselections** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Página de seleção do fornecedor de identidade** -apresenta uma lista de fornecedores de identidade que os utilizadores escolherem durante o início de sessão. As opções são, normalmente, fornecedores de identidade empresarial, os fornecedores de identidade social como o Facebook e Google + ou contas locais. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Seleção de fornecedor de identidade para inscrição** -apresenta uma lista de fornecedores de identidade que os utilizadores escolherem durante a inscrição. As opções são, normalmente, fornecedores de identidade empresarial, os fornecedores de identidade social como o Facebook e Google + ou contas locais. |
| **api.localaccountpasswordreset** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Esqueceu-se a página de palavra-passe** -exibe um formulário que os utilizadores tem de concluir para iniciar uma reposição de palavra-passe. |
| **api.localaccountsignin** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de início de sessão de conta local** -exibe um formulário para iniciar sessão com uma conta local que se baseia num endereço de e-mail ou um nome de utilizador. O formulário pode conter uma caixa de entrada de texto e a caixa de entrada de palavra-passe. |
| **api.localaccountsignup** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição de conta local** -exibe um formulário para se inscrever para uma conta local que se baseia num endereço de e-mail ou um nome de utilizador. O formulário pode conter vários controles de entrada, tais como: um texto de entrada caixa, uma caixa de entrada de palavra-passe, um botão de rádio, caixas de lista pendente de seleção única e caixas de verificação de seleção múltipla. |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Página do multi-factor authentication** -verifica se os números de telefone, com o texto ou de voz, durante a inscrição ou início de sessão. |
| **api.selfasserted** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Página de inscrição de conta de redes sociais** -exibe um formulário que os utilizadores tem de concluir quando se inscrevem, utilizando uma conta existente de um fornecedor de identidade de redes sociais. Esta página é semelhante para a conta de rede social de sessão anterior a página, exceto os campos de entrada de palavra-passe. |
| **api.selfasserted.profileupdate** | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Página de atualização de perfil** -exibe um formulário que os utilizadores podem aceder ao atualizar o respetivo perfil. Esta página é semelhante para a conta de redes sociais página de inscrição, exceto os campos de entrada de palavra-passe. |
| **api.signuporsignin** | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Página de inscrição ou início de sessão unificada** -lida com o processo de inscrição e início de sessão do utilizador. Os utilizadores podem utilizar fornecedores de identidade empresarial, os fornecedores de identidade social como o Facebook ou Google + ou contas locais. |
 
