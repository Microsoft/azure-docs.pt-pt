---
title: Migrar usando lixeira e restaurar - Base de Dados Azure para MySQL
description: Este artigo explica duas formas comuns de fazer cópias de segurança e restaurar bases de dados na sua Base de Dados Azure para mySQL, utilizando ferramentas como mysqldump, MySQL Workbench e PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: b15da2aa83231bfdc8732995888349b06ab56d15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78163782"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migrar a sua base de dados MySQL para a Dase de Dados do Azure para MySQL através da funcionalidade de captura e restauro
Este artigo explica duas formas comuns de fazer cópias de segurança e restaurar bases de dados na sua Base de Dados Azure para mySQL
- Despejo e restauro da linha de comando (usando mysqldump) 
- Despejo e restauro usando PHPMyAdmin 

## <a name="before-you-begin"></a>Antes de começar
Para passar por este guia de como orientar, você precisa ter:
- [Criar base de dados Azure para servidor MySQL - portal Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- utilitário de linha de comando [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) instalado numa máquina.
- MySQL [Workbench MySQL Workbench Download](https://dev.mysql.com/downloads/workbench/) ou outra ferramenta MySQL de terceiros para fazer despejo e restaurar comandos.

## <a name="use-common-tools"></a>Use ferramentas comuns
Utilize utilitários e ferramentas comuns, como a Bancada de Trabalho MySQL ou a mysqldump, para ligar e restaurar remotamente os dados na Base de Dados Azure para o MySQL. Utilize estas ferramentas na sua máquina cliente com uma ligação à Internet para ligar à Base de Dados Azure para mySQL. Utilize uma ligação encriptada SSL para as melhores práticas de segurança, consulte também a [conectividade Configure SSL na Base de Dados Azure para MySQL](concepts-ssl-connection-security.md). Não precisa de mover os ficheiros de despejo para qualquer local especial na nuvem ao migrar para a Base de Dados Azure para o MySQL. 

## <a name="common-uses-for-dump-and-restore"></a>Usos comuns para despejo e restauro
Pode utilizar utilitários MySQL, como mysqldump e mysqlpump, para despejar e carregar bases de dados numa base de dados Azure MySQL em vários cenários comuns. Noutros cenários, poderá utilizar a abordagem [Import and Export.](concepts-migrate-import-export.md)

- Utilize lixeiras de base de dados quando estiver a migrar toda a base de dados. Esta recomendação mantém-se ao mover uma grande quantidade de dados mySQL, ou quando pretende minimizar a interrupção do serviço para sites ou aplicações ao vivo. 
-  Confirme que todas as tabelas na base de dados utilizam o motor de armazenamento InnoDB ao carregar os dados para a Base de Dados do Azure para MySQL. A Base de Dados Azure para MySQL suporta apenas o motor de armazenamento InnoDB, pelo que não suporta motores de armazenamento alternativos. Se as suas tabelas estiverem configuradas com outros motores de armazenamento, converta-as no formato do motor InnoDB antes da migração para a Base de Dados Azure para o MySQL.
   Por exemplo, se tiver um WordPress ou WebApp utilizando as tabelas MyISAM, converta primeiro essas tabelas migrando para o formato InnoDB antes de restaurar para a Base de Dados Azure para o MySQL. Utilize a `ENGINE=InnoDB` cláusula para definir o motor utilizado ao criar uma nova tabela e, em seguida, transfira os dados para a tabela compatível antes da restauração. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Para evitar problemas de compatibilidade, confirme que utiliza a mesma versão do MySQL nos sistemas de origem e de destino ao capturar as bases de dados. Por exemplo, se o seu servidor MySQL existente for a versão 5.7, então deve migrar para a Base de Dados Azure para o MySQL configurado para executar a versão 5.7. O `mysql_upgrade` comando não funciona numa Base de Dados Azure para servidor MySQL, e não é suportado. Se precisar de fazer upgrade em versões MySQL, primeiro despejar ou exportar a sua base de dados de versão inferior para uma versão mais alta do MySQL no seu próprio ambiente. Em `mysql_upgrade`seguida, corra , antes de tentar migrar para uma Base de Dados Azure para MySQL.

## <a name="performance-considerations"></a>Considerações de desempenho
Para otimizar o desempenho, tome nota destas considerações ao despejar grandes bases de dados:
-   Use `exclude-triggers` a opção em mysqldump ao despejar bases de dados. Exclua os gatilhos dos ficheiros de despejo para evitar que os comandos do gatilho disparem durante a restauração dos dados. 
-   Utilize `single-transaction` a opção de definir o modo de isolamento de transações para READ REPETIVEL e enviar uma declaração sQL START TRANSACTION para o servidor antes de despejar dados. Despejar muitas tabelas numa única transação faz com que algum armazenamento extra seja consumido durante a restauração. A `single-transaction` opção `lock-tables` e a opção são mutuamente exclusivas porque o LOCK TABLES faz com que quaisquer transações pendentes sejam cometidas implicitamente. Para despejar mesas `single-transaction` grandes, `quick` combine a opção com a opção. 
-   Utilize `extended-insert` a sintaxe de várias linhas que inclui várias listas VALUE. Isto resulta num ficheiro de despejo mais pequeno e acelera as inserções quando o ficheiro é recarregado.
-  Utilize `order-by-primary` a opção em mysqldump ao despejar bases de dados, de modo a que os dados sejam escritos em ordem principal.
-   Utilize `disable-keys` a opção em mysqldump ao despejar dados, para desativar os constrangimentos das chaves estrangeiras antes da carga. Desativar os controlos de chaves estrangeiras proporciona ganhos de desempenho. Ative os constrangimentos e verifique os dados após a carga para garantir a integridade referencial.
-   Utilize mesas divididas quando apropriado.
-   Carregue os dados em paralelo. Evite demasiado paralelismo que o faça atingir um limite de recursos e monitorize os recursos utilizando as métricas disponíveis no portal Azure. 
-   Utilize `defer-table-indexes` a opção na misqlpump ao despejar bases de dados, para que a criação de índices ocorra após a carga dos dados das tabelas.
-   Utilize `skip-definer` a opção na misqlpump para omitir as cláusulas de definidor e SEGURANÇA SQL a partir da criação de declarações para visualizações e procedimentos armazenados.  Quando recarrega o ficheiro de despejo, cria objetos que utilizam os valores padrão definer e segurança SQL.
-   Copie os ficheiros de backup para uma bolha/loja Azure e execute o restauro a partir daí, que deve ser muito mais rápido do que realizar o restauro através da Internet.

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Criar um ficheiro de backup a partir da linha de comando usando mysqldump
Para fazer o apoio a uma base de dados MySQL existente no servidor local ou numa máquina virtual, execute o seguinte comando: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Os parâmetros a fornecer são:
- [uname] Nome de utilizador da sua base de dados 
- [passe] A palavra-passe para a sua base de dados (note que não há espaço entre -p e a palavra-passe) 
- [nome dbname] O nome da sua base de dados 
- [backupfile.sql] O nome de ficheiro para a sua cópia de segurança da base de dados 
- [--opt] A opção mysqldump 

Por exemplo, para fazer o backup a uma base de dados denominada 'testdb' no seu servidor MySQL com o nome de utilizador 'testuser' e sem palavra-passe para um ficheiro testdb_backup.sql, utilize o seguinte comando. O comando recoloca `testdb` a base `testdb_backup.sql`de dados num ficheiro chamado , que contém todas as declarações SQL necessárias para recriar a base de dados. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Para selecionar tabelas específicas na sua base de dados para fazer o seu trabalho de fundo, enumere os nomes de tabelas separados por espaços. Por exemplo, para fazer o apoio apenas à tabela 1 e às tabelas 2 do 'testdb', siga este exemplo: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Para fazer um backup mais de uma base de dados ao mesmo tempo, utilize o interruptor --base de dados e enumere os nomes da base de dados separados por espaços. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Criar uma base de dados sobre o target Azure Database para o servidor MySQL
Crie uma base de dados vazia na base de dados azure-alvo para o servidor MySQL onde pretende migrar os dados. Utilize uma ferramenta como a bancada MySQL para criar a base de dados. A base de dados pode ter o mesmo nome que a base de dados que contém os dados despejados ou pode criar uma base de dados com um nome diferente.

Para se ligar, localize as informações de ligação na **visão geral** da sua Base de Dados Azure para MySQL.

![Encontre as informações de ligação no portal Azure](./media/concepts-migrate-dump-restore/1_server-overview-name-login.png)

Adicione as informações de ligação na sua bancada de trabalho MySQL.

![Cadeia de conexão de bancada de trabalho MySQL](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Restaurar a sua base de dados MySQL utilizando a linha de comando ou a bancada de trabalho MySQL
Uma vez criado a base de dados de destino, pode utilizar o comando mysql ou a bancada mySQL workbench para restaurar os dados na base de dados específica recém-criada a partir do ficheiro de despejo.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Neste exemplo, restaure os dados na base de dados recém-criada na base de dados azure alvo para o servidor MySQL.
```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportação usando PHPMyAdmin
Para exportar, pode utilizar a ferramenta comum phpMyAdmin, que já pode ter instalado localmente no seu ambiente. Para exportar a sua base de dados MySQL utilizando PHPMyAdmin:
1. Abra a phpMyAdmin.
2. Selecione a sua base de dados. Clique no nome da base de dados na lista à esquerda. 
3. Clique no link **Export.** Uma nova página parece ver o despejo da base de dados.
4. Na área de Exportação, clique no link **Select All** para escolher as tabelas na sua base de dados. 
5. Na área de opções SQL, clique nas opções apropriadas. 
6. Clique no **Save como** opção de ficheiro e na opção de compressão correspondente e, em seguida, clique no botão **Go.** Deve aparecer uma caixa de diálogo que o leva a guardar o ficheiro localmente.

## <a name="import-using-phpmyadmin"></a>Importação com PHPMyAdmin
Importar a sua base de dados é semelhante à exportação. Faça as seguintes ações:
1. Abra a phpMyAdmin. 
2. Na página de configuração phpMyAdmin, clique em **Adicionar** para adicionar a sua Base de Dados Azure para o servidor MySQL. Forneça os detalhes da ligação e informações de login.
3. Crie uma base de dados devidamente nomeada e selecione-a à esquerda do ecrã. Para reescrever a base de dados existente, clique no nome da base de dados, selecione todas as caixas de verificação ao lado dos nomes das tabelas e selecione **Drop** para eliminar as tabelas existentes. 
4. Clique no link **SQL** para mostrar a página onde pode escrever em comandos SQL ou fazer upload do seu ficheiro SQL. 
5. Utilize o botão **de navegação** para encontrar o ficheiro base de dados. 
6. Clique no botão **Go** para exportar a cópia de segurança, execute os comandos SQL e recrie a sua base de dados.

## <a name="next-steps"></a>Passos seguintes
- [Ligue aplicações à Base de Dados Azure para MySQL](./howto-connection-string.md).
- Para mais informações sobre bases de dados migratórias para a Base de Dados Azure para MySQL, consulte o Guia de [Migração](https://aka.ms/datamigration)da Base de Dados .
