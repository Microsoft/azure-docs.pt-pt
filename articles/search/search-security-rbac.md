---
title: Definir funções rBAC para acesso administrativo azure
titleSuffix: Azure Cognitive Search
description: Controlo administrativo baseado em funções (RBAC) no portal Azure para controlar e delegar tarefas administrativas para a gestão da Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9262d01e35bd03a9116a30b070b023f578f0b15a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112553"
---
# <a name="set-rbac-roles-for-administrative-access-to-azure-cognitive-search"></a>Definir funções rBAC para acesso administrativo à Pesquisa Cognitiva Azure

O Azure fornece um [modelo global de autorização baseado em papéis](../role-based-access-control/role-assignments-portal.md) para todos os serviços geridos através do portal ou do Gestor de Recursos APIs. As funções de proprietário, colaborador e leitor determinam o nível de administração de *serviços* para utilizadores, grupos e diretores de segurança ativos atribuídos a cada função. 

> [!Note]
> Não existem controlos de acesso baseados em papéis para assegurar porções de um índice ou um subconjunto de documentos. Para acesso baseado na identidade sobre resultados de pesquisa, pode criar filtros de segurança para aparar resultados por identidade, removendo documentos para os quais o solicitador não deve ter acesso. Para mais informações, consulte [filtros](search-security-trimming-for-azure-search.md) de segurança e [Proteja-o com Diretório Ativo](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Tarefas de gestão por função

Para a Pesquisa Cognitiva Azure, as funções estão associadas a níveis de permissão que suportam as seguintes tarefas de gestão:

| Função | Tarefa |
| --- | --- |
| Proprietário |Crie ou elimine o serviço ou qualquer objeto no serviço, incluindo api-keys, índices, indexadores, fontes de dados indexantes e horários indexantes.<p>Ver estado de serviço, incluindo contagens e tamanho de armazenamento.<p>Adicionar ou eliminar a adesão a papéis (apenas um Proprietário pode gerir a adesão ao papel).<p>Os administradores de subscrição e os proprietários de serviços têm a desmembro automático na função Deproprietários. |
| Contribuinte |Mesmo nível de acesso que o Proprietário, menos a gestão de funções RBAC. Por exemplo, um Colaborador pode criar ou apagar objetos, ou visualizar e regenerar [teclas api,](search-security-api-keys.md)mas não pode modificar membros de funções. |
| [Função incorporada do Colaborador do Serviço de Pesquisa](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Equivalente ao papel de Contribuinte. |
| Leitor |Ver o essencial e as métricas do serviço. Os membros desta função não podem ver informações indexadas, indexadas, de dados ou de informação-chave.  |

As funções não concedem direitos de acesso ao ponto final do serviço. As operações de serviço de pesquisa, tais como gestão de índices, população indexada e consultas sobre dados de pesquisa, são controladas através de api-keys, não funções. Para mais informações, consulte [Gerir teclas api](search-security-api-keys.md).

## <a name="see-also"></a>Consulte também

+ [Gerir com o PowerShell](search-manage-powershell.md) 
+ [Desempenho e otimização na Pesquisa Cognitiva Azure](search-performance-optimization.md)
+ [Inicie-se com o Controlo de Acesso baseado em Papéis no portal Azure.](../role-based-access-control/overview.md)