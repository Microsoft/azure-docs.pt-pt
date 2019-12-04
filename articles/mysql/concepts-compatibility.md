---
title: Compatibilidade de drivers e ferramentas-banco de dados do Azure para MySQL
description: Este artigo descreve os drivers do MySQL e as ferramentas de gerenciamento que são compatíveis com o banco de dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 83c1463beec47ed3535811de822348cc7706f757
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765346"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Drivers MySQL e ferramentas de gerenciamento compatíveis com o banco de dados do Azure para MySQL
Este artigo descreve os drivers e as ferramentas de gerenciamento que são compatíveis com o banco de dados do Azure para MySQL.

## <a name="mysql-drivers"></a>Drivers do MySQL
O banco de dados do Azure para MySQL usa a Community Edition mais popular do banco de dados MySQL do mundo. Portanto, ele é compatível com uma ampla variedade de linguagens de programação e drivers. O objetivo é dar suporte às três versões mais recentes dos drivers do MySQL e esforços com autores da comunidade de software livre para melhorar constantemente a funcionalidade e a usabilidade dos drivers do MySQL continuar. Uma lista de drivers que foram testados e considerados compatíveis com o banco de dados do Azure para MySQL 5,6 e 5,7 é fornecida na tabela a seguir:

| **Linguagem de programação** | **Driver** | **Vincule** | **Versões compatíveis** | **Versões incompatíveis** | **Notas** |
| :----------------------- | :--------- | :-------- | :---------------------- | :------------------------ | :-------- |
| PHP | mysqli, pdo_mysql, mysqlnd | https://secure.php.net/downloads.php | 5,5, 5,6, 7. x | 5,3 | Para conexão PHP 7,0 com SSL MySQLi, adicione MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT na cadeia de conexão. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO definido: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` opção como false.|
| .NET | Conector MySQL assíncrono para .NET | https://github.com/mysql-net/MySqlConnector <br> [Pacote de instalação do NuGet](https://www.nuget.org/packages/MySqlConnector/) | 0,27 e depois | 0.26.5 e anterior | |
| .NET | Conector do MySQL/NET | https://github.com/mysql/mysql-connector-net | 8,0, 7,0, 6,10 |  | Um erro de codificação pode fazer com que as conexões falhem em alguns sistemas não UTF8 do Windows. |
| Node.js | mysqljs | https://github.com/mysqljs/mysql/ <br> Pacote de instalação do NPM:<br> Executar `npm install mysql` de NPM | 2,15 | 2.14.1 e anterior | |
| Node.js | nó-mysql2 | https://github.com/sidorares/node-mysql2 | 1.3.4 + | | |
| Go | Acessar o driver do MySQL | https://github.com/go-sql-driver/mysql/releases | 1,3, 1,4 | 1,2 e antes | Use `allowNativePasswords=true` na cadeia de conexão para a versão 1,3. A versão 1,4 contém uma correção e `allowNativePasswords=true` não é mais necessária. |
| Python | Conector do MySQL/Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2,0, 2,1, 2,2, use 8.0.16 + com MySQL 8,0  | 1.2.2 e anterior | |
| Python | PyMySQL | https://pypi.org/project/PyMySQL/ | 0.7.11, 0.8.0, 0.8.1, 0.9.3 + | 0.9.0-0.9.2 (regressão em web2py) | |
| Java | Conector do MariaDB/J | https://downloads.mariadb.org/connector-java/ | 2,1, 2,0, 1,6 | 1.5.5 e anterior | | 
| Java | Conector do MySQL/J | https://github.com/mysql/mysql-connector-j | 5.1.20 +, use 8.0.17 + com MySQL 8,0 | 5.1.9 e abaixo | |
| C | Conector do MySQL/C (libmysqlclient) | https://dev.mysql.com/doc/refman/5.7/en/c-api-implementations.html | 6.0.2 + | | |
| C | Conector do MySQL/ODBC (MyODBC) | https://github.com/mysql/mysql-connector-odbc | 3.51.29 + | | |
| C++ | Conector do MySQL/C++ | https://github.com/mysql/mysql-connector-cpp | 1.1.9 + | 1.1.3 e abaixo | | 
| C++ | MySQL + +| https://tangentsoft.net/mysql++ | 3.2.3 + | | |
| Ruby | mysql2 | https://github.com/brianmario/mysql2 | 0.4.10 + | | |
| R. | RMySQL | https://github.com/rstats-db/RMySQL | 0.10.16 + | | |
| Swift | MySQL-Swift | https://github.com/novi/mysql-swift | 0.7.2 + | | |
| Swift | vapor/MySQL | https://github.com/vapor/mysql-kit | 2.0.1 + | | |

## <a name="management-tools"></a>Ferramentas de Gestão
A vantagem de compatibilidade também se estende às ferramentas de gerenciamento de banco de dados. Suas ferramentas existentes devem continuar a trabalhar com o banco de dados do Azure para MySQL, desde que a manipulação do banco de dados opere dentro dos limites de permissões de usuário. Três ferramentas de gerenciamento de banco de dados comuns que foram testadas e encontradas para serem compatíveis com o banco de dados do Azure para MySQL 5,6 e 5,7 estão listadas na tabela a seguir:

|                                     | **MySQL Workbench 6. x e superior** | **Navicat 12** | **PHPMyAdmin 4. x e superior** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Criar, atualizar, ler, gravar, excluir | X | X | X |
| Conexão SSL | X | X | X |
| Preenchimento automático de consulta SQL | X | X |  |
| Importar e exportar dados | X | X | X | 
| Exportar para vários formatos | X | X | X |
| Backup e Restauro |  | X |  |
| Exibir parâmetros do servidor | X | X | X |
| Exibir conexões de cliente | X | X | X |

## <a name="next-steps"></a>Passos seguintes

- [Resolver problemas de ligação à Base de Dados do Azure para MySQL](howto-troubleshoot-common-connection-issues.md)