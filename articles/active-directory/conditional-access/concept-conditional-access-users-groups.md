---
title: Utilizadores e grupos na política de acesso condicional - Diretório Ativo Azure
description: Quem são utilizadores e grupos numa política de acesso condicional Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17312e44714c8bdb20e22ad9aeb950e46eb71e3e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80755281"
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
   - Todos os utilizadores convidados e externos
      - Esta seleção inclui quaisquer hóspedes B2B e utilizadores externos, incluindo qualquer utilizador com o `user type` atributo definido para `guest`. Esta seleção também se aplica a qualquer utilizador externo que tenha assinado a partir de uma organização diferente, como um Fornecedor de Soluções cloud (CSP). 
   - Funções de diretório
      - Permite que os administradores selecionem funções específicas de diretório Azure AD usadas para determinar a atribuição. Por exemplo, as organizações podem criar uma política mais restritiva para os utilizadores que atribuíram o papel de administrador global.
   - Utilizadores e grupos
      - Permite a segmentação de conjuntos específicos de utilizadores. Por exemplo, as organizações podem selecionar um grupo que contenha todos os membros do departamento de RH quando uma aplicação de RH é selecionada como a aplicação cloud. Um grupo pode ser qualquer tipo de grupo em Azure AD, incluindo grupos dinâmicos ou atribuídos de segurança e distribuição.

## <a name="exclude-users"></a>Excluir utilizadores

Quando as organizações incluem e excluem um utilizador ou grupo, o utilizador ou grupo é excluído da política, uma vez que uma ação exclui uma investida na política. As exclusões são geralmente utilizadas para acesso de emergência ou contas de vidro partido. Podem ser encontradas mais informações sobre as contas de acesso de emergência e por que razão são importantes nos seguintes artigos: 

* [Gerir contas de acesso de emergência em Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Criar uma estratégia resiliente de gestão de controlo de acesso com o Diretório Ativo azure](../authentication/concept-resilient-controls.md)

As seguintes opções estão disponíveis para excluir na criação de uma política de Acesso Condicional.

- Todos os utilizadores convidados e externos
   - Esta seleção inclui quaisquer hóspedes B2B e utilizadores externos, incluindo qualquer utilizador com o `user type` atributo definido para `guest`. Esta seleção também se aplica a qualquer utilizador externo que tenha assinado a partir de uma organização diferente, como um Fornecedor de Soluções cloud (CSP). 
- Funções de diretório
   - Permite que os administradores selecionem funções específicas de diretório Azure AD usadas para determinar a atribuição. Por exemplo, as organizações podem criar uma política mais restritiva para os utilizadores que atribuíram o papel de administrador global.
- Utilizadores e grupos
   - Permite a segmentação de conjuntos específicos de utilizadores. Por exemplo, as organizações podem selecionar um grupo que contenha todos os membros do departamento de RH quando uma aplicação de RH é selecionada como a aplicação cloud. Um grupo pode ser qualquer tipo de grupo em Azure AD, incluindo grupos dinâmicos ou atribuídos de segurança e distribuição.

### <a name="preventing-administrator-lockout"></a>Prevenção do bloqueio do administrador

Para evitar que um administrador se bloqueie do seu diretório ao criar uma política aplicada a **Todos os utilizadores** e todas as **aplicações,** verá o seguinte aviso.

> Não se tranque! Recomendamos que aplique uma política a um pequeno conjunto de utilizadores primeiro para verificar se se comporta como esperado. Recomendamos também a exclusão de pelo menos um administrador desta política. Isto garante que ainda tem acesso e pode atualizar uma apólice se for necessária uma mudança. Por favor, reveja os utilizadores e aplicações afetados.

Por predefinição, a política fornecerá uma opção para excluir o utilizador atual da apólice, mas este padrão pode ser ultrapassado pelo administrador, como mostra a seguinte imagem. 

![Aviso, não se tranque!](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

## <a name="next-steps"></a>Passos seguintes

- [Acesso Condicional: Aplicações ou ações em nuvem](concept-conditional-access-cloud-apps.md)

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)
