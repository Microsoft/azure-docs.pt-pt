---
title: Definir funções RBAC para acesso administrativo do Azure
titleSuffix: Azure Cognitive Search
description: RBAC (controle administrativo baseado em função) no portal do Azure para controlar e delegar tarefas administrativas para o gerenciamento de Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9262d01e35bd03a9116a30b070b023f578f0b15a
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112553"
---
# <a name="set-rbac-roles-for-administrative-access-to-azure-cognitive-search"></a>Definir funções RBAC para acesso administrativo ao Azure Pesquisa Cognitiva

O Azure fornece um [modelo de autorização baseado em função global](../role-based-access-control/role-assignments-portal.md) para todos os serviços gerenciados por meio do portal ou de APIs do Resource Manager. As funções proprietário, colaborador e leitor determinam o nível de *Administração de serviço* para Active Directory usuários, grupos e entidades de segurança atribuídos a cada função. 

> [!Note]
> Não há controles de acesso baseados em função para proteger partes de um índice ou um subconjunto de documentos. Para obter acesso baseado em identidade nos resultados da pesquisa, você pode criar filtros de segurança para cortar os resultados por identidade, removendo documentos para os quais o solicitante não deve ter acesso. Para obter mais informações, consulte [filtros de segurança](search-security-trimming-for-azure-search.md) e [proteger com Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Tarefas de gerenciamento por função

Para Pesquisa Cognitiva do Azure, as funções são associadas a níveis de permissão que dão suporte às seguintes tarefas de gerenciamento:

| Função | Tarefa |
| --- | --- |
| Proprietário |Crie ou exclua o serviço ou qualquer objeto no serviço, incluindo chaves de API, índices, indexadores, fontes de dados do indexador e agendas do indexador.<p>Exibir status do serviço, incluindo contagens e tamanho do armazenamento.<p>Adicionar ou excluir Associação de função (somente um proprietário pode gerenciar a associação de função).<p>Os administradores de assinatura e os proprietários de serviço têm associação automática na função proprietários. |
| Contribuinte |Mesmo nível de acesso como proprietário, menos gerenciamento de função RBAC. Por exemplo, um colaborador pode criar ou excluir objetos ou exibir e regenerar [chaves de API](search-security-api-keys.md), mas não pode modificar associações de função. |
| [Função interna de colaborador Serviço de Pesquisa](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Equivalente à função de colaborador. |
| Leitor |Exiba as métricas e conceitos básicos do serviço. Os membros dessa função não podem exibir informações de índice, indexador, fonte de dados ou chave.  |

As funções não concedem direitos de acesso ao ponto de extremidade de serviço. As operações de serviço de pesquisa, como gerenciamento de índice, preenchimento de índice e consultas em dados de pesquisa, são controladas por meio de chaves de API, não funções. Para obter mais informações, consulte [gerenciar API-Keys](search-security-api-keys.md).

## <a name="see-also"></a>Ver também

+ [Gerir com o PowerShell](search-manage-powershell.md) 
+ [Desempenho e otimização no Azure Pesquisa Cognitiva](search-performance-optimization.md)
+ [Introdução ao controle de acesso baseado em função no portal do Azure](../role-based-access-control/overview.md).