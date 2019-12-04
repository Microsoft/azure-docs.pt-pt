---
title: Gerenciar logs-CLI do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como configurar e acessar os logs do servidor (arquivos. log) no banco de dados do Azure para PostgreSQL-servidor único usando o CLI do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: be679be91d49516bd2f6c672eb53640cfad2ae2a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74763577"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Configurar e acessar logs de servidor usando CLI do Azure
Você pode baixar os logs de erro do servidor PostgreSQL usando a interface de linha de comando (CLI do Azure). No entanto, não há suporte para o acesso aos logs de transações. 

## <a name="prerequisites"></a>Pré-requisitos
Para percorrer este guia de instruções, você precisa de:
- [Banco de dados do Azure para servidor PostgreSQL](quickstart-create-server-database-azure-cli.md)
- O utilitário de linha de comando [CLI do Azure](/cli/azure/install-azure-cli) ou Azure cloud Shell no navegador

## <a name="configure-logging"></a>Configurar registro em log
Você pode configurar o servidor para acessar logs de consulta e logs de erros. Os logs de erros podem ter informações de vácuo, conexão e ponto de verificação automáticos.
1. Ative o registro em log.
2. Para habilitar o log de consultas, atualize **log\_instrução** e **log\_instrução min\_Duration\_** .
3. Período de retenção de atualização.

Para obter mais informações, consulte [Personalizando parâmetros de configuração do servidor](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs"></a>Listar logs
Para listar os arquivos de log disponíveis para o servidor, execute o comando [AZ postgres Server-logs List](/cli/azure/postgres/server-logs) .

Você pode listar os arquivos de log para o servidor **mydemoserver.Postgres.Database.Azure.com** no grupo de recursos **MyResource**Group. Em seguida, direcione a lista de arquivos de log para um arquivo de texto chamado **log\_arquivos\_List. txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Baixar logs localmente do servidor
Com o comando [AZ postgres Server-logs download](/cli/azure/postgres/server-logs) , você pode baixar arquivos de log individuais para seu servidor. 

Use o exemplo a seguir para baixar o arquivo de log específico para o servidor **mydemoserver.Postgres.Database.Azure.com** no grupo de recursos **MyResource** Group para seu ambiente local.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Passos seguintes
- Para saber mais sobre logs de servidor, confira [logs de servidor no banco de dados do Azure para PostgreSQL](concepts-server-logs.md).
- Para obter mais informações sobre parâmetros de servidor, consulte [Personalizar parâmetros de configuração do servidor usando CLI do Azure](howto-configure-server-parameters-using-cli.md).
