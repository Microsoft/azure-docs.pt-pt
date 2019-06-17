---
title: Definir funções RBAC para acesso administrativo do Azure no portal - Azure Search
description: Função de controlo administrativo baseado (RBAC) no portal do Azure para controlar e delegar tarefas administrativas para a gestão do Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 34def35eba1e5c1645e6e1f9a505704d153ac716
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61282392"
---
# <a name="set-rbac-roles-for-administrative-access"></a>Definir funções RBAC para acesso administrativo

O Azure fornece um [modelo de autorização baseada em funções globais](../role-based-access-control/role-assignments-portal.md) para todos os serviços geridos através do portal ou as APIs do Resource Manager. Funções de proprietário, Contribuidor e leitor determinam o nível de *administração de serviços* para utilizadores, grupos e entidades de segurança atribuídas a cada função do Active Directory. 

> [!Note]
> Não há nenhum controle de acesso baseado em funções para proteger as partes de um índice ou um subconjunto de documentos. Para acesso com base na identidade os resultados de pesquisa, pode criar filtros de segurança para limitar os resultados pela identidade, a remoção de documentos para o qual o requerente não deve ter acesso. Para obter mais informações, consulte [filtros de segurança](search-security-trimming-for-azure-search.md) e [seguro com o Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Tarefas de gestão por função

Para o Azure Search, as funções estão associadas a níveis de permissão que suportam as seguintes tarefas de gestão:

| Função | Tarefa |
| --- | --- |
| Owner |Criar ou eliminar o serviço ou de qualquer objeto no serviço, incluindo as chaves de api, índices, indexadores, origens de dados de indexador e agendas de indexador.<p>Ver o estado do serviço, incluindo contagens e tamanho de armazenamento.<p>Adicionar ou eliminar a associação de função (apenas um proprietário pode gerir a associação de função).<p>Os administradores da subscrição e os proprietários de serviços têm associação automática na função proprietários. |
| Contribuinte |Mesmo nível de acesso como proprietário, menos a gestão de funções do RBAC. Por exemplo, um Contribuidor pode criar ou eliminar objetos, ou ver e voltar a gerar [chaves de api](search-security-api-keys.md), mas não podem modificar associações das funções. |
| [Função incorporada de Contribuidor do serviço de pesquisa](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Equivalente à função de Contribuidor. |
| Leitor |Essentials do serviço de exibição e as métricas. Os membros desta função não podem ver o índice, indexador, origem de dados ou informações da chave.  |

Funções não conceda direitos de acesso para o ponto final de serviço. Pesquisa operações de serviço, como gestão de índices, a população do índice e consultas em dados de pesquisa, são controladas através de chaves de api, funções de não. Para obter mais informações, consulte [gerir chaves de api](search-security-api-keys.md).

## <a name="see-also"></a>Consulte também

+ [Gerir com o PowerShell](search-manage-powershell.md) 
+ [Desempenho e otimização no Azure Search](search-performance-optimization.md)
+ [Introdução ao controlo de acesso baseado em funções no portal do Azure](../role-based-access-control/overview.md).