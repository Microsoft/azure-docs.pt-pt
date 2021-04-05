---
title: 'Quickstart: Ligue-se usando C++ - Base de Dados Azure para o MySQL'
description: Este guia de início rápido disponibiliza um código de exemplo de C++ que pode utilizar para se ligar e consultar dados da Base de Dados do Azure para MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.devlang: cpp
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: e8b2842300e43a9de88cc0b9b3ae9ce6cd754612
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94535848"
---
# <a name="quickstart-use-connectorc-to-connect-and-query-data-in-azure-database-for-mysql"></a>Quickstart: Utilize o Conector/C++ para ligar e consultar dados na Base de Dados Azure para o MySQL

Este guia de introdução explica como se pode ligar a uma Base de Dados do Azure para MySQL através de uma aplicação C++. Explica como utilizar as instruções SQL para consultar, inserir, atualizar e eliminar dados da base de dados. Este tópico pressupõe que está familiarizado com o desenvolvimento usando C++ e é novo a trabalhar com a Azure Database para o MySQL.

## <a name="prerequisites"></a>Pré-requisitos

Este guia de introdução utiliza os recursos criados em qualquer um dos guias seguintes como ponto de partida:
- [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar uma Base de Dados do Azure para o servidor MySQL com a CLI do Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

Também tem de:
- Instalar o [.NET Framework](https://www.microsoft.com/net/download)
- Instalar [Estúdio Visual](https://www.visualstudio.com/downloads/)
- Instalar o [MySQL Connector/C++](https://dev.mysql.com/downloads/connector/cpp/) 
- Instalar o [Boost](https://www.boost.org/)

> [!IMPORTANT] 
> Certifique-se de que o endereço IP de que está a ligar foi adicionado as regras de firewall do servidor utilizando o [portal Azure](./howto-manage-firewall-using-portal.md) ou [O CLI do Azure](./howto-manage-firewall-using-cli.md)

## <a name="install-visual-studio-and-net"></a>Instalar o Visual Studio e .NET
Os passos nesta secção assumem que está familiarizado com o desenvolvimento através do .NET.

### <a name="windows"></a>**Windows**
- Instalar Visual Studio 2019 Community. Visual Studio 2019 Community é um IDE completo, extensível e gratuito. Com este IDE, pode criar aplicações modernas para aplicações Android, iOS, Windows, web e base de dados e serviços na nuvem. Pode instalar o .NET Framework completo ou apenas .NET Core: os fragmentos de código no Guia de introdução funcionam com qualquer um. Se já tiver o Visual Studio instalado no seu computador, ignore os dois passos seguintes.
   1. Descarregue o [instalador Visual Studio 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15). 
   2. Execute o instalador e siga as instruções de instalação, para concluí-la.

### <a name="configure-visual-studio"></a>**Configurar o Visual Studio**
1. Do Visual Studio, Project -> Properties -> Linker -> General > Diretórios adicionais de bibliotecas, adicione o diretório "\lib\opt" (por exemplo: C:\Program Files (x86)\MySQL\MySQL Connector C++ 1.1.9\lib\opt) do conector C++.
2. No Visual Studio, Project -> Properties -> C/C++ -> General -> Additional Include Directories:
   - Adicione o diretório "\incluir" do conector c++ (por exemplo: C:\Program Files (x86)\MySQL\MySQL Connector C++ 1.1.9\incluir \) .
   - Adicione o diretório de raiz da biblioteca Boost (por exemplo: C:\boost_1_64_0 \) .
3. No Visual Studio, Project -> Properties -> Linker -> Input > Additional Dependencies, adicione **mysqlcppconn.lib** no campo de texto.
4. Ou copia **mysqlcppconn.dll** da pasta da biblioteca de conector C++ no passo 3 para o mesmo diretório da aplicação executável ou adicione-a à variável ambiente para que a sua aplicação possa encontrá-la.

## <a name="get-connection-information"></a>Obter informações da ligação
Obtenha as informações de ligação necessárias para se ligar à Base de Dados do Azure para MySQL. Necessita do nome do servidor e das credenciais de início de sessão totalmente qualificados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No menu esquerdo do portal do Azure, clique em **Todos os recursos** e, em seguida, procure o servidor que acabou de criar, (por exemplo, **mydemoserver**).
3. Clique no nome do servidor.
4. No painel **Descrição geral** do servidor, tome nota do **Nome do servidor** e do **Nome de início de sessão de administrador do servidor**. Caso se esqueça da sua palavra-passe, também pode repor a palavra-passe neste painel.
 :::image type="content" source="./media/connect-cpp/1_server-overview-name-login.png" alt-text="Nome do servidor da Base de Dados do Azure para o MySQL":::

## <a name="connect-create-table-and-insert-data"></a>Ligar, criar tabela e inserir dados
Utilize o seguinte código para se ligar e carregar os dados com as instruções SQL **CREATE TABLE** e **INSERT INTO**. O código utiliza a classe sql::Driver com o método connect() para estabelecer uma ligação ao MySQL. Em seguida, o código utiliza o método createStatement() e o método execute() para executar os comandos da base de dados. 

Substitua os parâmetros Host, DBName, User e Password. Pode substituir os parâmetros pelos valores especificados quando criou o servidor e a base de dados. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::Statement *stmt;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }

    //please create database "quickstartdb" ahead of time
    con->setSchema("quickstartdb");

    stmt = con->createStatement();
    stmt->execute("DROP TABLE IF EXISTS inventory");
    cout << "Finished dropping table (if existed)" << endl;
    stmt->execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);");
    cout << "Finished creating table" << endl;
    delete stmt;

    pstmt = con->prepareStatement("INSERT INTO inventory(name, quantity) VALUES(?,?)");
    pstmt->setString(1, "banana");
    pstmt->setInt(2, 150);
    pstmt->execute();
    cout << "One row inserted." << endl;

    pstmt->setString(1, "orange");
    pstmt->setInt(2, 154);
    pstmt->execute();
    cout << "One row inserted." << endl;

    pstmt->setString(1, "apple");
    pstmt->setInt(2, 100);
    pstmt->execute();
    cout << "One row inserted." << endl;

    delete pstmt;
    delete con;
    system("pause");
    return 0;
}
```

## <a name="read-data"></a>Ler dados

Utilize o código seguinte para se ligar e ler dados com uma instrução SQL **SELECT**. O código utiliza a classe sql::Driver com o método connect() para estabelecer uma ligação ao MySQL. Em seguida, o código utiliza o método prepareStatement() e executeQuery() para executar os comandos select. Em seguida, o código utiliza next() para avançar para os registos nos resultados. Finalmente, o código utiliza getInt() e getString() para analisar os valores do registo.

Substitua os parâmetros Host, DBName, User e Password. Pode substituir os parâmetros pelos valores especificados quando criou o servidor e a base de dados. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }

    con->setSchema("quickstartdb");

    //select  
    pstmt = con->prepareStatement("SELECT * FROM inventory;");
    result = pstmt->executeQuery();

    while (result->next())
        printf("Reading from table=(%d, %s, %d)\n", result->getInt(1), result->getString(2).c_str(), result->getInt(3));

    delete result;
    delete pstmt;
    delete con;
    system("pause");
    return 0;
}
```

## <a name="update-data"></a>Atualizar dados
Utilize o código seguinte para se ligar e ler os dados com uma instrução SQL **UPDATE**. O código utiliza a classe sql::Driver com o método connect() para estabelecer uma ligação ao MySQL. Em seguida, o código utiliza o método prepareStatement() e executeQuery() para executar os comandos update. 

Substitua os parâmetros Host, DBName, User e Password. Pode substituir os parâmetros pelos valores especificados quando criou o servidor e a base de dados. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }
    
    con->setSchema("quickstartdb");

    //update
    pstmt = con->prepareStatement("UPDATE inventory SET quantity = ? WHERE name = ?");
    pstmt->setInt(1, 200);
    pstmt->setString(2, "banana");
    pstmt->executeQuery();
    printf("Row updated\n");

    delete con;
    delete pstmt;
    system("pause");
    return 0;
}
```


## <a name="delete-data"></a>Eliminar dados
Utilize o código seguinte para se ligar e ler os dados com a instrução SQL **DELETE**. O código utiliza a classe sql::Driver com o método connect() para estabelecer uma ligação ao MySQL. Em seguida, o código utiliza o método prepareStatement() e executeQuery() para executar os comandos delete.

Substitua os parâmetros Host, DBName, User e Password. Pode substituir os parâmetros pelos valores especificados quando criou o servidor e a base de dados. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }
    
    con->setSchema("quickstartdb");
        
    //delete
    pstmt = con->prepareStatement("DELETE FROM inventory WHERE name = ?");
    pstmt->setString(1, "orange");
    result = pstmt->executeQuery();
    printf("Row deleted\n");    
    
    delete pstmt;
    delete con;
    delete result;
    system("pause");
    return 0;
}
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar todos os recursos utilizados durante este arranque rápido, elimine o grupo de recursos utilizando o seguinte comando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Migrar a sua base de dados MySQL para a Dase de Dados do Azure para MySQL através da funcionalidade de captura e restauro](concepts-migrate-dump-restore.md)
