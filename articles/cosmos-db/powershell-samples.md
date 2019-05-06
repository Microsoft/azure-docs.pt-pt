---
title: Exemplos do Azure PowerShell para o Azure Cosmos DB
description: Exemplos do Azure PowerShell - Scripts para ajudá-lo a criar e gerir contas do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/08/2019
ms.author: mjbrown
ms.openlocfilehash: 68e845a05f4ebe2d1f25b55c00042c8925c8109e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65069306"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Exemplos do Azure PowerShell para o Azure Cosmos DB

A tabela seguinte inclui ligações para scripts de exemplo do Azure PowerShell para o Azure Cosmos DB para Core (SQL) API.

| |  |
|---|---|
|**Contas do Cosmos do Azure**||
|[Criar uma conta](scripts/powershell/sql/ps-account-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma conta API SQL do Azure Cosmos. |
|[Obter uma conta](scripts/powershell/sql/ps-account-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obter as propriedades de uma conta do Cosmos do Azure. |
|[Adicionar uma região](scripts/powershell/sql/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha uma conta do Cosmos do Azure e adicionar uma região para a lista de localizações. |
|[Alterar a prioridade de ativação pós-falha](scripts/powershell/sql/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Altere a prioridade de ativação pós-falha de uma conta do Cosmos do Azure com um acionador de ativação pós-falha manual. |
|[Etiquetas de atualização](scripts/powershell/sql/ps-account-tags-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize as etiquetas de uma conta do Cosmos do Azure. |
|[Obter chaves de contas](scripts/powershell/sql/ps-account-key-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha as chaves primárias e secundárias de uma conta do Cosmos do Azure. |
|[Voltar a gerar chaves da conta](scripts/powershell/sql/ps-account-key-regenerate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Regenere as chaves primárias e secundárias de uma conta do Cosmos do Azure. |
|[Lista cadeias de ligação](scripts/powershell/sql/ps-account-connection-string-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha as cadeias de ligação primária e secundária de uma conta do Cosmos do Azure. |
|[Criar a Firewall do IP](scripts/powershell/sql/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma Firewall do IP de uma conta do Cosmos do Azure. |
|[Eliminar uma conta do Cosmos do Azure](scripts/powershell/sql/ps-account-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Elimine uma conta do Cosmos do Azure. |
|**Bases de dados do Cosmos do Azure**||
| [Criar uma base de dados](scripts/powershell/sql/ps-database-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crie uma base de dados dentro de uma conta do Cosmos do Azure.|
| [Criar uma base de dados com débito de partilhado/nível base de dados](scripts/powershell/sql/ps-database-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crie uma base de dados do Cosmos do Azure com débito de nível de base de dados que é partilhado com seus contêineres.|
| [Liste todas as bases de dados](scripts/powershell/sql/ps-database-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Liste todas as bases de dados numa conta do Cosmos do Azure.|
| [Obter uma base de dados](scripts/powershell/sql/ps-database-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obter as propriedades de uma base de dados do Cosmos do Azure.|
|**Contentores do Azure Cosmos**||
| [Criar um contentor](scripts/powershell/sql/ps-container-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crie um contentor do Cosmos do Azure com o débito dedicado.|
| [Criar um contentor com a taxa de transferência partilhada](scripts/powershell/sql/ps-container-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crie um contentor do Cosmos do Azure com a taxa de transferência partilhada com outros contentores na base de dados.|
| [Criar um contentor com a política de índice](scripts/powershell/sql/ps-container-create-index-custom.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crie um contentor do Cosmos do Azure com uma política de índice personalizado.|
| [Criar um contentor com nenhuma política de índice](scripts/powershell/sql/ps-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crie um contentor do Cosmos do Azure com a política de índice desativada.|
| [Criar um contentor com chaves exclusivas e TTL](scripts/powershell/sql/ps-container-create-unique-key-ttl.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crie um Cosmos Azure configurado de contentor com uma restrição de chave exclusiva e time-to-live.|
| [Criar um contentor com resolução de conflitos](scripts/powershell/sql/ps-container-create-conflict-policy.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Criar um contentor do Cosmos do Azure com o último-escritor-wins entram em conflito de política de resolução.|
| [Listar todos os contentores](scripts/powershell/sql/ps-container-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Liste todos os contentores numa base de dados do Cosmos do Azure.|
| [Obter um contentor](scripts/powershell/sql/ps-container-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obter as propriedades para um contentor num banco de dados do Cosmos do Azure.|
| [Eliminar um contentor](scripts/powershell/sql/ps-container-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Elimine um contentor num banco de dados do Cosmos do Azure.|
|||