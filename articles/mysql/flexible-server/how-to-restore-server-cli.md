---
title: Restaurar a base de dados Azure para MySQL - Servidor Flexível com Azure CLI
description: Este artigo descreve como executar operações de restauro na Base de Dados Azure para o MySQL através do CLI Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 61a8439b770d8909e04d1b80a5219810dc72aa34
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509104"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-with-azure-cli"></a>Restauro pontual de uma Base de Dados Azure para MySQL - Servidor Flexível com Azure CLI


> [!IMPORTANT]
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Este artigo fornece um procedimento passo a passo para realizar recuperações pontuais em servidor flexível utilizando cópias de segurança.

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

## <a name="restore-a-server-from-backup-to-a-new-server"></a>Restaurar um servidor de backup para um novo servidor

Pode executar o seguinte comando para restaurar um servidor para uma cópia de segurança mais antiga existente.

**Utilização**
```azurecli
az mysql flexible-server restore --restore-time
                                 --source-server
                                 [--ids]
                                 [--location]
                                 [--name]
                                 [--no-wait]
                                 [--resource-group]
                                 [--subscription]
```

**Exemplo:** Restaurar um servidor a partir desta ```2021-03-03T13:10:00Z``` foto de reserva.

```azurecli
az mysql server restore \
--name mydemoserver-restored \
--resource-group myresourcegroup \
--restore-point-in-time "2021-03-03T13:10:00Z" \
--source-server mydemoserver
```
O tempo de restauro dependerá do tamanho dos dados armazenados no servidor.

## <a name="perform-post-restore-tasks"></a>Executar tarefas pós-restauro
Após a restauração concluída, deverá executar as seguintes tarefas para que os seus utilizadores e aplicações voltem a funcionar:

- Se o novo servidor pretende substituir o servidor original, redirecione clientes e aplicações de clientes para o novo servidor
- Certifique-se de que existem regras VNet adequadas para que os utilizadores se conectem. Estas regras não são copiadas do servidor original.
- Certifique-se de que estão em vigor logins e permissões de nível de base de dados apropriados
- Configure alertas conforme apropriado para o servidor recém-restaurador

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a continuidade do negócio](concepts-business-continuity.md)

