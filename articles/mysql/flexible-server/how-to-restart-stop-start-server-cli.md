---
title: Reiniciar/parar/iniciar - Portal Azure - Base de Dados Azure para O Servidor Flexível MySQL
description: Este artigo descreve como reiniciar/parar/iniciar operações na Base de Dados Azure para o MySQL através do CLI Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: c9e646ad40c669e51f052ac9888cdd7c6883a848
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509107"
---
# <a name="restartstopstart-an-azure-database-for-mysql---flexible-server-preview"></a>Reiniciar/parar/iniciar uma base de dados de Azure para o MySQL - Servidor Flexível (Pré-visualização)

> [!IMPORTANT]
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Este artigo mostra-lhe como executar o reinício, início e paragem do servidor flexível utilizando o Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos
- Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
- Instale ou atualize o Azure CLI para a versão mais recente. Consulte [a instalação do Azure CLI](/cli/azure/install-azure-cli).
-  Faça login na conta Azure utilizando o comando [de login az.](/cli/azure/reference-index#az-login) Note a propriedade **id,** que se refere ao **ID de subscrição** para a sua conta Azure.

    ```azurecli-interactive
    az login
    ````

- Se tiver várias subscrições, escolha a subscrição apropriada na qual pretende criar o servidor utilizando o ```az account set``` comando.
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- Crie um MySQL Flexible Server se ainda não tiver criado um utilizando o ```az mysql flexible-server create``` comando.

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="stop-a-running-server"></a>Parar um servidor de execução
Para parar um servidor, executar  ```az mysql flexible-server stop``` o comando. Se estiver a utilizar [o contexto local,](/cli/azure/config/param-persist)não precisa de apresentar argumentos.

**Utilização:**
```azurecli
az mysql flexible-server stop  [--name]
                               [--resource-group]
                               [--subscription]
```

**Exemplo sem contexto local:**
```azurecli
az mysql flexible-server stop  --resource-group --name myservername
```

**Exemplo com contexto local:**
```azurecli
az mysql flexible-server stop
```

## <a name="start-a-stopped-server"></a>Inicie um servidor parado
Para iniciar um servidor, executar  ```az mysql flexible-server start``` o comando. Se estiver a utilizar [o contexto local,](/cli/azure/config/param-persist)não precisa de apresentar argumentos.

**Utilização:**
```azurecli
az mysql flexible-server start [--name]
                               [--resource-group]
                               [--subscription]
```

**Exemplo sem contexto local:**
```azurecli
az mysql flexible-server start  --resource-group --name myservername
```

**Exemplo com contexto local:**
```azurecli
az mysql flexible-server start
```

> [!IMPORTANT]
> Uma vez reiniciado o servidor com sucesso, todas as operações de gestão estão agora disponíveis para o servidor flexível.

## <a name="restart-a-server"></a>Reiniciar um servidor
Para reiniciar um servidor, executar  ```az mysql flexible-server restart``` o comando. Se estiver a utilizar [o contexto local,](/cli/azure/config/param-persist)não precisa de apresentar argumentos.

**Utilização:**
```azurecli
az mysql flexible-server restart [--name]
                                 [--resource-group]
                                 [--subscription]
```

**Exemplo sem contexto local:**
```azurecli
az mysql flexible-server restart  --resource-group --name myservername
```

**Exemplo com contexto local:**
```azurecli
az mysql flexible-server restart
```


> [!IMPORTANT]
> Uma vez reiniciado o servidor com sucesso, todas as operações de gestão estão agora disponíveis para o servidor flexível.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [networking em Azure Database para MySQL Flexible Server](./concepts-networking.md)
- [Crie e gere a base de dados Azure para a rede virtual mySQL Flexible Server utilizando o portal Azure](./how-to-manage-virtual-network-portal.md).

