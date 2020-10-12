---
title: Cadeias de conexão - Base de Dados Azure para MySQL
description: Este documento lista as cadeias de conexão atualmente suportadas para aplicações que se conectam com a Base de Dados Azure para o MySQL, incluindo ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python e Ruby.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-python, devx-track-js
ms.openlocfilehash: 635cf55cab74ecdc32c5687eed8282a052ccf7c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331748"
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Como ligar aplicações à Base de Dados do Azure para MySQL
Este tópico lista os tipos de cadeias de ligação que são suportados pela Base de Dados Azure para o MySQL, juntamente com modelos e exemplos. Pode ter diferentes parâmetros e configurações na sua cadeia de ligação.

- Para obter o certificado, consulte [Como configurar a SSL](./howto-configure-ssl.md).
- {your_host} = \<servername> .mysql.database.azure.com
- {your_user}@{servername} = formato userID para autenticação correta.  Se utilizar apenas o userID, a autenticação falhará.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

Neste exemplo, o nome do servidor é `mydemoserver` , o nome da base de dados é , o nome de utilizador é , e a `wpdb` `WPAdmin` palavra-passe é `mypassword!2` . Como resultado, a cadeia de ligação deve ser:

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

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Obtenha os detalhes da cadeia de ligação a partir do portal Azure
No [portal Azure,](https://portal.azure.com)aceda à sua Base de Dados Azure para o servidor MySQL e, em seguida, clique em **Cadeias de Ligação** para obter a lista de cordas para o seu exemplo: :::image type="content" source="./media/howto-connection-strings/connection-strings-on-portal.png" alt-text="O painel de cordas de ligação no portal Azure":::

A cadeia fornece detalhes como o controlador, servidor e outros parâmetros de ligação da base de dados. Modifique estes exemplos para utilizar os seus próprios parâmetros, tais como nome de base de dados, palavra-passe, e assim por diante. Em seguida, pode utilizar este fio para ligar ao servidor a partir do seu código e aplicações.

## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre bibliotecas de ligação, consulte [Conceitos - Bibliotecas de ligação.](./concepts-connection-libraries.md)
