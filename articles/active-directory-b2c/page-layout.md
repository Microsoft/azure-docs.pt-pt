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
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b15c63545c71d4513abe9102b4de165e2ab5857a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499854"
---
# <a name="page-layout-versions"></a>Versões do esquema de página

Os pacotes de layout da página são periodicamente atualizados para incluir correções e melhorias nos seus elementos de página. O registo de alterações a seguir especifica as alterações introduzidas em cada versão.

## <a name="self-asserted-page-selfasserted"></a>Página autoafirmada (autoasserida)

**2.1.2**
- Corrigiu a questão da codificação da localização para línguas como o espanhol e o francês.

**2.1.1**

- Adicionei um UXString `heading` para `intro` além de exibir na página como um título. Isto é escondido por defeito.
- Suporte adicionado para guardar palavras-passe para iCloud Keychain.
- Suporte adicional para a utilização da política ou do parâmetro QueryString `pageFlavor` para selecionar o layout (clássico, oceanBlue ou slateGray).
- Adicionou isenções de responsabilidade na página autoafirmada.
- O foco é agora colocado no primeiro campo editável quando a página é carregada.
- O foco é agora colocado no primeiro campo de erro quando vários campos têm erros.
- O foco é agora colocado no botão 'alterar' depois de verificado o código de verificação por e-mail.

**2.1.0**

- Correções de localização e acessibilidade.

**2.0.0**

- Suporte adicionado para [controlos de exibição](display-controls.md) em políticas personalizadas.

**1.2.0**

