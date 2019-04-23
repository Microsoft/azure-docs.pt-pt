---
title: Criar e gerir a base de dados do Azure para as regras de firewall do PostgreSQL com a CLI do Azure
description: Este artigo descreve como criar e gerir a base de dados do Azure para as regras de firewall do PostgreSQL com linha de comandos da CLI do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 9a96361d3fb155ea5b400990690e3c2b1f65f819
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790812"
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Criar e gerir a base de dados do Azure para as regras de firewall do PostgreSQL com a CLI do Azure
Regras de firewall ao nível do servidor podem ser utilizadas para gerir o acesso a uma base de dados do Azure para o servidor PostgreSQL de um endereço IP específico ou intervalo de endereços IP. Usando o convenientes comandos da CLI do Azure, pode criar, atualizar, eliminar, lista e Mostrar regras de firewall para gerir o seu servidor. Para uma descrição geral da base de dados do Azure para PostgreSQL regras de firewall, consulte [base de dados do Azure para as regras de firewall do servidor PostgreSQL](concepts-firewall-rules.md).

Regras de rede (VNet) virtual também podem ser utilizadas para proteger o acesso ao seu servidor. Saiba mais sobre [criação e gestão de rede Virtual do serviço pontos finais e regras com a CLI do Azure](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, terá de:
- Instale [CLI do Azure](/cli/azure/install-azure-cli) utilitário da linha de comandos ou utilize o Azure Cloud Shell no browser.
- Uma [base de dados do Azure para PostgreSQL server e base de dados](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Configurar regras de firewall da base de dados do Azure para PostgreSQL
O [az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) comandos são utilizados para configurar regras de firewall.

## <a name="list-firewall-rules"></a>Listar regras de firewall 
Para listar as regras de firewall de servidor existentes, execute o [lista do az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) comando.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A saída lista as regras de firewall, se houver, por predefinição em JSON formatar. Pode utilizar o comutador `--output table` para um formato de tabela mais legível como o resultado.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Criar regra de firewall
Para criar uma nova regra de firewall no servidor, execute o [az postgres server firewall-rule criar](/cli/azure/postgres/server/firewall-rule) comando. 

```
To allow access to a singular IP address, provide the same address in the `--start-ip-address` and `--end-ip-address`, as in this example, replacing the IP shown here with your specific IP.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Para permitir que aplicações a partir de endereços IP do Azure para ligar à base de dados do Azure para o servidor PostgreSQL, forneça o endereço IP 0.0.0.0 como o IP inicial e final, tal como neste exemplo.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

Após a conclusão bem-sucedida, a saída do comando lista os detalhes da regra de firewall que criou, por predefinição no formato JSON. Se houver uma falha, o resultado mostra uma mensagem de erro em vez disso.

## <a name="update-firewall-rule"></a>Atualizar regra de firewall 
Atualizar uma regra de firewall existente no servidor com [atualização do az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) comando. Forneça o nome da regra de firewall existente como entrada e o início de atributos IP de IP e de fim para atualizar.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
Após a conclusão bem-sucedida, a saída do comando lista os detalhes da regra de firewall que atualizar, por predefinição no formato JSON. Se houver uma falha, o resultado mostra uma mensagem de erro em vez disso.
> [!NOTE]
> Se a regra de firewall não existir, este é criado pelo comando de atualização.

## <a name="show-firewall-rule-details"></a>Mostrar os detalhes da regra de firewall
Também pode mostrar os detalhes de uma regra de firewall ao nível do servidor existente executando [show do az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) comando.
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Após a conclusão bem-sucedida, a saída do comando lista os detalhes da regra de firewall que especificou, por predefinição no formato JSON. Se houver uma falha, o resultado mostra uma mensagem de erro em vez disso.

## <a name="delete-firewall-rule"></a>Eliminar regra de firewall
Para revogar o acesso para um intervalo IP para o servidor, eliminar uma regra de firewall existente ao executar o [az postgres server firewall-rule eliminar](/cli/azure/postgres/server/firewall-rule) comando. Forneça o nome da regra de firewall existentes.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Após a conclusão bem-sucedida, não há nenhuma saída. Após a falha, o texto da mensagem de erro é retornado.

## <a name="next-steps"></a>Passos Seguintes
- Da mesma forma, pode usar um navegador da web para [criar e gerir a base de dados do Azure para as regras de firewall do PostgreSQL no portal do Azure](howto-manage-firewall-using-portal.md).
- Saber mais sobre [base de dados do Azure para as regras de firewall do servidor PostgreSQL](concepts-firewall-rules.md).
- Proteger ainda mais o acesso ao seu servidor pela [criação e gestão de rede Virtual do serviço pontos finais e regras com a CLI do Azure](howto-manage-vnet-using-cli.md).
- Para obter ajuda na conexão com uma base de dados do Azure para o servidor PostgreSQL, consulte [bibliotecas de ligação para base de dados do Azure para PostgreSQL](concepts-connection-libraries.md).
