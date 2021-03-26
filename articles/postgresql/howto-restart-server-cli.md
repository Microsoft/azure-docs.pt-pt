---
title: Reiniciar servidor - Azure CLI - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como pode reiniciar uma Base de Dados Azure para PostgreSQL - Servidor Único utilizando o Azure CLI
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: af870c495ae707dd8319645eca979f0906234b4f
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608396"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Reinicie a base de dados de Azure para postgreSQL - Servidor Único utilizando o Azure CLI
Este tópico descreve como pode reiniciar uma Base de Dados Azure para servidor PostgreSQL. Pode ser necessário reiniciar o servidor por razões de manutenção, o que provoca uma falha curta à medida que o servidor executa a operação.

O reinício do servidor será bloqueado se o serviço estiver ocupado. Por exemplo, o serviço pode estar a processar uma operação previamente solicitada, como o escalar vCores.
 
> [!NOTE] 
> O tempo necessário para concluir um reinício depende do processo de recuperação postgreSQL. Para diminuir o tempo de reinício, recomendamos que minimize a quantidade de atividade que ocorre no servidor antes do reinício. Também pode querer aumentar a frequência de verificação. Também pode sintonizar os valores dos parâmetros relacionados com o checkpoint, incluindo `max_wal_size` . Recomenda-se também executar `CHECKPOINT` o comando antes de reiniciar o servidor.

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

## <a name="next-steps"></a>Passos seguintes

Saiba [como definir parâmetros na Base de Dados Azure para PostgreSQL](howto-configure-server-parameters-using-cli.md)
