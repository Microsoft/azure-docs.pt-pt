---
title: Gerenciar regras de firewall-CLI do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como criar e gerenciar regras de firewall no banco de dados do Azure para PostgreSQL-servidor único usando CLI do Azure linha de comando.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4af0fb288961689fb051bab8091c838f793cfcc3
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765652"
---
# <a name="create-and-manage-firewall-rules-in-azure-database-for-postgresql---single-server-using-azure-cli"></a>Criar e gerenciar regras de firewall no banco de dados do Azure para PostgreSQL-servidor único usando o CLI do Azure
As regras de firewall no nível de servidor podem ser usadas para gerenciar o acesso a um banco de dados do Azure para o servidor PostgreSQL de um endereço IP ou intervalo de endereços IP específico. Usando comandos de CLI do Azure convenientes, você pode criar, atualizar, excluir, listar e mostrar regras de firewall para gerenciar o servidor. Para obter uma visão geral das regras de firewall do banco de dados do Azure para PostgreSQL, consulte [regras de firewall do banco de dados do Azure para PostgreSQL](concepts-firewall-rules.md).

As regras de rede virtual (VNet) também podem ser usadas para proteger o acesso ao seu servidor. Saiba mais sobre como [criar e gerenciar pontos de extremidade de serviço de rede virtual e regras usando o CLI do Azure](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Pré-requisitos
Para percorrer este guia de instruções, você precisa de:
- Instale [CLI do Azure](/cli/azure/install-azure-cli) utilitário de linha de comando ou use o Azure cloud Shell no navegador.
- Um [banco de dados do Azure para servidor PostgreSQL e banco de dados](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Configurar regras de firewall para o banco de dados do Azure para PostgreSQL
Os comandos [AZ postgres Server firewall-Rule](/cli/azure/postgres/server/firewall-rule) são usados para configurar regras de firewall.

## <a name="list-firewall-rules"></a>Listar regras de firewall 
Para listar as regras de firewall do servidor existentes, execute o comando [AZ postgres Server firewall-Rule List](/cli/azure/postgres/server/firewall-rule) .
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A saída lista as regras de firewall, se houver, por padrão no formato JSON. Você pode usar a opção `--output table` para um formato de tabela mais legível como a saída.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Criar regra de firewall
Para criar uma nova regra de firewall no servidor, execute o comando [AZ postgres Server firewall-Rule Create](/cli/azure/postgres/server/firewall-rule) . 

```
To allow access to a singular IP address, provide the same address in the `--start-ip-address` and `--end-ip-address`, as in this example, replacing the IP shown here with your specific IP.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Para permitir que aplicativos de endereços IP do Azure se conectem ao seu banco de dados do Azure para servidor PostgreSQL, forneça o endereço IP 0.0.0.0 como o IP inicial e o IP final, como neste exemplo.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

Após o êxito, a saída do comando lista os detalhes da regra de firewall que você criou, por padrão no formato JSON. Se houver uma falha, a saída mostrará uma mensagem de erro em vez disso.

## <a name="update-firewall-rule"></a>Atualizar regra de firewall 
Atualize uma regra de firewall existente no servidor usando o comando [AZ postgres Server firewall-Rule Update](/cli/azure/postgres/server/firewall-rule) . Forneça o nome da regra de firewall existente como entrada e os atributos IP inicial e final a serem atualizados.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
Após o êxito, a saída do comando lista os detalhes da regra de firewall que você atualizou, por padrão, no formato JSON. Se houver uma falha, a saída mostrará uma mensagem de erro em vez disso.
> [!NOTE]
> Se a regra de firewall não existir, ela será criada pelo comando Update.

## <a name="show-firewall-rule-details"></a>Mostrar detalhes da regra de firewall
Você também pode mostrar os detalhes de uma regra de firewall no nível de servidor existente executando o comando [AZ postgres Server firewall-Rule show](/cli/azure/postgres/server/firewall-rule) .
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Após o êxito, a saída do comando lista os detalhes da regra de firewall que você especificou, por padrão no formato JSON. Se houver uma falha, a saída mostrará uma mensagem de erro em vez disso.

## <a name="delete-firewall-rule"></a>Excluir regra de firewall
Para revogar o acesso de um intervalo IP para o servidor, exclua uma regra de firewall existente executando o comando [AZ postgres Server firewall-Rule Delete](/cli/azure/postgres/server/firewall-rule) . Forneça o nome da regra de firewall existente.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Após o êxito, não há nenhuma saída. Após a falha, o texto da mensagem de erro é retornado.

## <a name="next-steps"></a>Passos seguintes
- Da mesma forma, você pode usar um navegador da Web para [criar e gerenciar regras de firewall do banco de dados do Azure para PostgreSQL usando o portal do Azure](howto-manage-firewall-using-portal.md).
- Saiba mais sobre [as regras de firewall de servidor do banco de dados do Azure para PostgreSQL](concepts-firewall-rules.md).
- Proteja ainda mais o acesso ao seu servidor [criando e gerenciando pontos de extremidade de serviço de rede virtual e regras usando o CLI do Azure](howto-manage-vnet-using-cli.md).
- Para obter ajuda para se conectar a um servidor de banco de dados do Azure para PostgreSQL, consulte [bibliotecas de conexões para o banco de dados do Azure para PostgreSQL](concepts-connection-libraries.md).
