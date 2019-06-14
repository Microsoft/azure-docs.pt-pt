---
title: Migrar a base de dados MySQL com a informação e restaurar na base de dados do Azure para MySQL
description: Este artigo explica as duas formas comuns de cópia de segurança e restaurar bases de dados na sua base de dados do Azure para MySQL, usando ferramentas como mysqldump, o MySQL Workbench e PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/02/2018
ms.openlocfilehash: e79c83ecb17c4dcd11f7ccbecded59e7d1d13dfd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60525785"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migrar a sua base de dados do MySQL para a base de dados do Azure para MySQL através de captura e restauro
Este artigo explica as duas formas comuns de cópia de segurança e restaurar bases de dados na sua base de dados do Azure para MySQL
- Captura e restauro a partir da linha de comandos (utilizar mysqldump) 
- Cópia de segurança e restaurar com PHPMyAdmin 

## <a name="before-you-begin"></a>Antes de começar
Para seguir este guia de procedimentos, tem de ter:
- [Criar base de dados do Azure para o servidor MySQL - portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) instalado numa máquina o utilitário da linha de comandos.
- MySQL Workbench [transferir o MySQL Workbench](https://dev.mysql.com/downloads/workbench/), Toad, Navicat ou outra ferramenta de MySQL de terceiros para cópia de segurança e restaurar os comandos.

## <a name="use-common-tools"></a>Utilize ferramentas comuns
Utilize os utilitários e ferramentas como o MySQL Workbench, mysqldump, Toad ou Navicat comuns para remotamente se ligar e restaurar os dados na base de dados do Azure para MySQL. Use tais ferramentas no seu computador cliente com uma ligação à internet para ligar à base de dados do Azure para MySQL. Utilizar uma ligação encriptada por SSL para melhores práticas de segurança, consulte também [configurar a conectividade SSL na base de dados do Azure para MySQL](concepts-ssl-connection-security.md). Não é necessário mover os arquivos de despejo para qualquer localização de cloud especiais ao migrar a base de dados do Azure para MySQL. 

## <a name="common-uses-for-dump-and-restore"></a>Utilizações comuns para a captura e restauro
Pode utilizar utilitários de MySQL como mysqldump e mysqlpump dump e load bancos de dados numa base de dados do MySQL do Azure em vários cenários comuns. Em outros casos, pode utilizar o [importar e exportar](concepts-migrate-import-export.md) abordar em vez disso.

- Base de dados de utilização despeja quando estiver a migrar a base de dados. Esta recomendação contém ao mover uma grande quantidade de dados do MySQL, ou quando deseja minimizar a interrupção do serviço de aplicações ou sites ao vivo. 
-  Certifique-se que todas as tabelas na base de dados utilizam o mecanismo de armazenamento InnoDB ao carregar os dados na base de dados do Azure para MySQL. Base de dados do Azure para MySQL suporta apenas mecanismo de armazenamento InnoDB e, portanto, não suporta os mecanismos de armazenamento alternativo. Se as suas tabelas estão configuradas com outros mecanismos de armazenamento, convertê-los para o formato do motor InnoDB antes da migração para a base de dados do Azure para MySQL.
   Por exemplo, se tiver um WordPress ou WebApp usando as tabelas MyISAM, comece por converter essas tabelas ao migrar para o formato de InnoDB antes de restaurar a base de dados do Azure para MySQL. Utilize a cláusula `ENGINE=InnoDB` para definir o mecanismo usado ao criar uma nova tabela, em seguida, transferir dados para a tabela compatível antes do restauro. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Para evitar problemas de compatibilidade, certifique-se de que a mesma versão do MySQL é utilizada nos sistemas de origem e de destino ao capturar a bases de dados. Por exemplo, se o servidor MySQL existente é a versão 5.7, em seguida, deve migrar a base de dados do Azure para MySQL configurado para executar a versão 5.7. O `mysql_upgrade` comando não funcionará num banco de dados do Azure para o servidor MySQL e não é suportado. Se precisar de atualizar entre versões do MySQL, primeiro a cópia de segurança ou exportar a sua base de dados de versão inferior para uma versão superior do MySQL em seu próprio ambiente. Em seguida, execute `mysql_upgrade`, antes de tentar uma migração para uma base de dados do Azure para MySQL.

## <a name="performance-considerations"></a>Considerações de desempenho
Para otimizar o desempenho, observar a estas considerações ao capturar a grandes bancos de dados:
-   Utilize o `exclude-triggers` opção na mysqldump ao capturar a bases de dados. Exclua os acionadores de arquivos de despejo para evitar os comandos de Acionador disparando durante a restauração de dados. 
-   Utilize o `single-transaction` a opção de definir o modo de isolamento de transação para ler REPETÍVEIS e envia uma instrução SQL de início de transação para o servidor antes de capturar a dados. Despejar muitas tabelas numa única transação faz com que algum armazenamento extra a ser consumida durante o restauro. O `single-transaction` opção e a `lock-tables` opção são mutuamente exclusivos, porque as tabelas de bloqueio faz com que todas as pendentes transações ser confirmada implicitamente. Para Despejar tabelas grandes, combinar a `single-transaction` opção com o `quick` opção. 
-   Utilize o `extended-insert` sintaxe de linha de vários que inclui várias listas de valor. Isso resulta num arquivo de despejo menor e acelera inserções, quando o ficheiro é recarregado.
-  Utilize o `order-by-primary` opção na mysqldump ao capturar a bases de dados, para que os dados são programados em ordem de chave primária.
-   Utilize o `disable-keys` opção na mysqldump ao capturar a dados, para desabilitar as restrições de chave estrangeira antes de carga. Desabilitar as verificações de chave estrangeiras fornece ganhos de desempenho. Ative as restrições e verifique se os dados após o carregamento para garantir a integridade referencial.
-   Utilize as tabelas particionadas, quando apropriado.
-   Carregar dados em paralelo. Evite muito paralelismo que seria fazer com que atingir um limite de recursos e monitorizar os recursos com as métricas disponíveis no portal do Azure. 
-   Utilize o `defer-table-indexes` opção na mysqlpump ao capturar a bases de dados, para que a criação de índices acontece depois dos dados de tabelas são carregados.
-   Copie os ficheiros de cópia de segurança para um blob do Azure/armazenamento e efetuar o restauro a partir daí, o que deve ser muito mais rápido do que efetuar o restauro na Internet.

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Criar um ficheiro de cópia de segurança a partir da linha de comandos a utilizar mysqldump
Para criar cópias de segurança uma base de dados MySQL no servidor no local ou numa máquina virtual, execute o seguinte comando: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Os parâmetros para fornecer são:
- [uname] O nome de utilizador de base de dados 
- [pass] A palavra-passe para a base de dados (Observe que não há espaço entre -p e a palavra-passe) 
- [dbname] O nome da base de dados 
- [backupfile.sql] o nome de ficheiro para a cópia de segurança da base de dados 
- [-optar ativamente por participar] A opção de mysqldump 

Por exemplo, para fazer backup de uma base de dados com o nome testdb no seu servidor MySQL com o nome de usuário "testuser" e com nenhuma palavra-passe para um ficheiro testdb_backup.sql, utilize o seguinte comando. O comando cria cópias de segurança do `testdb` base de dados para um arquivo chamado `testdb_backup.sql`, que contém todas as instruções SQL necessárias para voltar a criar a base de dados. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Para selecionar tabelas específicas na base de dados para criar cópias de segurança, liste os nomes de tabela separados por espaços. Por exemplo, a cópia de segurança apenas tabelas de tabela1 e tabela2 do "testdb", siga este exemplo: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Para criar cópias de segurança da base de dados de mais do que uma vez, utilize o-- base de dados mudar e listar os nomes de base de dados separados por espaços. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
Para fazer backup de todos os bancos de dados no servidor de uma só vez, deve usar a opção-- all-bases de dados.
```bash
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Criar uma base de dados no banco de dados do Azure de destino para o servidor MySQL
Crie uma base de dados vazia no banco de dados do Azure de destino para o servidor MySQL onde pretende migrar os dados. Utilize uma ferramenta como o MySQL Workbench, Toad ou Navicat para criar a base de dados. A base de dados pode ter o mesmo nome, como a base de dados que está contido dados capturados ou pode criar uma base de dados com um nome diferente.

Para entrar em contato, localize as informações de ligação no **descrição geral** da base de dados do Azure para MySQL.

![Localizar as informações de ligação no portal do Azure](./media/concepts-migrate-dump-restore/1_server-overview-name-login.png)

Adicione as informações de ligação para o MySQL Workbench.

![Cadeia de ligação do MySQL Workbench](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Restaurar a base de dados MySQL através da linha de comandos ou o MySQL Workbench
Depois de criar a base de dados de destino, pode utilizar o comando de mysql ou o MySQL Workbench para restaurar os dados para as específicas recentemente criado da base de dados a partir do arquivo de despejo.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Neste exemplo, restaure os dados na base de dados recentemente criada a base de dados do Azure de destino para o servidor MySQL.
```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportar com PHPMyAdmin
Para exportar, pode usar o phpMyAdmin ferramenta comum, que pode já ter instalado localmente no seu ambiente. Para exportar a base de dados MySQL através de PHPMyAdmin:
1. Abra phpMyAdmin.
2. Selecione a sua base de dados. Clique no nome de base de dados na lista à esquerda. 
3. Clique nas **exportar** ligação. É apresentada uma nova página Ver o despejo de base de dados.
4. Na área de exportação, clique nas **Selecionar tudo** ligação para escolher as tabelas na base de dados. 
5. Na área de opções de SQL, clique nas opções apropriadas. 
6. Clique nas **Salvar como arquivo** opção e a compactação correspondente de opção e, em seguida, clique no **vá** botão. Uma caixa de diálogo deve aparecer solicitando a guardar o ficheiro localmente.

## <a name="import-using-phpmyadmin"></a>Importar com PHPMyAdmin
A importação de sua base de dados é semelhante à exportação. Efetue as seguintes ações:
1. Abra phpMyAdmin. 
2. Na página de configuração de phpMyAdmin, clique em **adicionar** para adicionar a sua base de dados do Azure para o servidor MySQL. Forneça os detalhes da ligação e as informações de início de sessão.
3. Criar uma base de dados nomeado adequadamente e selecione-a no lado esquerdo do ecrã. Reescrever a base de dados existente, clique no nome de base de dados, selecione todas as caixas de verificação ao lado dos nomes de tabela e selecione **Drop** para eliminar as tabelas existentes. 
4. Clique nas **SQL** ligação para apresentar a página onde pode introduzir os comandos SQL ou carregar o ficheiro SQL. 
5. Utilize o **procurar** botão para encontrar o ficheiro de base de dados. 
6. Clique nas **ir** botão para exportar a cópia de segurança, execute os comandos SQL e voltar a criar a base de dados.

## <a name="next-steps"></a>Passos Seguintes
- [Ligar aplicações à base de dados do Azure para MySQL](./howto-connection-string.md).
- Para obter mais informações sobre como migrar bases de dados para a base de dados do Azure para MySQL, veja a [guia de migração de bases de dados](https://aka.ms/datamigration).
