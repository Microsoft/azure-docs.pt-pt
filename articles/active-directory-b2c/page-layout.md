---
title: Versões do esquema de página
titleSuffix: Azure AD B2C
description: Histórico de versão de layout de página para personalização de UI em políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 868d99a82009dc8545fc24ad1cfa1da3959da131
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88852080"
---
# <a name="page-layout-versions"></a>Versões do esquema de página

Os pacotes de layout da página são periodicamente atualizados para incluir correções e melhorias nos seus elementos de página. O registo de alterações a seguir especifica as alterações introduzidas em cada versão.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="self-asserted-page-selfasserted"></a>Página autoafirmada (autoasserida)

**2.1.0**

- Correções de localização e acessibilidade.

**2.0.0**

- Suporte adicionado para [controlos de exibição](display-controls.md) em políticas personalizadas.

**1.2.0**

- Os campos de nome/e-mail e palavra-passe utilizam agora o `form` elemento HTML para permitir que o Edge e o Internet Explorer (IE) guardem corretamente esta informação.
- Adicionou um atraso de validação de entrada de utilizador configurável para uma melhor experiência do utilizador.
- Correções de acessibilidade
- Agora pode adicionar o `data-preload="true"` atributo [nas suas tags HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) para controlar a ordem de carga para CSS e JavaScript.
  - Carregue os ficheiros CSS ligados ao mesmo tempo que o seu modelo HTML para que não 'cintilar' entre carregar os ficheiros.
  - Controle a ordem na qual as suas `script` etiquetas são recolhidas e executadas antes da carga da página.
- O campo de e-mail é agora `type=email` e os teclados móveis fornecerão as sugestões corretas
- Suporte para tradução do Chrome

**1.1.0**

- Alerta de cancelamento removido
- Classe CSS para elementos de erro
- Lógica de erro de exibição/ocultação melhorada
- CSS padrão removido

**1.0.0**

- Versão inicial

## <a name="unified-sign-in-sign-up-page-with-password-reset-link-unifiedssp"></a>Página de inscrição unificada com link de reset de palavra-passe (unifiedssp)

**2.1.0**

- Suporte adicional para várias ligações de inscrição.
- Suporte adicional para validação de entradas de utilizador de acordo com as regras predicados definidas na política.

**1.2.0**

- Os campos de nome/e-mail e palavra-passe utilizam agora o `form` elemento HTML para permitir que o Edge e o Internet Explorer (IE) guardem corretamente esta informação.
- Correções de acessibilidade
- Agora pode adicionar o `data-preload="true"` atributo [nas suas tags HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) para controlar a ordem de carga para CSS e JavaScript.
  - Carregue os ficheiros CSS ligados ao mesmo tempo que o seu modelo HTML para que não 'cintilar' entre carregar os ficheiros.
  - Controle a ordem na qual as suas `script` etiquetas são recolhidas e executadas antes da carga da página.
- O campo de e-mail é agora `type=email` e os teclados móveis fornecerão as sugestões corretas
- Suporte para tradução do Chrome

**1.1.0**

- Adicionado manter-me assinado no controlo (KMSI)

**1.0.0**

- Versão inicial

## <a name="mfa-page-multifactor"></a>Página MFA (multifactor)

**1.2.1**

- Correções de acessibilidade em modelos predefinidos

**1.2.0**

- Correções de acessibilidade
- Agora pode adicionar o `data-preload="true"` atributo [nas suas tags HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) para controlar a ordem de carga para CSS e JavaScript.
  - Carregue os ficheiros CSS ligados ao mesmo tempo que o seu modelo HTML para que não 'cintilar' entre carregar os ficheiros.
  - Controle a ordem na qual as suas `script` etiquetas são recolhidas e executadas antes da carga da página.
- O campo de e-mail é agora `type=email` e os teclados móveis fornecerão as sugestões corretas
- Suporte para tradução do Chrome

**1.1.0**

- Botão 'Confirmar Código' removido
- O campo de entrada para o código agora só leva a entrada até seis (6) caracteres
- A página tentará automaticamente verificar o código introduzido quando um código de 6 dígitos é introduzido, sem que nenhum botão tenha de ser clicado
- Se o código estiver errado, o campo de entrada é automaticamente limpo
- Após três (3) tentativas com um código incorreto, b2C envia um erro de volta para a parte de gestão
- Correções de acessibilidade
- CSS padrão removido

**1.0.0**

- Versão inicial

## <a name="exception-page-globalexception"></a>Página de Exceção (globalexception)

**1.2.0**

- Correções de acessibilidade
- Agora pode adicionar o `data-preload="true"` atributo [nas suas tags HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) para controlar a ordem de carga para CSS e JavaScript.
  - Carregue os ficheiros CSS ligados ao mesmo tempo que o seu modelo HTML para que não 'cintilar' entre carregar os ficheiros.
  - Controle a ordem na qual as suas `script` etiquetas são recolhidas e executadas antes da carga da página.
- O campo de e-mail é agora `type=email` e os teclados móveis fornecerão as sugestões corretas
- Suporte para tradução do Chrome

**1.1.0**

- Correção de acessibilidade
- Removido a mensagem padrão quando não há contacto com a apólice
- CSS padrão removido

**1.0.0**

- Versão inicial

## <a name="other-pages-providerselection-claimsconsent-unifiedssd"></a>Outras páginas (ProviderSelection, ClaimsConsent, UnifiedSSD)

**1.2.0**

- Correções de acessibilidade
- Agora pode adicionar o `data-preload="true"` atributo [nas suas tags HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) para controlar a ordem de carga para CSS e JavaScript.
  - Carregue os ficheiros CSS ligados ao mesmo tempo que o seu modelo HTML para que não 'cintilar' entre carregar os ficheiros.
  - Controle a ordem na qual as suas `script` etiquetas são recolhidas e executadas antes da carga da página.
- O campo de e-mail é agora `type=email` e os teclados móveis fornecerão as sugestões corretas
- Suporte para tradução do Chrome

**1.0.0**

- Versão inicial

## <a name="next-steps"></a>Passos seguintes

Para obter detalhes sobre como personalizar a interface do utilizador das suas aplicações em políticas personalizadas, consulte [Personalizar a interface do utilizador da sua aplicação utilizando uma política personalizada.](custom-policy-ui-customization.md)
