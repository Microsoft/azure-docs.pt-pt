---
title: Visão geral do controlo de acesso baseado em funções do Azure Ative Directory (RBAC)
description: Saiba como entender as partes de uma atribuição de funções e âmbito restrito no Azure Ative Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/20/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b66b004a0371b76f82d525972feefab9c89efb2d
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742051"
---
# <a name="overview-of-role-based-access-control-in-azure-active-directory"></a>Visão geral do controlo de acesso baseado em funções no Azure Ative Directory

Este artigo descreve como compreender o controlo de acesso baseado em funções do Azure Ative Directory (Azure AD). As funções AD AD permitem-lhe conceder permissões granulares aos seus administradores, cumprindo o princípio do menor privilégio. As funções azure AD incorporadas e personalizadas operam em conceitos semelhantes aos que encontrará no sistema de controlo de acesso baseado em [funções para recursos Azure](../../role-based-access-control/overview.md) (funções Azure). A [diferença entre estes dois sistemas de controlo de acesso baseados em funções](../../role-based-access-control/rbac-and-directory-admin-roles.md) é:

- As funções AD do Azure controlam o acesso aos recursos da AD do Azure, tais como utilizadores, grupos e aplicações que utilizam a API do gráfico
- As funções Azure controlam o acesso a recursos Azure, tais como máquinas virtuais ou armazenamento utilizando a Azure Resource Management

Ambos os sistemas contêm definições de funções igualmente utilizadas e atribuições de funções. No entanto, as permissões de papel da AD Azure não podem ser usadas em funções personalizadas Azure e vice-versa.

## <a name="understand-azure-ad-role-based-access-control"></a>Compreenda o controlo de acesso baseado em funções da Azure AD
A Azure AD suporta 2 tipos de definições de funções - 
* [Funções incorporadas](./permissions-reference.md)
* [Funções personalizadas](./custom-create.md)

As funções incorporadas estão fora das funções de caixa que têm um conjunto fixo de permissões. Estas definições de papel não podem ser modificadas. Há muitos [papéis incorporados](./permissions-reference.md) que a Azure AD suporta, e a lista está a crescer. Para arredondar as bordas e satisfazer os seus requisitos sofisticados, a Azure AD também suporta [funções personalizadas.](./custom-create.md) Conceder permissão usando funções Azure AD personalizadas é um processo em duas etapas que envolve criar uma definição de papel personalizada e, em seguida, atribuí-la usando uma atribuição de papel. Uma definição de função personalizada é uma coleção de permissões que você adiciona a partir de uma lista pré-configurada. Estas permissões são as mesmas permissões usadas nas funções incorporadas.  

Uma vez criada a definição de papel personalizada (ou usando um papel incorporado), pode atribuí-la a um utilizador criando uma atribuição de funções. Uma atribuição de funções concede ao utilizador as permissões numa definição de função num âmbito especificado. Este processo em duas etapas permite-lhe criar uma definição de papel única e atribuí-la muitas vezes em diferentes âmbitos. Um âmbito define o conjunto de recursos Azure AD a que o membro tem acesso. O âmbito mais comum é o âmbito de organização (org-wide). Um papel personalizado pode ser atribuído no âmbito org-wide, o que significa que o membro do papel tem o papel permissões sobre todos os recursos na organização. Uma função personalizada também pode ser atribuída no âmbito do objeto. Um exemplo de um âmbito de objeto seria uma única aplicação. A mesma função pode ser atribuída a um utilizador em todas as aplicações da organização e depois a outro utilizador com âmbito apenas da app Contoso Expense Reports.  

