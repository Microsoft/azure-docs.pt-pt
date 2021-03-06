---
title: Utilizadores e grupos na política de acesso condicional - Azure Ative Directory
description: Quem são utilizadores e grupos numa política de acesso condicional Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e1f4c7272c3db3b1e4cd834a621b66f519c6f69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952818"
---
# <a name="conditional-access-users-and-groups"></a>Acesso Condicional: Utilizadores e grupos

Uma política de Acesso Condicional deve incluir uma atribuição do utilizador como um dos sinais no processo de decisão. Os utilizadores podem ser incluídos ou excluídos das políticas de Acesso Condicional. O Azure Ative Directory avalia todas as políticas e garante que todos os requisitos são cumpridos antes de conceder acesso ao utilizador. 

> [!VIDEO https://www.youtube.com/embed/5DsW1hB3Jqs]

## <a name="include-users"></a>Incluir utilizadores

Esta lista de utilizadores normalmente inclui todos os utilizadores que uma organização está a direcionar numa política de Acesso Condicional. 

As seguintes opções estão disponíveis para incluir ao criar uma política de acesso condicional.

- Nenhum
   - Nenhum utilizadores selecionados
- Todos os utilizadores
   - Todos os utilizadores que existam no diretório, incluindo os hóspedes B2B.
- Selecionar utilizadores e grupos
   - Todos os utilizadores convidados e externos
      - Esta seleção inclui quaisquer hóspedes B2B e utilizadores externos, incluindo qualquer utilizador com o `user type` atributo definido para `guest` . Esta seleção também se aplica a qualquer utilizador externo inscrito numa organização diferente como um Cloud Solution Provider (CSP). 
   - Funções de diretório
      - Permite que os administradores selecionem funções específicas de diretório Azure AD incorporadas usadas para determinar a atribuição de políticas. Por exemplo, as organizações podem criar uma política mais restritiva para os utilizadores atribuídos ao papel de administrador global. Outros tipos de funções não são suportados, incluindo funções administrativas e funções personalizadas.
   - Utilizadores e grupos
      - Permite direcionar conjuntos específicos de utilizadores. Por exemplo, as organizações podem selecionar um grupo que contenha todos os membros do departamento de RH quando uma aplicação HR é selecionada como a aplicação cloud. Um grupo pode ser qualquer tipo de grupo em Azure AD, incluindo grupos dinâmicos ou atribuídos de segurança e distribuição. A política será aplicada aos utilizadores e grupos aninhados.

> [!IMPORTANT]
> Ao selecionar quais utilizadores e grupos estão incluídos numa Política de Acesso Condicional, existe um limite para o número de utilizadores individuais que podem ser adicionados diretamente a uma política de Acesso Condicional. Se houver uma grande quantidade de utilizadores individuais que são necessários para serem adicionados diretamente a uma política de Acesso Condicional, recomendamos colocar os utilizadores em um grupo, e atribuir o grupo à política de Acesso Condicional.

> [!WARNING]
> Se os utilizadores ou grupos forem membros de mais de 2048 grupos, o seu acesso pode ser bloqueado. Este limite aplica-se tanto à adesão direta como a um grupo aninhado.

> [!WARNING]
> As políticas de Acesso Condicional não suportam os utilizadores atribuídos a uma função de diretório [a uma unidade administrativa](../roles/admin-units-assign-roles.md) ou a funções de diretórios dirigidas diretamente a um objeto, como através de [funções personalizadas](../roles/custom-create.md).

## <a name="exclude-users"></a>Excluir utilizadores

Quando as organizações incluem e excluem um utilizador ou grupo, o utilizador ou grupo é excluído da política, uma vez que uma ação de exclusão substitui um incluído na política. As exclusões são geralmente utilizadas para acesso de emergência ou contas de break-glass. Mais informações sobre contas de acesso de emergência e por que são importantes podem ser encontradas nos seguintes artigos: 

* [Gerir contas de acesso de emergência no AAD](../roles/security-emergency-access.md)
* [Crie uma estratégia resiliente de gestão de controlo de acessos com o Azure Ative Directory](../authentication/concept-resilient-controls.md)

As seguintes opções estão disponíveis para excluir ao criar uma política de acesso condicional.

- Todos os utilizadores convidados e externos
   - Esta seleção inclui quaisquer hóspedes B2B e utilizadores externos, incluindo qualquer utilizador com o `user type` atributo definido para `guest` . Esta seleção também se aplica a qualquer utilizador externo inscrito numa organização diferente como um Cloud Solution Provider (CSP). 
- Funções de diretório
   - Permite que os administradores selecionem funções específicas de diretório Azure AD usados para determinar a atribuição. Por exemplo, as organizações podem criar uma política mais restritiva para os utilizadores atribuídos ao papel de administrador global.
- Utilizadores e grupos
   - Permite direcionar conjuntos específicos de utilizadores. Por exemplo, as organizações podem selecionar um grupo que contenha todos os membros do departamento de RH quando uma aplicação HR é selecionada como a aplicação cloud. Um grupo pode ser qualquer tipo de grupo em Azure AD, incluindo grupos dinâmicos ou atribuídos de segurança e distribuição.

### <a name="preventing-administrator-lockout"></a>Prevenção do bloqueio do administrador

Para evitar que um administrador se bloqueie fora do seu diretório ao criar uma política aplicada a **todos os utilizadores** e **todas as aplicações**, verá o seguinte aviso.

> Não se tranque! Recomendamos aplicar uma política a um pequeno conjunto de utilizadores primeiro para verificar se se comporta como esperado. Recomendamos também a exclusão de pelo menos um administrador desta política. Isto garante que ainda tem acesso e pode atualizar uma política se for necessária uma alteração. Por favor, reveja os utilizadores e aplicações afetados.

Por predefinição, a política fornecerá uma opção para excluir o utilizador atual da apólice, mas este padrão pode ser ultrapassado pelo administrador como mostrado na imagem seguinte. 

![Aviso, não se tranque fora!](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

Se te encontrares trancado,[o que fazer se estiveres trancado fora do portal Azure?](troubleshoot-conditional-access.md#what-to-do-if-you-are-locked-out-of-the-azure-portal)

## <a name="next-steps"></a>Passos seguintes

- [Acesso Condicional: aplicativos ou ações na nuvem](concept-conditional-access-cloud-apps.md)

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)
