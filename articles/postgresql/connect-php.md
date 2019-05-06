---
title: Utilizar o PHP para ligar à base de dados do Azure para PostgreSQL - servidor único
description: Este início rápido fornece um exemplo de código PHP que pode utilizar para ligar e consultar dados da base de dados do Azure para PostgreSQL - único servidor.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.devlang: php
ms.topic: quickstart
ms.date: 2/28/2018
ms.openlocfilehash: c6320eabd4d5808e8cfe1d39ec4a72bbbce42f77
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067366"
---
# <a name="azure-database-for-postgresql---single-server-use-php-to-connect-and-query-data"></a>Base de dados do Azure para PostgreSQL - servidor único: Utilizar o PHP para se ligar e consultar dados
Este início rápido explica como se pode ligar a uma Base de Dados do Azure para PostgreSQL através de uma aplicação [PHP](https://secure.php.net/manual/intro-whatis.php). Explica como utilizar as instruções SQL para consultar, inserir, atualizar e eliminar dados da base de dados. Os passos neste artigo pressupõem que está familiarizado com a programação com PHP e que nunca trabalhou com a Base de Dados do Azure para PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos
Este guia de início rápido utiliza os recursos criados em qualquer um destes guias como ponto de partida:
- [Criar BD - Portal](quickstart-create-server-database-portal.md)
- [Criar BD - CLI do Azure](quickstart-create-server-database-azure-cli.md)

## <a name="install-php"></a>Instalar o PHP
Instale o PHP no seu próprio servidor ou crie uma [aplicação Web](../app-service/overview.md) do Azure que inclua o PHP.

### <a name="windows"></a>Windows
- Transfira a [versão 7.1.4 do PHP (x64) não segura para threads](https://windows.php.net/download#php-7.1)
- Instale o PHP e veja o [manual do PHP](https://secure.php.net/manual/install.windows.php) para mais configurações
- O código utiliza a classe **pgsql** (ext/php_pgsql.dll) incluída na instalação do PHP. 
- Ative a extensão **pgsql** ao editar o ficheiro de configuração php.ini, geralmente localizado em `C:\Program Files\PHP\v7.1\php.ini`. O ficheiro de configuração deve conter uma linha com o texto `extension=php_pgsql.so`. Se não for apresentado, adicione o texto e guarde o ficheiro. Se o texto estiver presente, mas comentado com um prefixo de ponto e vírgula, remova os comentários do texto ao remover o ponto e vírgula.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
- Transfira a [versão 7.1.4 do PHP (x64) não segura para threads](https://secure.php.net/downloads.php) 
- Instale o PHP e consulte o [manual do PHP](https://secure.php.net/manual/install.unix.php) para mais configurações
- O código utiliza a classe **pgsql** (php_pgsql.so). Instale-o ao executar `sudo apt-get install php-pgsql`.
- Ative a extensão **pgsql** ao editar o ficheiro de configuração `/etc/php/7.0/mods-available/pgsql.ini`. O ficheiro de configuração deve conter uma linha com o texto `extension=php_pgsql.so`. Se não for apresentado, adicione o texto e guarde o ficheiro. Se o texto estiver presente, mas comentado com um prefixo de ponto e vírgula, remova os comentários do texto ao remover o ponto e vírgula.

### <a name="macos"></a>MacOS
- Transfira a [versão 7.1.4 do PHP](https://secure.php.net/downloads.php)
- Instale o PHP e consulte o [manual do PHP](https://secure.php.net/manual/install.macosx.php) para mais configurações

## <a name="get-connection-information"></a>Obter informações da ligação
Obtenha as informações de ligação necessárias para se ligar à Base de Dados do Azure para PostgreSQL. Necessita do nome do servidor e das credenciais de início de sessão totalmente qualificados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu esquerdo do portal do Azure, clique em **Todos os recursos** e, em seguida, procure o servidor que acabou de criar, (por exemplo, **mydemoserver**).
3. Clique no nome do servidor.
4. No painel **Descrição geral** do servidor, tome nota do **Nome do servidor** e do **Nome de início de sessão de administrador do servidor**. Caso se esqueça da sua palavra-passe, também pode repor a palavra-passe neste painel.
 ![Nome do servidor da Base de Dados do Azure para PostgreSQL](./media/connect-php/1-connection-string.png)

## <a name="connect-and-create-a-table"></a>Ligar-se e criar uma tabela
Utilize o código seguinte para ligar e criar uma tabela com a instrução SQL **CREATE TABLE**, seguida das instruções SQL **INSERT INTO** para adicionar linhas à tabela.

O código chama o método [pg_connect()](https://secure.php.net/manual/en/function.pg-connect.php) para ligar à Base de Dados do Azure para PostgreSQL. Em seguida, chama o método [pg_query()](https://secure.php.net/manual/en/function.pg-query.php) várias vezes para executar vários comandos e [pg_last_error()](https://secure.php.net/manual/en/function.pg-last-error.php) para verificar os detalhes, se tiver ocorrido um erro. Em seguida, chama o método [pg_close()](https://secure.php.net/manual/en/function.pg-close.php) para fechar a ligação.

Substitua os parâmetros `$host`, `$database`, `$user` e `$password` pelos seus próprios valores. 

```php
<?php
    // Initialize connection variables.
    $host = "mydemoserver.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mydemoserver";
    $password = "<server_admin_password>";

    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password") 
        or die("Failed to create connection to database: ". pg_last_error(). "<br/>");
    print "Successfully created connection to database.<br/>";

    // Drop previous table of same name if one exists.
    $query = "DROP TABLE IF EXISTS inventory;";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");
    print "Finished dropping table (if existed).<br/>";

    // Create table.
    $query = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");
    print "Finished creating table.<br/>";

    // Insert some data into table.
    $name = '\'banana\'';
    $quantity = 150;
    $query = "INSERT INTO inventory (name, quantity) VALUES ($1, $2);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");

    $name = '\'orange\'';
    $quantity = 154;
    $query = "INSERT INTO inventory (name, quantity) VALUES ($name, $quantity);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");

    $name = '\'apple\'';
    $quantity = 100;
    $query = "INSERT INTO inventory (name, quantity) VALUES ($name, $quantity);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error()). "<br/>";

    print "Inserted 3 rows of data.<br/>";

    // Closing connection
    pg_close($connection);
?>
```

## <a name="read-data"></a>Ler dados
Utilize o código seguinte para se ligar e ler dados com uma instrução SQL **SELECT**. 

 O código chama o método [pg_connect()](https://secure.php.net/manual/en/function.pg-connect.php) para ligar à Base de Dados do Azure para PostgreSQL. Em seguida, chama o método [pg_query()](https://secure.php.net/manual/en/function.pg-query.php) para executar o comando SELECT ao manter os resultados num conjunto de resultados, e [pg_last_error()](https://secure.php.net/manual/en/function.pg-last-error.php) para verificar os detalhes se tiver ocorrido um erro.  Para ler o conjunto de resultados, é chamado o método [pg_fetch_row()](https://secure.php.net/manual/en/function.pg-fetch-row.php) num ciclo, uma vez por linha, e os dados das linhas são obtidos numa matriz `$row`, com um valor de dados por coluna em cada posição de matriz.  Para libertar o conjunto de resultados, é chamado o método [pg_free_result()](https://secure.php.net/manual/en/function.pg-free-result.php). Em seguida, chama o método [pg_close()](https://secure.php.net/manual/en/function.pg-close.php) para fechar a ligação.

Substitua os parâmetros `$host`, `$database`, `$user` e `$password` pelos seus próprios valores. 

```php
<?php
    // Initialize connection variables.
    $host = "mydemoserver.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mydemoserver";
    $password = "<server_admin_password>";
    
    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password")
                or die("Failed to create connection to database: ". pg_last_error(). "<br/>");

    print "Successfully created connection to database. <br/>";

    // Perform some SQL queries over the connection.
    $query = "SELECT * from inventory";
    $result_set = pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");
    while ($row = pg_fetch_row($result_set))
    {
        print "Data row = ($row[0], $row[1], $row[2]). <br/>";
    }

    // Free result_set
    pg_free_result($result_set);

    // Closing connection
    pg_close($connection);
?>
```

## <a name="update-data"></a>Atualizar dados
Utilize o código seguinte para se ligar e atualizar os dados com uma instrução SQL **UPDATE**.

O código chama o método [pg_connect()](https://secure.php.net/manual/en/function.pg-connect.php) para ligar à Base de Dados do Azure para PostgreSQL. Em seguida, chama o método [pg_query()](https://secure.php.net/manual/en/function.pg-query.php) para executar um comando e [pg_last_error()](https://secure.php.net/manual/en/function.pg-last-error.php) para verificar os detalhes, se tiver ocorrido um erro. Em seguida, chama o método [pg_close()](https://secure.php.net/manual/en/function.pg-close.php) para fechar a ligação.

Substitua os parâmetros `$host`, `$database`, `$user` e `$password` pelos seus próprios valores. 

```php
<?php
    // Initialize connection variables.
    $host = "mydemoserver.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mydemoserver";
    $password = "<server_admin_password>";

    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password")
                or die("Failed to create connection to database: ". pg_last_error(). ".<br/>");

    print "Successfully created connection to database. <br/>";

    // Modify some data in table.
    $new_quantity = 200;
    $name = '\'banana\'';
    $query = "UPDATE inventory SET quantity = $new_quantity WHERE name = $name;";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). ".<br/>");
    print "Updated 1 row of data. </br>";

    // Closing connection
    pg_close($connection);
?>
```


## <a name="delete-data"></a>Eliminar dados
Utilize o código seguinte para se ligar e ler os dados com a instrução SQL **DELETE**. 

 O código chama o método [pg_connect()](https://secure.php.net/manual/en/function.pg-connect.php) para ligar à Base de Dados do Azure para PostgreSQL. Em seguida, chama o método [pg_query()](https://secure.php.net/manual/en/function.pg-query.php) para executar um comando e [pg_last_error()](https://secure.php.net/manual/en/function.pg-last-error.php) para verificar os detalhes, se tiver ocorrido um erro. Em seguida, chama o método [pg_close()](https://secure.php.net/manual/en/function.pg-close.php) para fechar a ligação.

Substitua os parâmetros `$host`, `$database`, `$user` e `$password` pelos seus próprios valores. 

```php
<?php
    // Initialize connection variables.
    $host = "mydemoserver.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mydemoserver";
    $password = "<server_admin_password>";

    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password")
            or die("Failed to create connection to database: ". pg_last_error(). ". </br>");

    print "Successfully created connection to database. <br/>";

    // Delete some data from table.
    $name = '\'orange\'';
    $query = "DELETE FROM inventory WHERE name = $name;";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). ". <br/>");
    print "Deleted 1 row of data. <br/>";

    // Closing connection
    pg_close($connection);
?>
```

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Migrar a base de dados com Exportar e Importar](./howto-migrate-using-export-and-import.md)
