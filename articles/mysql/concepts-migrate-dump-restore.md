---
title: Migrar usando despejo e restauro - Azure Database for MySQL
description: Este artigo explica duas formas comuns de fazer o back backs de apoio e restaurar as bases de dados na sua Base de Dados Azure para o MySQL, utilizando ferramentas como mysqldump, MySQL Workbench e PHPMyAdmin.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: f21587fe6a48d042ed98c126beb2a7dcaa39b7d8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94537922"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migrar a sua base de dados MySQL para a Dase de Dados do Azure para MySQL através da funcionalidade de captura e restauro

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Este artigo explica duas formas comuns de fazer o back backs de apoio e restaurar as bases de dados na sua Base de Dados Azure para o MySQL
- Despejar e restaurar a partir da linha de comando (usando mysqldump)
- Despejar e restaurar usando PHPMyAdmin

Também pode consultar o [Guia de Migração da Base de Dados](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) para obter informações detalhadas e utilizar casos sobre bases de dados migratórias para a Base de Dados Azure para o MySQL. Este guia fornece orientações que conduzirão ao planeamento e execução bem-sucedidos de uma migração MySQL para Azure.

## <a name="before-you-begin"></a>Antes de começar
Para passar por este guia, você precisa ter:
- [Criar Base de Dados Azure para servidor MySQL - Portal Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- utilitário de linha de comando [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) instalado numa máquina.
- [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) ou outra ferramenta MySQL de terceiros para fazer despejá-lo e restaurar comandos.

> [!TIP]
> Se procura migrar grandes bases de dados com tamanhos de base de dados superiores a 1 TBs, talvez deva considerar a utilização de ferramentas comunitárias como **o mydumper/myloader** que suporta a exportação e importação paralelas. Saiba [como migrar grandes bases de dados mySQL.](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699)


## <a name="common-use-cases-for-dump-and-restore"></a>Casos de utilização comuns para despejo e restauro

Os casos de utilização mais comuns são:

- **Deslocando-se de outro prestador de serviços gerido** - A maioria dos prestadores de serviços geridos não podem fornecer acesso ao ficheiro de armazenamento físico por razões de segurança, pelo que a cópia de segurança lógica e a restauração é a única opção para migrar.
- **Migrar do ambiente no local ou máquina virtual** - Azure Database for MySQL não suporta a restauração de backups físicos que fazem backups lógicos e restauram como a abordagem ÚNICA.
- **Mover o armazenamento de backup de armazenamento localmente redundante para armazenamento geo-redundante** - A Azure Database for MySQL permite configurar armazenamento localmente redundante ou geo-redundante para cópia de segurança só é permitido durante a criação do servidor. Assim que o servidor tiver sido aprovisionado, não poderá alterar a opção de redundância do armazenamento de cópias de segurança. Para mover o seu armazenamento de reserva de armazenamento local redundante para armazenamento geo-redundante, despejar e restaurar é a opção ÚNICA. 
-  **A migração de motores de armazenamento alternativos para InnoDB** - Azure Database para o MySQL suporta apenas o motor de armazenamento InnoDB, pelo que não suporta motores de armazenamento alternativos. Se as suas tabelas estiverem configuradas com outros motores de armazenamento, converta-as no formato do motor InnoDB antes da migração para Azure Database para o MySQL.

    Por exemplo, se tiver um WordPress ou WebApp utilizando as tabelas MyISAM, primeiro converta essas tabelas migrando para o formato InnoDB antes de restaurar a Base de Dados Azure para o MySQL. Utilize a cláusula `ENGINE=InnoDB` para definir o motor utilizado ao criar uma nova tabela e, em seguida, transfira os dados para a tabela compatível antes da restauração.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
> [!Important]
> - Para evitar problemas de compatibilidade, confirme que utiliza a mesma versão do MySQL nos sistemas de origem e de destino ao capturar as bases de dados. Por exemplo, se o seu servidor MySQL existente for a versão 5.7, então deve migrar para a Base de Dados Azure para o MySQL configurado para executar a versão 5.7. O `mysql_upgrade` comando não funciona numa Base de Dados Azure para o servidor MySQL e não é suportado. 
> - Se precisar de fazer upgrade através das versões MySQL, primeiro despeje ou exporte a sua base de dados de versão inferior para uma versão mais alta do MySQL no seu próprio ambiente. Em `mysql_upgrade` seguida, corra , antes de tentar migrar para uma base de dados Azure para MySQL.

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

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Criar uma base de dados na base de dados Azure alvo para servidor MySQL
Crie uma base de dados vazia na base de dados Azure target para o servidor MySQL onde pretende migrar os dados. Utilize uma ferramenta como a bancada MySQL Workbench ou mysql.exe para criar a base de dados. A base de dados pode ter o mesmo nome que a base de dados que contém os dados despejados ou pode criar uma base de dados com um nome diferente.

Para se conectar, localize as informações de ligação na **visão geral** da sua Base de Dados Azure para o MySQL.

:::image type="content" source="./media/concepts-migrate-dump-restore/1_server-overview-name-login.png" alt-text="Encontre as informações de ligação no portal Azure":::

Adicione as informações de ligação na sua bancada MySQL Workbench.

:::image type="content" source="./media/concepts-migrate-dump-restore/2_setup-new-connection.png" alt-text="Cadeia de conexão mysql workbench":::

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

## <a name="dump-and-restore-using-mysqldump-utility"></a>Despejar e restaurar usando o utilitário mysqldump

### <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Criar um ficheiro de backup a partir da linha de comando usando mysqldump
Para fazer o back up de uma base de dados MySQL existente no servidor local ou numa máquina virtual, execute o seguinte comando:
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Os parâmetros a fornecer são:
- [uname] Nome de utilizador da sua base de dados
- [passe] A palavra-passe da sua base de dados (note que não há espaço entre -p e a palavra-passe)
- [dbname] O nome da sua base de dados
- [ficheiro de reserva.sql] O nome de ficheiro para a sua cópia de segurança da base de dados
- [--opt] A opção mysqldump

Por exemplo, para fazer o back-up de uma base de dados chamada 'testdb' no seu servidor MySQL com o nome de utilizador 'testuser' e sem palavra-passe para um ficheiro testdb_backup.sql, utilize o seguinte comando. O comando reensi para `testdb` um ficheiro chamado , que contém todas as `testdb_backup.sql` declarações SQL necessárias para recriar a base de dados. Certifique-se de que o nome de utilizador 'testuser' tem pelo menos o privilégio SELECT para mesas de despejo, SHOW VIEW para vistas despejadas, TRIGGER para gatilhos despejados e TABELAS DE BLOQUEIO se a opção de transação única não for utilizada.

```bash
GRANT SELECT, LOCK TABLES, SHOW VIEW ON *.* TO 'testuser'@'hostname' IDENTIFIED BY 'password';
```
Agora executar mysqldump para criar a cópia de segurança da base de `testdb` dados

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

### <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Restaurar a sua base de dados MySQL utilizando a linha de comando ou a bancada mySQL Workbench
Uma vez criado a base de dados-alvo, pode utilizar o comando mysql ou a bancada mySQL workbench para restaurar os dados na base de dados recentemente criada a partir do ficheiro de despejo.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Neste exemplo, restaurar os dados na base de dados recém-criada na base de dados target Azure para o servidor MySQL.

Aqui está um exemplo para como usar este **mysql** para **single server** :

```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```
Aqui está um exemplo para como usar este **mysql** para **servidor flexível** :

```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin -p testdb < testdb_backup.sql
```
---

## <a name="dump-and-restore-using-phpmyadmin"></a>Despejar e restaurar usando PHPMyAdmin
Siga estes passos para despejar e restaurar uma base de dados usando PHPMyadmin.

> [!NOTE]
> Para um único servidor, o nome de utilizador deve estar neste formato , username@servername ' mas para o servidor flexível pode simplesmente usar 'username' Se utilizar username@servername ' para servidor flexível, a ligação falhará.

### <a name="export-with-phpmyadmin"></a>Exportação com PHPMyadmin
Para exportar, pode utilizar a ferramenta comum phpMyAdmin, que já pode ter instalado localmente no seu ambiente. Para exportar a sua base de dados MySQL utilizando PHPMyAdmin:
1. Abra phpMyAdmin.
2. Selecione a sua base de dados. Clique no nome da base de dados na lista à esquerda.
3. Clique no link **Exportação.** Uma nova página parece ver o despejo da base de dados.
4. Na área De Exportação, clique no link **Select All** para escolher as tabelas na sua base de dados.
5. Na área de opções SQL, clique nas opções apropriadas.
6. Clique na opção Guardar como opção **de ficheiro** e na opção de compressão correspondente e, em seguida, clique no botão **Go.** Deve aparecer uma caixa de diálogo que o leva a guardar o ficheiro localmente.

### <a name="import-using-phpmyadmin"></a>Importação usando PHPMyAdmin
Importar a sua base de dados é semelhante à exportação. Fazer as seguintes ações:
1. Abra phpMyAdmin.
2. Na página de configuração phpMyAdmin, clique em **Adicionar** para adicionar a sua Base de Dados Azure para o servidor MySQL. Forneça os detalhes da ligação e informações de login.
3. Crie uma base de dados devidamente nomeada e selecione-a à esquerda do ecrã. Para reescrever a base de dados existente, clique no nome da base de dados, selecione todas as caixas de verificação ao lado dos nomes das tabelas e selecione **Drop** para eliminar as tabelas existentes.
4. Clique no link **SQL** para mostrar a página onde pode escrever em comandos SQL ou fazer upload do seu ficheiro SQL.
5. Utilize o botão de navegação para encontrar o ficheiro de base de **dados.**
6. Clique no botão **Go** para exportar a cópia de segurança, execute os comandos SQL e recrie a sua base de dados.

## <a name="known-issues"></a>Problemas Conhecidos
Para questões conhecidas, dicas e truques, recomendamos que olhe para o nosso [blog techcommunity.](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/tips-and-tricks-in-using-mysqldump-and-mysql-restore-to-azure/ba-p/916912)

## <a name="next-steps"></a>Passos seguintes
- [Conecte as aplicações à Base de Dados Azure para o MySQL](./howto-connection-string.md).
- Para obter mais informações sobre bases de dados migratórias para a Base de Dados Azure para o MySQL, consulte o [Guia de Migração da Base de Dados.](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)
- Se procura migrar grandes bases de dados com tamanhos de base de dados superiores a 1 TBs, talvez deva considerar a utilização de ferramentas comunitárias como **o mydumper/myloader** que suporta a exportação e importação paralelas. Saiba [como migrar grandes bases de dados mySQL.](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699)
