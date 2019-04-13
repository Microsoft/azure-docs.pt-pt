---
title: Ligar aplicações à base de dados do Azure para MySQL
description: Este documento apresenta uma lista de cadeias de ligação suportado atualmente para aplicações ligar à base de dados do Azure para MySQL, incluindo o ADO.NET (c#), JDBC, node. js, ODBC, PHP, Python e Ruby.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 503b7764ba7958a5448903b217b00e204ffcbf61
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527409"
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Como ligar aplicações à base de dados do Azure para MySQL
Este tópico lista os tipos de cadeia de ligação que são suportados pela base de dados do Azure para MySQL, em conjunto com modelos e exemplos. Pode ter parâmetros diferentes e as definições na sua cadeia de ligação.

- Para obter o certificado, consulte [como configurar o SSL](./howto-configure-ssl.md).
- {your_host} = \<servername>.mysql.database.azure.com
- {your_user}@{servername} = formato do ID de utilizador para a autenticação corretamente.  Se usar apenas o ID de utilizador, a autenticação irá falhar.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

Neste exemplo, é o nome do servidor `mydemoserver`, o nome de base de dados é `wpdb`, é o nome de utilizador `WPAdmin`, e a palavra-passe é `mypassword!2`. Como resultado, a cadeia de ligação deve ser:

```ado.net
Server= "mydemoserver.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {username@servername}, {your_password}";
```

## <a name="nodejs"></a>Node.js
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {username@servername}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {username@servername}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={username@servername}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {username@servername}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Obter detalhes de cadeia de caracteres da ligação do portal do Azure
Na [portal do Azure](https://portal.azure.com), aceda à sua base de dados do Azure para o servidor MySQL e, em seguida, clique em **cadeias de ligação** para obter a lista de cadeia de caracteres para a sua instância: ![O painel de cadeias de ligação no portal do Azure](./media/howto-connection-strings/connection-strings-on-portal.png)

A cadeia de caracteres fornece detalhes como o driver, o servidor e o outro banco de dados de parâmetros de ligação. Modificar estes exemplos para utilizar os seus parâmetros, como o nome de base de dados, palavra-passe e assim por diante. Em seguida, pode utilizar essa cadeia de caracteres para ligar ao servidor a partir do seu código e aplicativos.

## <a name="next-steps"></a>Passos Seguintes
- Para obter mais informações sobre bibliotecas de ligação, veja [conceitos - bibliotecas de ligação](./concepts-connection-libraries.md).
