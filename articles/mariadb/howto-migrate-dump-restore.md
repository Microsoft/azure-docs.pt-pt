---
title: Migrar seu banco de dados MariaDB usando despejo e restauração no banco de dados do Azure para MariaDB
description: Este artigo explica duas maneiras comuns de fazer backup e restaurar bancos de dados em seu Azure Database para MariaDB, usando ferramentas como mysqldump, MySQL Workbench e PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 05626535a2ab2d8da29b8c817ebfe84c257c76aa
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845056"
---
# <a name="migrate-your-mariadb-database-to-azure-database-for-mariadb-using-dump-and-restore"></a>Migrar seu banco de dados MariaDB para o banco de dados do Azure para MariaDB usando despejo e restauração
Este artigo explica duas maneiras comuns de fazer backup e restaurar bancos de dados no banco de dados do Azure para MariaDB
- Despejar e restaurar da linha de comando (usando mysqldump) 
- Despejar e restaurar usando PHPMyAdmin

## <a name="before-you-begin"></a>Antes de começar
Para percorrer este guia de instruções, você precisa ter:
- [Criar banco de dados do Azure para MariaDB Server-portal do Azure](quickstart-create-mariadb-server-database-using-azure-portal.md)
- utilitário de linha de comando [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) instalado em um computador.
- MySQL Workbench [Download](https://dev.mysql.com/downloads/workbench/)do MySQL Workbench, Toad, Navicat ou outra ferramenta de MySQL de terceiros para executar comandos de despejo e restauração.

## <a name="use-common-tools"></a>Usar ferramentas comuns
Use ferramentas e utilitários comuns, como MySQL Workbench, mysqldump, Toad ou Navicat para conectar-se remotamente e restaurar dados para o Azure Database para MariaDB. Use essas ferramentas no computador cliente com uma conexão com a Internet para se conectar ao banco de dados do Azure para MariaDB. Use uma conexão criptografada SSL para obter melhores práticas de segurança, consulte também [Configurar a conectividade SSL no banco de dados do Azure para MariaDB](concepts-ssl-connection-security.md). Você não precisa mover os arquivos de despejo para qualquer local de nuvem especial ao migrar para o banco de dados do Azure para MariaDB. 

## <a name="common-uses-for-dump-and-restore"></a>Usos comuns para despejo e restauração
Você pode usar os utilitários do MySQL, como mysqldump e mysqlpump, para despejar e carregar bancos de dados em um Azure database for MariaDB Server em vários cenários comuns. 

<!--In other scenarios, you may use the [Import and Export](howto-migrate-import-export.md) approach instead.-->

- Use despejos de banco de dados quando estiver migrando todo o banco de dados. Essa recomendação é mantida ao mover uma grande quantidade de dados ou quando você deseja minimizar a interrupção do serviço para sites ou aplicativos dinâmicos. 
-  Confirme que todas as tabelas na base de dados utilizam o motor de armazenamento InnoDB ao carregar os dados no Azure Database for MariaDB. O banco de dados do Azure para MariaDB dá suporte apenas ao mecanismo de armazenamento InnoDB e, portanto, não oferece suporte a mecanismos de armazenamento alternativos. Se as tabelas estiverem configuradas com outros mecanismos de armazenamento, converta-as no formato de mecanismo InnoDB antes de migrar para o banco de dados do Azure para MariaDB.
   Por exemplo, se você tiver um WordPress ou WebApp usando as tabelas MyISAM, primeiro converta essas tabelas migrando para o formato InnoDB antes de restaurar para o banco de dados do Azure para MariaDB. Use a cláusula `ENGINE=InnoDB` para definir o mecanismo usado ao criar uma nova tabela e, em seguida, transfira os dados para a tabela compatível antes da restauração. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Para evitar problemas de compatibilidade, confirme que utiliza a mesma versão do MariaDB nos sistemas de origem e de destino ao capturar as bases de dados. Por exemplo, se o servidor MariaDB existente for da versão 10,2, você deverá migrar para o banco de dados do Azure para MariaDB configurado para executar a versão 10,2. O `mysql_upgrade` comando não funciona em um banco de dados do Azure para o servidor MariaDB e não tem suporte. Se você precisar atualizar em versões do MariaDB, primeiro despeje ou exporte seu banco de dados de versão inferior para uma versão mais recente do MariaDB em seu próprio ambiente. Em seguida `mysql_upgrade`, execute, antes de tentar a migração em um banco de dados do Azure para MariaDB.

## <a name="performance-considerations"></a>Considerações de desempenho
Para otimizar o desempenho, tome nota dessas considerações ao despejar grandes bancos de dados:
-   Use a `exclude-triggers` opção em mysqldump ao despejar bancos de dados. Exclua gatilhos de arquivos de despejo para evitar o acionamento de comandos de gatilho durante a restauração de dados. 
-   Use a `single-transaction` opção para definir o modo de isolamento da transação para leitura repetida e enviar uma instrução SQL de iniciar transação para o servidor antes de despejar dados. Despejar muitas tabelas em uma única transação faz com que algum armazenamento extra seja consumido durante a restauração. A `single-transaction` opção e a `lock-tables` opção são mutuamente exclusivas porque as tabelas de bloqueio fazem com que todas as transações pendentes sejam confirmadas implicitamente. Para despejar tabelas grandes, combine `single-transaction` a opção com `quick` a opção. 
-   Use a `extended-insert` sintaxe de várias linhas que inclui várias listas de valores. Isso resulta em um arquivo de despejo menor e acelera as inserções quando o arquivo é recarregado.
-  Use a `order-by-primary` opção em mysqldump ao despejar bancos de dados, para que eles sejam inseridos em script na ordem de chave primária.
-   Use a `disable-keys` opção em mysqldump ao despejar dados, para desabilitar as restrições de chave estrangeira antes do carregamento. A desabilitação das verificações de chave estrangeira fornece ganhos de desempenho. Habilite as restrições e verifique os dados após a carga para garantir a integridade referencial.
-   Use tabelas particionadas quando apropriado.
-   Carregar dados em paralelo. Evite muito paralelismo que faria com que você atingisse um limite de recursos e monitorasse os recursos usando as métricas disponíveis no portal do Azure. 
-   Use a `defer-table-indexes` opção em mysqlpump ao despejar bancos de dados, para que a criação do índice ocorra após o carregamento das tabelas.
-   Copie os arquivos de backup para um repositório/BLOB do Azure e execute a restauração a partir daí, o que deve ser muito mais rápido do que executar a restauração pela Internet.

## <a name="create-a-backup-file"></a>Criar um arquivo de backup
Para fazer backup de um banco de dados MariaDB existente no servidor local ou em uma máquina virtual, execute o seguinte comando usando mysqldump: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Os parâmetros a serem fornecidos são:
- uname Seu nome de usuário do banco de dados 
- passá A senha do banco de dados (Observe que não há espaço entre-p e a senha) 
- NomeDoBancoDeDados O nome do seu banco de dados 
- [backupfile. SQL] o nome do arquivo para o backup do banco de dados 
- [--aceitar] A opção mysqldump 

Por exemplo, para fazer backup de um banco de dados denominado ' TestDB ' em seu servidor MariaDB com o nome de usuário ' testuser ' e sem senha para um arquivo testdb_backup. SQL, use o comando a seguir. O comando faz backup do `testdb` banco de dados em um `testdb_backup.sql`arquivo chamado, que contém todas as instruções SQL necessárias para recriar o banco de dados. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Para selecionar tabelas específicas no banco de dados para backup, liste os nomes de tabela separados por espaços. Por exemplo, para fazer backup somente das tabelas Table1 e Table2 do ' TestDB ', siga este exemplo: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Para fazer backup de mais de um banco de dados de uma só vez, use a opção--Database e liste os nomes de banco de dados separados por espaços. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Criar um banco de dados no servidor de destino
Crie um banco de dados vazio no banco de dados do Azure de destino para o servidor MariaDB em que você deseja migrar os dados. Use uma ferramenta como MySQL Workbench, Toad ou Navicat para criar o banco de dados. O banco de dados pode ter o mesmo nome que o banco de dado que está continha o despejado ou você pode criar um banco de dados com um nome diferente.

Para se conectar, localize as informações de conexão na **visão geral** do banco de dados do Azure para MariaDB.

![Localizar as informações de conexão no portal do Azure](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

Adicione as informações de conexão ao seu MySQL Workbench.

![Cadeia de conexão do MySQL Workbench](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Restaurar o banco de dados MariaDB
Depois de criar o banco de dados de destino, você pode usar o comando MySQL ou o MySQL Workbench para restaurar os dados para o banco de dado criado recentemente do arquivo de despejo.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Neste exemplo, restaure os dados no banco de dados recém-criado no banco de dados do Azure de destino para o servidor MariaDB.
```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportar usando PHPMyAdmin
Para exportar, você pode usar a ferramenta comum phpMyAdmin, que você pode já ter instalado localmente em seu ambiente. Para exportar seu banco de dados MariaDB usando PHPMyAdmin:
1. Abra phpMyAdmin.
2. Selecione seu banco de dados. Clique no nome do banco de dados na lista à esquerda. 
3. Clique no link **Exportar** . Uma nova página é exibida para exibir o despejo do banco de dados.
4. Na área exportar, clique no link **selecionar tudo** para escolher as tabelas em seu banco de dados. 
5. Na área opções SQL, clique nas opções apropriadas. 
6. Clique na opção **salvar como arquivo** e na opção de compactação correspondente e, em seguida, clique no botão **ir** . Uma caixa de diálogo deve aparecer solicitando que você salve o arquivo localmente.

## <a name="import-using-phpmyadmin"></a>Importar usando PHPMyAdmin
Importar seu banco de dados é semelhante à exportação. Execute as seguintes ações:
1. Abra phpMyAdmin. 
2. Na página configuração do phpMyAdmin, clique em **Adicionar** para adicionar o banco de dados do Azure para o servidor MariaDB. Forneça os detalhes da conexão e as informações de logon.
3. Crie um banco de dados nomeado adequadamente e selecione-o à esquerda da tela. Para reescrever o banco de dados existente, clique no nome do banco de dados, marque todas as caixas de seleção ao lado dos nomes de tabela e selecione **descartar** para excluir as tabelas existentes. 
4. Clique no link **SQL** para mostrar a página onde você pode digitar comandos SQL ou carregar o arquivo SQL. 
5. Use o botão **procurar** para localizar o arquivo de banco de dados. 
6. Clique no botão **ir** para exportar o backup, execute os comandos SQL e recrie o banco de dados.

## <a name="next-steps"></a>Passos Seguintes
- [Conectar aplicativos ao banco de dados do Azure para MariaDB](./howto-connection-string.md).
 
<!--
- For more information about migrating databases to Azure Database for MariaDB, see the [Database Migration Guide](https://aka.ms/datamigration).
-->
