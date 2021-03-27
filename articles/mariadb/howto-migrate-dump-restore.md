---
title: Migrar com despejo e restauro - Azure Database for MariaDB
description: Este artigo explica duas formas comuns de fazer o back back bases de dados na sua base de dados Azure para a MariaDB utilizando ferramentas como mysqldump, MySQL Workbench e phpMyAdmin.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 2/27/2020
ms.openlocfilehash: 35b1e84bdf74afd9577c7c98f023179dd769cd66
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628225"
---
# <a name="migrate-your-mariadb-database-to-an-azure-database-for-mariadb-by-using-dump-and-restore"></a>Migrar a sua base de dados MariaDB para uma base de dados Azure para a MariaDB utilizando o despejo e restaurar

Este artigo explica duas formas comuns de fazer o back backs de apoio e restaurar as bases de dados na sua base de dados Azure para a MariaDB:
- Despeje e restaure utilizando uma ferramenta de linha de comando (utilizando o mysqldump) 
- Despejar e restaurar usando phpMyAdmin

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar a migrar a sua base de dados, faça o seguinte:
- Criar uma [Base de Dados Azure para servidor MariaDB - Portal Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).
- Instale o utilitário da linha de comando [mysqldump.](https://mariadb.com/kb/en/library/mysqldump/)
- Descarregue e instale [a MySQL Workbench](https://dev.mysql.com/downloads/workbench/) ou outra ferramenta MySQL de terceiros para executar comandos de despejo e restauro.

## <a name="use-common-tools"></a>Use ferramentas comuns
Utilize utilitários e ferramentas comuns como o MySQL Workbench ou o mysqldump para ligar e restaurar remotamente os dados na sua base de dados Azure para MariaDB. Utilize estas ferramentas na sua máquina cliente com uma ligação à Internet para ligar à base de dados Azure para a MariaDB. Utilize uma ligação encriptada SSL como uma melhor prática de segurança. Para obter mais informações, consulte [a conectividade Configure SSL na Base de Dados Azure para MariaDB](concepts-ssl-connection-security.md). Não precisa de mover os ficheiros de despejo para qualquer localização especial na nuvem quando migrar dados para a sua base de dados Azure para a MariaDB. 

## <a name="common-uses-for-dump-and-restore"></a>Usos comuns para despejo e restauro
Você pode usar utilitários MySQL, como mysqldump e mysqlpump para despejar e carregar bases de dados em uma base de dados Azure para servidor MariaDB em vários cenários comuns. 

- Usa depósitos de base quando estás a migrar uma base de dados inteira. Esta recomendação mantém-se quando está a mover uma grande quantidade de dados, ou quando pretende minimizar a interrupção do serviço para sites ou aplicações ao vivo. 
-  Certifique-se de que todas as tabelas da base de dados utilizam o motor de armazenamento InnoDB quando estiver a carregar dados na sua base de dados Azure para o MariaDB. A Azure Database for MariaDB suporta apenas o motor de armazenamento InnoDB, e nenhum outro motor de armazenamento. Se as suas tabelas estiverem configuradas com outros motores de armazenamento, converta-as no formato do motor InnoDB antes de as migrar para a base de dados Azure para a MariaDB.
   
   Por exemplo, se tiver uma aplicação WordPress ou uma aplicação web que utilize as tabelas MyISAM, primeiro converta essas tabelas migrando-as para o formato InnoDB antes de as restaurar na base de dados Azure para MariaDB. Utilize a cláusula `ENGINE=InnoDB` para definir o motor para a criação de uma nova tabela e, em seguida, transfira os dados para a tabela compatível antes de restaurá-lo. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Para evitar quaisquer problemas de compatibilidade quando estiver a despejar bases de dados, certifique-se de que está a usar a mesma versão do MariaDB nos sistemas de origem e destino. Por exemplo, se o seu servidor MariaDB existente for a versão 10.2, deve migrar para a sua base de dados Azure para MariaDB que está configurada para executar a versão 10.2. O `mysql_upgrade` comando não funciona numa Base de Dados Azure para o servidor MariaDB, e não é suportado. Se precisar de fazer upgrade através das versões MariaDB, primeiro despeje ou exporte a sua base de dados de versão anterior para uma versão posterior do MariaDB no seu próprio ambiente. Em seguida, pode correr `mysql_upgrade` antes de tentar migrar para a sua base de dados Azure para MariaDB.

## <a name="performance-considerations"></a>Considerações de desempenho
Para otimizar o desempenho quando estiver a despejar grandes bases de dados, tenha em mente as seguintes considerações:
-   Use a `exclude-triggers` opção no mysqldump. Exclua os gatilhos dos ficheiros de despejo para evitar que o gatilho dispare durante a restauração dos dados. 
-   Utilize a `single-transaction` opção para definir o modo de isolamento de transação para REPETível READ e envie uma declaração DE SQL DE START TRANSACTION para o servidor antes de despejar dados. Despejar muitas tabelas numa única transação faz com que algum armazenamento extra seja consumido durante a restauração. A `single-transaction` opção e a `lock-tables` opção são mutuamente exclusivas. Isto porque as tabelas de bloqueio fazem com que quaisquer transações pendentes sejam cometidas implicitamente. Para despejar mesas grandes, combine a `single-transaction` opção com a `quick` opção. 
-   Utilize a `extended-insert` sintaxe de várias linhas que inclui várias listas DE VALOR. Esta abordagem resulta num ficheiro de despejo mais pequeno e acelera as inserções quando o ficheiro é recarregado.
-  Use a `order-by-primary` opção no mysqldump quando estiver a despejar bases de dados, para que os dados sejam descritos na ordem principal.
-   Use a `disable-keys` opção no mysqldump quando estiver a despejar dados, para desativar as restrições de chaves estrangeiras antes da carga. A desativação de verificações de chaves estrangeiras ajuda a melhorar o desempenho. Ative os constrangimentos e verifique os dados após a carga para garantir a integridade referencial.
-   Utilize mesas divisórias quando apropriado.
-   Carregue os dados em paralelo. Evite demasiado paralelismo, o que pode fazer com que atinja um limite de recursos e monitorize os recursos utilizando as métricas disponíveis no portal Azure. 
-   Use a `defer-table-indexes` opção no mysqlpump quando estiver a despejar bases de dados, para que a criação de índices aconteça após o envio de dados de tabela.
-   Copie os ficheiros de cópias de segurança para uma loja de bolhas Azure e execute a restauração a partir daí. Esta abordagem deve ser muito mais rápida do que realizar o restauro através da internet.

## <a name="create-a-backup-file"></a>Criar um ficheiro de backup

Para fazer uma base de dados MariaDB existente no servidor local ou numa máquina virtual, executar o seguinte comando utilizando o mysqldump: 

```bash
$ mysqldump --opt -u <uname> -p<pass> <dbname> > <backupfile.sql>
```

Os parâmetros a fornecer são:
- *\<uname>*: O nome do utilizador da sua base de dados 
- *\<pass>*: A palavra-passe da sua base de dados (note que não há espaço entre -p e a palavra-passe) 
- *\<dbname>*: O nome da sua base de dados 
- *\<backupfile.sql>*: O nome do ficheiro da sua cópia de segurança da sua base de dados 
- *\<--opt>*: A opção mysqldump 

Por exemplo, para fazer o back-up de uma base de dados chamada *testdb* no seu servidor MariaDB com o *testuser* de nome de utilizador e sem senha para um ficheiro testdb_backup.sql, utilize o seguinte comando. O comando reensi para `testdb` um ficheiro chamado , que contém todas as `testdb_backup.sql` declarações SQL necessárias para recriar a base de dados. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Para selecionar tabelas específicas para fazer o back up na sua base de dados, liste os nomes das tabelas, separados por espaços. Por exemplo, para apoiar apenas as tabelas1 e as tabelas2 do *testdb,* siga este exemplo: 

```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

Para fazer o back up mais de uma base de dados ao mesmo tempo, use o interruptor de base de dados e liste os nomes da base de dados, separados por espaços. 

```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Criar uma base de dados no servidor alvo
Crie uma base de dados vazia na base de dados Azure target para o servidor MariaDB onde pretende migrar os dados. Utilize uma ferramenta como a bancada MySQL Workbench para criar a base de dados. A base de dados pode ter o mesmo nome que a base de dados que contém os dados despejados, ou pode criar uma base de dados com um nome diferente.

Para se conectar, localize as informações de ligação no painel **de visão geral** da sua base de dados Azure para MariaDB.

![Screenshot do painel de visão geral para uma base de dados Azure para o servidor MariaDB no portal Azure.](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

Na bancada MySQL Workbench, adicione as informações de ligação.

![Screenshot do painel mySQL Connections na bancada MySQL Workbench.](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Restaurar a sua base de dados MariaDB
Depois de criar a base de dados-alvo, pode utilizar o comando mysql ou a bancada mySQL workbench para restaurar os dados na base de dados recém-criada a partir do ficheiro de despejo.

```bash
mysql -h <hostname> -u <uname> -p<pass> <db_to_restore> < <backupfile.sql>
```

Neste exemplo, você restaura os dados na base de dados recém-criada na base de dados target Azure para o servidor MariaDB.

```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-your-mariadb-database-by-using-phpmyadmin"></a>Exporte a sua base de dados MariaDB utilizando o phpMyAdmin

Para exportar, pode utilizar a ferramenta comum phpMyAdmin, que pode já estar instalada localmente no seu ambiente. Para exportar a sua base de dados MariaDB, faça o seguinte:
1. Abra phpMyAdmin.
1. No painel esquerdo, selecione a sua base de dados e, em seguida, selecione a **ligação Exportação.** Uma nova página parece ver o despejo da base de dados.
1. Na área **Exportação,** **selecione** o link Select All para escolher as tabelas na sua base de dados. 
1. Na área de **opções SQL,** selecione as opções apropriadas. 
1. Selecione a opção **'Guardar como** ficheiro' e a opção de compressão correspondente e, em seguida, selecione **Ir**. A pedido, guarde o ficheiro localmente.

## <a name="import-your-database-by-using-phpmyadmin"></a>Importe a sua base de dados utilizando o phpMyAdmin

O processo de importação é semelhante ao processo de exportação. Faça o seguinte:
1. Abra phpMyAdmin. 
1. Na página de configuração phpMyAdmin, **selecione Adicionar** para adicionar a sua Base de Dados Azure para o servidor MariaDB. 
1. Insira os detalhes da ligação e informações de login.
1. Crie uma base de dados devidamente nomeada e, em seguida, selecione-a no painel esquerdo. Para reescrever a base de dados existente, selecione o nome da base de dados, selecione todas as caixas de verificação ao lado dos nomes das tabelas e selecione **Drop** para eliminar as tabelas existentes. 
1. Selecione o link **SQL** para mostrar a página onde pode introduzir comandos SQL ou fazer upload do seu ficheiro SQL. 
1. Selecione o botão de navegação para encontrar o ficheiro de base de **dados.** 
1. Selecione o botão **Go** para exportar a cópia de segurança, execute os comandos SQL e recrie a sua base de dados.

## <a name="next-steps"></a>Passos seguintes
- [Conecte as aplicações à sua base de dados Azure para o MariaDB.](./howto-connection-string.md)
