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
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3d0cb06f84fdd96d099e05f55ba62c37cb1192c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78183980"
---
# <a name="page-layout-versions"></a>Versões do esquema de página

Os pacotes de layout da página são periodicamente atualizados para incluir correções e melhorias nos seus elementos de página. O registo de alterações a seguir especifica as alterações introduzidas em cada versão.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="200"></a>2.0.0

- Página autoafirmada `selfasserted` ()
  - Suporte adicionado para [controlos de exibição](display-controls.md) em políticas personalizadas.

## <a name="120"></a>1.2.0

- Todas as páginas
  - Correções de acessibilidade
  - Agora pode adicionar o `data-preload="true"` atributo [nas suas tags HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) para controlar a ordem de carga para CSS e JavaScript.
    - Carregue os ficheiros CSS ligados ao mesmo tempo que o seu modelo HTML para que não 'cintilar' entre carregar os ficheiros.
    - Controle a ordem na qual as suas `script` etiquetas são recolhidas e executadas antes da carga da página.
  - O campo de e-mail é agora `type=email` e os teclados móveis fornecerão as sugestões corretas
  - Suporte para tradução do Chrome
- Páginas unificadas e autoafirmadas
  - Os campos de nome/e-mail e palavra-passe utilizam agora o `form` elemento HTML para permitir que o Edge e o Internet Explorer (IE) guardem corretamente esta informação.

## <a name="110"></a>1.1.0

- Página de exceção (globalexception)
  - Correção de acessibilidade
  - Removido a mensagem padrão quando não há contacto com a apólice
  - CSS padrão removido
- Página MFA (multifactor)
  - Botão 'Confirmar Código' removido
  - O campo de entrada para o código agora só leva a entrada até seis (6) caracteres
  - A página tentará automaticamente verificar o código introduzido quando um código de 6 dígitos é introduzido, sem que nenhum botão tenha de ser clicado
  - Se o código estiver errado, o campo de entrada é automaticamente limpo
  - Após três (3) tentativas com um código incorreto, b2C envia um erro de volta para a parte de gestão
  - Correções de acessibilidade
  - CSS padrão removido
- Página autoafirmada (autoasserida)
  - Alerta de cancelamento removido
  - Classe CSS para elementos de erro
  - Lógica de erro de exibição/ocultação melhorada
  - CSS padrão removido
- SSP unificado (ssp unificado)
  - Adicionado manter-me assinado no controlo (KMSI)

## <a name="100"></a>1.0.0

- Versão inicial

## <a name="next-steps"></a>Próximos passos

Para obter detalhes sobre como personalizar a interface do utilizador das suas aplicações em políticas personalizadas, consulte [Personalizar a interface do utilizador da sua aplicação utilizando uma política personalizada.](custom-policy-ui-customization.md)
