---
title: Compatibilidade de condutores e ferramentas - Base de Dados Azure para MariaDB
description: Este artigo descreve os condutores mariadb e ferramentas de gestão compatíveis com a Base de Dados Azure para mariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a0cee198f028fd90e04dac15e98d7cd33aee9201
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79532353"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>Motoristas mariaDB e ferramentas de gestão compatíveis com Base de Dados Azure para MariaDB

Este artigo descreve os condutores e ferramentas de gestão compatíveis com a Base de Dados Azure para a MariaDB.

## <a name="mariadb-drivers"></a>Motoristas MariaDB

A Base de Dados Azure para MariaDB utiliza a edição comunitária do servidor MariaDB. Por conseguinte, é compatível com uma grande variedade de linguagens de programação e condutores. A API e o protocolo da MariaDB são compatíveis com os utilizados pela MySQL. Isto significa que os conectores que trabalham com a MySQL também devem trabalhar com a MariaDB.

O objetivo é apoiar as três versões mais recentes dos condutores da MariaDB, e os esforços com autores da comunidade de código aberto para melhorar constantemente a funcionalidade e a usabilidade dos condutores da MariaDB continuam. Na tabela:

**Controlador** | **Ligações** | **Versões compatíveis** | **Versões incompatíveis** | **Notas**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | Para a ligação PHP 7.0 com o SSL MySQLi, adicione MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT na cadeia de ligação. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Conjunto de ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` DOP: opção para falso.
.NET | [MySqlConnector no GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Pacote de instalação da Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0.27 e depois | 0.26.5 e antes |
Conector/NET MySQL | [Conector/NET MySQL](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | Um bug de codificação pode fazer com que as ligações falhem em alguns sistemas Windows não UTF8.
Node.js |  [MySQLjs no GitHub](https://github.com/mysqljs/mysql/) <br> Pacote de instalação da NPM:<br> Fugir `npm install mysql` do NPM | 2.15 | 2.14.1 e antes
GO | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 e antes | Utilize `allowNativePasswords=true` na cadeia de ligação para a versão 1.3. A versão 1.4 `allowNativePasswords=true` contém uma correção e já não é necessária.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 e antes |
Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 e antes |

## <a name="management-tools"></a>Ferramentas de Gestão

A vantagem de compatibilidade estende-se também às ferramentas de gestão de bases de dados. As suas ferramentas existentes devem continuar a trabalhar com a Base de Dados Azure para o MariaDB, desde que a manipulação da base de dados funcione dentro dos limites das permissões dos utilizadores. Três ferramentas comuns de gestão de bases de dados que foram testadas e consideradas compatíveis com a Base de Dados Azure para MariaDB 10.2 estão listadas no quadro seguinte:

| | **Bancada de trabalho MySQL 6.x e para cima** | **Navicat 12** | **PHPMyAdmin 4.x e para cima**
---|---|---|---
Criar, Atualizar, Ler, Escrever, Excluir | X | X | X
Conexão SSL | X | X | X
Conclusão automática da consulta SQL | X | X |
Dados de Importação e Exportação | X | X | X
Exportação para Múltiplos Formatos | X | X | X
Cópia de Segurança e Restauro |  | X |
Parâmetros do servidor de visualização | X | X | X
Mostrar ligações ao cliente | X | X | X

## <a name="next-steps"></a>Passos seguintes

- [Resolver problemas de ligação ao Azure Database for MariaDB](howto-troubleshoot-common-connection-issues.md)