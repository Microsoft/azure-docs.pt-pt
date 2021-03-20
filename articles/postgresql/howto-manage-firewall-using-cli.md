---
title: Gerir regras de firewall - Azure CLI - Azure Database for PostgreSQL - Single Server
description: Este artigo descreve como criar e gerir regras de firewall na Base de Dados Azure para PostgreSQL - Servidor Único utilizando a linha de comando Azure CLI.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3a559b8c65ab57b0144b807a3b4cc1faa912d430
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93422744"
---
# <a name="create-and-manage-firewall-rules-in-azure-database-for-postgresql---single-server-using-azure-cli"></a>Criar e gerir regras de firewall na Base de Dados Azure para PostgreSQL - Servidor Único usando Azure CLI
As regras de firewall ao nível do servidor podem ser utilizadas para gerir o acesso a uma Base de Dados Azure para o Servidor PostgreSQL a partir de um endereço IP específico ou intervalo de endereços IP específicos. Utilizando comandos CLI convenientes, pode criar, atualizar, excluir, listar e mostrar regras de firewall para gerir o seu servidor. Para obter uma visão geral das regras de firewall do Azure Database para as regras de firewall postgreSQL, consulte [a Base de Dados Azure para as regras de firewall do Servidor PostgreSQL](concepts-firewall-rules.md).

As regras da Rede Virtual (VNet) também podem ser usadas para garantir o acesso ao seu servidor. Saiba mais sobre [a criação e gestão de pontos finais de serviços de Rede Virtual e regras utilizando o Azure CLI](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia, precisa:
- Instale o utilitário da linha de comando [Azure CLI](/cli/azure/install-azure-cli) ou utilize o Azure Cloud Shell no navegador.
- Uma [base de dados Azure para servidor e base de dados PostgreSQL](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Configure regras de firewall para Azure Database for PostgreSQL
Os [comandos de regra de firewall do servidor az postgres](/cli/azure/postgres/server/firewall-rule) são usados para configurar regras de firewall.

## <a name="list-firewall-rules"></a>Listar regras de firewall 
Para listar as regras de firewall do servidor existentes, executar o comando [da lista de regras de firewall do servidor az postgres.](/cli/azure/postgres/server/firewall-rule)
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A saída lista as regras de firewall, se houver, por padrão no formato JSON. Pode utilizar o interruptor `--output table` para um formato de mesa mais legível como saída.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Criar regra de firewall
Para criar uma nova regra de firewall no servidor, executar o comando [de firewall do servidor az postgres criar.](/cli/azure/postgres/server/firewall-rule) 


Para permitir o acesso a um endereço IP singular, forneça o mesmo endereço no `--start-ip-address` `--end-ip-address` e, como neste exemplo, substituindo o IP aqui mostrado pelo seu IP específico.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Para permitir que as aplicações a partir de endereços IP do Azure se conectem à sua Base de Dados Azure para o servidor PostgreSQL, forneça o endereço IP 0.0.0 como o IP inicial e o IP final, como neste exemplo.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando selecionar esta opção, certifique-se de que as suas permissões de início de sessão e de utilizador limitam o acesso apenas a utilizadores autorizados.
> 

Após o sucesso, a saída de comando lista os detalhes da regra de firewall que criou, por padrão no formato JSON. Em caso de falha, a saída mostra uma mensagem de erro.

## <a name="update-firewall-rule"></a>Atualizar regra de firewall 
Atualize uma regra de firewall existente no servidor utilizando o comando [de atualização de regra de firewall do servidor az postgres.](/cli/azure/postgres/server/firewall-rule) Forneça o nome da regra de firewall existente como entrada e os atributos IP e IP inicial para atualização.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
Após o sucesso, a saída de comando lista os detalhes da regra de firewall que atualizou, por padrão no formato JSON. Em caso de falha, a saída mostra uma mensagem de erro.
> [!NOTE]
> Se a regra de firewall não existir, é criada pelo comando de atualização.

## <a name="show-firewall-rule-details"></a>Mostrar detalhes da regra da firewall
Também pode mostrar os detalhes de uma regra de firewall ao nível do servidor existente, executando o comando [de regra de firewall do servidor az postgres.](/cli/azure/postgres/server/firewall-rule)
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Após o sucesso, a saída de comando lista os detalhes da regra de firewall que especificou, por padrão no formato JSON. Em caso de falha, a saída mostra uma mensagem de erro.

## <a name="delete-firewall-rule"></a>Eliminar regra de firewall
Para revogar o acesso a um intervalo de IP para o servidor, elimine uma regra de firewall existente executando o comando [de eliminação da regra de firewall do servidor az postgres.](/cli/azure/postgres/server/firewall-rule) Forneça o nome da regra de firewall existente.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Após o sucesso, não há saída. Após falha, o texto da mensagem de erro é devolvido.

## <a name="next-steps"></a>Passos seguintes
- Da mesma forma, pode utilizar um navegador web para criar e gerir a [Base de Dados Azure para regras de firewall postgreSQL utilizando o portal Azure](howto-manage-firewall-using-portal.md).
- Conheça mais sobre a [Base de Dados Azure para as regras de firewall do Servidor PostgreSQL](concepts-firewall-rules.md).
- Acesso mais seguro ao seu servidor através da [criação e gestão de pontos finais de serviço de Rede Virtual e regras utilizando o Azure CLI](howto-manage-vnet-using-cli.md).
- Para obter ajuda na ligação a uma base de dados Azure para servidor PostgreSQL, consulte [as bibliotecas de conexão para a base de dados Azure para postgreSQL](concepts-connection-libraries.md).
