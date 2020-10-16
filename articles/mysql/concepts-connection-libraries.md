---
title: Bibliotecas de conexão - Azure Database for MySQL
description: Este artigo lista cada biblioteca ou controlador que os programas do cliente podem usar ao ligar à Base de Dados Azure para o MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 8/3/2020
ms.openlocfilehash: 9d4d862389a607b18ee5f2440069fa2eb6cf7bc4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87553041"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Bibliotecas de conexão para Azure Database para MySQL
Este artigo lista cada biblioteca ou controlador que os programas do cliente podem usar ao ligar à Base de Dados Azure para o MySQL.

## <a name="client-interfaces"></a>Interfaces de clientes
O MySQL oferece conectividade padrão do controlador de base de dados para a utilização do MySQL com aplicações e ferramentas compatíveis com os padrões da indústria ODBC e JDBC. Qualquer sistema que funcione com ODBC ou JDBC pode utilizar o MySQL.

| **Idioma** | **Plataforma** | **Recurso adicional** | **Transferência** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [MySQL condutor nativo para PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Transferência](https://secure.php.net/downloads.php) |
| ODBC | Plataformas Windows, Linux, Mac OS X e Unix | [Guia de desenvolvimento mysql connector/ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Transferência](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Guia de programador MySQL Connector/Net](https://dev.mysql.com/doc/connector-net/en/) | [Transferência](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Plataforma independente | [Guia de desenvolvimento MySQL Connector/J 5.1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Transferência](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/nó-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Transferência](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Guia de desenvolvedores mysql connector/python](https://dev.mysql.com/doc/connector-python/en/) | [Transferência](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Guia de desenvolvimento MySQL Connector/C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Transferência](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Guia de desenvolvimento do conector MySQL/C](https://dev.mysql.com/doc/c-api/8.0/en/) | [Transferência](https://dev.mysql.com/downloads/connector/c/)
| Perl | Plataformas Windows, Linux, Mac OS X e Unix | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Transferência](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Passos seguintes
Leia estes quickstarts sobre como ligar e consultar a Base de Dados Azure para o MySQL utilizando o seu idioma de eleição:

- [PHP](./connect-php.md)
- [Java](./connect-java.md)
- [.NET (C#)](./connect-csharp.md)
- [Python](./connect-python.md)
- [Node.JS](./connect-nodejs.md)
- [Ruby](./connect-ruby.md)
- [C++](connect-cpp.md)
- [Ir](./connect-go.md)
