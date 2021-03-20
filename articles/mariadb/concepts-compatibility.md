---
title: Compatibilidade de motoristas e ferramentas - Base de Dados Azure para MariaDB
description: Este artigo descreve os controladores e ferramentas de gestão MariaDB compatíveis com a Base de Dados Azure para a MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 49fd2c8da3ba0a44f4901bc6c7a2241e9e20df4d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98659874"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>Motoristas e ferramentas de gestão MariaDB compatíveis com Azure Database para MariaDB

Este artigo descreve os controladores e ferramentas de gestão compatíveis com a Base de Dados Azure para a MariaDB.

## <a name="mariadb-drivers"></a>Motoristas MariaDB

A Azure Database for MariaDB utiliza a edição comunitária do servidor MariaDB. Portanto, é compatível com uma grande variedade de linguagens de programação e motoristas. A API da MariaDB e o protocolo são compatíveis com os utilizados pelo MySQL. Isto significa que os conectores que trabalham com o MySQL também devem trabalhar com a MariaDB.

O objetivo é apoiar as três versões mais recentes dos condutores da MariaDB, e os esforços com autores da comunidade de código aberto para melhorar constantemente a funcionalidade e usabilidade dos condutores mariaDB continuam. No quadro seguinte está fornecida uma lista de condutores que tenham sido testados e que se encontrem compatíveis com a Base de Dados Azure para MariaDB 10.2:

**Controlador** | **Ligações** | **Versões compatíveis** | **Versões incompatíveis** | **Notas**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | Para a ligação PHP 7.0 com o SSL MySQLi, adicione MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT na cadeia de ligação. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Conjunto do DOP: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` opção para falso.
.NET | [MySqlConnector no GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Pacote de instalação da Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0.27 e depois | 0.26.5 e antes |
Conector MySQL/NET | [Conector MySQL/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | Um erro de codificação pode causar a falha de ligações em alguns sistemas windows não UTF8.
Node.js |  [MySQLjs no GitHub](https://github.com/mysqljs/mysql/) <br> Pacote de instalação da NPM:<br> Correr `npm install mysql` a partir de NPM | 2.15 | 2.14.1 e antes
GO | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 e antes | Utilizar `allowNativePasswords=true` no fio de ligação para a versão 1.3. A versão 1.4 contém uma correção e `allowNativePasswords=true` já não é necessária.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 e antes |
Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 e antes |

## <a name="management-tools"></a>Ferramentas de Gestão

A vantagem de compatibilidade estende-se também às ferramentas de gestão de bases de dados. As ferramentas existentes devem continuar a trabalhar com a Base de Dados Azure para o MariaDB, desde que a manipulação da base de dados funcione dentro dos limites das permissões do utilizador. Três ferramentas comuns de gestão de bases de dados que foram testadas e consideradas compatíveis com a Base de Dados Azure para MariaDB 10.2 estão listadas no quadro seguinte:

| Ação | **MySQL Workbench 6.x e acima** | **Navicat 12** | **PHPMyAdmin 4.x e para cima**
---|---|---|---
Criar, atualizar, ler, escrever, excluir | X | X | X
Ligação SSL | X | X | X
Conclusão automática de consulta SQL | X | X |
Dados de Importação e Exportação | X | X | X
Exportação para múltiplos formatos | X | X | X
Cópia de Segurança e Restauro |  | X |
Mostrar parâmetros do servidor | X | X | X
Mostrar ligações ao cliente | X | X | X

## <a name="next-steps"></a>Passos seguintes

- [Resolver problemas de ligação ao Azure Database for MariaDB](howto-troubleshoot-common-connection-issues.md)