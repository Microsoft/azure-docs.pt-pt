---
title: Compatibilidade do condutor e das ferramentas - Base de Dados Azure para o MySQL
description: Este artigo descreve os controladores e ferramentas de gestão mySQL que são compatíveis com a Base de Dados Azure para o MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: f2ea7af0ec9c740c07cd3a5e4ebd88a9ad1a031d
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542648"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Condutores e ferramentas de gestão MySQL compatíveis com a Base de Dados Azure para o MySQL
Este artigo descreve os controladores e ferramentas de gestão compatíveis com a Base de Dados Azure para o MySQL.

## <a name="mysql-drivers"></a>Motoristas MySQL
A Azure Database for MySQL usa a edição comunitária mais popular do mundo da base de dados MySQL. Portanto, é compatível com uma grande variedade de linguagens de programação e motoristas. O objetivo é apoiar as três versões mais recentes dos condutores do MySQL, e os esforços com autores da comunidade de código aberto para melhorar constantemente a funcionalidade e usabilidade dos condutores do MySQL continuam. Está fornecida no quadro do seguinte quadro uma lista de condutores que tenham sido testados e que se encontrem compatíveis com a Base de Dados Azure para o MySQL 5.6 e 5.7:

| **Linguagem de programação** | **Controlador** | **Ligações** | **Versões compatíveis** | **Versões incompatíveis** | **Notas** |
| :----------------------- | :--------- | :-------- | :---------------------- | :------------------------ | :-------- |
| PHP | mysqli, pdo_mysql, mysqlnd | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | Para a ligação PHP 7.0 com o SSL MySQLi, adicione MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT na cadeia de ligação. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Conjunto do DOP: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` opção para falso.|
| .NET | Conector Async MySQL para .NET | https://github.com/mysql-net/MySqlConnector <br> [Pacote de instalação da Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0.27 e depois | 0.26.5 e antes | |
| .NET | Conector MySQL/NET | https://github.com/mysql/mysql-connector-net | 6.6.3 ,7.0 ,8.0 |  | Um erro de codificação pode causar a falha de ligações em alguns sistemas windows não UTF8. |
| Node.js | mysqljs | https://github.com/mysqljs/mysql/ <br> Pacote de instalação da NPM:<br> Correr `npm install mysql` a partir de NPM | 2.15 | 2.14.1 e antes | |
| Node.js | nó-mysql2 | https://github.com/sidorares/node-mysql2 | 1.3.4+ | | |
| Go | Go MySQL Driver | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 e antes | Utilizar `allowNativePasswords=true` no fio de ligação para a versão 1.3. A versão 1.4 contém uma correção e `allowNativePasswords=true` já não é necessária. |
| Python | Conector/Python MySQL | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2, use 8.0.16+ com MySQL 8.0  | 1.2.2 e antes | |
| Python | PyMySQL | https://pypi.org/project/PyMySQL/ | 0.7.11, 0.8.0, 0.8.1, 0.9.3+ | 0.9.0 - 0.9.2 (regressão na web2py) | |
| Java | Conector MariaDB/J | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 e antes | | 
| Java | Conector MySQL/J | https://github.com/mysql/mysql-connector-j | 5.1.21+, use 8.0.17+ com MySQL 8.0 | 5.1.20 e abaixo | |
| C | Conector MySQL/C (libmysqlclient) | https://dev.mysql.com/doc/refman/5.7/en/c-api-implementations.html | 6.0.2+ | | |
| C | Conector MySQL/ODBC (myodbc) | https://github.com/mysql/mysql-connector-odbc | 3.51.29+ | | |
| C++ | Conector MySQL/C++ | https://github.com/mysql/mysql-connector-cpp | 1.1.9+ | 1.1.3 e abaixo | | 
| C++ | MySQL++| https://github.com/tangentsoft/mysqlpp | 3.2.3+ | | |
| Ruby | mysql2 | https://github.com/brianmario/mysql2 | 0.4.10+ | | |
| R | RMySQL | https://github.com/rstats-db/RMySQL | 0.10.16+ | | |
| Swift | mysql-swift | https://github.com/novi/mysql-swift | 0.7.2+ | | |
| Swift | vapor/mysql | https://github.com/vapor/mysql-kit | 2.0.1+ | | |

## <a name="management-tools"></a>Ferramentas de Gestão
A vantagem de compatibilidade estende-se também às ferramentas de gestão de bases de dados. As ferramentas existentes devem continuar a trabalhar com a Base de Dados Azure para o MySQL, desde que a manipulação da base de dados funcione dentro dos limites das permissões do utilizador. Três ferramentas comuns de gestão de bases de dados que foram testadas e consideradas compatíveis com a Base de Dados Azure para o MySQL 5.6 e 5.7 estão listadas no quadro seguinte:

|                                     | **MySQL Workbench 6.x e acima** | **Navicat 12** | **PHPMyAdmin 4.x e para cima** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| **Criar, atualizar, ler, escrever, excluir** | X | X | X |
| **Ligação SSL** | X | X | X |
| **Conclusão automática de consulta SQL** | X | X |  |
| **Dados de Importação e Exportação** | X | X | X |
| **Exportação para múltiplos formatos** | X | X | X |
| **Backup e Restauro** |  | X |  |
| **Mostrar parâmetros do servidor** | X | X | X |
| **Mostrar ligações ao cliente** | X | X | X |

## <a name="next-steps"></a>Passos seguintes

- [Resolver problemas de ligação à Base de Dados do Azure para MySQL](howto-troubleshoot-common-connection-issues.md)