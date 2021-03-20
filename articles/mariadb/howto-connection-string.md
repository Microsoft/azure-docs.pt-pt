---
title: Cadeias de conexão - Base de Dados Azure para MariaDB
description: Este documento lista as cadeias de conexão atualmente suportadas para aplicações que se conectam com a Base de Dados Azure para MariaDB, incluindo ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python e Ruby.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 8119f0bfd1f9007cab0df93ad2c7ef22b4db2199
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98662182"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Como ligar aplicações ao Azure Database for MariaDB
Este tópico lista os tipos de cadeias de ligação que são suportados pela Base de Dados Azure para MariaDB, juntamente com modelos e exemplos. Pode ter diferentes parâmetros e configurações na sua cadeia de ligação.

- Para obter o certificado, consulte [Como configurar a SSL](./howto-configure-ssl.md).
- {your_host} = [nome de servidor].mariadb.database.azure.com
- {your_user}@{servername} = formato userID para autenticação correta.  Se utilizar apenas o userID, a autenticação falhará.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

Neste exemplo, o nome do servidor é `mydemoserver` , o nome da base de dados é , o nome de utilizador é , e a `wpdb` `WPAdmin` palavra-passe é `mypassword!2` . Como resultado, a cadeia de ligação deve ser:

```csharp
Server= "mydemoserver.mariadb.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```java
String url ="jdbc:mariadb://{your_host}:3306/{your_database}?useSSL=true&trustServerCertificate=true"; myDbConn = DriverManager.getConnection(url, "{username@servername}", {your_password});
```

## <a name="nodejs"></a>Node.js
```javascript
var conn = mysql.createConnection({host: "{your_host}", user: "{your_username}", password: {your_password}, database: {your_database}, port: 3306, ssl:{ca:fs.readFileSync({ca-cert filename})}});
```

## <a name="odbc"></a>ODBC
```cpp
DRIVER={MARIADB ODBC 3.0 Driver}; Server="{your_host}"; Port=3306; Database={your_database}; Uid="{username@servername}"; Pwd={your_password}; sslca={ca-cert filename}; sslverify=1;
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "{your_host}", "{username@servername}", {your_password}, {your_database}, 3306);
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user="{username@servername}", password={your_password}, host="{your_host}", port=3306, database={your_database}, ssl_ca={ca-cert filename}, ssl_verify_cert=true)
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: "{username@servername}", password: {your_password}, database: {your_database}, host: "{your_host}", port: 3306, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA')
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Obtenha os detalhes da cadeia de ligação a partir do portal Azure
No [portal Azure,](https://portal.azure.com)aceda à sua Base de Dados Azure para servidor MariaDB e, em seguida, clique em **Cadeias de Ligação** para obter a lista de cordas para o seu exemplo: ![ O painel de cordas de ligação no portal Azure](./media/howto-connection-strings/connection-strings-on-portal.png)

A cadeia fornece detalhes como o controlador, servidor e outros parâmetros de ligação da base de dados. Modifique estes exemplos para utilizar os seus próprios parâmetros, tais como nome de base de dados, palavra-passe, e assim por diante. Em seguida, pode utilizar este fio para ligar ao servidor a partir do seu código e aplicações.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->
