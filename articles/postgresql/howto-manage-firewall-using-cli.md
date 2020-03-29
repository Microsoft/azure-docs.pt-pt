---
title: Gerir regras de firewall - Azure CLI - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como criar e gerir regras de firewall na Base de Dados Azure para PostgreSQL - Servidor Único utilizando a linha de comando Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4af0fb288961689fb051bab8091c838f793cfcc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74765652"
---
# <a name="create-and-manage-firewall-rules-in-azure-database-for-postgresql---single-server-using-azure-cli"></a>Criar e gerir regras de firewall na Base de Dados Azure para PostgreSQL - Servidor Único utilizando o Azure CLI
As regras de firewall ao nível do servidor podem ser utilizadas para gerir o acesso a uma Base de Dados Azure para o Servidor PostgreSQL a partir de um endereço IP específico ou gama de endereços IP. Utilizando comandos convenientes do Azure CLI, pode criar, atualizar, eliminar, listar e mostrar regras de firewall para gerir o seu servidor. Para obter uma visão geral da Base de Dados Azure para as regras de firewall PostgreSQL, consulte a Base de Dados Azure para obter regras de [firewall do PostgreSQL Server](concepts-firewall-rules.md).

As regras da Rede Virtual (VNet) também podem ser usadas para garantir o acesso ao seu servidor. Saiba mais sobre [a criação e gestão de pontos finais de serviço da Rede Virtual e regras usando o Azure CLI](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia de como guiar, você precisa:
- Instale o utilitário de linha de comando [Azure CLI](/cli/azure/install-azure-cli) ou utilize a Casca de Nuvem Azure no navegador.
- Uma Base de Dados Azure para servidor e base de [dados PostgreSQL](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Configure regras de firewall para Base de Dados Azure para PostgreSQL
Os comandos de [firewall do servidor az postgres](/cli/azure/postgres/server/firewall-rule) são usados para configurar regras de firewall.

## <a name="list-firewall-rules"></a>Lista rindo regras de firewall 
Para listar as regras de firewall do servidor existentes, execute o comando de [lista de firewall do servidor az postgres.](/cli/azure/postgres/server/firewall-rule)
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A saída lista as regras de firewall, se houver, por padrão no formato JSON. Pode utilizar o `--output table` interruptor para um formato de mesa mais legível como saída.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Criar regra de firewall
Para criar uma nova regra de firewall no servidor, executar a regra de firewall do [servidor az postgres criar](/cli/azure/postgres/server/firewall-rule) comando. 

```
To allow access to a singular IP address, provide the same address in the `--start-ip-address` and `--end-ip-address`, as in this example, replacing the IP shown here with your specific IP.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Para permitir que as aplicações dos endereços IP do Azure se conectem à sua Base de Dados Azure para servidor PostgreSQL, forneça o endereço IP 0.0.0.0 como IP inicial e IP final, como neste exemplo.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

Após o sucesso, a saída de comando lista os detalhes da regra de firewall que criou, por padrão no formato JSON. Se houver uma falha, a saída mostra uma mensagem de erro.

## <a name="update-firewall-rule"></a>Atualizar a regra da firewall 
Atualize uma regra de firewall existente no servidor utilizando o comando de [atualização de firewall do servidor az postgres.](/cli/azure/postgres/server/firewall-rule) Forneça o nome da regra de firewall existente como entrada, e os atributos IP e IP finais de início para atualizar.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
Após o sucesso, a saída de comando lista os detalhes da regra de firewall que atualizou, por padrão no formato JSON. Se houver uma falha, a saída mostra uma mensagem de erro.
> [!NOTE]
> Se a regra da firewall não existir, é criada pelo comando de atualização.

## <a name="show-firewall-rule-details"></a>Mostrar detalhes da regra da firewall
Também pode mostrar os detalhes de uma regra de firewall de nível de servidor existente executando o comando de [firewall do servidor az postgres.](/cli/azure/postgres/server/firewall-rule)
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Após o sucesso, a saída de comando lista os detalhes da regra de firewall que especificou, por padrão no formato JSON. Se houver uma falha, a saída mostra uma mensagem de erro.

## <a name="delete-firewall-rule"></a>Eliminar a regra da firewall
Para revogar o acesso a um intervalo IP para o servidor, elimine uma regra de firewall existente executando o comando de eliminação da regra de firewall do [servidor az postgres.](/cli/azure/postgres/server/firewall-rule) Forneça o nome da regra de firewall existente.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Após o sucesso, não há saída. Após a falha, o texto da mensagem de erro é devolvido.

## <a name="next-steps"></a>Passos seguintes
- Da mesma forma, pode utilizar um navegador web para criar e gerir a Base de Dados Azure para regras de [firewall PostgreSQL utilizando o portal Azure](howto-manage-firewall-using-portal.md).
- Entenda mais sobre a Base de Dados Azure para as regras de [firewall do Servidor PostgreSQL](concepts-firewall-rules.md).
- Acesso mais seguro ao seu servidor [criando e gerindo pontos finais de serviço de Rede Virtual e regras usando o Azure CLI](howto-manage-vnet-using-cli.md).
- Para obter ajuda na ligação a uma Base de Dados Azure para servidor PostgreSQL, consulte bibliotecas de [ligação para base de dados Azure para PostgreSQL](concepts-connection-libraries.md).
