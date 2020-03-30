---
title: Gerir regras de firewall - Azure CLI - Base de Dados Azure para MariaDB
description: Este artigo descreve como criar e gerir a Base de Dados Azure para regras de firewall MariaDB utilizando a linha de comando Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 898b65f07140bca04bd97ff7314b01920b783914
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530636"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>Crie e gerea Base de Dados Azure para regras de firewall MariaDB utilizando o Azure CLI
As regras de firewall ao nível do servidor podem ser usadas para gerir o acesso a uma Base de Dados Azure para o Servidor MariaDB a partir de um endereço IP específico ou de uma série de endereços IP. Utilizando comandos convenientes do Azure CLI, pode criar, atualizar, eliminar, listar e mostrar regras de firewall para gerir o seu servidor. Para obter uma visão geral da Base de Dados Azure para firewalls MariaDB, consulte [a Base de Dados Azure para obter regras](./concepts-firewall-rules.md)de firewall do servidor MariaDB .

As regras da Rede Virtual (VNet) também podem ser usadas para garantir o acesso ao seu servidor. Saiba mais sobre [a criação e gestão de pontos finais de serviço da Rede Virtual e regras usando o Azure CLI](howto-manage-vnet-cli.md).

## <a name="prerequisites"></a>Pré-requisitos
* [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).
* Uma [Base de Dados Azure para servidor MariaDB e base de dados.](quickstart-create-mariadb-server-database-using-azure-cli.md)

## <a name="firewall-rule-commands"></a>Comandos de regra de firewall:
O comando de **firewall do servidor az mariadb** é utilizado a partir do Azure CLI para criar, eliminar, listar, mostrar e atualizar regras de firewall.

Comandos:
- **criar**: Criar uma regra de firewall do servidor Azure MariaDB.
- **eliminar**uma regra de firewall do servidor Azure MariaDB.
- **lista**: Lista rindo as regras de firewall do servidor Azure MariaDB.
- **mostrar**: Mostrar os detalhes de uma regra de firewall do servidor Azure MariaDB.
- **atualização**: Atualizar uma regra de firewall do servidor Azure MariaDB.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>Inscreva-se no Azure e enumere a sua Base de Dados Azure para servidores MariaDB
Ligue o Azure CLI com a sua conta Azure utilizando o comando **de login az.**

1. A partir da linha de comando, executar o seguinte comando:
   ```azurecli
   az login
   ```
   Este comando produz um código para usar no próximo passo.

2. Utilize um navegador web [https://aka.ms/devicelogin](https://aka.ms/devicelogin)para abrir a página e, em seguida, insira o código.

3. No momento, inscreva-se usando as suas credenciais Azure.

4. Após a autorização do seu login, uma lista de subscrições é impressa na consola. Copie o ID da subscrição desejada para definir a subscrição atual para usar. Use o comando definido da [conta az.](/cli/azure/account#az-account-set)
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. Liste as Bases de Dados Azure para servidores MariaDB para o seu grupo de subscrição e recursos se não tiver a certeza dos nomes. Use o comando da lista de [servidores az mariadb.](/cli/azure/mariadb/server#az-mariadb-server-list)

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   Note o atributo de nome na listagem, que precisa especificar o servidor MariaDB para trabalhar. Se necessário, confirme os detalhes para esse servidor e utilize o atributo de nome para garantir que está correto. Use o comando do [servidor az mariadb.](/cli/azure/mariadb/server#az-mariadb-server-show)

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>Lista de regras de firewall na Base de Dados Azure para o Servidor MariaDB 
Utilizando o nome do servidor e o nome do grupo de recursos, lista as regras de firewall do servidor existentes no servidor. Utilize o comando da lista de firewall do [servidor az mariadb.](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-list)  Note que o atributo do nome do servidor está especificado no interruptor **--servidor** e não no interruptor **de nome.** 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A saída lista as regras, se houver, no formato JSON (por predefinição). Pode utilizar o interruptor **de tabela -output** para obter os resultados num formato de tabela mais legível.
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Criar uma regra de firewall na Base de Dados Azure para o Servidor MariaDB
Utilizando o nome do servidor Azure MariaDB e o nome do grupo de recursos, crie uma nova regra de firewall no servidor. Utilize a firewall do [servidor az mariadb criar](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) comando. Forneça um nome para a regra, bem como o IP inicial e o IP final (para fornecer acesso a uma série de endereços IP) para a regra.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Para permitir o acesso a um único endereço IP, forneça o mesmo endereço IP que o IP inicial e o IP final, como neste exemplo.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Para permitir que as aplicações dos endereços IP do Azure se conectem à sua Base de Dados Azure para o servidor MariaDB, forneça o endereço IP 0.0.0.0 como IP inicial e IP final, como neste exemplo.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

Após o sucesso, cada criação de saída de comando lista os detalhes da regra de firewall que criou, em formato JSON (por padrão). Se houver uma falha, a saída mostra texto de mensagem de erro.

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Atualizar uma regra de firewall na Base de Dados Azure para servidor MariaDB 
Utilizando o nome do servidor Azure MariaDB e o nome do grupo de recursos, atualize uma regra de firewall existente no servidor. Utilize o comando de atualização de firewall do [servidor az mariadb.](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-update) Forneça o nome da regra de firewall existente como entrada, bem como os atributos IP de início e IP finais para atualizar.
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Após o sucesso, a saída de comando lista os detalhes da regra de firewall que atualizou, em formato JSON (por padrão). Se houver uma falha, a saída mostra texto de mensagem de erro.

> [!NOTE]
> Se a regra da firewall não existir, a regra é criada pelo comando de atualização.

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>Mostrar detalhes da regra da firewall na Base de Dados Azure para o Servidor MariaDB
Utilizando o nome do servidor Azure MariaDB e o nome do grupo de recursos, mostre os detalhes da regra de firewall existentes a partir do servidor. Use o comando de [firewall do servidor az mariadb.](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-show) Forneça o nome da regra de firewall existente como entrada.
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Após o sucesso, a saída de comando lista os detalhes da regra de firewall que especificou, no formato JSON (por padrão). Se houver uma falha, a saída mostra texto de mensagem de erro.

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Eliminar uma regra de firewall na Base de Dados Azure para o Servidor MariaDB
Utilizando o nome do servidor Azure MariaDB e o nome do grupo de recursos, remova uma regra de firewall existente do servidor. Utilize o comando de eliminação do [servidor az mariadb.](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-delete) Forneça o nome da regra de firewall existente.
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Após o sucesso, não há saída. Após a falha, o texto da mensagem de erro mostra.

## <a name="next-steps"></a>Passos seguintes
- Conheça mais sobre a Base de Dados Azure para as regras de firewall do [Servidor MariaDB.](./concepts-firewall-rules.md)
- [Crie e gerea Base de Dados Azure para regras de firewall MariaDB utilizando o portal Azure](./howto-manage-firewall-portal.md).
- Acesso mais seguro ao seu servidor [criando e gerindo pontos finais de serviço de Rede Virtual e regras usando o Azure CLI](howto-manage-vnet-cli.md).
