---
title: Resolver a corrupção na Base de Dados - Base de Dados Azure para o MySQL
description: Saiba como corrigir problemas de corrupção na base de dados para a Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 815b24d25e674e5460cc50d7eb6871f740994893
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938937"
---
# <a name="troubleshoot-database-corruption-on-azure-database-for-mysql"></a>Combate à corrupção na base de dados de resolução de problemas na Base de Dados Azure para o MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

A corrupção na base de dados pode causar tempo de inatividade para a sua aplicação e também é fundamental resolver o problema a tempo de evitar a perda de dados. Quando ocorrer corrupção na base de dados, verá no seu servidor registos deste erro **InnoDB: Corrupção de página de base de dados no disco ou uma falha**.

Neste guia irá aprender a corrigir se a base de dados ou a tabela forem corrompidas. A Azure Database for MySQL utiliza o motor InnoDB e possui operações automatizadas de verificação e reparação de corrupção. O InnoDB verifica se há páginas corrompidas, realizando datas de verificação em cada página que lê, e se encontrar uma discrepância de dados, irá parar automaticamente o servidor MySQL.

Experimente qualquer uma destas opções abaixo para ajudar a mitigar rapidamente os problemas de corrupção na sua base de dados.

## <a name="restart-your-mysql-server"></a>Reinicie o seu servidor MySQL

Normalmente nota que uma base de dados ou tabela é corrompida quando a sua aplicação acede a essa base de dados de ro de tabela. Uma vez que o InnoDB possui um mecanismo de recuperação de falhas que pode resolver a maioria dos problemas quando o servidor é reiniciado. Assim, reiniciar o servidor deve ajudar o servidor a recuperar de uma falha que fez com que a base de dados estivesse em mau estado.

##  <a name="resolve-data-corruption-with-dump-and-restore-method"></a>Resolver a corrupção de dados com o método de despejo e restauro

Recomenda-se resolver a questão da corrupção com um **método de despejo e restauro.** Isto envolve ter acesso à tabela corrompida, utilizando o utilitário **mysqldump** para criar uma cópia de segurança lógica da tabela, que irá manter a estrutura da tabela e os dados dentro dela, e, em seguida, recarregar a tabela de volta para a base de dados.

### <a name="backup-your-database-or-tables"></a>Faça backup da sua base de dados ou tabelas

> [!Important]
> - Faça com que tenha configurado uma regra de firewall para aceder ao servidor a partir da sua máquina de cliente. Veja como configurar a [regra de firewall no servidor único](howto-manage-firewall-using-portal.md) e na regra de firewall no servidor [flexível](flexible-server/how-to-connect-tls-ssl.md).
> - Utilize a opção SSL ```--ssl-cert``` para **mysqldump** se o SSL estiver ativado

Crie um ficheiro de backup a partir da linha de comando usando o mysqldump usando este comando

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Os parâmetros a fornecer são:
- [ssl-cert=/caminho/para/pem] Descarregue o certificado SSL na sua máquina de cliente e desemende o caminho no comando. NÃO utilize isto é SSL está desativado.
- [Anfitrião] é a sua Base de Dados Azure para o servidor MySQL
- [uname] é o nome de utilizador do seu administrador de servidor
- [passe] é a senha para o seu utilizador administrador
- [dbname] é o nome da sua base de dados
- [backupfile.sql] se o nome de ficheiro para a sua base de dados backup

> [!Important]
> - Para um servidor único, utilize o formato ```admin-user@servername``` para substituir ```myserveradmin``` nos comandos abaixo.
> - Para o servidor flexível, utilize o formato ```admin-user``` para substituir ```myserveradmin``` nos comandos abaixo.

Se uma tabela específica for corrompida, selecione tabelas específicas na sua base de dados para fazer o back up usando este exemplo
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

Para fazer o back up de uma ou mais bases de dados, utilize o interruptor de base de dados e liste os nomes da base de dados separados por espaços.

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

###  <a name="restore-your-database-or-tables"></a>Restaurar a sua base de dados ou tabelas

Os seguintes passos mostram como o TP restaura a sua base de dados ou tabelas. Uma vez criado o ficheiro de cópia de segurança, pode restaurar a tabela ou bases de dados utilizando o utilitário**mysql.** Executar o comando como mostrado abaixo:

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Aqui está um exemplo da restauração ```testdb``` do ficheiro de backup criado com **mysqldump**. 

> [!Important]
> - Para um servidor único, utilize o formato ```admin-user@servername``` para substituir no comando ```myserveradmin``` abaixo.
> - Para o servidor flexível, utilize o formato ```admin-user``` para substituir no comando ```myserveradmin``` abaixo. 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>Passos Seguintes
Se os passos acima não ajudarem a resolver o problema, pode sempre restaurar todo o servidor.
- [Restaurar uma base de dados Azure para o MySQL Single Server](howto-restore-server-portal.md)
- [Restaurar uma base de dados Azure para o MySQL Flexible Server](flexible-server/how-to-restore-server-portal.md)



