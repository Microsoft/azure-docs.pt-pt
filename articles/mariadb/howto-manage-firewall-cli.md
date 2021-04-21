---
title: Gerir regras de firewall - Azure CLI - Azure Database for MariaDB
description: Este artigo descreve como criar e gerir a Base de Dados Azure para regras de firewall MariaDB usando a linha de comando Azure CLI.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 87ff75a07bd1b91121d614e0f41c0ecf216e1b41
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791720"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>Criar e gerir a Base de Dados de Azure para regras de firewall MariaDB utilizando o Azure CLI
As regras de firewall ao nível do servidor podem ser utilizadas para gerir o acesso a uma Base de Dados Azure para o Servidor MariaDB a partir de um endereço IP específico ou de uma série de endereços IP. Utilizando comandos CLI convenientes, pode criar, atualizar, excluir, listar e mostrar regras de firewall para gerir o seu servidor. Para obter uma visão geral da Base de Dados Azure para firewalls MariaDB, consulte [a Base de Dados Azure para as regras de firewall do servidor MariaDB](./concepts-firewall-rules.md).

As regras da Rede Virtual (VNet) também podem ser usadas para garantir o acesso ao seu servidor. Saiba mais sobre [a criação e gestão de pontos finais de serviços de Rede Virtual e regras utilizando o Azure CLI](howto-manage-vnet-cli.md).

## <a name="prerequisites"></a>Pré-requisitos
* [Instale o Azure CLI](/cli/azure/install-azure-cli).
* Uma [base de dados Azure para servidor e base de dados MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Comandos de regra de firewall:
O comando de regra de firewall do **servidor az mariadb** é utilizado a partir do CLI Azure para criar, eliminar, listar, mostrar e atualizar as regras de firewall.

Comandos:
- **criar**: Criar uma regra de firewall do servidor Azure MariaDB.
- **eliminar**: Elimine uma regra de firewall do servidor Azure MariaDB.
- **lista**: Listar as regras de firewall do servidor Azure MariaDB.
- **show**: Mostrar os detalhes de uma regra de firewall do servidor Azure MariaDB.
- **atualização**: Atualize uma regra de firewall do servidor Azure MariaDB.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>Inscreva-se no Azure e enuncie a sua Base de Dados Azure para servidores MariaDB
Ligue o Azure CLI de forma segura à sua conta Azure utilizando o comando **de login az.**

1. A partir da linha de comando, executar o seguinte comando:
   ```azurecli
   az login
   ```
   Este comando produz um código para usar no passo seguinte.

2. Utilize um navegador web para abrir a página [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e, em seguida, introduza o código.

3. Ao solicitar, inscreva-se usando as suas credenciais Azure.

4. Após o seu login ser autorizado, uma lista de subscrições é impressa na consola. Copie o ID da subscrição desejada para definir a subscrição atual para usar. Utilize o comando [conjunto de conta az.](/cli/azure/account#az_account_set)
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. Enuncie as bases de dados Azure para servidores MariaDB para o seu grupo de subscrição e recursos se não tiver a certeza dos nomes. Use o comando [da lista de servidores az mariadb.](/cli/azure/mariadb/server#az_mariadb_server_list)

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   Note o atributo de nome na listagem, que precisa de especificar o servidor MariaDB para trabalhar. Se necessário, confirme os detalhes desse servidor e utilize o atributo de nome para garantir que está correto. Use o comando de exibição do [servidor az mariadb.](/cli/azure/mariadb/server#az_mariadb_server_show)

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>Listar regras de firewall na Base de Dados Azure para o Servidor MariaDB 
Utilizando o nome do servidor e o nome do grupo de recursos, liste as regras de firewall do servidor existentes no servidor. Utilize o comando [da lista de firewall do servidor az mariadb.](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_list)  Note que o atributo nome do servidor está especificado no interruptor **do servidor** e não no interruptor **de nome.** 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A saída lista as regras, se houver, no formato JSON (por padrão). Pode utilizar o interruptor **de tabela de saída** para obter os resultados num formato de mesa mais legível.
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Criar uma regra de firewall na Base de Dados Azure para o Servidor MariaDB
Utilizando o nome do servidor Azure MariaDB e o nome do grupo de recursos, crie uma nova regra de firewall no servidor. Utilize a [firewall do servidor az mariadb criar](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_create) comando. Fornecer um nome para a regra, bem como o IP inicial e IP final (para fornecer acesso a uma série de endereços IP) para a regra.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Para permitir o acesso a um único endereço IP, forneça o mesmo endereço IP que o IP inicial e o IP final, como neste exemplo.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Para permitir que as aplicações a partir de endereços IP do Azure se conectem à sua Base de Dados Azure para o servidor MariaDB, forneça o endereço IP 0.0.0.0 como o IP de início e o IP final, como neste exemplo.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando selecionar esta opção, certifique-se de que as suas permissões de início de sessão e de utilizador limitam o acesso apenas a utilizadores autorizados.
> 

Após o sucesso, cada uma cria a saída de comando lista os detalhes da regra de firewall que criou, no formato JSON (por padrão). Em caso algum caso, existe uma falha, a saída mostra o texto da mensagem de erro.

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Atualizar uma regra de firewall na Base de Dados Azure para servidor MariaDB 
Utilizando o nome do servidor Azure MariaDB e o nome do grupo de recursos, atualize uma regra de firewall existente no servidor. Utilize o comando de atualização da [firewall do servidor az mariadb.](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_update) Forneça o nome da regra de firewall existente como entrada, bem como os atributos IP de início e final para a atualização.
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Após o sucesso, a saída de comando lista os detalhes da regra de firewall que atualizou, no formato JSON (por padrão). Em caso algum caso, existe uma falha, a saída mostra o texto da mensagem de erro.

> [!NOTE]
> Se a regra de firewall não existir, a regra é criada pelo comando de atualização.

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>Mostrar detalhes da regra de firewall na Base de Dados Azure para o Servidor MariaDB
Utilizando o nome do servidor Azure MariaDB e o nome do grupo de recursos, mostre os detalhes da regra de firewall existentes a partir do servidor. Use o comando de demonstração de [firewall do servidor az mariadb.](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_show) Forneça o nome da regra de firewall existente como entrada.
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Após o sucesso, a saída de comando lista os detalhes da regra de firewall que especificou, no formato JSON (por padrão). Em caso algum caso, existe uma falha, a saída mostra o texto da mensagem de erro.

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Eliminar uma regra de firewall na Base de Dados Azure para o Servidor MariaDB
Utilizando o nome do servidor Azure MariaDB e o nome do grupo de recursos, remova uma regra de firewall existente do servidor. Utilize o comando de eliminação do [servidor az mariadb.](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_delete) Forneça o nome da regra de firewall existente.
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Após o sucesso, não há saída. Após falha, o texto da mensagem de erro aparece.

## <a name="next-steps"></a>Passos seguintes
- Mais informações sobre [a Base de Dados Azure para as regras de firewall do Servidor MariaDB](./concepts-firewall-rules.md).
- [Crie e gere a Base de Dados Azure para regras de firewall MariaDB utilizando o portal Azure](./howto-manage-firewall-portal.md).
- Acesso mais seguro ao seu servidor através da [criação e gestão de pontos finais de serviço de Rede Virtual e regras utilizando o Azure CLI](howto-manage-vnet-cli.md).