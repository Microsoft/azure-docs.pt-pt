---
title: Resolver a corrupção na base de dados - Base de Dados Azure para o MySQL
description: Neste artigo, você vai aprender sobre como corrigir problemas de corrupção na base de dados na Base de Dados Azure para o MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 21e4189e56f704129710da5b1d39613c4e1b1df5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91766893"
---
# <a name="troubleshoot-database-corruption-in-azure-database-for-mysql"></a>Combate à corrupção na base de dados de resolução de problemas na Base de Dados Azure para o MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

A corrupção na base de dados pode causar tempo de inatividade para a sua aplicação. Também é fundamental resolver problemas de corrupção a tempo de evitar a perda de dados. Quando ocorrer corrupção na base de dados, verá este erro nos registos do seu servidor: `InnoDB: Database page corruption on disk or a failed.`

Neste artigo, você vai aprender a resolver problemas de base de dados ou de corrupção de mesa. A base de dados Azure para o MySQL utiliza o motor InnoDB. Apresenta operações automatizadas de verificação e reparação de corrupção. O InnoDB verifica se há páginas corruptas, executando as contas em todas as páginas que lê. Se encontrar uma discrepância de dados, irá parar automaticamente o servidor MySQL.

Tente as seguintes opções para mitigar rapidamente os seus problemas de corrupção na base de dados.

## <a name="restart-your-mysql-server"></a>Reinicie o seu servidor MySQL

Normalmente, nota que uma base de dados ou tabela é corrupta quando a sua aplicação acede à tabela ou à base de dados. O InnoDB possui um mecanismo de recuperação de falhas que pode resolver a maioria dos problemas quando o servidor é reiniciado. Assim, reiniciar o servidor pode ajudar o servidor a recuperar de uma falha que fez com que a base de dados estivesse em mau estado.

## <a name="use-the-dump-and-restore-method"></a>Use o método de despejo e restauro

Recomendamos que resolva os problemas de corrupção utilizando um método *de despejo e restauro.* Este método envolve:
1. A aceder à mesa corrupta.
1. Usando o utilitário mysqldump para criar uma cópia de segurança lógica da tabela. A cópia de segurança conservará a estrutura da tabela e os dados dentro dela.
1. Recarregando a mesa para a base de dados.

### <a name="back-up-your-database-or-tables"></a>Ressou a sua base de dados ou tabelas

> [!Important]
> - Certifique-se de que configura uma regra de firewall para aceder ao servidor a partir da sua máquina de cliente. Para obter mais informações, consulte [a regra de configuração de uma regra de firewall no Single Server](howto-manage-firewall-using-portal.md) e [configuure uma regra de firewall no Servidor Flexível](flexible-server/how-to-connect-tls-ssl.md).
> - Utilize a opção SSL `--ssl-cert` para o mysqldump se tiver SSL ativado.

Crie um ficheiro de reserva a partir da linha de comando utilizando o mysqldump. Utilize este comando:

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Descrições dos parâmetros:
- `[ssl-cert=/path/to/pem]`: O caminho para o certificado SSL. Descarregue o certificado SSL na sua máquina de cliente e desafie o caminho no comando. Não utilize este parâmetro se o SSL estiver desativado.
- `[host]`: A sua base de dados Azure para o servidor MySQL.
- `[uname]`: O nome de utilizador do seu administrador do servidor.
- `[pass]`: A palavra-passe para o seu utilizador administrativo.
- `[dbname]`: O nome da sua base de dados.
- `[backupfile.sql]`: O nome do ficheiro da sua base de dados.

> [!Important]
> - Para o Servidor Único, utilize o formato `admin-user@servername` para substituir `myserveradmin` nos seguintes comandos.
> - Para o Servidor Flexível, utilize o formato `admin-user` para substituir `myserveradmin` nos seguintes comandos.

Se uma tabela específica for corrupta, selecione tabelas específicas na sua base de dados para fazer o back up:
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

Para fazer uma ou mais bases de dados, utilize o `--database` comutador e enuqueca os nomes da base de dados, separados por espaços:

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

### <a name="restore-your-database-or-tables"></a>Restaurar a sua base de dados ou tabelas

Os seguintes passos mostram como restaurar a sua base de dados ou tabelas. Depois de criar o ficheiro de backup, pode restaurar as tabelas ou bases de dados utilizando o utilitário mysql. Execute este comando:

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Aqui está um exemplo que restaura `testdb` a partir de um ficheiro de backup criado com mysqldump: 

> [!Important]
> - Para o Servidor Único, utilize o formato `admin-user@servername` para substituir no seguinte `myserveradmin` comando.
> - Para o Servidor Flexível, utilize o formato ```admin-user``` para substituir no seguinte `myserveradmin` comando. 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>Passos seguintes
Se os passos anteriores não resolverem o problema, pode sempre restaurar todo o servidor:
- [Restaurar o servidor na Base de Dados Azure para o MySQL - Servidor Único](howto-restore-server-portal.md)
- [Restaurar o servidor na Base de Dados Azure para o MySQL - Servidor Flexível](flexible-server/how-to-restore-server-portal.md)



