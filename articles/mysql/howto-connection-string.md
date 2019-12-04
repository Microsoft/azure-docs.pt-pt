---
title: Cadeias de conexão-banco de dados do Azure para MySQL
description: Este documento lista as cadeias de conexão com suporte no momento para que os aplicativos se conectem ao bancoC#de dados do Azure para MySQL, incluindo ADO.net (), JDBC, Node. js, ODBC, PHP, Python e Ruby.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: bee98accd8ac404eb223975571b082dae754571a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770497"
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Como conectar aplicativos ao banco de dados do Azure para MySQL
Este tópico lista os tipos de cadeia de conexão que têm suporte do banco de dados do Azure para MySQL, juntamente com modelos e exemplos. Você pode ter parâmetros e configurações diferentes na cadeia de conexão.

- Para obter o certificado, consulte [como configurar o SSL](./howto-configure-ssl.md).
- {your_host} = \<ServerName >. mysql. Database. Azure. com
- {your_user} @ {ServerName} = formato de userID para autenticação correta.  Se você usar somente o userID, a autenticação falhará.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

Neste exemplo, o nome do servidor é `mydemoserver`, o nome do banco de dados é `wpdb`, o nome de usuário é `WPAdmin`e a senha é `mypassword!2`. Como resultado, a cadeia de conexão deve ser:

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

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Obter os detalhes da cadeia de conexão do portal do Azure
Na [portal do Azure](https://portal.azure.com), acesse o banco de dados do Azure para servidor MySQL e clique em **cadeias de conexão** para obter a lista de cadeias de caracteres da sua instância: ![o painel cadeias de conexão no portal do Azure](./media/howto-connection-strings/connection-strings-on-portal.png)

A cadeia de caracteres fornece detalhes como o driver, o servidor e outros parâmetros de conexão de banco de dados. Modifique esses exemplos para usar seus próprios parâmetros, como nome do banco de dados, senha e assim por diante. Você pode usar essa cadeia de caracteres para se conectar ao servidor de seu código e aplicativos.

## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre bibliotecas de conexões, consulte [conceitos-bibliotecas de conexões](./concepts-connection-libraries.md).
