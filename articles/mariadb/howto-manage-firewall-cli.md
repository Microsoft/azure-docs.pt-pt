---
title: Criar e gerir a base de dados do Azure para as regras de firewall da MariaDB com a CLI do Azure
description: Este artigo descreve como criar e gerir a base de dados do Azure para as regras de firewall da MariaDB com a CLI do Azure da linha de comandos.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 11/10/2018
ms.openlocfilehash: f457246b429062625e6542cfdaf00f3526a85209
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58083237"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>Criar e gerir a base de dados do Azure para regras de firewall da MariaDB com a CLI do Azure
Regras de firewall ao nível do servidor permitem aos administradores gerir o acesso a uma base de dados do Azure para MariaDB Server de um endereço IP específico ou um intervalo de endereços IP. Usando o convenientes comandos da CLI do Azure, pode criar, atualizar, eliminar, lista e Mostrar regras de firewall para gerir o seu servidor. Para uma descrição geral da base de dados do Azure para MariaDB firewalls, consulte [base de dados do Azure para MariaDB regras de firewall de servidor](./concepts-firewall-rules.md)

## <a name="prerequisites"></a>Pré-requisitos
* [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).
* Uma [base de dados do Azure para MariaDB servidor e base de dados](quickstart-create-mariadb-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Comandos de regra de firewall:
O **az mariadb server firewall-rule** comando é utilizado a partir da CLI do Azure para criar, eliminar, listar, mostrar e atualizar regras de firewall.

Comandos:
- **Criar**: Crie uma regra de firewall do servidor de Azure MariaDB.
- **delete**: Elimine uma regra de firewall do servidor de Azure MariaDB.
- **list**: Liste as regras de firewall do servidor de Azure MariaDB.
- **Mostrar**: Mostre os detalhes de um servidor de Azure MariaDB regra de firewall.
- **update**: Atualize uma regra de firewall do servidor de Azure MariaDB.

## <a name="log-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>Inicie sessão no Azure e listar a sua base de dados do Azure para MariaDB servidores
Ligar em segurança a CLI do Azure com a sua conta do Azure utilizando o **início de sessão az** comando.

1. Na linha de comando, execute o seguinte comando:
   ```azurecli
   az login
   ```
   Esse comando gera um código para utilizar no próximo passo.

2. Utilize um browser para abrir a página [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin)e, em seguida, introduza o código.

3. Na linha de comandos, inicie sessão com as suas credenciais do Azure.

4. Após o início de sessão é autorizado, uma lista de subscrições é impresso na consola do. Copie o ID da subscrição pretendida para definir a subscrição atual a utilizar. Utilize o [conjunto de conta de az](/cli/azure/account#az-account-set) comando.
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. Se não souber os nomes de, liste as bases de dados do Azure para MariaDB servidores para o seu grupo de recursos e subscrição. Utilize o [lista de servidores de mariadb az](/cli/azure/mariadb/server#az-mariadb-server-list) comando.

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   Tenha em atenção o atributo de nome na listagem, que tem de especificar o servidor de MariaDB para trabalhar no. Se for necessário, confirme os detalhes para esse servidor e usando o atributo de nome para se certificar de que está correto. Utilize o [show de servidor az mariadb](/cli/azure/mariadb/server#az-mariadb-server-show) comando.

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>Lista de regras de firewall na base de dados do Azure para o servidor de MariaDB 
Com o nome do servidor e o nome do grupo de recursos, liste as regras de firewall de servidor existente no servidor. Utilize o [lista de firewall de servidor de mariadb az](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-list) comando.  Tenha em atenção que o atributo de nome de servidor especificado no **– servidor** mudar e não na **– nome** mudar. 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A saída lista as regras, se houver, em JSON formatar (por predefinição). Pode utilizar o **– tabela de saída** comutador para enviar os resultados num formato de tabela mais legível.
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Criar uma regra de firewall na base de dados do Azure para MariaDB Server
Com o nome do servidor MariaDB do Azure e o nome do grupo de recursos, crie uma nova regra de firewall no servidor. Utilize o [firewall de servidor az mariadb criar](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) comando. Forneça um nome para a regra, bem como o IP de início e fim de IP (para fornecer acesso a um intervalo de endereços IP) para a regra.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Para permitir o acesso para um endereço IP único, forneça o mesmo endereço IP que o IP inicial e final de IP, tal como neste exemplo.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Para permitir que aplicações a partir de endereços IP do Azure para ligar à base de dados do Azure para MariaDB server, forneça o endereço IP 0.0.0.0 como o IP inicial e final, tal como neste exemplo.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

Após a conclusão bem-sucedida, cada criar comando de saída lista os detalhes da regra de firewall que criou, no formato JSON (por predefinição). Se houver uma falha, o resultado mostra texto de mensagem de erro em vez disso.

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Atualizar uma regra de firewall na base de dados do Azure para o servidor de MariaDB 
Com o nome do servidor MariaDB do Azure e o nome do grupo de recursos, atualize uma regra de firewall existente no servidor. Utilize o [atualização de firewall do servidor de mariadb az](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-update) comando. Forneça o nome da regra de firewall existente como entrada, bem como o início de atributos IP de IP e de fim para atualizar.
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Após a conclusão bem-sucedida, a saída do comando lista os detalhes da regra de firewall que atualizar, no formato JSON (por predefinição). Se houver uma falha, o resultado mostra texto de mensagem de erro em vez disso.

> [!NOTE]
> Se a regra de firewall não existir, a regra é criada pelo comando de atualização.

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>Mostrar os detalhes da regra de firewall na base de dados do Azure para o servidor de MariaDB
Com o nome do servidor MariaDB do Azure e o nome do grupo de recursos, mostram a firewall existente detalhes da regra do servidor. Utilize o [show de firewall de servidor de mariadb az](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-show) comando. Forneça o nome da regra de firewall existente como entrada.
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Após a conclusão bem-sucedida, a saída do comando lista os detalhes da regra de firewall que especificou, no formato JSON (por predefinição). Se houver uma falha, o resultado mostra texto de mensagem de erro em vez disso.

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Eliminar uma regra de firewall na base de dados do Azure para o servidor de MariaDB
Com o nome do servidor MariaDB do Azure e o nome do grupo de recursos, remova uma regra de firewall existente do servidor. Utilize o [firewall de servidor az mariadb eliminar](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-delete) comando. Forneça o nome da regra de firewall existentes.
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Após a conclusão bem-sucedida, não há nenhuma saída. Após a falha, exibe texto da mensagem de erro.

## <a name="next-steps"></a>Passos Seguintes
- Saber mais sobre [base de dados do Azure para as regras de firewall do servidor de MariaDB](./concepts-firewall-rules.md).
- [Criar e gerir a base de dados do Azure para MariaDB regras de firewall com o portal do Azure](./howto-manage-firewall-portal.md).
