---
title: Reiniciar servidor - Azure CLI - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como pode reiniciar uma Base de Dados Azure para PostgreSQL - Servidor Único utilizando o Azure CLI
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: f9881a4517f77587cecb2dcd04befaddb523965b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647652"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Reinicie a base de dados de Azure para postgreSQL - Servidor Único utilizando o Azure CLI
Este tópico descreve como pode reiniciar uma Base de Dados Azure para servidor PostgreSQL. Pode ser necessário reiniciar o servidor por razões de manutenção, o que provoca uma falha curta à medida que o servidor executa a operação.

O reinício do servidor será bloqueado se o serviço estiver ocupado. Por exemplo, o serviço pode estar a processar uma operação previamente solicitada, como o escalar vCores.
 
O tempo necessário para concluir um reinício depende do processo de recuperação postgreSQL. Para diminuir o tempo de reinício, recomendamos que minimize a quantidade de atividade que ocorre no servidor antes do reinício.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia:
- Criar uma [base de dados Azure para servidor PostgreSQL](quickstart-create-server-up-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este artigo requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="restart-the-server"></a>Reiniciar o servidor

Reiniciar o servidor com o seguinte comando:

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Próximos passos

Saiba [como definir parâmetros na Base de Dados Azure para PostgreSQL](howto-configure-server-parameters-using-cli.md)
