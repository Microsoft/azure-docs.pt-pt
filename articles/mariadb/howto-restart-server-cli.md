---
title: Reiniciar servidor - Azure CLI - Azure Database for MariaDB
description: Este artigo descreve como pode reiniciar uma Base de Dados Azure para servidor MariaDB utilizando o Azure CLI.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8ae69adfe83b871eb29c85fc4d03e817026ec006
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664924"
---
# <a name="restart-azure-database-for-mariadb-server-using-the-azure-cli"></a>Reinicie a base de dados de Azure para o servidor MariaDB utilizando o Azure CLI
Este tópico descreve como pode reiniciar uma Base de Dados Azure para servidor MariaDB. Pode ser necessário reiniciar o servidor por razões de manutenção, o que provoca uma falha curta à medida que o servidor executa a operação.

O reinício do servidor será bloqueado se o serviço estiver ocupado. Por exemplo, o serviço pode estar a processar uma operação previamente solicitada, como o escalar vCores.

O tempo necessário para concluir um reinício depende do processo de recuperação do MariaDB. Para diminuir o tempo de reinício, recomendamos que minimize a quantidade de atividade que ocorre no servidor antes do reinício.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia:

- Precisa de uma [base de dados Azure para o servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md).
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este artigo requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.


## <a name="restart-the-server"></a>Reiniciar o servidor

Reiniciar o servidor com o seguinte comando:

```azurecli-interactive
az mariadb server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Passos seguintes

Saiba [como definir parâmetros na Base de Dados Azure para MariaDB](howto-configure-server-parameters-cli.md)
