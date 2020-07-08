---
title: Reiniciar servidor - Azure CLI - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como pode reiniciar uma Base de Dados Azure para PostgreSQL - Servidor Único utilizando o Azure CLI
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 13b26b545f9e95ce2457e4f8d9cf32da59cd91e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74770157"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Reinicie a base de dados de Azure para postgreSQL - Servidor Único utilizando o Azure CLI
Este tópico descreve como pode reiniciar uma Base de Dados Azure para servidor PostgreSQL. Pode ser necessário reiniciar o servidor por razões de manutenção, o que provoca uma falha curta à medida que o servidor executa a operação.

O reinício do servidor será bloqueado se o serviço estiver ocupado. Por exemplo, o serviço pode estar a processar uma operação previamente solicitada, como o escalar vCores.
 
O tempo necessário para concluir um reinício depende do processo de recuperação postgreSQL. Para diminuir o tempo de reinício, recomendamos que minimize a quantidade de atividade que ocorre no servidor antes do reinício.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia, precisa:
- Uma [base de dados Azure para servidor PostgreSQL](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Este guia de como fazer requer que utilize a versão 2.0 do Azure CLI ou posterior. Para confirmar a versão, no pedido de comando do Azure CLI, insira `az --version` . Para instalar ou atualizar, consulte [instalar o Azure CLI]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Reiniciar o servidor

Reiniciar o servidor com o seguinte comando:

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Próximos passos

Saiba [como definir parâmetros na Base de Dados Azure para PostgreSQL](howto-configure-server-parameters-using-cli.md)