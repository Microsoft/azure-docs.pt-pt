---
title: Reiniciar servidor - Azure CLI - Azure Database for MySQL
description: Este artigo descreve como pode reiniciar uma Base de Dados Azure para o servidor MySQL utilizando o Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: ab2ef4315ea035d051065e1e148577fd73dfae26
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86107959"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Reinicie a base de dados do Azure para o servidor MySQL utilizando o Azure CLI
Este tópico descreve como pode reiniciar uma Base de Dados Azure para o servidor MySQL. Pode ser necessário reiniciar o servidor por razões de manutenção, o que provoca uma falha curta à medida que o servidor executa a operação.

O reinício do servidor será bloqueado se o serviço estiver ocupado. Por exemplo, o serviço pode estar a processar uma operação previamente solicitada, como o escalar vCores.

O tempo necessário para concluir um reinício depende do processo de recuperação do MySQL. Para diminuir o tempo de reinício, recomendamos que minimize a quantidade de atividade que ocorre no servidor antes do reinício.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia, precisa:
- Uma [base de dados Azure para o servidor MySQL](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Este guia de como fazer requer que utilize a versão 2.0 do Azure CLI ou posterior. Para confirmar a versão, no pedido de comando do Azure CLI, insira `az --version` . Para instalar ou atualizar, consulte [instalar o Azure CLI]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Reiniciar o servidor

Reiniciar o servidor com o seguinte comando:

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Próximos passos

Saiba [como definir parâmetros na Base de Dados Azure para o MySQL](howto-configure-server-parameters-using-cli.md)