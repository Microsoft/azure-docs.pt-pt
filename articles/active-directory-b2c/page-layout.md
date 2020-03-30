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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183980"
---
# <a name="page-layout-versions"></a>Versões do esquema de página

Os pacotes de layout da página são periodicamente atualizados para incluir correções e melhorias nos seus elementos de página. O seguinte registo de alteração especifica as alterações introduzidas em cada versão.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="200"></a>2.0.0

- Página autoafirmada (`selfasserted`)
  - Suporte adicional para controlos de [exibição](display-controls.md) em políticas personalizadas.

## <a name="120"></a>1.2.0

- Todas as páginas
  - Correções de acessibilidade
  - Agora pode adicionar `data-preload="true"` o atributo [nas suas tags HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) para controlar a ordem de carga para CSS e JavaScript.
    - Carregue os ficheiros CSS ligados ao mesmo tempo que o seu modelo HTML para que não 'cintile' entre carregar os ficheiros.
    - Controle a ordem `script` em que as suas etiquetas são rebuscadas e executadas antes da carga da página.
  - O campo `type=email` de e-mail é agora e os teclados móveis fornecerão as sugestões corretas
  - Suporte para tradução do Chrome
- Páginas unificadas e autoafirmadas
  - Os campos de username/email `form` e password utilizam agora o elemento HTML para permitir que o Edge e o Internet Explorer (IE) guardem corretamente esta informação.

## <a name="110"></a>1.1.0

- Página de exceção (exceção global)
  - Correção de acessibilidades
  - Removeu a mensagem predefinida quando não há contacto com a apólice
  - CSS padrão removido
- Página de MFA (multifactor)
  - Botão 'Confirmar Código' removido
  - O campo de entrada para o código agora só leva entrada até seis (6) caracteres
  - A página tentará automaticamente verificar o código introduzido quando um código de 6 dígitos for introduzido, sem que qualquer botão tenha de ser clicado
  - Se o código estiver errado, o campo de entrada é automaticamente limpo
  - Após três (3) tentativas com um código incorreto, b2C envia um erro de volta para a parte de fiação
  - Correções de acessibilidade
  - CSS padrão removido
- Página autoafirmada (autoafirmada)
  - Alerta de cancelamento removido
  - Classe CSS para elementos de erro
  - Lógica de erro de mostrar/esconder melhorada
  - CSS padrão removido
- SSP unificada (unificada)
  - Adicionado manter-me assinado no controlo (KMSI)

## <a name="100"></a>1.0.0

- Versão inicial

## <a name="next-steps"></a>Passos seguintes

Para mais detalhes sobre como personalizar a interface de utilizador das suas aplicações em políticas personalizadas, consulte [Personalizar a interface de utilizador da sua aplicação utilizando uma política personalizada](custom-policy-ui-customization.md).
