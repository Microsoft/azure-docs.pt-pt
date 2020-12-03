---
title: Utilizadores, grupos, licenciamento e funções no Azure Ative Directory
description: A relação entre utilizadores e licenças atribuídas, funções de administrador, adesão a grupos no Azure Active Directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.reviewer: krbain
ms.date: 12/02/2020
ms.topic: overview
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
services: active-directory
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce4330b72ef7e1440689e0ec4af9009f551553a3
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547275"
---
# <a name="what-is-enterprise-user-management"></a>O que é a gestão de utilizadores da empresa?

Este artigo apresenta o administrador do Azure AD relativamente às principais tarefas de [gestão de identidades](../fundamentals/active-directory-whatis.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) para os utilizadores, em termos dos respetivos grupos, licenças, aplicações empresariais implementadas e funções de administrador. À medida que a sua organização cresce, pode utilizar grupos e funções de administrador do Azure AD para:

* Atribuir licenças a grupos em vez de individualmente
* Delegar permissões para distribuir o trabalho de gestão do Azure AD por funções com menos privilégios
* Atribuir acesso a aplicações empresariais a grupos

## <a name="assign-users-to-groups"></a>Atribuir utilizadores a grupos

Pode utilizar os grupos no Azure AD para atribuir licenças a um grande número de utilizadores ou atribuir acesso de utilizador a aplicações empresariais implementadas. Pode utilizar grupos para atribuir todas as funções de administrador, exceto para Administrador Global em AD AZure, ou pode conceder acesso a recursos externos, tais como aplicações SaaS ou sites SharePoint.

Para obter flexibilidade adicional e reduzir o trabalho de gestão da adesão a grupos, pode utilizar [grupos dinâmicos](groups-create-rule.md) no Azure AD para expandir e efetuar automaticamente a adesão a grupos. Precisará de uma licença do Azure AD Premium P1 para cada utilizador exclusivo que seja membro de um ou mais grupos dinâmicos.

## <a name="assign-licenses-to-groups"></a>Atribuir licenças a grupos

Atribuir ou remover licenças de utilizadores individualmente pode exigir tempo e atenção. Se [atribuir licenças a grupos](../fundamentals/license-users-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) em vez disso, pode facilitar a gestão de licenças em grande escala.

No Azure AD, quando os utilizadores aderem a um grupo licenciado, são-lhes automaticamente atribuídas as licenças adequadas. Quando os utilizadores saem do grupo, o Azure AD remove as respetivas atribuições de licenças. Sem os grupos do Azure AD, teria de escrever um script do PowerShell ou utilizar o Graph API para adicionar ou remover licenças de utilizador em massa dos utilizadores que aderem à organização ou saem dela.

Se não existem licenças suficientes disponíveis ou se ocorrer um problema, como planos de serviço que não podem ser atribuídos ao mesmo tempo, pode ver o estado de qualquer problema de licenciamento do grupo no portal do Azure.

>[!NOTE]
>Esta funcionalidade de licenciamento baseado no grupo está atualmente em pré-visualização pública. Durante a pré-visualização, a funcionalidade está disponível com qualquer plano de licença pago ou versão de avaliação do Azure Active Directory (Azure AD).

## <a name="delegate-administrator-roles"></a>Delegar funções de administrador

Muitas organizações grandes querem opções para que os seus utilizadores obtenham permissões suficientes para as tarefas de trabalho sem atribuir a poderosa função de Administrador Global, por exemplo, a utilizadores que têm de registar aplicações. Eis um exemplo das novas funções de administrador do Azure AD para ajudá-lo a distribuir o trabalho de gestão de aplicações com mais granularidade:

 Nome da função | Resumo das permissões
 --------- | -------------------
 **Administrador de Aplicações** | Pode adicionar e gerir aplicações empresariais e registos de aplicações, e configurar as definições de proxy de aplicações. Os Administradores de Aplicações podem ver as políticas e dispositivos de Acesso Condicional, mas não geri-los.
 **Administrador de Aplicações na Cloud** | Pode adicionar e gerir aplicações empresariais e registos de aplicações empresariais. Esta função tem todas as permissões do administrador do aplicações, exceto que não é possível gerir as definições de proxy de aplicações.
**Programador de Aplicações** | Pode adicionar e atualizar os registos de aplicações, mas não pode gerir aplicações empresariais nem configurar um proxy de aplicação.

Estão a ser adicionadas novas funções de administrador do Azure AD. Consulte o portal do Azure ou a [referência de permissões de funções de administrador](../roles/permissions-reference.md) para obter as funções disponíveis atualmente.

## <a name="assign-app-access"></a>Atribuir acesso a aplicações

Você pode usar Azure AD para atribuir acesso de grupo às [aplicações empresariais que são implementadas na sua organização AZure AD](../manage-apps/assign-user-or-group-access-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context). Se combinar grupos dinâmicos com a atribuição de grupos a aplicações, pode automatizar as atribuições de acesso a aplicações de utilizador, à medida que a sua organização cresce. Precisará de uma licença do Azure Active Directory Premium P1 ou Premium P2 para atribuir acesso a aplicações empresariais.

O Azure AD também oferece um controle granular dos dados que fluem entre a aplicação e os grupos aos quais atribuir acesso. Em [Aplicações Empresariais](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps), abra uma aplicação e selecione **Aprovisionamento** para:

* Configurar o aprovisionamento automático das aplicações que o suportam
* Fornecer credenciais para ligar à API de gestão de utilizadores da aplicação
* Configurar os mapeamentos que controlam os atributos de utilizador que fluem entre o Azure AD e a aplicação, quando as contas de utilizador são aprovisionadas ou atualizadas
* Iniciar e parar o serviço de aprovisionamento do Azure AD para uma aplicação, limpar a cache de ou reiniciar o serviço
* Ver o **Relatório de atividades de aprovisionamento** que fornece um registo de todos os utilizadores e grupos criados, atualizados e removidos entre o Azure AD e a aplicação, e o **relatório de erros de aprovisionamento** que fornece mensagens de erro mais detalhadas

## <a name="next-steps"></a>Passos seguintes

Se for um administrador principiante do Azure AD, conheça os princípios básicos em [Noções Básicas do Azure Active Directory](../fundamentals/index.yml).

Ou pode começar a [criar grupos](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context), [atribuir licenças](../fundamentals/license-users-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context), [atribuir acesso a aplicações](../manage-apps/assign-user-or-group-access-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) ou [atribuir funções de administrador](../roles/permissions-reference.md).