---
title: Gerir o servidor - Azure CLI - Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Saiba como gerir uma Base de Dados Azure para PostgreSQL - Servidor Flexível a partir do CLI Azure.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 6c088188802590139ee895c45fb26959138db07e
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91939805"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>Gerir uma base de dados de Azure para PostgreSQL - Servidor Flexível utilizando o Azure CLI

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

Este artigo mostra-lhe como gerir o seu Servidor Flexível implantado no Azure. As tarefas de gestão incluem o dimensionamento de cálculo e armazenamento, o reset da palavra-passe de administração e a visualização de detalhes do servidor.

## <a name="prerequisites"></a>Pré-requisitos
Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar. Este artigo requer que esteja a executar a versão 2.0 do Azure CLI ou mais tarde localmente. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

Terá de iniciar sessão na sua conta utilizando o comando [de login az.](https://docs.microsoft.com/cli/azure/reference-index#az-login) Note a propriedade **id,** que se refere ao **ID de subscrição** para a sua conta Azure.

```azurecli-interactive
az login
```

Selecione a subscrição específica sob a sua conta usando o comando [conjunto de conta az.](/cli/azure/account) Tome nota do valor de **id** da saída de **login az** para usar como valor para o argumento **de subscrição** no comando. Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Para obter toda a sua subscrição, utilize [a lista de conta az](https://docs.microsoft.com/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Se ainda não criou um servidor flexível, por favor crie um para começar com este como orientar.

## <a name="scale-compute-and-storage"></a>Cálculo e armazenamento em escala

Pode escalar o seu nível de cálculo, vCores e armazenamento facilmente usando o seguinte comando. Pode ver toda a operação do servidor que pode executar [a visão geral do servidor flexível do servidor de pós-contas](https://azure.microsoft.com/services/postgresql/)

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Aqui estão os detalhes dos argumentos acima:

**Definição** | **Valor de exemplo** | **Descrição**
---|---|---
name | mydemoserver | Insira um nome único para o seu servidor. O nome do servidor pode conter apenas letras minúsculas, números e o caráter de hífen (-). Tem de conter entre 3 e 63 carateres.
resource-group | myResourceGroup | Indique o nome do grupo de recursos do Azure.
sku-name|Standard_D4ds_v3|Insira o nome do nível e tamanho do cálculo. Segue a convenção Standard_{VM tamanho} em abreviatura. Consulte os [níveis de preços](../concepts-pricing-tiers.md) para obter mais informações.
storage-size | 6144 | A capacidade de armazenamento do servidor (a unidade é megabytes). Mínimo 5120 e aumentos em 1024 incrementos.

> [!IMPORTANT]
> O armazenamento não pode ser reduzido. 

## <a name="manage-postgresql-databases-on-a-server"></a>Gerir bases de dados PostgreSQL num servidor

Estão disponíveis diversas aplicações para ligar à sua Base de Dados do Azure para o servidor PostSQL. Se o seu computador cliente tiver o PostgreSQL instalado, pode utilizar uma instância local de [psql](https://www.postgresql.org/docs/current/static/app-psql.html) para ligar a um servidor PostgreSQL do Azure. Vamos utilizar agora o utilitário da linha de comandos psql para ligar ao servidor PostgreSQL do Azure.

1. Execute o comando psql seguinte para ligar a uma Base de Dados do Azure para o servidor PostgreSQL

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Por exemplo, o comando seguinte liga à base de dados predefinida com o nome **postgres** no servidor PostgreSQL **mydemoserver.postgres.database.azure.com** com as credenciais de acesso. Introduza o `<server_admin_password>` que escolheu quando lhe for pedida a palavra-passe.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Quando estiver ligado, o utilitário psql apresenta uma linha de comandos postgres onde escreve os comandos de sql. Na saída da ligação inicial, poderá aparecer um aviso, porque o psql que está a utilizar pode ter uma versão diferente da versão do servidor da Base de Dados do Azure para PostgreSQL.

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
   > "psql: FATAL: no pg_hba.conf entrada para hospedeiro `<IP address>` , utilizador "myadmin", base de dados "postgres", SSL em FATAL: Ligação SSL é necessária. Especifique as opções de SSL e volte a tentar.
   >
   > Confirme que o IP do seu cliente é permitido nas regras de firewall.

2. Crie uma base de dados em branco chamada "postgresdb" no pedido, digitando o seguinte comando:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. A pedido, execute o seguinte comando para mudar as ligações para o **pós-campanha de**base de dados recém-criado:

    ```bash
    \c postgresdb
    ```

4. Escreva `\q` e selecione a tecla Enter para sair do psql.

Ligou-se ao servidor da Base de Dados do Azure para PostgreSQL através do psql e criou uma base de dados de utilizador em branco.

## <a name="reset-admin-password"></a>Redefinir a palavra-passe de administração
Pode alterar a palavra-passe da função do administrador com este comando
```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Certifique-se de que a palavra-passe é mínima de 8 caracteres e máximo de 128 caracteres.
> A palavra-passe deve conter caracteres de três das seguintes categorias: letras maiúsculas em inglês, letras minúsculas, números e caracteres não alfanuméricos.

## <a name="delete-a-server"></a>Excluir um servidor

Se quiser eliminar o servidor Flexível PostgreSQL, pode executar o comando [de eliminação flexível do servidor de pós-folhas az.](/cli/azure/postgres/flexible-server#az-PostgreSQL-flexible-server-delete)

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passos seguintes

- [Compreender backup e restaurar conceitos](concepts-backup-restore.md)
- [Sintonize e monitorize o servidor](concepts-monitoring.md)
