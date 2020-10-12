---
title: Gerir regras de firewall - Azure CLI - Azure Database for MySQL
description: Este artigo descreve como criar e gerir a Base de Dados Azure para as regras de firewall mySQL usando a linha de comando Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: d981bf66ef9c17fda031e66e12e18a2ad9c67cc3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87503060"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Criar e gerir a Base de Dados Azure para as regras de firewall mySQL utilizando o Azure CLI
As regras de firewall ao nível do servidor podem ser utilizadas para gerir o acesso a uma Base de Dados Azure para o Servidor MySQL a partir de um endereço IP específico ou de uma série de endereços IP. Utilizando comandos CLI convenientes, pode criar, atualizar, excluir, listar e mostrar regras de firewall para gerir o seu servidor. Para obter uma visão geral da Base de Dados Azure para firewalls MySQL, consulte [a Base de Dados Azure para as regras de firewall do servidor MySQL](./concepts-firewall-rules.md).

As regras da Rede Virtual (VNet) também podem ser usadas para garantir o acesso ao seu servidor. Saiba mais sobre [a criação e gestão de pontos finais de serviços de Rede Virtual e regras utilizando o Azure CLI](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Pré-requisitos
* [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).
* Uma [base de dados Azure para servidor e base de dados MySQL](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Comandos de regra de firewall:
O comando de regra de firewall do **servidor az mysql** é utilizado a partir do CLI Azure para criar, eliminar, listar, mostrar e atualizar as regras de firewall.

Comandos:
- **criar**: Criar uma regra de firewall do servidor Azure MySQL.
- **eliminar**: Eliminar uma regra de firewall do servidor Azure MySQL.
- **lista**: Listar as regras de firewall do servidor Azure MySQL.
- **show**: Mostrar os detalhes de uma regra de firewall do servidor Azure MySQL.
- **atualização**: Atualize uma regra de firewall do servidor Azure MySQL.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Inscreva-se no Azure e enuseça a sua Base de Dados Azure para servidores MySQL
Ligue o Azure CLI de forma segura à sua conta Azure utilizando o comando **de login az.**

1. A partir da linha de comando, executar o seguinte comando:
    ```azurecli
    az login
    ```
   Este comando produz um código para usar no passo seguinte.

2. Utilize um navegador web para abrir a página [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e, em seguida, introduza o código.

3. Ao solicitar, inscreva-se usando as suas credenciais Azure.

4. Após o seu login ser autorizado, uma lista de subscrições é impressa na consola. Copie o ID da subscrição desejada para definir a subscrição atual para usar. Utilize o comando [conjunto de conta az.](/cli/azure/account#az-account-set)
    ```azurecli-interactive
    az account set --subscription <your subscription id>
    ```

5. Enuprosse as bases de dados Azure para servidores MySQL para o seu grupo de subscrição e recursos se não tiver a certeza dos nomes. Use o comando [da lista de servidores az mysql.](/cli/azure/mysql/server#az-mysql-server-list)

    ```azurecli-interactive
    az mysql server list --resource-group myresourcegroup
    ```

   Note o atributo de nome na listagem, que precisa de especificar o servidor MySQL para funcionar. Se necessário, confirme os detalhes desse servidor e utilize o atributo de nome para garantir que está correto. Use o comando [de exibição do servidor az mysql.](/cli/azure/mysql/server#az-mysql-server-show)

    ```azurecli-interactive
    az mysql server show --resource-group myresourcegroup --name mydemoserver
    ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Listar as regras de firewall na Base de Dados Azure para o Servidor MySQL 
Utilizando o nome do servidor e o nome do grupo de recursos, liste as regras de firewall do servidor existentes no servidor. Use o comando [da lista de firewall do servidor az mysql.](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-list)  Note que o atributo nome do servidor está especificado no interruptor **do servidor** e não no interruptor **de nome.** 
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A saída lista as regras, se houver, no formato JSON (por padrão). Pode utilizar o interruptor **de tabela de saída** para obter os resultados num formato de mesa mais legível.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Criar uma regra de firewall na Base de Dados Azure para o Servidor MySQL
Utilizando o nome do servidor Azure MySQL e o nome do grupo de recursos, crie uma nova regra de firewall no servidor. Utilize a firewall do [servidor az mysql criar](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) comando. Fornecer um nome para a regra, bem como o IP inicial e IP final (para fornecer acesso a uma série de endereços IP) para a regra.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Para permitir o acesso a um único endereço IP, forneça o mesmo endereço IP que o IP inicial e o IP final, como neste exemplo.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Para permitir que as aplicações a partir de endereços IP do Azure se conectem à sua Base de Dados Azure para o servidor MySQL, forneça o endereço IP 0.0.0 como o IP de início e o IP final, como neste exemplo.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mysql --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando selecionar esta opção, certifique-se de que as suas permissões de início de sessão e de utilizador limitam o acesso apenas a utilizadores autorizados.
> 

Após o sucesso, cada uma cria a saída de comando lista os detalhes da regra de firewall que criou, no formato JSON (por padrão). Em caso algum caso, existe uma falha, a saída mostra o texto da mensagem de erro.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Atualizar uma regra de firewall na Base de Dados Azure para o servidor MySQL 
Utilizando o nome do servidor Azure MySQL e o nome do grupo de recursos, atualize uma regra de firewall existente no servidor. Utilize o comando de atualização da [firewall do servidor az mysql.](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-update) Forneça o nome da regra de firewall existente como entrada, bem como os atributos IP de início e final para a atualização.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Após o sucesso, a saída de comando lista os detalhes da regra de firewall que atualizou, no formato JSON (por padrão). Em caso algum caso, existe uma falha, a saída mostra o texto da mensagem de erro.

> [!NOTE]
> Se a regra de firewall não existir, a regra é criada pelo comando de atualização.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Mostrar detalhes da regra de firewall na Base de Dados Azure para o Servidor MySQL
Utilizando o nome do servidor Azure MySQL e o nome do grupo de recursos, mostre os detalhes da regra de firewall existentes a partir do servidor. Use o comando de demonstração de [firewall do servidor az mysql.](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-show) Forneça o nome da regra de firewall existente como entrada.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Após o sucesso, a saída de comando lista os detalhes da regra de firewall que especificou, no formato JSON (por padrão). Em caso algum caso, existe uma falha, a saída mostra o texto da mensagem de erro.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Eliminar uma regra de firewall na Base de Dados Azure para o Servidor MySQL
Utilizando o nome do servidor Azure MySQL e o nome do grupo de recursos, remova uma regra de firewall existente do servidor. Utilize o comando de eliminação do [servidor az mysql.](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-delete) Forneça o nome da regra de firewall existente.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Após o sucesso, não há saída. Após falha, o texto da mensagem de erro aparece.

## <a name="next-steps"></a>Passos seguintes
- Conheça mais sobre as [regras de firewall do MySQL Server](./concepts-firewall-rules.md).
- [Crie e gere a base de dados Azure para as regras de firewall mySQL utilizando o portal Azure](./howto-manage-firewall-using-portal.md).
- Acesso mais seguro ao seu servidor através da [criação e gestão de pontos finais de serviço de Rede Virtual e regras utilizando o Azure CLI](howto-manage-vnet-using-cli.md).
