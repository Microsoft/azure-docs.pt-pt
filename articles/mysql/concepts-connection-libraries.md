---
title: Bibliotecas de ligação - Base de Dados Azure para MySQL
description: Este artigo lista cada biblioteca ou controlador que os programas de clientes podem usar quando se conecta à Base de Dados Azure para mySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5f83f937b8d9ec50ec7dc6ec781bff1f435eb45a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79537198"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Bibliotecas de ligação para base de dados Azure para MySQL
Este artigo lista cada biblioteca ou controlador que os programas de clientes podem usar quando se conecta à Base de Dados Azure para mySQL.

## <a name="client-interfaces"></a>Interfaces de cliente
A MySQL oferece conectividade padrão do controlador de base de dados para usar o MySQL com aplicações e ferramentas compatíveis com os padrões da indústria ODBC e JDBC. Qualquer sistema que funcione com a ODBC ou a JDBC pode utilizar o MySQL.

| **Idioma** | **Plataforma** | **Recurso Adicional** | **Transferir** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [MsQL motorista nativo para PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Transferir](https://secure.php.net/downloads.php) |
| ODBC | Windows, Linux, Mac OS X e unix | [Guia de desenvolvimento do Conector MySQL/ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Transferir](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Guia de programador/programador MySQL](https://dev.mysql.com/doc/connector-net/en/) | [Transferir](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Plataforma independente | [Guia de desenvolvimento do Conector MySQL/J 5.1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Transferir](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/nó-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Transferir](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Guia de desenvolvimento do Conector/Python MySQL](https://dev.mysql.com/doc/connector-python/en/) | [Transferir](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Guia de desenvolvimento do Conector MySQL/C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Transferir](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Guia de desenvolvimento do Conector/C do MySQL](https://dev.mysql.com/doc/refman/8.0/en/c-api.html) | [Transferir](https://dev.mysql.com/downloads/connector/c/)
| Perl | Windows, Linux, Mac OS X e unix | [DBD::Mysql](https://metacpan.org/pod/DBD::mysql) | [Transferir](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Passos seguintes
Leia estes quickstarts sobre como ligar e consultar a Base de Dados Azure para mySQL utilizando a sua linguagem de eleição:

[PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET (C#)](./connect-csharp.md) | [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [Go](./connect-go.md)

