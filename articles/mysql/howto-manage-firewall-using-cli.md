---
title: Gerenciar regras de firewall-CLI do Azure-banco de dados do Azure para MySQL
description: Este artigo descreve como criar e gerenciar regras de firewall do banco de dados do Azure para MySQL usando CLI do Azure linha de comando.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 0c64a5d80bace68f72274224ca9ba7c99a84f86a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74764955"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Criar e gerenciar regras de firewall do banco de dados do Azure para MySQL usando o CLI do Azure
As regras de firewall no nível de servidor podem ser usadas para gerenciar o acesso a um servidor de banco de dados do Azure para MySQL de um endereço IP específico ou de um intervalo de endereços IP. Usando comandos de CLI do Azure convenientes, você pode criar, atualizar, excluir, listar e mostrar regras de firewall para gerenciar o servidor. Para obter uma visão geral do banco de dados do Azure para firewalls MySQL, consulte [regras de firewall do banco de dados do Azure para MySQL](./concepts-firewall-rules.md).

As regras de rede virtual (VNet) também podem ser usadas para proteger o acesso ao seu servidor. Saiba mais sobre como [criar e gerenciar pontos de extremidade de serviço de rede virtual e regras usando o CLI do Azure](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Pré-requisitos
* [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).
* Um banco de dados do [Azure para servidor MySQL e banco de dados](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Comandos de regra de firewall:
O comando **AZ MySQL Server firewall-Rule** é usado no CLI do Azure para criar, excluir, listar, mostrar e atualizar regras de firewall.

Comandos
- **criar**: criar uma regra de firewall do servidor MySQL do Azure.
- **excluir**: excluir uma regra de firewall do servidor MySQL do Azure.
- **lista**: listar as regras de firewall do servidor MySQL do Azure.
- **Mostrar**: Mostre os detalhes de uma regra de firewall do servidor MySQL do Azure.
- **atualização**: atualizar uma regra de firewall do servidor MySQL do Azure.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Entre no Azure e liste seu banco de dados do Azure para servidores MySQL
Conecte CLI do Azure com segurança à sua conta do Azure usando o comando **AZ login** .

1. Na linha de comando, execute o seguinte comando:
    ```azurecli
    az login
    ```
   Esse comando gera um código a ser usado na próxima etapa.

2. Use um navegador da Web para abrir a página [https://aka.ms/devicelogin](https://aka.ms/devicelogin)e, em seguida, insira o código.

3. No prompt, entre usando suas credenciais do Azure.

4. Depois que o logon for autorizado, uma lista de assinaturas será impressa no console do. Copie a ID da assinatura desejada para definir a assinatura atual a ser usada. Use o comando [AZ Account Set](/cli/azure/account#az-account-set) .
    ```azurecli-interactive
    az account set --subscription <your subscription id>
    ```

5. Liste os bancos de dados do Azure para servidores MySQL para sua assinatura e grupo de recursos se você não tiver certeza dos nomes. Use o comando [AZ MySQL Server List](/cli/azure/mysql/server#az-mysql-server-list) .

    ```azurecli-interactive
    az mysql server list --resource-group myresourcegroup
    ```

   Observe o atributo Name na lista, em que você precisa especificar o servidor MySQL no qual trabalhar. Se necessário, confirme os detalhes desse servidor e usando o atributo Name para garantir que esteja correto. Use o comando [AZ MySQL Server show](/cli/azure/mysql/server#az-mysql-server-show) .

    ```azurecli-interactive
    az mysql server show --resource-group myresourcegroup --name mydemoserver
    ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Listar regras de firewall no banco de dados do Azure para servidor MySQL 
Usando o nome do servidor e o nome do grupo de recursos, liste as regras de firewall do servidor existentes no servidor. Use o comando [AZ MySQL Server firewall List](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-list) .  Observe que o atributo de nome do servidor é especificado na opção **--Server** e não na opção **--Name** . 
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A saída lista as regras, se houver, no formato JSON (por padrão). Você pode usar a opção de **tabela de saída** para gerar os resultados em um formato de tabela mais legível.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Criar uma regra de firewall no banco de dados do Azure para servidor MySQL
Usando o nome do servidor MySQL do Azure e o nome do grupo de recursos, crie uma nova regra de firewall no servidor. Use o comando [AZ MySQL Server firewall Create](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) . Forneça um nome para a regra, bem como o IP inicial e o IP final (para fornecer acesso a um intervalo de endereços IP) para a regra.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Para permitir o acesso de um único endereço IP, forneça o mesmo endereço IP que o IP inicial e o IP final, como neste exemplo.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Para permitir que aplicativos de endereços IP do Azure se conectem ao seu banco de dados do Azure para servidor MySQL, forneça o endereço IP 0.0.0.0 como o IP inicial e o IP final, como neste exemplo.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mysql --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

Após o êxito, cada saída de comando Create lista os detalhes da regra de firewall que você criou, no formato JSON (por padrão). Se houver uma falha, a saída mostrará o texto da mensagem de erro em vez disso.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Atualizar uma regra de firewall no banco de dados do Azure para servidor MySQL 
Usando o nome do servidor MySQL do Azure e o nome do grupo de recursos, atualize uma regra de firewall existente no servidor. Use o comando [AZ MySQL Server firewall Update](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-update) . Forneça o nome da regra de firewall existente como entrada, bem como os atributos IP inicial e IP final a serem atualizados.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Após o êxito, a saída do comando lista os detalhes da regra de firewall que você atualizou, no formato JSON (por padrão). Se houver uma falha, a saída mostrará o texto da mensagem de erro em vez disso.

> [!NOTE]
> Se a regra de firewall não existir, a regra será criada pelo comando de atualização.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Mostrar detalhes da regra de firewall no banco de dados do Azure para servidor MySQL
Usando o nome do servidor MySQL do Azure e o nome do grupo de recursos, mostre os detalhes da regra de firewall existente no servidor. Use o comando [AZ MySQL Server firewall show](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-show) . Forneça o nome da regra de firewall existente como entrada.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Após o êxito, a saída do comando lista os detalhes da regra de firewall que você especificou, no formato JSON (por padrão). Se houver uma falha, a saída mostrará o texto da mensagem de erro em vez disso.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Excluir uma regra de firewall no banco de dados do Azure para servidor MySQL
Usando o nome do servidor MySQL do Azure e o nome do grupo de recursos, remova uma regra de firewall existente do servidor. Use o comando [AZ MySQL Server firewall Delete](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-delete) . Forneça o nome da regra de firewall existente.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Após o êxito, não há nenhuma saída. Após a falha, o texto da mensagem de erro é exibido.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [as regras de firewall do banco de dados do Azure para MySQL](./concepts-firewall-rules.md).
- [Crie e gerencie as regras de firewall do banco de dados do Azure para MySQL usando o portal do Azure](./howto-manage-firewall-using-portal.md).
- Proteja ainda mais o acesso ao seu servidor [criando e gerenciando pontos de extremidade de serviço de rede virtual e regras usando o CLI do Azure](howto-manage-vnet-using-cli.md).