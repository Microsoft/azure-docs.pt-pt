---
title: Selecione um layout de página-Azure Active Directory B2C
description: Saiba mais sobre como selecionar um layout de página em Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 612d2e3a9a5a324f7d6d8e1b63b6b7e297047239
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063845"
---
# <a name="select-a-page-layout-in-azure-active-directory-b2c-using-custom-policies"></a>Selecionar um layout de página em Azure Active Directory B2C usando políticas personalizadas

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Você pode habilitar o código do lado do cliente JavaScript em suas políticas de Azure Active Directory B2C (Azure AD B2C) se estiver usando fluxos de usuário ou políticas personalizadas. Para habilitar o JavaScript para seus aplicativos, você deve adicionar um elemento à sua [política personalizada](active-directory-b2c-overview-custom.md), selecionar um layout de página e usar [b2clogin.com](b2clogin.md) em suas solicitações.

Um layout de página é uma associação de elementos que Azure AD B2C fornece e o conteúdo que você fornece.

Este artigo discute como selecionar um layout de página em Azure AD B2C Configurando-o em uma política personalizada.

> [!NOTE]
> Se você quiser habilitar o JavaScript para fluxos de usuário, consulte [JavaScript e versões de layout de página em Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="replace-datauri-values"></a>Substitua os valores de URI de dados

Em suas políticas personalizadas, talvez tenha [ContentDefinitions](contentdefinitions.md) que definem os modelos HTML utilizados no percurso do utilizador. O **ContentDefinition** contém uma **URI de dados** que se refere a elementos da página fornecidos pelo Azure AD B2C. O **LoadUri** é o caminho relativo para o conteúdo HTML e CSS que fornecer.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

Para selecionar um layout de página, você altera os valores de **DataUri** em seu [ContentDefinitions](contentdefinitions.md) em suas políticas. Alternando a partir do antigo **URI de dados** valores para os novos valores, selecionar um pacote imutável. A vantagem de utilizar este pacote é que sabe que não altere e causar um comportamento inesperado na sua página.

Para configurar um layout de página, use a tabela a seguir para localizar os valores de **DataUri** .

| Valor do URI de dados antigo | Novo valor de URI de dados |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0` |

## <a name="version-change-log"></a>Log de alterações de versão

Os pacotes de layout de página são atualizados periodicamente para incluir correções e aprimoramentos em seus elementos de página. O log de alterações a seguir especifica as alterações introduzidas em cada versão.

### <a name="110"></a>1.1.0

- Página de exceção (globalexception)
  - Correção de acessibilidade
  - Removeu a mensagem padrão quando não há contato da política
  - CSS padrão removido
- Página MFA (multifator)
  - Botão ' confirmar código ' removido
  - O campo de entrada para o código agora só usa a entrada de até seis (6) caracteres
  - A página tentará automaticamente verificar o código inserido quando um código de seis dígitos for inserido, sem qualquer botão que precise ser clicado
  - Se o código estiver errado, o campo de entrada será limpo automaticamente
  - Depois de três (3) tentativas com um código incorreto, o B2C envia um erro de volta para a terceira parte confiável
  - Correções de acessibilidade
  - CSS padrão removido
- Página autodeclarada (selfasserted)
  - Removido o alerta de cancelamento
  - Classe CSS para elementos error
  - Mostrar/ocultar a lógica de erro aprimorada
  - CSS padrão removido
- SSP unificado (unifiedssp)
  - Controle KMSI (Mantenha-me conectado) adicionado

### <a name="100"></a>1.0.0

- Versão inicial

## <a name="next-steps"></a>Passos Seguintes

Encontrar mais informações sobre como pode personalizar a interface do usuário das suas aplicações na [personalizar a interface de utilizador da sua aplicação utilizando uma política personalizada no Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
