---
title: 'Quickstart: Conecte-se usando PHP - Base de Dados Azure para MySQL - Servidor Flexível'
description: Este quickstart fornece várias amostras de código PHP que pode usar para ligar e consultar dados da Base de Dados Azure para o MySQL - Servidor Flexível.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: dc6b069e3c7686ec6964dab890e503aa193cf6fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92545111"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>Quickstart: Utilize PHP para ligar e consultar dados na Base de Dados Azure para o MySQL - Servidor Flexível

> [!IMPORTANT]
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Este quickstart demonstra como ligar-se a uma Base de Dados Azure para o MySQL Flexible Server utilizando uma aplicação [PHP.](https://secure.php.net/manual/intro-whatis.php) Explica como utilizar as instruções SQL para consultar, inserir, atualizar e eliminar dados da base de dados. Este artigo pressupõe que está familiarizado com o desenvolvimento usando PHP e que é novo a trabalhar com a Azure Database para o MySQL Flexible Server.

## <a name="prerequisites"></a>Pré-requisitos
Este guia de início rápido utiliza os recursos criados em qualquer um desTes guias como ponto de partida:

- [Criar uma base de dados Azure para o MySQL Flexible Server utilizando o portal Azure](./quickstart-create-server-portal.md)
- [Criar uma base de dados Azure para o MySQL Flexible Server utilizando o Azure CLI](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>Preparar a estação de trabalho do seu cliente
1. Se criou o seu servidor flexível com *acesso privado (VNet Integration)*, terá de se ligar ao seu servidor a partir de um recurso dentro do mesmo VNet que o seu servidor. Pode criar uma máquina virtual e adicioná-la ao VNet criado com o seu servidor flexível. Consulte a [Criação e gestão da Base de Dados Azure para a rede virtual MySQL Flexible Server utilizando o Azure CLI](./how-to-manage-virtual-network-cli.md).

2. Se criou o seu servidor flexível com *acesso público (endereços IP autorizados)*, pode adicionar o seu endereço IP local à lista de regras de firewall no seu servidor. Consulte para [criar e gerir a Base de Dados Azure para as regras de firewall do Servidor Flexível MySQL utilizando o Azure CLI](./how-to-manage-firewall-cli.md).

### <a name="install-php"></a>Instalar o PHP

Instale o PHP no seu próprio servidor ou crie uma [aplicação Web](../../app-service/overview.md) do Azure que inclua o PHP.  Consulte para [criar e gerir regras de firewall](./how-to-manage-firewall-portal.md) para aprender a criar regras de firewall.

#### <a name="macos"></a>macOS

1. Descarregue [a versão PHP 7.1.4](https://secure.php.net/downloads.php).
2. Instale PHP e consulte o [manual PHP](https://secure.php.net/manual/install.macosx.php) para uma posterior configuração.

#### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Descarregue [a versão PHP 7.1.4 sem fios (x64).](https://secure.php.net/downloads.php)
2. Instale PHP e consulte o [manual PHP](https://secure.php.net/manual/install.unix.php) para uma posterior configuração.

#### <a name="windows"></a>Windows

1. Descarregue [a versão PHP 7.1.4 sem fios (x64).](https://windows.php.net/download#php-7.1)
2. Instale PHP e consulte o [manual PHP](https://secure.php.net/manual/install.windows.php) para uma posterior configuração.

## <a name="get-connection-information"></a>Obter informações da ligação

Obtenha as informações de ligação necessárias para ligar à Base de Dados Azure para o MySQL Flexible Server. Precisa do nome do servidor totalmente qualificado e assinar em credenciais.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, procure o servidor que criou (como o **mydemoserver).**
3. Selecione o nome do servidor.
4. No painel **Descrição geral** do servidor, tome nota do **Nome do servidor** e do **Nome de início de sessão de administrador do servidor**. Caso se esqueça da sua palavra-passe, também pode repor a palavra-passe neste painel.
 <!---:::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connecting-to-flexible-server-using-tlsssl-in-php"></a>Ligação ao servidor flexível utilizando TLS/SSL em PHP

Para estabelecer uma ligação encriptada ao seu servidor flexível sobre o TLS/SSL a partir da sua aplicação, consulte as seguintes amostras de código. Você pode baixar o certificado necessário para comunicar através de TLS/SSL a partir de [https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)

```php using SSL
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

## <a name="connect-and-create-a-table"></a>Ligar e criar uma tabela

Utilize o seguinte código para se ligar e crie uma tabela com a instrução SQL **CREATE TABLE**.

O código utiliza a classe da **extensão MySQL melhorada** (mysqli) incluída em PHP. O código chama os métodos [mysqli_init](https://secure.php.net/manual/mysqli.init.php) e [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) para se ligar ao MySQL. Em seguida, chama o método [mysqli_query](https://secure.php.net/manual/mysqli.query.php) para executar a consulta. Em seguida, chama o método [mysqli_close](https://secure.php.net/manual/mysqli.close.php) para fechar a ligação.

Substitua os parâmetros host, username, password e db_name pelos seus próprios valores.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

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

//Close the connection
mysqli_close($conn);
?>
```

## <a name="insert-data"></a>Inserir dados

Utilize o código seguinte para se ligar e inserir dados com uma instrução SQL **INSERT**.

O código utiliza a classe da **extensão MySQL melhorada** (mysqli) incluída em PHP. O código utiliza o método [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) para criar uma instrução de introdução preparada e, em seguida, une os parâmetros de cada valor introduzido na coluna através do método [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php). O código executa a instrução através do método [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) e, depois, fecha a instrução através do método [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php).

Substitua os parâmetros host, username, password e db_name pelos seus próprios valores.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)")) {
mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
mysqli_stmt_execute($stmt);
printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

// Close the connection
mysqli_close($conn);
?>
```

## <a name="read-data"></a>Ler dados

Utilize o código seguinte para se ligar e ler dados com uma instrução SQL **SELECT**.  O código utiliza a classe da **extensão MySQL melhorada** (mysqli) incluída em PHP. O código utiliza o método [mysqli_query](https://secure.php.net/manual/mysqli.query.php) para executar a consulta sql e o método [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) para obter os registos daí resultantes.

Substitua os parâmetros host, username, password e db_name pelos seus próprios valores.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res)) {
var_dump($row);
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="update-data"></a>Atualizar dados

Utilize o código seguinte para se ligar e atualizar os dados com uma instrução SQL **UPDATE**.

O código utiliza a classe da **extensão MySQL melhorada** (mysqli) incluída em PHP. O código utiliza o método [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) para criar uma instrução de atualização preparada e, em seguida, une os parâmetros de cada valor introduzido na coluna através do método [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php). O código executa a instrução através do método [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) e, depois, fecha a instrução através do método [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php).

Substitua os parâmetros host, username, password e db_name pelos seus próprios valores.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Update statement
$product_name = 'BrandNewProduct';
$new_product_price = 15.1;
if ($stmt = mysqli_prepare($conn, "UPDATE Products SET Price = ? WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 'ds', $new_product_price, $product_name);
mysqli_stmt_execute($stmt);
printf("Update: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));

//Close the connection
mysqli_stmt_close($stmt);
}

mysqli_close($conn);
?>
```


## <a name="delete-data"></a>Eliminar dados
Utilize o código seguinte para se ligar e ler os dados com a instrução SQL **DELETE**.

O código utiliza a classe da **extensão MySQL melhorada** (mysqli) incluída em PHP. O código utiliza o método [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) para criar uma instrução de eliminação preparada e, em seguida, une os parâmetros da cláusula «onde» na instrução através do método [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php). O código executa a instrução através do método [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) e, depois, fecha a instrução através do método [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php).

Substitua os parâmetros host, username, password e db_name pelos seus próprios valores.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

//Close the connection
mysqli_close($conn);
?>
```
## <a name="next-steps"></a>Passos seguintes
- [Conectividade encriptada utilizando a Segurança da Camada de Transporte (TLS 1.2) na Base de Dados Azure para o MySQL - Servidor Flexível](./how-to-connect-tls-ssl.md).
- Saiba mais sobre [networking na Base de Dados Azure para o MySQL Flexible Server](./concepts-networking.md).
- [Crie e gere a base de dados Azure para as regras de firewall do Servidor Flexível MySQL utilizando o portal Azure](./how-to-manage-firewall-portal.md).
- [Crie e gere a base de dados Azure para a rede virtual mySQL Flexible Server utilizando o portal Azure](./how-to-manage-virtual-network-portal.md).