---
title: Ligar aplicações à base de dados do Azure para MariaDB
description: Este documento apresenta uma lista de cadeias de ligação suportado atualmente para aplicações ligar à base de dados do Azure para MariaDB, incluindo o ADO.NET (c#), JDBC, node. js, ODBC, PHP, Python e Ruby.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 70cd25ff63101fa2a477cde2502d5d286b289366
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61039685"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Como ligar aplicações à base de dados do Azure para MariaDB
Este tópico lista os tipos de cadeia de ligação que são suportados pela base de dados do Azure para MariaDB, juntamente com modelos e exemplos. Pode ter parâmetros diferentes e as definições na sua cadeia de ligação.

- Para obter o certificado, consulte [como configurar o SSL](./howto-configure-ssl.md).
- {your_host} = [servername].mariadb.database.azure.com
- {your_user}@{servername} = formato do ID de utilizador para a autenticação corretamente.  Se usar apenas o ID de utilizador, a autenticação irá falhar.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

Neste exemplo, é o nome do servidor `mydemoserver`, o nome de base de dados é `wpdb`, é o nome de utilizador `WPAdmin`, e a palavra-passe é `mypassword!2`. Como resultado, a cadeia de ligação deve ser:

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

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Obter detalhes de cadeia de caracteres da ligação do portal do Azure
Na [portal do Azure](https://portal.azure.com), aceda à sua base de dados do Azure para MariaDB server e, em seguida, clique em **cadeias de ligação** para obter a lista de cadeia de caracteres para a sua instância: ![O painel de cadeias de ligação no portal do Azure](./media/howto-connection-strings/connection-strings-on-portal.png)

A cadeia de caracteres fornece detalhes como o driver, o servidor e o outro banco de dados de parâmetros de ligação. Modificar estes exemplos para utilizar os seus parâmetros, como o nome de base de dados, palavra-passe e assim por diante. Em seguida, pode utilizar essa cadeia de caracteres para ligar ao servidor a partir do seu código e aplicativos.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->
