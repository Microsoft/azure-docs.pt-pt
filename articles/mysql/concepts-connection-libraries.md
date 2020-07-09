---
title: Bibliotecas de conexão - Azure Database for MySQL
description: Este artigo lista cada biblioteca ou controlador que os programas do cliente podem usar ao ligar à Base de Dados Azure para o MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5f83f937b8d9ec50ec7dc6ec781bff1f435eb45a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79537198"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Bibliotecas de conexão para Azure Database para MySQL
Este artigo lista cada biblioteca ou controlador que os programas do cliente podem usar ao ligar à Base de Dados Azure para o MySQL.

## <a name="client-interfaces"></a>Interfaces de clientes
O MySQL oferece conectividade padrão do controlador de base de dados para a utilização do MySQL com aplicações e ferramentas compatíveis com os padrões da indústria ODBC e JDBC. Qualquer sistema que funcione com ODBC ou JDBC pode utilizar o MySQL.

| **Linguagem** | **Plataforma** | **Recurso adicional** | **Transferência** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [MySQL condutor nativo para PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Transferência](https://secure.php.net/downloads.php) |
| ODBC | Plataformas Windows, Linux, Mac OS X e Unix | [Guia de desenvolvimento mysql connector/ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Transferência](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Guia de programador MySQL Connector/Net](https://dev.mysql.com/doc/connector-net/en/) | [Transferência](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Plataforma independente | [Guia de desenvolvimento MySQL Connector/J 5.1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Transferência](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/nó-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Transferência](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Guia de desenvolvedores mysql connector/python](https://dev.mysql.com/doc/connector-python/en/) | [Transferência](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Guia de desenvolvimento MySQL Connector/C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Transferência](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Guia de desenvolvimento do conector MySQL/C](https://dev.mysql.com/doc/refman/8.0/en/c-api.html) | [Transferência](https://dev.mysql.com/downloads/connector/c/)
| Rio Perl | Plataformas Windows, Linux, Mac OS X e Unix | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Transferência](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Passos seguintes
Leia estes quickstarts sobre como ligar e consultar a Base de Dados Azure para o MySQL utilizando o seu idioma de eleição:

[PHP](./connect-php.md)  |  [Rio Java](./connect-java.md)  |   [.NET (C#)](./connect-csharp.md)  |  [Pitão](./connect-python.md)  |  [Node.JS](./connect-nodejs.md)  |  [Rio Ruby](./connect-ruby.md)  |  [C++](connect-cpp.md)  |  [Vai](./connect-go.md)

