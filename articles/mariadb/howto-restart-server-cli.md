---
title: Servidor de reinício - Azure CLI - Base de Dados Azure para MariaDB
description: Este artigo descreve como pode reiniciar uma Base de Dados Azure para o servidor MariaDB utilizando o Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: f5572611b99245fd62b4e0a9d73e6ed728e42f5b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79530653"
---
# <a name="restart-azure-database-for-mariadb-server-using-the-azure-cli"></a>Reiniciar base de dados Azure para servidor MariaDB utilizando o Azure CLI
Este tópico descreve como pode reiniciar uma Base de Dados Azure para o servidor MariaDB. Pode ser necessário reiniciar o servidor por razões de manutenção, o que provoca uma pequena paragem à medida que o servidor executa a operação.

O reinício do servidor será bloqueado se o serviço estiver ocupado. Por exemplo, o serviço pode estar a processar uma operação previamente solicitada, como a escala vCores.

O tempo necessário para concluir um reinício depende do processo de recuperação do MariaDB. Para diminuir o tempo de reinício, recomendamos que minimize a quantidade de atividade que ocorre no servidor antes do reinício.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia de como orientar, precisa de:
- Uma [Base de Dados Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Este guia de como fazer requer que utilize a versão 2.0 do Azure CLI ou posterior. Para confirmar a versão, no aviso de `az --version`comando Azure CLI, introduza . Para instalar ou atualizar, consulte [Instalar o Azure CLI]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Reiniciar o servidor

Reiniciar o servidor com o seguinte comando:

```azurecli-interactive
az mariadb server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Passos seguintes

Saiba [como definir parâmetros na Base de Dados Azure para MariaDB](howto-configure-server-parameters-cli.md)