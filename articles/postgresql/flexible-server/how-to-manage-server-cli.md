---
title: Gerir o servidor - Azure CLI - Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Saiba como gerir uma Base de Dados Azure para PostgreSQL - Servidor Flexível a partir do CLI Azure.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 278f8f816909a7e365d7e45d04c5169950e79a65
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96493683"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-by-using-the-azure-cli"></a>Gerir uma Base de Dados Azure para PostgreSQL - Servidor Flexível utilizando o Azure CLI

> [!IMPORTANT]
> A base de dados Azure para PostgreSQL - Servidor Flexível está em pré-visualização.

Este artigo mostra-lhe como gerir o seu servidor flexível implantado no Azure. As tarefas de gestão incluem o dimensionamento de cálculo e armazenamento, o reset da palavra-passe de administração e a visualização de detalhes do servidor.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar. 

Você precisará executar a versão 2.0 do Azure CLI, ou mais tarde, localmente. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Inicie sessão na sua conta utilizando o comando [de login az.](/cli/azure/reference-index#az-login) 

```azurecli-interactive
az login
```

Selecione a sua subscrição utilizando o comando [conjunto de conta az.](/cli/azure/account) Tome nota do valor de **id** da saída de **login az** para usar como valor para o argumento de **subscrição** no seguinte comando. Se tiver várias subscrições, escolha a subscrição para a qual o recurso deve ser faturado. Para identificar todas as suas subscrições, utilize o comando [da lista de conta az.](/cli/azure/account#az-account-list)

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Se ainda não criou um servidor flexível, terá de o fazer para seguir este guia de como fazer.

## <a name="scale-compute-and-storage"></a>Cálculo e armazenamento em escala

Pode escalar facilmente o seu nível de cálculo, vCores e armazenamento utilizando o seguinte comando. Para obter uma lista de todas as operações do servidor que pode executar, consulte a visão [geral do servidor flexível az postgres.](/cli/azure/postgres/flexible-server)

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Seguem-se os pormenores relativos aos argumentos do código anterior:

**Definição** | **Valor de exemplo** | **Descrição**
---|---|---
name | mydemoserver | Insira um nome único para o seu servidor. O nome do servidor pode conter apenas letras minúsculas, números e o caráter de hífen (-). Deve conter 3 a 63 caracteres.
resource-group | myResourceGroup | Indique o nome do grupo de recursos do Azure.
sku-name|Standard_D4ds_v3|Insira o nome do nível e tamanho do cálculo. O valor segue a convenção *Standard_{VM tamanho}* em abreviatura. Consulte os [níveis de preços](../concepts-pricing-tiers.md) para obter mais informações.
storage-size | 6144 | Introduza a capacidade de armazenamento do servidor em megabytes. O mínimo é de 5120, aumentando em incrementos de 1024.

> [!IMPORTANT]
> Não se pode reduzir o armazenamento. 

## <a name="manage-postgresql-databases-on-a-server"></a>Gerir bases de dados PostgreSQL num servidor

Estão disponíveis diversas aplicações para ligar à sua Base de Dados do Azure para o servidor PostSQL. Se o seu computador cliente tiver PostgreSQL instalado, pode utilizar uma instância local de [psql.](https://www.postgresql.org/docs/current/static/app-psql.html) Vamos agora utilizar a ferramenta de linha de comando psql para ligar à Base de Dados Azure para o servidor PostgreSQL.

1. Executar o seguinte comando **psql:**

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Por exemplo, o seguinte comando liga-se à base de dados predefinido chamada **postgres** no seu servidor PostgreSQL mydemoserver.postgres.database.azure.com através **das** suas credenciais de acesso. Quando for solicitado, entre na `<server_admin_password>` que escolheu.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Depois de ligar, a ferramenta psql exibe uma solicitação **de postgres** onde pode introduzir comandos SQL. Aparecerá um aviso na saída inicial da ligação se a versão do psql que está a utilizar for diferente da versão no servidor Azure Database para o servidor PostgreSQL.

   Exemplo de saída psql:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Se a firewall não estiver configurada para permitir o endereço IP do seu cliente, ocorre o seguinte erro:
   >
   > "psql: FATAL: no pg_hba.conf entrada para hospedeiro `<IP address>` , utilizador "myadmin", base de dados "postgres", SSL em FATAL: Ligação SSL é necessária. Especifique as opções SSL e redabile."
   >
   > Confirme que o endereço IP do seu cliente é permitido nas regras de firewall.

2. Crie uma base de dados em branco chamada **postgresdb** digitando o seguinte comando à medida:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. A pedido, executar o seguinte comando para mudar as ligações para o **pós-campanha de** base de dados recém-criado:

    ```bash
    \c postgresdb
    ```

4. Digite  `\q` e selecione Enter para sair do PSQL.

Nesta secção, ligou-se à Base de Dados Azure para o servidor PostgreSQL via PSQL e criou uma base de dados de utilizadores em branco.

## <a name="reset-the-admin-password"></a>Redefinir a senha de administração

Pode alterar a palavra-passe da função do administrador com o seguinte comando:

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Escolha uma palavra-passe com um mínimo de 8 caracteres e um máximo de 128 caracteres. A palavra-passe deve conter caracteres de três das seguintes categorias: 
> - Letras maiúsculas
> - Letras minúsculas
> - Números
> - Caracteres não alfanuméricos

## <a name="delete-a-server"></a>Excluir um servidor

Para eliminar o Azure Database para o servidor flexível PostgreSQL, executar o comando [de eliminação flexível do servidor de pós-folhas az.](/cli/azure/postgres/flexible-server#az-PostgreSQL-flexible-server-delete)

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passos seguintes

- [Compreender backup e restaurar conceitos](concepts-backup-restore.md)
- [Sintonize e monitorize o servidor](concepts-monitoring.md)