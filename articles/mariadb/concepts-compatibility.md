---
title: Compatibilidade de drivers e ferramentas – banco de dados do Azure para MariaDB
description: Este artigo descreve os drivers MariaDB e as ferramentas de gerenciamento que são compatíveis com o banco de dados do Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: daec0aaf04cae26b6467cc4472305e75517cee5d
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772992"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>Drivers MariaDB e ferramentas de gerenciamento compatíveis com o banco de dados do Azure para MariaDB

Este artigo descreve os drivers e as ferramentas de gerenciamento que são compatíveis com o banco de dados do Azure para MariaDB.

## <a name="mariadb-drivers"></a>Drivers MariaDB

O banco de dados do Azure para MariaDB usa a Community Edition do MariaDB Server. Portanto, ele é compatível com uma ampla variedade de linguagens de programação e drivers. A API e o protocolo do MariaDB são compatíveis com os usados pelo MySQL. Isso significa que os conectores que funcionam com o MySQL também devem funcionar com o MariaDB.

O objetivo é dar suporte às três versões mais recentes do MariaDB drivers e esforços com autores da comunidade de software livre para melhorar constantemente a funcionalidade e a usabilidade dos drivers do MariaDB continuar. Uma lista de drivers que foram testados e considerados compatíveis com o banco de dados do Azure para MariaDB 10,2 é fornecida na tabela a seguir:

**Driver** | **Vincule** | **Versões compatíveis** | **Versões incompatíveis** | **Notas**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5,5, 5,6, 7. x | 5,3 | Para conexão PHP 7,0 com SSL MySQLi, adicione MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT na cadeia de conexão. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO definido: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` opção como false.
.NET | [MySqlConnector no GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Pacote de instalação do NuGet](https://www.nuget.org/packages/MySqlConnector/) | 0,27 e depois | 0.26.5 e anterior |
Conector do MySQL/NET | [Conector do MySQL/NET](https://github.com/mysql/mysql-connector-net) | 8,0, 7,0, 6,10 |  | Um erro de codificação pode fazer com que as conexões falhem em alguns sistemas não UTF8 do Windows.
Node.js |  [MySQLjs no GitHub](https://github.com/mysqljs/mysql/) <br> Pacote de instalação do NPM:<br> Executar `npm install mysql` de NPM | 2,15 | 2.14.1 e anterior
GO | https://github.com/go-sql-driver/mysql/releases | 1,3, 1,4 | 1,2 e antes | Use `allowNativePasswords=true` na cadeia de conexão para a versão 1,3. A versão 1,4 contém uma correção e `allowNativePasswords=true` não é mais necessária.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2,0, 2,1, 2,2 | 1.2.2 e anterior |
Java | https://downloads.mariadb.org/connector-java/ | 2,1, 2,0, 1,6 | 1.5.5 e anterior |

## <a name="management-tools"></a>Ferramentas de Gestão

A vantagem de compatibilidade também se estende às ferramentas de gerenciamento de banco de dados. Suas ferramentas existentes devem continuar a trabalhar com o banco de dados do Azure para MariaDB, desde que a manipulação do banco de dados opere dentro dos limites de permissões de usuário. Três ferramentas comuns de gerenciamento de banco de dados que foram testadas e encontradas para serem compatíveis com o banco de dados do Azure para MariaDB 10,2 estão listadas na tabela a seguir:

| | **MySQL Workbench 6. x e superior** | **Navicat 12** | **PHPMyAdmin 4. x e superior**
---|---|---|---
Criar, atualizar, ler, gravar, excluir | X | X | X
Conexão SSL | X | X | X
Preenchimento automático de consulta SQL | X | X |
Importar e exportar dados | X | X | X
Exportar para vários formatos | X | X | X
Backup e Restauro |  | X |
Exibir parâmetros do servidor | X | X | X
Exibir conexões de cliente | X | X | X

## <a name="next-steps"></a>Passos seguintes

- [Resolver problemas de ligação ao Azure Database for MariaDB](howto-troubleshoot-common-connection-issues.md)