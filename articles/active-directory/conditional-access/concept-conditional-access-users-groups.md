---
title: Utilizadores e grupos na política de acesso condicional - Diretório Ativo Azure
description: Quem são utilizadores e grupos numa política de acesso condicional Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36898e75680771a9cb084fa142bb635ddbf51c70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77192132"
---
# <a name="conditional-access-users-and-groups"></a>Acesso Condicional: Utilizadores e grupos

Uma política de acesso condicional deve incluir uma atribuição do utilizador como um dos sinais no processo de decisão. Os utilizadores podem ser incluídos ou excluídos das políticas de Acesso Condicional. 

![Utilizador como sinal nas decisões tomadas pelo Acesso Condicional](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>Incluir utilizadores

Esta lista de utilizadores inclui normalmente todos os utilizadores que uma organização está a direcionar para uma política de Acesso Condicional. 

As seguintes opções estão disponíveis para incluir na criação de uma política de Acesso Condicional.

- Nenhuma
   - Nenhum utilizador selecionado
- Todos os utilizadores
   - Todos os utilizadores que existam no diretório, incluindo convidados B2B.
- Selecionar utilizadores e grupos
   - Todos os utilizadores convidados e externos (pré-visualização)
      - Esta seleção inclui quaisquer hóspedes B2B e utilizadores externos, incluindo qualquer utilizador com o `user type` atributo definido para `guest`. Esta seleção também se aplica a qualquer utilizador externo que tenha assinado a partir de uma organização diferente, como um Fornecedor de Soluções cloud (CSP). 
   - Funções de diretório (pré-visualização)
      - Permite que os administradores selecionem funções específicas de diretório Azure AD usadas para determinar a atribuição. Por exemplo, as organizações podem criar uma política mais restritiva para os utilizadores que atribuíram o papel de administrador global.
   - Utilizadores e grupos
      - Permite a segmentação de conjuntos específicos de utilizadores. Por exemplo, as organizações podem selecionar um grupo que contenha todos os membros do departamento de RH quando uma aplicação de RH é selecionada como a aplicação cloud. Um grupo pode ser qualquer tipo de grupo em Azure AD, incluindo grupos dinâmicos ou atribuídos de segurança e distribuição.

## <a name="exclude-users"></a>Excluir utilizadores

As exclusões são geralmente utilizadas para acesso de emergência ou contas de vidro partido. Podem ser encontradas mais informações sobre as contas de acesso de emergência e por que razão são importantes nos seguintes artigos: 

* [Gerir contas de acesso de emergência em Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Criar uma estratégia resiliente de gestão de controlo de acesso com o Diretório Ativo azure](../authentication/concept-resilient-controls.md)

As seguintes opções estão disponíveis para excluir na criação de uma política de Acesso Condicional.

- Todos os utilizadores convidados e externos (pré-visualização)
   - Esta seleção inclui quaisquer hóspedes B2B e utilizadores externos, incluindo qualquer utilizador com o `user type` atributo definido para `guest`. Esta seleção também se aplica a qualquer utilizador externo que tenha assinado a partir de uma organização diferente, como um Fornecedor de Soluções cloud (CSP). 
- Funções de diretório (pré-visualização)
   - Permite que os administradores selecionem funções específicas de diretório Azure AD usadas para determinar a atribuição. Por exemplo, as organizações podem criar uma política mais restritiva para os utilizadores que atribuíram o papel de administrador global.
- Utilizadores e grupos
   - Permite a segmentação de conjuntos específicos de utilizadores. Por exemplo, as organizações podem selecionar um grupo que contenha todos os membros do departamento de RH quando uma aplicação de RH é selecionada como a aplicação cloud. Um grupo pode ser qualquer tipo de grupo em Azure AD, incluindo grupos dinâmicos ou atribuídos de segurança e distribuição.

## <a name="next-steps"></a>Passos seguintes

- [Acesso Condicional: Aplicações ou ações em nuvem](concept-conditional-access-cloud-apps.md)

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)
