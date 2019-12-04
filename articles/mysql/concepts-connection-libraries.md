---
title: Bibliotecas de conexões-banco de dados do Azure para MySQL
description: Este artigo lista cada biblioteca ou driver que os programas cliente podem usar ao se conectarem ao banco de dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 276adcb796c6fcdf1ef2d38458ca8f6ac73e5f5a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765280"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Bibliotecas de conexão para o banco de dados do Azure para MySQL
Este artigo lista cada biblioteca ou driver que os programas cliente podem usar ao se conectarem ao banco de dados do Azure para MySQL.

## <a name="client-interfaces"></a>Interfaces de cliente
O MySQL oferece conectividade de driver de banco de dados padrão para usar o MySQL com aplicativos e ferramentas compatíveis com os padrões do setor ODBC e JDBC. Qualquer sistema que funcione com ODBC ou JDBC pode usar o MySQL.

| **Language** (Idioma) | **Plataforma** | **Recurso adicional** | **Transferência** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [Driver nativo do MySQL para PHP-mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Transferência](https://secure.php.net/downloads.php) |
| ODBC | Plataformas Windows, Linux, Mac OS X e UNIX | [Guia do desenvolvedor do MySQL Connector/ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Transferência](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Guia do desenvolvedor do MySQL Connector/Net](https://dev.mysql.com/doc/connector-net/en/) | [Transferência](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Independente de plataforma | [Guia do desenvolvedor do MySQL Connector/J 5,1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Transferência](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Transferência](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux Mac OS X | [Guia do desenvolvedor do MySQL Connector/Python](https://dev.mysql.com/doc/connector-python/en/) | [Transferência](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux Mac OS X | [Guia do desenvolvedorC++ /conector do MySQL](https://dev.mysql.com/doc/connector-cpp/en/) | [Transferência](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux Mac OS X | [Guia do desenvolvedor do MySQL Connector/C](https://dev.mysql.com/doc/refman/8.0/en/c-api.html) | [Transferência](https://dev.mysql.com/downloads/connector/c/)
| Perl | Plataformas Windows, Linux, Mac OS X e UNIX | [DBD:: MySQL](https://metacpan.org/pod/DBD::mysql) | [Transferência](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Passos seguintes
Leia estes guias de início rápido sobre como se conectar e consultar o banco de dados do Azure para MySQL usando a linguagem de sua escolha:

[Php](./connect-php.md) | [Java](./connect-java.md) |  [.net (C#)](./connect-csharp.md) | [Python](./connect-python.md) | [node. js](./connect-nodejs.md) |  | do [C++](connect-cpp.md) [Ruby](./connect-ruby.md) | [go](./connect-go.md)

