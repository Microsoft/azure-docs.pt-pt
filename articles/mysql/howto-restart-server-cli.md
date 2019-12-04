---
title: Reiniciar o servidor-CLI do Azure-banco de dados do Azure para MySQL
description: Este artigo descreve como você pode reiniciar um servidor de banco de dados do Azure para MySQL usando o CLI do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 146c531805ff22544797c44c8df32b0e5d1e83b0
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774228"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Reinicie o banco de dados do Azure para servidor MySQL usando o CLI do Azure
Este tópico descreve como você pode reiniciar um servidor de banco de dados do Azure para MySQL. Talvez seja necessário reiniciar o servidor por motivos de manutenção, o que causa uma pequena interrupção à medida que o servidor executa a operação.

A reinicialização do servidor será bloqueada se o serviço estiver ocupado. Por exemplo, o serviço pode estar processando uma operação solicitada anteriormente, como dimensionar vCores.

O tempo necessário para concluir uma reinicialização depende do processo de recuperação do MySQL. Para diminuir o tempo de reinicialização, recomendamos que você minimize a quantidade de atividade que ocorre no servidor antes da reinicialização.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de instruções, você precisa de:
- Um [banco de dados do Azure para servidor MySQL](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Este guia de instruções requer que você use CLI do Azure versão 2,0 ou posterior. Para confirmar a versão, no prompt de comando CLI do Azure, digite `az --version`. Para instalar ou atualizar, consulte [instalar CLI do Azure]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Reiniciar o servidor

Reinicie o servidor com o seguinte comando:

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [como definir parâmetros no banco de dados do Azure para MySQL](howto-configure-server-parameters-using-cli.md)