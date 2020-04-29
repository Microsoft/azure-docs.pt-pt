---
title: Cordas de ligação - Base de Dados Azure para MariaDB
description: Este documento lista as cadeias de ligação atualmente suportadas para aplicações de ligação com a Base de Dados Azure para MariaDB, incluindo ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python e Ruby.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 74574fb905ae4ebd2552ef97cd0b5430eea6363a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79530228"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Como ligar aplicações ao Azure Database for MariaDB
Este tópico lista os tipos de cordas de ligação que são suportados pela Azure Database para MariaDB, juntamente com modelos e exemplos. Pode ter diferentes parâmetros e configurações na sua cadeia de ligação.

- Para obter o certificado, consulte [Como configurar o SSL](./howto-configure-ssl.md).
- {your_host} = [nome de servidor].mariadb.database.azure.com
- {your_user}@{servername} = formato userID para autenticação corretamente.  Se utilizar apenas o ID do utilizador, a autenticação falhará.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

Neste exemplo, o nome `mydemoserver`do servidor `wpdb`é , o `WPAdmin`nome da `mypassword!2`base de dados é , o nome do utilizador é , e a palavra-passe é . Como resultado, a corda de ligação deve ser:

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

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Obtenha os detalhes da cadeia de ligação do portal Azure
No [portal Azure,](https://portal.azure.com)vá à sua Base de Dados Azure para o servidor MariaDB ![e, em seguida, clique em cordas de **Ligação** para obter a lista de cordas por exemplo: As cordas de ligação painelno portal Azure](./media/howto-connection-strings/connection-strings-on-portal.png)

A cadeia fornece detalhes como o controlador, servidor e outros parâmetros de ligação à base de dados. Modifique estes exemplos para utilizar os seus próprios parâmetros, tais como nome de base de dados, palavra-passe, e assim por diante. Em seguida, pode utilizar esta cadeia para se ligar ao servidor a partir do seu código e aplicações.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->
