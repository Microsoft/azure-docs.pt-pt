---
title: "Controladores de MySQL e compatibilidade de ferramentas de gestão"
description: "Este artigo descreve os controladores de MySQL e ferramentas de gestão que são compatíveis com a base de dados do Azure para MySQL."
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 5fc13ef07b61feb9e9fdd73123a09daa61f6aaf3
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2018
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Controladores de MySQL e ferramentas de gestão compatíveis com a base de dados do Azure para MySQL
Este artigo descreve os controladores e ferramentas de gestão que são compatíveis com a base de dados do Azure para MySQL.

## <a name="mysql-drivers"></a>MySQL Drivers
Base de dados do Azure para MySQL utiliza a edição community mais popular do mundo da base de dados MySQL. Por conseguinte, é compatível com uma ampla variedade de controladores e linguagens de programação. O objetivo é para suportar as versões mais recentes três controladores MySQL e esforços com autores da Comunidade open source para constantemente melhorar a funcionalidade e a facilidade de utilização de controladores de MySQL continuar. É fornecida uma lista de controladores que tenham sido testado e a ser compatível com a base de dados do Azure para MySQL 5.6 e 5.7 encontrado na tabela seguinte:

| **Controlador** | **Links** | **Versões compatíveis** | **Versões de uma** | **Notas** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | http://php.net/downloads.php | 5.5 5.6 7. x | 5.3 | Para PHP 7.0 ligação com SSL MySQLi, adicione MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT na cadeia de ligação. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Conjunto PDO: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` opção para FALSO.|
| .Net | [MySqlConnector no GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Pacote de instalação do Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0.27 e após | 0.26.5 e antes | |
| Nodejs |  [MySQLjs no GitHub](https://github.com/mysqljs/mysql/releases) <br> Pacote de instalação de NPM:<br> Executar `npm install mysql` de NPM | 2.15 | 2.14.1 e antes | |
| GO | https://github.com/go-sql-driver/mysql/releases | 1.3 | 1.2 e antes | Utilizar allowNativePasswords = true na cadeia de ligação |
| Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 e antes | |
| Java | https://downloads.mariadb.org/connector-java/ | 2.1 2.0 1.6 | 1.5.5 e antes | |

## <a name="management-tools"></a>Ferramentas de Gestão
Expande a vantagem de compatibilidade para ferramentas de gestão de bases de dados bem. As ferramentas existentes devem continuar a trabalhar com a base de dados do Azure para MySQL, desde que funciona a manipulação de base de dados dentro do confines de permissões de utilizador. Três ferramentas de gestão da base de dados comuns que foram testadas e não for compatível com a base de dados do Azure para MySQL 5.6 e 5.7 estão listadas na seguinte tabela:

|                                     | **MySQL Workbench 6. x e até** | **Navicat 12** | **PHPMyAdmin 4. x e até** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Criar, atualizar, ler, escrever, eliminar | X | X | X |
| Ligação de SSL | X | X | X |
| Conclusão de automática de consulta SQL | X | X |  |
| Importar e exportar dados | X | X | X |
| Exportar para vários formatos | X | X | X |
| Cópia de Segurança e Restauro |  | X |  |
| Apresentar os parâmetros de servidor | X | X | X |
| Apresentar ligações de cliente | X | X | X |
