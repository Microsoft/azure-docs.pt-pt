---
title: Definir funções Azure para acesso administrativo Azure
titleSuffix: Azure Cognitive Search
description: Controlo de acesso baseado em funções (Azure RBAC) no portal Azure para controlar e delegar tarefas administrativas para a gestão da Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: ee122727100ec0abad0dfe93b9e5f1be0276cb8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92519504"
---
# <a name="set-azure-roles-for-administrative-access-to-azure-cognitive-search"></a>Definir funções Azure para acesso administrativo à Pesquisa Cognitiva Azure

O Azure fornece um [modelo global de autorização baseado em papéis](../role-based-access-control/role-assignments-portal.md) para todos os serviços geridos através do portal ou apis do Gestor de Recursos. As funções de Proprietário, Colaborador e Leitor determinam o nível de administração de *serviços* para utilizadores, grupos e princípios de segurança ativos atribuídos a cada função. 

> [!Note]
> Não existe um controlo de acesso baseado em funções (Azure RBAC) para assegurar conteúdos no serviço. Utilizará uma chave API de administração ou consultará a chave API para pedidos autenticados para o próprio serviço. Para o acesso à identidade sobre os resultados da pesquisa, pode criar filtros de segurança para aparar resultados por identidade, removendo documentos para os quais o solicitador não deve ter acesso. Para obter mais informações, consulte [os filtros de segurança.](search-security-trimming-for-azure-search.md)

## <a name="management-tasks-by-role"></a>Tarefas de gestão por função

Para a Azure Cognitive Search, as funções estão associadas a níveis de permissão que suportam as seguintes tarefas de gestão:

| Função | Tarefa |
| --- | --- |
| Proprietário |Criar ou eliminar o serviço ou qualquer objeto no serviço, incluindo api-keys, índices, indexadores, fontes de dados indexantes e horários de indexante.<p>Ver estado de serviço, incluindo contagens e tamanho de armazenamento.<p>Adicione ou elimine a adesão de funções (apenas um Proprietário pode gerir a adesão ao papel).<p>Os administradores de subscrição e os proprietários de serviços têm adesão automática na função Desema. |
| Contribuinte | O mesmo nível de acesso que o Proprietário, menos a gestão de funções Azure. Por exemplo, um Contribuinte pode criar ou apagar objetos, ou ver e regenerar [as teclas api,](search-security-api-keys.md)mas não pode modificar os membros de funções.<br><br>[O Contribuinte do Serviço de Pesquisa](../role-based-access-control/built-in-roles.md#search-service-contributor) é equivalente ao papel genérico do Contribuinte incorporado. |
| Leitor |Ver o essencial do serviço, como o ponto final do serviço, subscrição, grupo de recursos, região, nível e capacidade. Também pode ver métricas de serviço, como consultas médias por segundo, no separador Monitor. Os membros desta função não podem ver informações de índice, indexante, fonte de dados ou skillset. Isto inclui dados de utilização para esses objetos, tais como quantos índices existem no serviço. |

As funções não concedem direitos de acesso ao ponto final do serviço. As operações de serviço de pesquisa, tais como gestão de índices, população de índices e consultas sobre dados de pesquisa, são controladas através de api-keys, não funções. Para obter mais informações, consulte [Gerir as teclas api.](search-security-api-keys.md)

## <a name="permissions-table"></a>Tabela de permissões

A tabela seguinte resume as operações permitidas na Pesquisa Cognitiva Azure e qual a chave que desbloqueia o acesso a uma determinada operação.

As permissões Azure RBAC aplicam-se às operações do portal e à gestão de serviços (criar, eliminar ou alterar um serviço ou as suas chaves API). As chaves API são criadas após a existência de um serviço e aplicam-se às operações de conteúdo do serviço. Adicionalmente, para operações relacionadas com o conteúdo no portal, tais como a criação ou eliminação de objetos, um Proprietário ou Colaborador Azure RBAC interage com o serviço com uma chave API de administrador implícita.

| Operação | Controlado por |
|-----------|-------------------------|
| Criar um serviço | Permissões Azure RBAC: Proprietário ou Colaborador |
| Escalar um serviço | Permissões Azure RBAC: Proprietário ou Colaborador|
| Apagar um serviço | Permissões Azure RBAC: Proprietário ou Colaborador |
| Gerir chaves de administração ou consulta | Permissões Azure RBAC: Proprietário ou Colaborador|
| Ver informações de serviço no portal ou numa API de gestão | Permissões Azure RBAC: Proprietário, Colaborador ou Leitor  |
| Ver informações e métricas de objetos no portal ou numa API de gestão | Permissões Azure RBAC: Proprietário ou Colaborador |
| Criar, modificar, eliminar objetos no serviço: <br>Índices e componentes (incluindo definições de analisador, perfis de pontuação, opções DE CORS), indexadores, fontes de dados, sinónimos, sugestivos | Chave de administração se utilizar um API, Proprietário ou Colaborador Azure RBAC se utilizar o portal |
| Consulta de um índice | Administrador ou chave de consulta se utilizar um API, Proprietário ou Colaborador Azure RBAC se utilizar o portal |
| Informação do sistema de consulta sobre objetos, tais como estatísticas de retorno, contagem e listas de objetos | Chave de administração se utilizar um API, Proprietário ou Colaborador Azure RBAC se utilizar o portal |

## <a name="next-steps"></a>Passos seguintes

+ [Gerir com o PowerShell](search-manage-powershell.md) 
+ [Desempenho e otimização na Pesquisa Cognitiva Azure](search-performance-optimization.md)
+ [O que é o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md).
