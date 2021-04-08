---
title: Grupos de pesquisa e filtros membros e proprietários (pré-visualização) - Azure Ative Directory | Microsoft Docs
description: Pesquisar e filtrar membros e proprietários do portal Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d78a2a4e0f14d99a7a1ecada915857f59422bb58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96547377"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Grupos de pesquisa e membros (pré-visualização) no Azure Ative Directory

Este artigo diz-lhe como procurar membros e proprietários de um grupo e como usar filtros de pesquisa como parte da pré-visualização de melhoria de grupos no portal Azure Ative Directory (Azure AD). Existem muitas melhorias nas experiências dos grupos para ajudá-lo a gerir os seus grupos, incluindo membros e proprietários, de forma rápida e fácil. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

As alterações nesta pré-visualização incluem:

- Novas capacidades de pesquisa de grupos, tais como a pesquisa de substring em nomes de grupo
- Novas opções de filtragem e triagem nas listas de membros e proprietários
- Novas capacidades de pesquisa para listas de membros e proprietários
- Contagens de grupo mais precisas para grandes grupos

## <a name="enabling-and-managing-the-preview"></a>Habilitação e gestão da pré-visualização

Facilitámos a entrada na pré-estreia:

  1. Inscreva-se no [portal AD AZure](https://portal.azure.com)e selecione **Grupos**.
  2. Na página Grupos – Todos os grupos, selecione o banner no topo da página para se juntar à pré-visualização.

Também pode consultar as funcionalidades e melhorias mais recentes selecionando **informações de pré-visualização** na página **de todos os grupos.** Depois de se juntar à pré-visualização, pode ver a etiqueta de pré-visualização em todas as páginas de grupos que têm melhorias e fazem parte da pré-visualização. Nem todas as páginas de grupos foram atualizadas como parte desta pré-visualização.

Se tiver algum problema, pode reativar a experiência do legado selecionando o banner no topo da página **de todos os grupos.** Agradecemos o seu feedback para que possamos melhorar a nossa experiência.

## <a name="group-search-and-sorting"></a>Pesquisa e triagem em grupo

A pesquisa de lista de grupos foi melhorada para que, quando pode introduzir uma cadeia de pesquisa, a pesquisa realize automaticamente uma `startswith` pesquisa e substring na lista de nomes de grupo. A pesquisa de sub-cordas é realizada apenas em palavras inteiras, e não inclui caracteres especiais. A procura de sub-mente é sensível a casos.

![novas pesquisas de substring na página de Todos os Grupos](./media/groups-members-owners-search/groups-search-preview.png)

Por exemplo, uma procura por "política" irá agora devolver tanto a "política do MDM – Ocidente" como o "Grupo político". Um grupo chamado "New_policy" não seria devolvido.

- Também pode efetuar a mesma pesquisa nas listas de membros do grupo.
- Agora pode classificar a lista de grupos pelo nome usando as setas à direita da coluna de nomes que se dirige para classificar a lista em ordem ascendente ou descendente.

## <a name="group-member-search-and-filtering"></a>Pesquisa e filtragem de membros do grupo

### <a name="search-group-member-and-owner-lists"></a>Listas de membros do grupo de pesquisa e de proprietários

Agora pode pesquisar os membros de um grupo específico pelo nome, e realizar a mesma pesquisa na lista de proprietários do grupo também. Na nova experiência, se introduzir uma corda na caixa de pesquisa, uma pesquisa começa automaticamente realizada. Por exemplo, uma busca por "Scott" vai devolver Scott Wilkinson.

![novas pesquisas de substring nas listas de membros do grupo e proprietários](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>Lista de membros do filtro e proprietários

Além de pesquisar, agora pode filtrar as listas de membros e proprietários por tipo de utilizador. Esta é a informação encontrada na coluna Tipo utilizador da lista. Assim, você pode filtrar a lista por membros e convidados para determinar se há algum hóspede no grupo.

### <a name="view-and-manage-membership"></a>Ver e gerir a adesão

Além de ver os membros diretos de um grupo específico, pode agora ver a lista de todos os membros do grupo na página dos membros. A lista de membros inclui todos os membros únicos do grupo, incluindo quaisquer membros transitórios.

Também pode pesquisar e filtrar a lista de membros diretos e a lista de todos os membros individualmente. A filtragem da lista de todos os membros não afeta os filtros que são aplicados na lista de membros diretos.

## <a name="improved-group-member-counts"></a>Contagem de membros melhorados do grupo

Melhorámos a página **geral** do grupo para fornecer contagem de membros do grupo para grupos de todos os tamanhos. Pode ver que o membro conta mesmo para grupos com mais de 1.000 membros. Pode agora ver o número total de membros diretos de um grupo e a contagem total de membros (todos os membros únicos do grupo, incluindo membros transitórios) na página **'Visão Geral'.**

![Maior precisão nas contagens de membros do grupo](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>Passos seguintes

Estes artigos fornecem informações adicionais sobre o trabalho com grupos em Azure AD.

- [Ver os seus grupos e membros](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Gerir associação ao grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerir regras dinâmicas dos utilizadores num grupo](groups-create-rule.md)
- [Editar as suas definições de grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerir o acesso a recursos através de grupos](../fundamentals/active-directory-manage-groups.md)
- [Gerir o acesso a aplicações SaaS através de grupos](groups-saasapps.md)
- [Gerir grupos através de comandos do PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md)
- [Adicione uma assinatura Azure ao Azure Ative Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
