---
title: Pesquisa e filtros grupos membros e proprietários (pré-visualização) - Diretório Ativo Azure / Microsoft Docs
description: Pesquisar e filtrar grupos membros e proprietários no portal Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/28/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a815446b79b3e5ec0a75e5d179953956643b16c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78206117"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Grupos de pesquisa e membros (pré-visualização) no Diretório Ativo do Azure

Este artigo diz-lhe como procurar membros e proprietários de um grupo e como usar filtros de pesquisa como parte da pré-visualização de melhoria de grupos no portal Azure Ative Directory (Azure AD). Existem muitas melhorias nas experiências dos grupos para ajudá-lo a gerir os seus grupos, incluindo membros e proprietários, de forma rápida e fácil. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

As alterações nesta pré-visualização incluem:

- Novos grupos procuram capacidades, tais como pesquisa de substring em nomes de grupos
- Novas opções de filtragem e triagem nas listas de membros e proprietários
- Novas capacidades de pesquisa para listas de membros e proprietários
- Contagem de grupos mais precisos para grandes grupos

## <a name="enabling-and-managing-the-preview"></a>Habilitar e gerir a pré-visualização

Facilitámos a adesão à pré-estreia:

  1. Inscreva-se no [portal Azure AD](https://portal.azure.com)e selecione **Grupos**.
  2. A partir da página Grupos – Todos os grupos, selecione o banner na parte superior da página para se juntar à pré-visualização.

Também pode consultar as funcionalidades e melhorias mais recentes selecionando informações de **Pré-visualização** na página **de Todos os grupos.** Depois de se juntar à pré-visualização, pode ver a etiqueta de pré-visualização em todas as páginas de grupos que têm melhorias e fazem parte da pré-visualização. Nem todas as páginas de grupos foram atualizadas como parte desta pré-visualização.

Se tiver algum problema, pode mudar a experiência do legado selecionando o banner no topo da página **de Todos os grupos.** Agradecemos o seu feedback para que possamos melhorar a nossa experiência.

## <a name="group-search-and-sorting"></a>Pesquisa e triagem em grupo

A pesquisa da lista de grupos foi melhorada de modo a que, `startswith` quando pode introduzir uma cadeia de pesquisa, a pesquisa execute automaticamente uma pesquisa e substring na lista de nomes de grupos. A pesquisa substring é realizada apenas em palavras inteiras, e não inclui caracteres especiais. A procura de subcordas é sensível a casos.

![novas pesquisas substring na página De Todos os Grupos](./media/groups-members-owners-search/groups-search-preview.png)

Por exemplo, uma procura por "política" irá agora devolver tanto a "política do MDM – Ocidente" como o "Grupo Político". Um grupo chamado "New_policy" não seria devolvido.

- Também pode realizar a mesma pesquisa nas listas de membros do grupo.
- Agora pode classificar a lista de grupos pelo nome usando as setas à direita da coluna de nome soro para classificar a lista em ordem ascendente ou descendente.

## <a name="group-member-search-and-filtering"></a>Pesquisa e filtragem de membros do grupo

### <a name="search-group-member-and-owner-lists"></a>Listas de membros do grupo de pesquisa e proprietários

Agora pode pesquisar os membros de um grupo específico pelo nome e realizar a mesma pesquisa na lista dos proprietários do grupo também. Na nova experiência, se introduzir uma cadeia na caixa de pesquisa, será executada automaticamente uma pesquisa. Por exemplo, uma busca por "Scott" vai devolver Scott Wilkinson.

![novas pesquisas substring sobre os membros do grupo e listas de proprietários](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>Lista de membros e proprietários de filtros

Além da pesquisa, agora pode filtrar as listas de membros e proprietários por tipo de utilizador. Esta é a informação encontrada na coluna tipo utilizador da lista. Assim, você pode filtrar a lista por membros e convidados para determinar se há algum hóspede no grupo.

### <a name="view-and-manage-membership"></a>Ver e gerir a adesão

Além de visualizar os membros diretos de um grupo específico, pode agora ver a lista de todos os membros do grupo dentro da página dos deputados. A lista de membros inclui todos os membros únicos do grupo, incluindo quaisquer membros transitórios.

Também pode pesquisar e filtrar a lista de membros diretos e a lista de todos os membros individualmente. Filtrar a lista de todos os membros não afeta os filtros que são aplicados na lista de membros diretos.

## <a name="improved-group-member-counts"></a>Contagem melhorada dos membros do grupo

Melhorámos a página de **visão geral** do grupo para fornecer contagens de membros do grupo para grupos de todos os tamanhos. Pode ver que o membro conta mesmo para grupos com mais de 1.000 membros. Pode agora ver o número total de membros diretos para um grupo e a contagem total de membros (todos os membros únicos do grupo, incluindo membros transitórios) na página **overview.**

![Maior precisão nas contagens de membros do grupo](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>Passos seguintes

Estes artigos fornecem informações adicionais sobre o trabalho com grupos em Azure AD.

- [Ver os seus grupos e membros](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Gerir associação ao grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerir regras dinâmicas dos utilizadores num grupo](groups-create-rule.md)
- [Editar as suas definições de grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerir o acesso a recursos através de grupos](../fundamentals/active-directory-manage-groups.md)
- [Gerir o acesso a aplicações SaaS através de grupos](groups-saasapps.md)
- [Gerir grupos através de comandos do PowerShell](groups-settings-v2-cmdlets.md)
- [Adicione uma subscrição Azure ao Azure Ative Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
