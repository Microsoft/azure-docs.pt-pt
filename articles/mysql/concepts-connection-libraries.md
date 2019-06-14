---
title: Bibliotecas de ligação para base de dados do Azure para MySQL
description: Este artigo lista cada driver ou biblioteca que os programas de cliente podem utilizar ao ligar à base de dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 6ce0f2c761ede7d326f52f4d93d7f1b0bfa98cb2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60525567"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Bibliotecas de ligação para base de dados do Azure para MySQL
Este artigo lista cada driver ou biblioteca que os programas de cliente podem utilizar ao ligar à base de dados do Azure para MySQL.

## <a name="client-interfaces"></a>Interfaces de cliente
MySQL oferece a conectividade de driver de base de dados standard para utilizar o MySQL com aplicações e ferramentas que são compatíveis com padrões da indústria, ODBC e JDBC. Qualquer sistema que funciona com ODBC ou JDBC pode utilizar o MySQL.

| **Language** (Idioma) | **Plataforma** | **Recursos adicionais** | **Transferência** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [Controlador nativo do MySQL para PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Transferência](https://secure.php.net/downloads.php) |
| ODBC | Plataformas Windows, Linux, Mac OS X e Unix | [Guia do programador do MySQL Connector/ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Transferência](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Guia do programador do MySQL Connector/Net](https://dev.mysql.com/doc/connector-net/en/) | [Transferência](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Independente de plataforma | [Guia do programador do MySQL Connector/J 5.1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Transferência](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Transferência](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Guia do programador do MySQL Connector/Python](https://dev.mysql.com/doc/connector-python/en/) | [Transferência](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Guia do programador do MySQL Connector/C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Transferência](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Guia do programador do MySQL Connector/C](https://dev.mysql.com/doc/connector-c/en/) | [Transferência](https://dev.mysql.com/downloads/connector/c/)
| Perl | Plataformas Windows, Linux, Mac OS X e Unix | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Transferência](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Passos Seguintes
Leia estes guias de introdução sobre como ligar e consultar base de dados do Azure para MySQL através de linguagem de sua escolha:

[PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET (C#)](./connect-csharp.md) | [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [Go](./connect-go.md)

