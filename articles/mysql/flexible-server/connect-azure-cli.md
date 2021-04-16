---
title: 'Quickstart: Conecte-se usando Azure CLI - Base de Dados Azure para MySQL - Servidor Flexível'
description: Este quickstart fornece várias formas de se conectar com O Azure CLI com Azure Database para MySQL - Servidor Flexível.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/01/2021
ms.openlocfilehash: e0fd5969a3c4f84b6e8f98e99335bf120179e7af
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481094"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-mysql---flexible-server"></a>Quickstart: Conecte-se e questione-se com o Azure CLI com a Azure Database for MySQL - Servidor Flexível

> [!IMPORTANT]
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Este quickstart demonstra como ligar-se a uma Base de Dados Azure para o MySQL Flexible Server utilizando o Azure CLI com ```az mysql flexible-server connect``` comando. Este comando permite testar a conectividade do servidor da base de dados e executar consultas diretamente contra o seu servidor.  Também pode utilizar executar o comando num modo interativo para executar várias consultas.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se não tiver um, [faça um julgamento gratuito.](https://azure.microsoft.com/free/)
- Instalar a versão mais recente do [Azure CLI](/cli/azure/install-azure-cli) (2.20.0 ou superior)
- Iniciar sessão usando O Azure CLI com ```az login``` comando 
- Ligue a persistência do parâmetro com ```az config param-persist on``` . A persistência de parâmetros irá ajudá-lo a usar o contexto local sem ter que repetir muitos argumentos como grupo de recursos ou localização, etc.

## <a name="create-an-mysql-flexible-server"></a>Criar um Servidor Flexível MySQL

Iremos começar por criar um servidor MySQL gerido. Em [Azure Cloud Shell](https://shell.azure.com/), execute o seguinte script e tome nota do nome do servidor , **nome** de **utilizador** e  **palavra-passe** gerados a partir deste comando.

```azurecli
az mysql flexible-server create --public-access <your-ip-address>
```

Pode fornecer argumentos adicionais para este comando para personalizá-lo. Consulte todos os argumentos para [criar um servidor flexível az mysql](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create).

## <a name="create-a-database"></a>Criar uma base de dados
Executar o seguinte comando para criar uma base de **dados, nova base de dados** se ainda não tiver criado uma.

```azurecli
az mysql flexible-server db create -d newdatabase
```

## <a name="view-all-the-arguments"></a>Ver todos os argumentos
Pode ver todos os argumentos para este comando com ```--help``` argumento. 

```azurecli
az mysql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Ligação do servidor de base de dados de teste
Execute o seguinte script para testar e validar a ligação à base de dados a partir do seu ambiente de desenvolvimento.

```azurecli
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```

**Exemplo:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```

Deve ver a seguinte saída para uma ligação bem sucedida:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Se a ligação falhar, experimente estas soluções:
- Verifique se a porta 3306 está aberta na sua máquina cliente.
- se o nome de utilizador e a palavra-passe do administrador do servidor estiverem corretos
- se tiver uma regra de firewall configurada para a sua máquina cliente
- se configurar o seu servidor com acesso privado em rede virtual, certifique-se de que a sua máquina cliente está na mesma rede virtual.

## <a name="run-single-query"></a>Executar consulta única
Execute o seguinte comando para executar uma única consulta utilizando ```--querytext``` o argumento, ```-q``` .

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Exemplo:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```

Verá uma saída como mostrado abaixo:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to mysqldemoserver1.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection to mysqldemoserver1
Local context is turned on. Its information is saved in working directory C:\Users\sumuth. You can run `az local-context off` to turn it off.
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
Pode executar várias consultas utilizando o modo **interativo.** Para ativar o modo interativo, executar o seguinte comando

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p <password> --interactive
```

**Exemplo:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase --interactive
```

Você verá a experiência da concha **MySQL** como mostrado abaixo:

```bash
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password:
mysql 5.7.29-log
mycli 1.22.2
Chat: https://gitter.im/dbcli/mycli
Mail: https://groups.google.com/forum/#!forum/mycli-users
Home: http://mycli.net
Thanks to the contributor - Martijn Engler
newdatabase> CREATE TABLE table1 (id int NOT NULL, val int,txt varchar(200));
Query OK, 0 rows affected
Time: 2.290s
newdatabase1> INSERT INTO table1 values (1,100,'text1');
Query OK, 1 row affected
Time: 0.199s
newdatabase1> SELECT * FROM table1;
+----+-----+-------+
| id | val | txt   |
+----+-----+-------+
| 1  | 100 | text1 |
+----+-----+-------+
1 row in set
Time: 0.149s
newdatabase>exit;
Goodbye!
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```


## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
* [Ligue à Base de Dados Azure para o MySQL - Servidor Flexível com ligações encriptadas](how-to-connect-tls-ssl.md)
* [Gerir o servidor](./how-to-manage-server-cli.md)

