---
title: 'Quickstart: Conecte-se usando Azure CLI - Base de Dados Azure para PostgreSQL - Servidor Flexível'
description: Este quickstart fornece várias formas de se conectar com O Azure CLI com Azure Database para PostgreSQL - Servidor Flexível.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/06/2021
ms.openlocfilehash: 7526644e02b0ed4d0522ad00a27b691ece98754a
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479241"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-postgresql---flexible-server"></a>Quickstart: Conecte-se e questione-se com o Azure CLI com a Azure Database for PostgreSQL - Servidor Flexível

> [!IMPORTANT]
> A Azure Database for PostgreSQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Este quickstart demonstra como ligar a uma Base de Dados Azure para o Servidor Flexível PostgreSQL utilizando o Azure CLI com ```az postgres flexible-server connect``` comando. Este comando permite testar a conectividade com o servidor da base de dados e executar consultas. Também pode executar várias consultas utilizando o modo interativo. 

## <a name="prerequisites"></a>Pré-requisitos
- Uma conta do Azure. Se não tiver um, [faça um julgamento gratuito.](https://azure.microsoft.com/free/)
- Instalar a versão mais recente do [Azure CLI](/cli/azure/install-azure-cli) (2.20.0 ou superior)
- Iniciar sessão usando O Azure CLI com ```az login``` comando 
- Ligue a persistência do parâmetro com ```az config param-persist on``` . A persistência de parâmetros irá ajudá-lo a usar o contexto local sem ter que repetir muitos argumentos como grupo de recursos ou localização.

## <a name="create-an-postgresql-flexible-server"></a>Criar um servidor flexível postgreSQL

A primeira coisa que vamos criar é um servidor PósgreSQL gerido. Em [Azure Cloud Shell](https://shell.azure.com/), execute o seguinte script e tome nota do nome do servidor , **nome** de **utilizador** e  **palavra-passe** gerados a partir deste comando.

```azurecli
az postgres flexible-server create --public-access <your-ip-address>
```
Pode fornecer argumentos adicionais para este comando para personalizá-lo. Consulte todos os argumentos para [a criação de postgres flexíveis az](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create).

## <a name="view-all-the-arguments"></a>Ver todos os argumentos
Pode ver todos os argumentos para este comando com ```--help``` argumento. 

```azurecli
az postgresql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Ligação do servidor de base de dados de teste
Pode testar e validar a ligação à base de dados a partir do seu ambiente de desenvolvimento utilizando o comando.

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```
**Exemplo:** 
```azurecli
az postgres flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d postgres
```
Verá a saída se a ligação foi bem sucedida.
```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

Se a ligação falhar, experimente estas soluções:
- Verifique se a porta 5432 está aberta na sua máquina cliente.
- se o nome de utilizador e a palavra-passe do administrador do servidor estiverem corretos
- se tiver uma regra de firewall configurada para a sua máquina cliente
- se configurar o seu servidor com acesso privado em rede virtual, certifique-se de que a sua máquina cliente está na mesma rede virtual.

## <a name="run-single-query"></a>Executar consulta única
Pode executar uma única consulta com o comando utilizando o ```--querytext``` argumento, ```-q``` .

```azurecli
az postgres flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> -q "<query-text>"
```

**Exemplo:** 
```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -q "select * from table1;" --output table
```

Verá uma saída como mostrado abaixo:

```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
Txt    Val
-----  -----
test   200
test   200
test   200
test   200
test   200
test   200
test   200
```

## <a name="run-multiple-queries-using-interactive-mode"></a>Executar várias consultas usando o modo interativo
Pode executar várias consultas utilizando o modo **interativo** . Para ativar o modo interativo, executar o seguinte comando

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```

**Exemplo:**

```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb --interactive
```

Você verá a experiência da concha **psql** como mostrado abaixo:

```bash
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password for earthyTurtle7:
Server: PostgreSQL 12.5
Version: 3.0.0
Chat: https://gitter.im/dbcli/pgcli
Home: http://pgcli.com
postgres> create database pollsdb;
CREATE DATABASE
Time: 0.308s
postgres> exit
Goodbye!
Local context is turned on. Its information is saved in working directory C:\sunitha. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```


## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Gerir o servidor](./how-to-manage-server-cli.md)
