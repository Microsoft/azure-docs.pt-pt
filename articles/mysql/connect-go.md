---
title: Ligar à Base de Dados do Azure para MySQL através de Go
description: Este início rápido proporciona vários exemplos de código Go que pode utilizar para se ligar e consultar dados da Base de Dados do Azure para MySQL.
author: jasonwhowell
ms.author: jasonh
ms.service: mysql
ms.custom: mvc
ms.devlang: go
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: 4abc34044d188be6b770439f06e8d4dab7f3ab93
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57897661"
---
# <a name="azure-database-for-mysql-use-go-language-to-connect-and-query-data"></a>Base de Dados do Azure para MySQL: Utilize a linguagem Go para ligar e consultar dados
Este guia de introdução demonstra como se pode ligar a uma Base de Dados do Azure para MySQL a partir de plataformas Windows, Ubuntu, Linux e Apple macOS com código escrito na linguagem [Go](https://golang.org/). Explica como utilizar as instruções SQL para consultar, inserir, atualizar e eliminar dados da base de dados. Este tópico pressupõe que está familiarizado com a programação com Go e que nunca trabalhou com a Base de Dados do Azure para MySQL.

## <a name="prerequisites"></a>Pré-requisitos
Este guia de início rápido utiliza os recursos criados em qualquer um desTes guias como ponto de partida:
- [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar uma Base de Dados do Azure para o servidor MySQL com a CLI do Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-go-and-mysql-connector"></a>Instalar o Go e o conector MySQL
Instale o [Go](https://golang.org/doc/install) e o [go-sql-driver para MySQL](https://github.com/go-sql-driver/mysql#installation) no computador. Dependendo da sua plataforma, siga os passos na secção apropriada:

### <a name="windows"></a>Windows
1. [Transfira](https://golang.org/dl/) e instale o Go para o Microsoft Windows, de acordo com as [instruções de instalação](https://golang.org/doc/install).
2. Inicie a linha de comandos a partir do menu Iniciar.
3. Crie uma pasta para o seu projeto, como `mkdir  %USERPROFILE%\go\src\mysqlgo`.
4. Altere o diretório para a pasta do projeto, como `cd %USERPROFILE%\go\src\mysqlgo`.
5. Defina a variável de ambiente de GOPATH para apontar para o diretório do código de origem. `set GOPATH=%USERPROFILE%\go`.
6. Execute o comando `go get github.com/go-sql-driver/mysql` para instalar o [go-sql-driver para mysql](https://github.com/go-sql-driver/mysql#installation).

   Para resumir, instale o Go e, em seguida, execute estes comandos na linha de comandos:
   ```cmd
   mkdir  %USERPROFILE%\go\src\mysqlgo
   cd %USERPROFILE%\go\src\mysqlgo
   set GOPATH=%USERPROFILE%\go
   go get github.com/go-sql-driver/mysql
   ```

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Inicie a shell de Bash. 
2. Execute `sudo apt-get install golang-go` para instalar o Go.
3. Crie uma pasta para o projeto no seu diretório raiz, como `mkdir -p ~/go/src/mysqlgo/`.
4. Altere o diretório para a pasta, como `cd ~/go/src/mysqlgo/`.
5. Defina a variável de ambiente GOPATH para apontar para um diretório de origem válido, como a pasta do go do diretório raiz atual. Na shell do Bash, execute `export GOPATH=~/go` para adicionar o diretório do go como o GOPATH da sessão de shell atual.
6. Execute o comando `go get github.com/go-sql-driver/mysql` para instalar o [go-sql-driver para mysql](https://github.com/go-sql-driver/mysql#installation).

   Em resumo, execute estes comandos do bash:
   ```bash
   sudo apt-get install golang-go
   mkdir -p ~/go/src/mysqlgo/
   cd ~/go/src/mysqlgo/
   export GOPATH=~/go/
   go get github.com/go-sql-driver/mysql
   ```

### <a name="apple-macos"></a>Apple macOS
1. Transfira e instale o Go em conformidade com as [instruções de instalação](https://golang.org/doc/install) da sua plataforma. 
2. Inicie a shell de Bash.
3. Crie uma pasta para o projeto no seu diretório raiz, como `mkdir -p ~/go/src/mysqlgo/`.
4. Altere o diretório para a pasta, como `cd ~/go/src/mysqlgo/`.
5. Defina a variável de ambiente GOPATH para apontar para um diretório de origem válido, como a pasta do go do diretório raiz atual. Na shell do Bash, execute `export GOPATH=~/go` para adicionar o diretório do go como o GOPATH da sessão de shell atual.
6. Execute o comando `go get github.com/go-sql-driver/mysql` para instalar o [go-sql-driver para mysql](https://github.com/go-sql-driver/mysql#installation).

   Em resumo, instale o Go e execute estes comandos do bash:
   ```bash
   mkdir -p ~/go/src/mysqlgo/
   cd ~/go/src/mysqlgo/
   export GOPATH=~/go/
   go get github.com/go-sql-driver/mysql
   ```

## <a name="get-connection-information"></a>Obter informações da ligação
Obtenha as informações de ligação necessárias para se ligar à Base de Dados do Azure para MySQL. Necessita do nome do servidor e das credenciais de início de sessão totalmente qualificados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu esquerdo do portal do Azure, clique em **Todos os recursos** e, em seguida, procure o servidor que acabou de criar, (por exemplo, **mydemoserver**).
3. Clique no nome do servidor.
4. No painel **Descrição geral** do servidor, tome nota do **Nome do servidor** e do **Nome de início de sessão de administrador do servidor**. Caso se esqueça da sua palavra-passe, também pode repor a palavra-passe neste painel.
 ![Nome do servidor da Base de Dados do Azure para o MySQL](./media/connect-go/1_server-overview-name-login.png)
   

## <a name="build-and-run-go-code"></a>Criar e executar código Go 
1. Para escrever o código Golang, pode utilizar um editor de textos simples, como o Blobo de Notas no Microsoft Windows, o [vi](https://manpages.ubuntu.com/manpages/xenial/man1/nvi.1.html#contenttoc5) ou o [Nano](https://www.nano-editor.org/) no Ubuntu ou o TextEdit em macOS. Se preferir um Ambiente de Desenvolvimento Interativo (IDE) rico, experimente o [Gogland](https://www.jetbrains.com/go/), da Jetbrains, o [Visual Studio Code](https://code.visualstudio.com/), da Microsoft, ou o [Atom](https://atom.io/).
2. Cole o código Go das secções seguintes em ficheiros de texto e, em seguida, guarde-os na pasta do projeto com a extensão de ficheiro \*.go, (como, por exemplo, o caminho do Windows `%USERPROFILE%\go\src\mysqlgo\createtable.go` ou o caminho do Linux `~/go/src/mysqlgo/createtable.go`).
3. Localize as constantes `HOST`, `DATABASE`, `USER` e `PASSWORD` no código e, em seguida, substitua os valores de exemplo pelos seus próprios valores. 
4. Inicie a linha de comandos ou a shell de Bash. Altere o diretório para a pasta do projeto . Por exemplo, no Windows `cd %USERPROFILE%\go\src\mysqlgo\`. No Linux, `cd ~/go/src/mysqlgo/`.  Alguns dos editores de IDE mencionados oferecem capacidades de depuração e runtime sem que sejam necessários comandos da shell.
5. Execute o código com o comando `go run createtable.go`, para compilar a aplicação e executá-la. 
6. Em alternativa, para criar o código numa aplicação nativa, `go build createtable.go`, e inicie `createtable.exe` para executar a aplicação.

## <a name="connect-create-table-and-insert-data"></a>Ligar, criar tabela e inserir dados
Utilize o seguinte código para se ligar ao servidor, criar uma tabela e carregar os dados com uma instrução SQL **INSERT**. 

O código importa três pacotes: o [pacote sql](https://golang.org/pkg/database/sql/), o [go sql driver for mysql](https://github.com/go-sql-driver/mysql#installation) como um controlador para comunicar com a Base de Dados do Azure para MySQL e o [pacote fmt](https://golang.org/pkg/fmt/) para entrada e saída de dados impressos na linha de comandos.

O código chama o método [sql.Open()](http://go-database-sql.org/accessing.html) para ligar à Base de Dados do Azure para MySQL e verifica a ligação através do método [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). É utilizado um [identificador de base de dados](https://golang.org/pkg/database/sql/#DB) ao manter o conjunto de ligações para o servidor da base de dados. O código chama o método[Exec()](https://golang.org/pkg/database/sql/#DB.Exec) várias vezes para executar vários comandos DDL. Também utiliza [Prepare()](http://go-database-sql.org/prepared.html) e Exec() para executar instruções preparadas com diferentes parâmetros para inserir três linhas. De cada vez, é utilizado um método checkError() personalizado para verificar se ocorreu um erro e se deve ser chamado o método "panic", para sair.

Substitua as constantes `host`, `database`, `user` e `password` pelos seus próprios valores. 

```Go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "mydemoserver.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@mydemoserver"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Drop previous table of same name if one exists.
    _, err = db.Exec("DROP TABLE IF EXISTS inventory;")
    checkError(err)
    fmt.Println("Finished dropping table (if existed).")

    // Create table.
    _, err = db.Exec("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
    checkError(err)
    fmt.Println("Finished creating table.")

    // Insert some data into table.
    sqlStatement, err := db.Prepare("INSERT INTO inventory (name, quantity) VALUES (?, ?);")
    res, err := sqlStatement.Exec("banana", 150)
    checkError(err)
    rowCount, err := res.RowsAffected()
    fmt.Printf("Inserted %d row(s) of data.\n", rowCount)

    res, err = sqlStatement.Exec("orange", 154)
    checkError(err)
    rowCount, err = res.RowsAffected()
    fmt.Printf("Inserted %d row(s) of data.\n", rowCount)

    res, err = sqlStatement.Exec("apple", 100)
    checkError(err)
    rowCount, err = res.RowsAffected()
    fmt.Printf("Inserted %d row(s) of data.\n", rowCount)
    fmt.Println("Done.")
}

```

## <a name="read-data"></a>Ler dados
Utilize o código seguinte para se ligar e ler dados com uma instrução SQL **SELECT**. 

O código importa três pacotes: o [pacote sql](https://golang.org/pkg/database/sql/), o [go sql driver for mysql](https://github.com/go-sql-driver/mysql#installation) como um controlador para comunicar com a Base de Dados do Azure para MySQL e o [pacote fmt](https://golang.org/pkg/fmt/) para entrada e saída de dados impressos na linha de comandos.

O código chama o método [sql.Open()](http://go-database-sql.org/accessing.html) para ligar à Base de Dados do Azure para MySQL e verifica a ligação através do método [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). É utilizado um [identificador de base de dados](https://golang.org/pkg/database/sql/#DB) ao manter o conjunto de ligações para o servidor da base de dados. O código chama o método [Query()](https://golang.org/pkg/database/sql/#DB.Query) para executar o comando de seleção. Em seguida, executa[Next()](https://golang.org/pkg/database/sql/#Rows.Next) para iterar o conjunto de resultados e [Scan()](https://golang.org/pkg/database/sql/#Rows.Scan) para analisar os valores das colunas, guardando os valores em variáveis. De cada vez, é utilizado um método checkError() personalizado para verificar se ocorreu um erro e se deve ser chamado o método “panic”, para sair.

Substitua as constantes `host`, `database`, `user` e `password` pelos seus próprios valores. 

```Go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "mydemoserver.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@mydemoserver"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Variables for printing column data when scanned.
    var (
        id       int
        name     string
        quantity int
    )

    // Read some data from the table.
    rows, err := db.Query("SELECT id, name, quantity from inventory;")
    checkError(err)
    defer rows.Close()
    fmt.Println("Reading data:")
    for rows.Next() {
        err := rows.Scan(&id, &name, &quantity)
        checkError(err)
        fmt.Printf("Data row = (%d, %s, %d)\n", id, name, quantity)
    }
    err = rows.Err()
    checkError(err)
    fmt.Println("Done.")
}
```

## <a name="update-data"></a>Atualizar dados
Utilize o código seguinte para se ligar e atualizar os dados com a instrução SQL **UPDATE**. 

O código importa três pacotes: o [pacote sql](https://golang.org/pkg/database/sql/), o [go sql driver for mysql](https://github.com/go-sql-driver/mysql#installation) como um controlador para comunicar com a Base de Dados do Azure para MySQL e o [pacote fmt](https://golang.org/pkg/fmt/) para entrada e saída de dados impressos na linha de comandos.

O código chama o método [sql.Open()](http://go-database-sql.org/accessing.html) para ligar à Base de Dados do Azure para MySQL e verifica a ligação através do método [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). É utilizado um [identificador de base de dados](https://golang.org/pkg/database/sql/#DB) ao manter o conjunto de ligações para o servidor da base de dados. O código chama o método [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) para executar o comando de atualização. De cada vez, é utilizado um método checkError() personalizado para verificar se ocorreu um erro e se deve ser chamado o método “panic”, para sair.

Substitua as constantes `host`, `database`, `user` e `password` pelos seus próprios valores. 

```Go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "mydemoserver.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@mydemoserver"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Modify some data in table.
    rows, err := db.Exec("UPDATE inventory SET quantity = ? WHERE name = ?", 200, "banana")
    checkError(err)
    rowCount, err := rows.RowsAffected()
    fmt.Printf("Deleted %d row(s) of data.\n", rowCount)
    fmt.Println("Done.")
}
```

## <a name="delete-data"></a>Eliminar dados
Utilize o código seguinte para se ligar e remover os dados com uma instrução SQL **DELETE**. 

O código importa três pacotes: o [pacote sql](https://golang.org/pkg/database/sql/), o [go sql driver for mysql](https://github.com/go-sql-driver/mysql#installation) como um controlador para comunicar com a Base de Dados do Azure para MySQL e o [pacote fmt](https://golang.org/pkg/fmt/) para entrada e saída de dados impressos na linha de comandos.

O código chama o método [sql.Open()](http://go-database-sql.org/accessing.html) para ligar à Base de Dados do Azure para MySQL e verifica a ligação através do método [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). É utilizado um [identificador de base de dados](https://golang.org/pkg/database/sql/#DB) ao manter o conjunto de ligações para o servidor da base de dados. O código chama o método [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) para executar o comando de eliminação. De cada vez, é utilizado um método checkError() personalizado para verificar se ocorreu um erro e se deve ser chamado o método “panic”, para sair.

Substitua as constantes `host`, `database`, `user` e `password` pelos seus próprios valores. 

```Go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "mydemoserver.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@mydemoserver"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Modify some data in table.
    rows, err := db.Exec("DELETE FROM inventory WHERE name = ?", "orange")
    checkError(err)
    rowCount, err := rows.RowsAffected()
    fmt.Printf("Deleted %d row(s) of data.\n", rowCount)
    fmt.Println("Done.")
}
```

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Migrar a base de dados com Exportar e Importar](./concepts-migrate-import-export.md)
