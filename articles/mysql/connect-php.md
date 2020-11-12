---
title: 'Quickstart: Conecte-se usando PHP - Azure Database para MySQL'
description: Este guia de início rápido proporciona vários exemplos de código PHP que pode utilizar para se ligar e consultar dados da Base de Dados do Azure para MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: ae767905e24e2d7ddf3b8e12ec77b1efe782cf85
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535610"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql"></a>Quickstart: Utilize PHP para ligar e consultar dados na Base de Dados Azure para o MySQL
Este guia de início rápido explica como se pode ligar a uma Base de Dados do Azure para MySQL através de uma aplicação [PHP](https://secure.php.net/manual/intro-whatis.php). Explica como utilizar as instruções SQL para consultar, inserir, atualizar e eliminar dados da base de dados.

## <a name="prerequisites"></a>Pré-requisitos
Para este arranque rápido você precisa:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free)
- Criar uma base de dados Azure para o servidor único mySQL utilizando [o portal Azure](./quickstart-create-mysql-server-database-using-azure-portal.md) <br/> ou [Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md) se não tiver um.
- Com base no facto de estar a utilizar acesso público ou privado, complete **uma das** ações abaixo para permitir a conectividade.

    |Ação| Método de conectividade|Manual de instruções|
    |:--------- |:--------- |:--------- |
    | **Configurar as regras de firewall** | Público | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
    | **Configure Serviço Endpoint** | Público | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
    | **Configure a ligação privada** | Privado | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Criar uma base de dados e um utilizador não administrador](/howto-create-users?tabs=single-server)
- Instale a versão PHP mais recente para o seu sistema operativo
    - [PHP no macOS](https://secure.php.net/manual/install.macosx.php)
    - [PHP em Linux](https://secure.php.net/manual/install.unix.php)
    - [PHP no Windows](https://secure.php.net/manual/install.windows.php)

> [!NOTE]
> Estamos a usar a biblioteca [MySQLi](https://www.php.net/manual/en/book.mysqli.php) para gerir a ligação e consulta do servidor neste arranque rápido.

## <a name="get-connection-information"></a>Obter informações da ligação
Pode obter as informações de ligação do servidor de base de dados a partir do portal Azure seguindo estes passos:

1. Faça login no [portal Azure](https://portal.azure.com/).
2. Navegue para as Bases de Dados Azure para a página MySQL. Pode pesquisar e selecionar **Azure Database para o MySQL**.
:::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Encontre a base de dados Azure para o MySQL":::

2. Selecione o seu servidor MySQL (como **o mydemoserver).**
3. Na página **'Vista Geral',** copie o nome do servidor totalmente qualificado ao lado **do nome do Servidor** e o nome de utilizador administrativo ao lado do nome de login do **servidor**. Para copiar o nome do servidor ou o nome do anfitrião, paire sobre ele e selecione o ícone **Copy.**

> [!IMPORTANT]
> - Se esqueceu a sua palavra-passe, pode [redefinir a palavra-passe.](./howto-create-manage-server-portal.md#update-admin-password)
> - Substitua os **parâmetros de anfitrião, nome de utilizador, palavra-passe** e **db_name** pelos seus próprios valores**

## <a name="step-1-connect-to-the-server"></a>Passo 1: Ligar ao servidor
SSL é ativado por padrão. Poderá ser necessário descarregar o [certificado DigiCertGlobalRootG2 SSL](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) para se ligar ao seu ambiente local. Este código chama:
- [mysqli_init](https://secure.php.net/manual/mysqli.init.php) de inicializar o MySQLi.
- [mysqli_ssl_set](https://www.php.net/manual/en/mysqli.ssl-set.php) apontar para o caminho do certificado SSL. Isto é necessário para o seu ambiente local, mas não é necessário para app Service Web App ou máquinas Azure Virtual.
- [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) ligar-se ao MySQL.
- [mysqli_close](https://secure.php.net/manual/mysqli.close.php) de fechar a ligação.


```php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Initializes MySQLi
$conn = mysqli_init();

// If using  Azure Virtual machines or Azure Web App, 'mysqli-ssl_set()' is not required as the certificate is already installed on the machines.
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootG2.crt.pem", NULL, NULL);

// Establish the connection
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);

//If connection failed, show the error
if (mysqli_connect_errno($conn))
{
    die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
[Tendo problemas? Deixe-nos saber](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-create-a-table"></a>Passo 2: Criar uma tabela
Utilize o seguinte código para ligar. Este código chama:
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php) fazer a consulta.
```php
// Run the create table query
if (mysqli_query($conn, '
CREATE TABLE Products (
`Id` INT NOT NULL AUTO_INCREMENT ,
`ProductName` VARCHAR(200) NOT NULL ,
`Color` VARCHAR(50) NOT NULL ,
`Price` DOUBLE NOT NULL ,
PRIMARY KEY (`Id`)
);
')) {
printf("Table created\n");
}
```

## <a name="step-3-insert-data"></a>Passo 3: Inserir dados
Utilize o seguinte código para inserir dados utilizando uma declaração **INSERT** SQL. Este código utiliza os métodos:
- [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) criar uma declaração de inserção preparada
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) ligar os parâmetros para cada valor da coluna inserida.
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php)
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) fechar a declaração utilizando o método


```php
//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)"))
{
    mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
    mysqli_stmt_execute($stmt);
    printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
    mysqli_stmt_close($stmt);
}

```

## <a name="step-4-read-data"></a>Passo 4: Ler dados
Utilize o seguinte código para ler os dados utilizando uma declaração **SELECT** SQL.  O código utiliza o método:
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php) executar a consulta **SELECT**
- [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) para ir buscar as filas resultantes.

```php
//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res))
 {
    var_dump($row);
 }

```


## <a name="step-5-delete-data"></a>Passo 5: Eliminar dados
Utilize as seguintes linhas de eliminação de código utilizando uma declaração **DELETE** SQL. O código utiliza os métodos:
- [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) criar uma declaração de eliminação preparada
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) liga os parâmetros
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) executa a declaração de eliminação preparada
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) encerra a declaração

```php
//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}
```

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar todos os recursos utilizados durante este arranque rápido, elimine o grupo de recursos utilizando o seguinte comando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Gerir a base de dados do Azure para o servidor MySQL utilizando o Portal](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Gerir a base de dados do Azure para o servidor MySQL utilizando o CLI](./how-to-manage-single-server-cli.md)

[Não consegue encontrar o que procura? Deixe-nos saber.](https://aka.ms/mysql-doc-feedback)