- Os campos de nome/e-mail e palavra-passe utilizam agora o `form` elemento HTML para permitir que o Edge e o Internet Explorer (IE) guardem corretamente esta informação.
- Adicionou um atraso de validação de entrada de utilizador configurável para uma melhor experiência do utilizador.
- Correções de acessibilidade
- Corrigiu um problema de acessibilidade para que as mensagens de erro sejam agora lidas pelo Narrador. 
- O foco é agora colocado no campo da palavra-passe após a verificação do e-mail.
- Removido `autofocus` do controlo da caixa de verificação. 
- Suporte adicionado para um controlo de visualização para verificação de números de telefone.
- Agora pode adicionar o `data-preload="true"` atributo [nas suas tags HTML](customize-ui-with-html.md#guidelines-for-using-custom-page-content)
  - Carregue os ficheiros CSS ligados ao mesmo tempo que o seu modelo HTML para que não 'cintilar' entre carregar os ficheiros.
  - Controle a ordem na qual as suas `script` etiquetas são recolhidas e executadas antes da carga da página.
- O campo de e-mail é agora `type=email` e os teclados móveis fornecerão as sugestões corretas.
- Suporte para tradução do Chrome.
- Suporte adicional para marca da empresa em páginas de fluxo de utilizador.

**1.1.0**

- Alerta de cancelamento removido
- Classe CSS para elementos de erro
- Lógica de erro de exibição/ocultação melhorada
- CSS padrão removido

**1.0.0**

- Versão inicial

## <a name="unified-sign-in-sign-up-page-with-password-reset-link-unifiedssp"></a>Página de inscrição unificada com link de reset de palavra-passe (unifiedssp)

> [!TIP]
> Se você localizar a sua página para suportar vários locais, ou idiomas em um fluxo de utilizador. O artigo [de localização IDs](localization-string-ids.md) fornece a lista de IDs de localização que pode usar para a versão de página que seleciona.

**2.1.2**
- Corrigiu a questão da codificação da localização para línguas como o espanhol e o francês.
- Permitindo que o link "esqueceu-se da palavra-passe" para usar como troca de reclamações. Para obter mais informações, consulte [a palavra-passe self-service reposta](add-password-reset-policy.md#self-service-password-reset-recommended).

**2.1.1**
- Adicionei um UXString `heading` para `intro` além de exibir na página como um título. Isto é escondido por defeito.
- Suporte adicional para a utilização da política ou do parâmetro QueryString `pageFlavor` para selecionar o layout (clássico, oceanBlue ou slateGray).
- Suporte adicionado para guardar palavras-passe para iCloud Keychain.
- O foco é agora colocado no primeiro campo de erro quando vários campos têm erros.
- O foco é agora colocado no primeiro campo editável quando a página é carregada.
- Adicionou uma nova localização para o link de seleção do fornecedor de `bottomUnderFormClaimsProviderSelections` sinistros.
- UXStrings removidos que já não são utilizados.

**2.1.0**

- Suporte adicional para várias ligações de inscrição.
- Suporte adicional para validação de entradas de utilizador de acordo com as regras predicados definidas na política.

**1.2.0**

- Os campos de nome/e-mail e palavra-passe utilizam agora o `form` elemento HTML para permitir que o Edge e o Internet Explorer (IE) guardem corretamente esta informação.
- Correções de acessibilidade
- Agora pode adicionar o `data-preload="true"` atributo [nas suas tags HTML](customize-ui-with-html.md#guidelines-for-using-custom-page-content) para controlar a ordem de carga para CSS e JavaScript.
  - Carregue os ficheiros CSS ligados ao mesmo tempo que o seu modelo HTML para que não 'cintilar' entre carregar os ficheiros.
  - Controle a ordem na qual as suas `script` etiquetas são recolhidas e executadas antes da carga da página.
- O campo de e-mail é agora `type=email` e os teclados móveis fornecerão as sugestões corretas.
- Suporte para tradução do Chrome.
- Suporte adicional para a marca de inquilinos em páginas de fluxo de utilizador.

**1.1.0**

- Adicionado manter-me assinado no controlo (KMSI)

**1.0.0**

- Versão inicial

## <a name="mfa-page-multifactor"></a>Página MFA (multifactor)

**1.2.2**
- Corrigiu um problema com o preenchimento automático do código de verificação ao utilizar o iOS.
- Corrigi um problema com a reorientação de um token para a parte de confiante do Android Webview. 
- Adicionei um UXString `heading` para `intro` além de exibir na página como um título. Isto é escondido por defeito.  
- Suporte adicional para a utilização da política ou do parâmetro QueryString `pageFlavor` para selecionar o layout (clássico, oceanBlue ou slateGray).

**1.2.1**

- Correções de acessibilidade em modelos predefinidos

**1.2.0**

- Correções de acessibilidade
- Agora pode adicionar o `data-preload="true"` atributo [nas suas tags HTML](customize-ui-with-html.md#guidelines-for-using-custom-page-content) para controlar a ordem de carga para CSS e JavaScript.
  - Carregue os ficheiros CSS ligados ao mesmo tempo que o seu modelo HTML para que não 'cintilar' entre carregar os ficheiros.
  - Controle a ordem na qual as suas `script` etiquetas são recolhidas e executadas antes da carga da página.
- O campo de e-mail é agora `type=email` e os teclados móveis fornecerão as sugestões corretas
- Suporte para tradução do Chrome.
- Suporte adicional para a marca de inquilinos em páginas de fluxo de utilizador.

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
- Agora pode adicionar o `data-preload="true"` atributo [nas suas tags HTML](customize-ui-with-html.md#guidelines-for-using-custom-page-content) para controlar a ordem de carga para CSS e JavaScript.
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
- Agora pode adicionar o `data-preload="true"` atributo [nas suas tags HTML](customize-ui-with-html.md#guidelines-for-using-custom-page-content) para controlar a ordem de carga para CSS e JavaScript.
  - Carregue os ficheiros CSS ligados ao mesmo tempo que o seu modelo HTML para que não 'cintilar' entre carregar os ficheiros.
  - Controle a ordem na qual as suas `script` etiquetas são recolhidas e executadas antes da carga da página.
- O campo de e-mail é agora `type=email` e os teclados móveis fornecerão as sugestões corretas
- Suporte para tradução do Chrome

**1.0.0**

- Versão inicial

## <a name="next-steps"></a>Passos seguintes

Para obter detalhes sobre como personalizar a interface do utilizador das suas aplicações em políticas personalizadas, consulte [Personalizar a interface do utilizador da sua aplicação utilizando uma política personalizada.](customize-ui-with-html.md)
