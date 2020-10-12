---
title: Definir funções Azure para acesso administrativo Azure
titleSuffix: Azure Cognitive Search
description: Controlo de acesso baseado em funções (RBAC) no portal Azure para controlar e delegar tarefas administrativas para a gestão da Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: f0c8fe6b8df5efef0cf3948c8d628d20c79502ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88928688"
---
# <a name="set-azure-roles-for-administrative-access-to-azure-cognitive-search"></a>Definir funções Azure para acesso administrativo à Pesquisa Cognitiva Azure

O Azure fornece um [modelo global de autorização baseado em papéis](../role-based-access-control/role-assignments-portal.md) para todos os serviços geridos através do portal ou apis do Gestor de Recursos. As funções de Proprietário, Colaborador e Leitor determinam o nível de administração de *serviços* para utilizadores, grupos e princípios de segurança ativos atribuídos a cada função. 

> [!Note]
> Não existe um controlo de acesso baseado em funções (RBAC) para assegurar partes de um índice ou de um subconjunto de documentos. Para o acesso à identidade sobre os resultados da pesquisa, pode criar filtros de segurança para aparar resultados por identidade, removendo documentos para os quais o solicitador não deve ter acesso. Para obter mais informações, consulte [filtros de segurança](search-security-trimming-for-azure-search.md) e [Secure with Ative Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Tarefas de gestão por função

Para a Azure Cognitive Search, as funções estão associadas a níveis de permissão que suportam as seguintes tarefas de gestão:

| Função | Tarefa |
| --- | --- |
| Proprietário |Criar ou eliminar o serviço ou qualquer objeto no serviço, incluindo api-keys, índices, indexadores, fontes de dados indexantes e horários de indexante.<p>Ver estado de serviço, incluindo contagens e tamanho de armazenamento.<p>Adicione ou elimine a adesão de funções (apenas um Proprietário pode gerir a adesão ao papel).<p>Os administradores de subscrição e os proprietários de serviços têm adesão automática na função Desema. |
| Contribuinte |O mesmo nível de acesso que o Proprietário, menos a gestão de funções Azure. Por exemplo, um Contribuinte pode criar ou apagar objetos, ou ver e regenerar [as teclas api,](search-security-api-keys.md)mas não pode modificar os membros de funções. |
| [Papel integrado do colaborador do serviço de pesquisa](../role-based-access-control/built-in-roles.md#search-service-contributor) | Equivalente ao papel de Contribuinte. |
| Leitor |Ver o essencial do serviço e métricas. Os membros desta função não podem ver índice, indexador, fonte de dados ou informações-chave.  |

As funções não concedem direitos de acesso ao ponto final do serviço. As operações de serviço de pesquisa, tais como gestão de índices, população de índices e consultas sobre dados de pesquisa, são controladas através de api-keys, não funções. Para obter mais informações, consulte [Gerir as teclas api.](search-security-api-keys.md)

## <a name="permissions-table"></a>Tabela de permissões

A tabela seguinte resume as operações permitidas na Pesquisa Cognitiva Azure e qual a chave que desbloqueia o acesso a uma determinada operação.

| Operação | Permissões |
|-----------|-------------------------|
| Criar um serviço | Titular de subscrição Azure |
| Escalar um serviço | Chave de administração, Rbac Owner ou Contribuinte no recurso  |
| Apagar um serviço | Chave de administração, Rbac Owner ou Contribuinte no recurso |
| Criar, modificar, eliminar objetos no serviço: <br>Índices e componentes (incluindo definições de analisador, perfis de pontuação, opções DE CORS), indexadores, fontes de dados, sinónimos, sugestivos | Chave de administração, Rbac Owner ou Contribuinte no recurso |
| Consulta de um índice | Chave de administração ou consulta (RBAC não aplicável) |
| Informações do sistema de consulta, tais como estatísticas de retorno, contagem e listas de objetos | Chave de administração, RBAC sobre o recurso (Proprietário, Colaborador, Leitor) |
| Gerir as teclas de administração | Chave de administração, Rbac Proprietário ou Colaborador no recurso |
| Gerir chaves de consulta |  Chave de administração, Rbac Proprietário ou Colaborador no recurso  |

## <a name="see-also"></a>Consulte também

+ [Gerir com o PowerShell](search-manage-powershell.md) 
+ [Desempenho e otimização na Pesquisa Cognitiva Azure](search-performance-optimization.md)
+ [Começa com Role-Based Controlo de Acesso no portal Azure](../role-based-access-control/overview.md).