As funções azure AD incorporadas e personalizadas operam em conceitos semelhantes ao controlo de acesso baseado em [funções Azure (Azure RBAC)](../develop/access-tokens.md#payload-claims). A [diferença entre estes dois sistemas de controlo de acesso baseados em funções](../../role-based-access-control/rbac-and-directory-admin-roles.md) é que o Azure RBAC controla o acesso aos recursos Azure, tais como máquinas virtuais ou armazenamento usando a Azure Resource Management, e as funções personalizadas AZURE controlam o acesso aos recursos Azure AD usando a API do gráfico. Ambos os sistemas alavancam o conceito de definições de funções e atribuições de funções. As permissões Azure AD RBAC não podem ser incluídas em funções de Azure e vice-versa.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Como a Azure AD determina se um utilizador tem acesso a um recurso

Seguem-se os passos de alto nível que o Azure AD utiliza para determinar se tem acesso a um recurso de gestão. Utilize estas informações para resolver problemas de acesso.

1. Um utilizador (ou principal de serviço) adquire um símbolo para o microsoft Graph ou Azure AD Graph.
1. O utilizador faz uma chamada da API para o Azure Ative Directory (Azure AD) através do Microsoft Graph ou Azure AD Graph utilizando o token emitido.
1. Dependendo das circunstâncias, a Azure AD toma uma das seguintes ações:
   - Avalia as funções do utilizador com base na [alegação de wids](../../active-directory-b2c/access-tokens.md) no token de acesso do utilizador.
   - Recupera todas as atribuições de funções que se candidatam ao utilizador, direta ou através da adesão ao grupo, ao recurso em que as medidas estão a ser tomadas.
1. A Azure AD determina se a ação na chamada API está incluída nas funções que o utilizador tem para este recurso.
1. Se o utilizador não tiver um papel na ação no âmbito solicitado, o acesso não é concedido. Caso contrário, o acesso é concedido.

## <a name="role-assignment"></a>Atribuição de função

Uma atribuição de funções é um recurso AD Azure que anexa uma *definição* de papel a um *utilizador* num *âmbito* específico para conceder acesso aos recursos Azure AD. O acesso é concedido ao criar uma atribuição de função e o acesso é revogado ao remover uma atribuição de função. No fundo, uma função é composta por três elementos:

- Utilizador do Azure Active Directory
- Definição de função
- Âmbito do recurso

Pode [criar atribuições de funções](custom-create.md) utilizando o portal Azure, Azure AD PowerShell ou API de gráficos. Também pode [ver as atribuições para um papel personalizado.](custom-view-assignments.md#view-the-assignments-of-a-role)

O diagrama seguinte mostra um exemplo de uma atribuição de função. Neste exemplo, Chris Green foi atribuído ao administrador de registo da App papel personalizado no âmbito do registo da aplicação Contoso Widget Builder. A atribuição concede ao Chris as permissões da função de administrador de registo da App apenas para este registo específico da aplicação.

![Atribuição de funções é como as permissões são aplicadas e tem três partes](./media/custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Principal de segurança

Um principal de segurança representa o utilizador que deve ser atribuído ao acesso aos recursos Azure AD. Um utilizador é um indivíduo que tem um perfil de utilizador no Azure Ative Directory.

### <a name="role"></a>Função

Uma definição de papel, ou papel, é uma coleção de permissões. Uma definição de papel lista as operações que podem ser realizadas em recursos AD Azure, tais como criar, ler, atualizar e apagar. Existem dois tipos de papéis em Azure AD:

- Papéis incorporados criados pela Microsoft que não podem ser alterados.
- Papéis personalizados criados e geridos pela sua organização.

### <a name="scope"></a>Âmbito

Um âmbito é a restrição de ações permitidas a um determinado recurso AD Azure como parte de uma atribuição de funções. Quando atribui uma função, pode especificar um âmbito que limita o acesso do administrador a um recurso específico. Por exemplo, se pretender conceder a um desenvolvedor um papel personalizado, mas apenas para gerir um registo específico de candidatura, pode incluir o registo específico de candidatura como âmbito na atribuição de funções.

## <a name="required-license-plan"></a>Plano de licença exigido

A utilização de funções incorporadas no Azure AD é gratuita, enquanto as funções personalizadas requerem uma licença Azure AD Premium P1. Para encontrar a licença certa para os seus requisitos, veja [Comparação das funcionalidades disponíveis geralmente das edições Gratuita, Básica e Premium](https://azure.microsoft.com/pricing/details/active-directory).

## <a name="next-steps"></a>Próximos passos

- [Compreender as funções do Azure AD](concept-understand-roles.md)
- Crie atribuições de funções personalizadas utilizando [o portal Azure AD PowerShell e API de gráficos](custom-create.md)
- [Ver as atribuições para um papel personalizado](custom-view-assignments.md)