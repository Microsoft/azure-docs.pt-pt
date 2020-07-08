---
title: Migrar usando despejo e restauro - Azure Database for MySQL
description: Este artigo explica duas formas comuns de fazer o back backs de apoio e restaurar as bases de dados na sua Base de Dados Azure para o MySQL, utilizando ferramentas como mysqldump, MySQL Workbench e PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: c30faa31f6f733f80d4bfd5184c09d9fdbd6f389
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85971186"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migrar a sua base de dados MySQL para a Dase de Dados do Azure para MySQL através da funcionalidade de captura e restauro
Este artigo explica duas formas comuns de fazer o back backs de apoio e restaurar as bases de dados na sua Base de Dados Azure para o MySQL
- Despejar e restaurar a partir da linha de comando (usando mysqldump) 
- Despejar e restaurar usando PHPMyAdmin 

## <a name="before-you-begin"></a>Before you begin
Para passar por este guia, você precisa ter:
- [Criar Base de Dados Azure para servidor MySQL - Portal Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- utilitário de linha de comando [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) instalado numa máquina.
- MySQL Workbench [MySQL Workbench Download](https://dev.mysql.com/downloads/workbench/) ou outra ferramenta mySQL de terceiros para fazer despesar e restaurar comandos.

Se procura migrar grandes bases de dados com tamanhos de base de dados superiores a 1 TBs, talvez deva considerar a utilização de ferramentas comunitárias como o mydumper/myloader que suporta a exportação e importação paralelas. O despejo paralelo e a restauração podem ajudar a reduzir significativamente o tempo de migração para grandes bases de dados. Pode consultar o nosso [blog techcommunity](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699) para as melhores práticas para migrar grandes bases de dados para a Azure Database para o serviço MySQL utilizando ferramentas mydumper/myloader.

## <a name="use-common-tools"></a>Use ferramentas comuns
Utilize utilitários e ferramentas comuns como mySQL Workbench ou mysqldump para ligar e restaurar remotamente dados em Azure Database para MySQL. Utilize estas ferramentas na sua máquina cliente com uma ligação à Internet para ligar à Base de Dados Azure para o MySQL. Utilize uma ligação encriptada SSL para as melhores práticas de segurança, consulte também a [conectividade SSL configurada na Base de Dados Azure para o MySQL](concepts-ssl-connection-security.md). Não precisa de mover os ficheiros de despejo para qualquer local especial na nuvem ao migrar para a Base de Dados Azure para o MySQL. 

## <a name="common-uses-for-dump-and-restore"></a>Usos comuns para despejo e restauro
Pode utilizar utilitários MySQL como mysqldump e mysqlpump para despejar e carregar bases de dados numa Base de Dados Azure MySQL em vários cenários comuns. Noutros cenários, poderá utilizar a abordagem [de Importação e Exportação.](concepts-migrate-import-export.md)

- Use depósitos de base de dados quando estiver a migrar toda a base de dados. Esta recomendação mantém-se ao mover uma grande quantidade de dados do MySQL, ou quando pretende minimizar a interrupção do serviço para sites ou aplicações ao vivo. 
-  Confirme que todas as tabelas na base de dados utilizam o motor de armazenamento InnoDB ao carregar os dados para a Base de Dados do Azure para MySQL. A Azure Database for MySQL suporta apenas o motor de armazenamento InnoDB e, portanto, não suporta motores de armazenamento alternativos. Se as suas tabelas estiverem configuradas com outros motores de armazenamento, converta-as no formato do motor InnoDB antes da migração para Azure Database para o MySQL.
   Por exemplo, se tiver um WordPress ou WebApp utilizando as tabelas MyISAM, primeiro converta essas tabelas migrando para o formato InnoDB antes de restaurar a Base de Dados Azure para o MySQL. Utilize a cláusula `ENGINE=InnoDB` para definir o motor utilizado ao criar uma nova tabela e, em seguida, transfira os dados para a tabela compatível antes da restauração. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Para evitar problemas de compatibilidade, confirme que utiliza a mesma versão do MySQL nos sistemas de origem e de destino ao capturar as bases de dados. Por exemplo, se o seu servidor MySQL existente for a versão 5.7, então deve migrar para a Base de Dados Azure para o MySQL configurado para executar a versão 5.7. O `mysql_upgrade` comando não funciona numa Base de Dados Azure para o servidor MySQL e não é suportado. Se precisar de fazer upgrade através das versões MySQL, primeiro despeje ou exporte a sua base de dados de versão inferior para uma versão mais alta do MySQL no seu próprio ambiente. Em `mysql_upgrade` seguida, corra , antes de tentar migrar para uma base de dados Azure para MySQL.

## <a name="performance-considerations"></a>Considerações de desempenho
Para otimizar o desempenho, tome conhecimento destas considerações ao despejar grandes bases de dados:
-   Utilize a `exclude-triggers` opção no mysqldump ao despejar bases de dados. Exclua os gatilhos dos ficheiros de despejo para evitar que os comandos do gatilho disparem durante a restauração dos dados. 
-   Utilize a `single-transaction` opção para definir o modo de isolamento de transação para REPETível READ e envie uma declaração DE SQL DE START TRANSACTION para o servidor antes de despejar dados. Despejar muitas tabelas numa única transação faz com que algum armazenamento extra seja consumido durante a restauração. A `single-transaction` opção e a `lock-tables` opção são mutuamente exclusivas porque lock tables faz com que quaisquer transações pendentes sejam cometidas implicitamente. Para despejar mesas grandes, combine a `single-transaction` opção com a `quick` opção. 
-   Utilize a `extended-insert` sintaxe de várias linhas que inclui várias listas DE VALOR. Isto resulta num ficheiro de despejo menor e acelera as inserções quando o ficheiro é recarregado.
-  Utilize a `order-by-primary` opção no mysqldump ao despejar bases de dados, de modo a que os dados são descritos na ordem principal.
-   Utilize a `disable-keys` opção no mysqldump ao despejar dados, para desativar as restrições de chaves estrangeiras antes da carga. A desativação de verificações de chaves estrangeiras proporciona ganhos de desempenho. Ative os constrangimentos e verifique os dados após a carga para garantir a integridade referencial.
-   Utilize mesas divisórias quando apropriado.
-   Carregue os dados em paralelo. Evite demasiado paralelismo que o faria atingir um limite de recursos e monitorizar os recursos utilizando as métricas disponíveis no portal Azure. 
-   Utilize a `defer-table-indexes` opção no mysqlpump ao despejar bases de dados, de modo que a criação de índices acontece após o envio de dados das tabelas.
-   Utilize a `skip-definer` opção no mysqlpump para omitir cláusulas de definição e SQL SECURITY a partir da criação de declarações para visualizações e procedimentos armazenados.  Quando recarrega o ficheiro de despejo, cria objetos que utilizam os valores de DEFINIÇÃO PADRÃO e SQL SECURITY.
-   Copie os ficheiros de cópias de segurança para uma bolha/loja Azure e execute a restauração a partir daí, o que deve ser muito mais rápido do que executar o restauro através da Internet.

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Criar um ficheiro de backup a partir da linha de comando usando mysqldump
Para fazer o back up de uma base de dados MySQL existente no servidor local ou numa máquina virtual, execute o seguinte comando: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Os parâmetros a fornecer são:
- [uname] Nome de utilizador da sua base de dados 
- [passe] A palavra-passe da sua base de dados (note que não há espaço entre -p e a palavra-passe) 
- [dbname] O nome da sua base de dados 
- [backupfile.sql] O nome de ficheiro para a sua base de dados 
- [--opt] A opção mysqldump 

Por exemplo, para fazer o back-up de uma base de dados chamada 'testdb' no seu servidor MySQL com o nome de utilizador 'testuser' e sem palavra-passe para um ficheiro testdb_backup.sql, utilize o seguinte comando. O comando reensi para `testdb` um ficheiro chamado , que contém todas as `testdb_backup.sql` declarações SQL necessárias para recriar a base de dados. Certifique-se de que o nome de utilizador 'testuser' tem pelo menos o privilégio SELECT para mesas de despejo, SHOW VIEW para vistas despejadas, TRIGGER para gatilhos despejados e TABELAS DE BLOQUEIO se a opção de transação única não for utilizada.

```bash
GRANT SELECT, LOCK TABLES, SHOW VIEW ON *.* TO 'testuser'@'hostname' IDENTIFIED BY 'password';
```

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Para selecionar tabelas específicas na sua base de dados para fazer o back up, liste os nomes das tabelas separados por espaços. Por exemplo, para apoiar apenas as tabelas1 e as tabelas2 do 'testdb', siga este exemplo: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Para fazer o back up mais de uma base de dados ao mesmo tempo, use o interruptor de base de dados e liste os nomes da base de dados separados por espaços. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Criar uma base de dados na base de dados Azure alvo para servidor MySQL
Crie uma base de dados vazia na base de dados Azure target para o servidor MySQL onde pretende migrar os dados. Utilize uma ferramenta como a bancada MySQL Workbench para criar a base de dados. A base de dados pode ter o mesmo nome que a base de dados que contém os dados despejados ou pode criar uma base de dados com um nome diferente.

Para se conectar, localize as informações de ligação na **visão geral** da sua Base de Dados Azure para o MySQL.

![Encontre as informações de ligação no portal Azure](./media/concepts-migrate-dump-restore/1_server-overview-name-login.png)

Adicione as informações de ligação na sua bancada MySQL Workbench.

![Cadeia de conexão mysql workbench](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)

## <a name="preparing-the-target-azure-database-for-mysql-server-for-fast-data-loads"></a>Preparar a base de dados Azure alvo para o servidor MySQL para cargas de dados rápidas
Para preparar o target Azure Database para o servidor MySQL para cargas de dados mais rápidas, os seguintes parâmetros e configuração do servidor precisam de ser alterados.
- max_allowed_packet – definido para 1073741824 (ou seja, 1GB) para evitar qualquer problema de transbordo devido a longas filas.
- slow_query_log – definido para OFF para desligar o registo de consulta lenta. Isto eliminará a sobrecarga causada por uma consulta lenta durante as cargas de dados.
- query_store_capture_mode – definido para ZERO para desligar a Loja de Consultas. Isto eliminará as despesas gerais causadas pelas atividades de amostragem pela Query Store.
- innodb_buffer_pool_size – Escalar o servidor para 32 vCore Memory Optimized SKU do nível de Preços do portal durante a migração para aumentar o innodb_buffer_pool_size. Innodb_buffer_pool_size só podem ser aumentados aumentando o cálculo para a Base de Dados Azure para o servidor MySQL.
- innodb_io_capacity & innodb_io_capacity_max - Altere para 9000 a partir dos parâmetros do Servidor no portal Azure para melhorar a utilização do IO para otimizar a velocidade de migração.
- innodb_write_io_threads & innodb_write_io_threads - Altere para 4 a partir dos parâmetros do Servidor no portal Azure para melhorar a velocidade de migração.
- Escalar o nível de armazenamento – Os IOPs para Azure Database para o servidor MySQL aumentam progressivamente com o aumento do nível de armazenamento. Para cargas mais rápidas, pode querer aumentar o nível de armazenamento para aumentar os IOPs a provisionados. Por favor, lembre-se que o armazenamento só pode ser aumentado, não para baixo.

Uma vez concluída a migração, pode reverter os parâmetros do servidor e calcular a configuração do nível para os valores anteriores. 

## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Restaurar a sua base de dados MySQL utilizando a linha de comando ou a bancada mySQL Workbench
Uma vez criado a base de dados-alvo, pode utilizar o comando mysql ou a bancada mySQL workbench para restaurar os dados na base de dados recentemente criada a partir do ficheiro de despejo.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Neste exemplo, restaurar os dados na base de dados recém-criada na base de dados target Azure para o servidor MySQL.
```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```
## <a name="export-using-phpmyadmin"></a>Exportação com PHPMyAdmin
Para exportar, pode utilizar a ferramenta comum phpMyAdmin, que já pode ter instalado localmente no seu ambiente. Para exportar a sua base de dados MySQL utilizando PHPMyAdmin:
1. Abra phpMyAdmin.
2. Selecione a sua base de dados. Clique no nome da base de dados na lista à esquerda. 
3. Clique no link **Exportação.** Uma nova página parece ver o despejo da base de dados.
4. Na área De Exportação, clique no link **Select All** para escolher as tabelas na sua base de dados. 
5. Na área de opções SQL, clique nas opções apropriadas. 
6. Clique na opção Guardar como opção **de ficheiro** e na opção de compressão correspondente e, em seguida, clique no botão **Go.** Deve aparecer uma caixa de diálogo que o leva a guardar o ficheiro localmente.

## <a name="import-using-phpmyadmin"></a>Importação usando PHPMyAdmin
Importar a sua base de dados é semelhante à exportação. Fazer as seguintes ações:
1. Abra phpMyAdmin. 
2. Na página de configuração phpMyAdmin, clique em **Adicionar** para adicionar a sua Base de Dados Azure para o servidor MySQL. Forneça os detalhes da ligação e informações de login.
3. Crie uma base de dados devidamente nomeada e selecione-a à esquerda do ecrã. Para reescrever a base de dados existente, clique no nome da base de dados, selecione todas as caixas de verificação ao lado dos nomes das tabelas e selecione **Drop** para eliminar as tabelas existentes. 
4. Clique no link **SQL** para mostrar a página onde pode escrever em comandos SQL ou fazer upload do seu ficheiro SQL. 
5. Utilize o botão de navegação para encontrar o ficheiro de base de **dados.** 
6. Clique no botão **Go** para exportar a cópia de segurança, execute os comandos SQL e recrie a sua base de dados.

## <a name="known-issues"></a>Problemas Conhecidos
Para questões conhecidas, dicas e truques, recomendamos que olhe para o nosso [blog techcommunity.](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/tips-and-tricks-in-using-mysqldump-and-mysql-restore-to-azure/ba-p/916912)

## <a name="next-steps"></a>Próximos passos
- [Conecte as aplicações à Base de Dados Azure para o MySQL](./howto-connection-string.md).
- Para obter mais informações sobre bases de dados migratórias para a Base de Dados Azure para o MySQL, consulte o [Guia de Migração da Base de Dados.](https://aka.ms/datamigration)
