---
title: Cadeias de conexão-banco de dados do Azure para MariaDB
description: Este documento lista as cadeias de conexão com suporte no momento para que os aplicativos se conectem ao bancoC#de dados do Azure para MariaDB, incluindo ADO.net (), JDBC, Node. js, ODBC, PHP, Python e Ruby.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 1b598385d533b3fc157a7a90ecc34c3cb18df4ac
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767284"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Como conectar aplicativos ao banco de dados do Azure para MariaDB
Este tópico lista os tipos de cadeia de conexão que têm suporte do banco de dados do Azure para MariaDB, juntamente com modelos e exemplos. Você pode ter parâmetros e configurações diferentes na cadeia de conexão.

- Para obter o certificado, consulte [como configurar o SSL](./howto-configure-ssl.md).
- {your_host} = [servername]. MariaDB. Database. Azure. com
- {your_user} @ {ServerName} = formato de userID para autenticação correta.  Se você usar somente o userID, a autenticação falhará.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

Neste exemplo, o nome do servidor é `mydemoserver`, o nome do banco de dados é `wpdb`, o nome de usuário é `WPAdmin`e a senha é `mypassword!2`. Como resultado, a cadeia de conexão deve ser:

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

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Obter os detalhes da cadeia de conexão do portal do Azure
Na [portal do Azure](https://portal.azure.com), vá para o banco de dados do Azure para o servidor MariaDB e clique em **cadeias de conexão** para obter a lista de cadeias de caracteres da sua instância: ![o painel cadeias de conexão no portal do Azure](./media/howto-connection-strings/connection-strings-on-portal.png)

A cadeia de caracteres fornece detalhes como o driver, o servidor e outros parâmetros de conexão de banco de dados. Modifique esses exemplos para usar seus próprios parâmetros, como nome do banco de dados, senha e assim por diante. Você pode usar essa cadeia de caracteres para se conectar ao servidor de seu código e aplicativos.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->
