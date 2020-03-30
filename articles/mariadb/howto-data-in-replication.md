---
title: Configure a replicação de dados - Base de Dados Azure para MariaDB
description: Este artigo descreve como configurar a Replicação de Dados em Base de Dados Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 51b800dde140affd222f2bdb341c0fbf3a57d8cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530160"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Configure a replicação de dados em Base de Dados Azure para MariaDB

Este artigo descreve como configurar a Replicação de Dados na Base de Dados Azure para O DNDb configurando os servidores mestre e réplica. Este artigo assume que tem alguma experiência anterior com servidores MariaDB e bases de dados.

Para criar uma réplica na Base de Dados Azure para o serviço MariaDB, a Replicação data-in sincroniza os dados de um servidor mariadb mestre no local, em máquinas virtuais (VMs), ou em serviços de base de dados em nuvem.

> [!NOTE]
> Se o seu servidor principal for versão 10.2 ou mais recente, recomendamos que instale replicação de Dados utilizando o ID de [Transação Global](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Criar um servidor MariaDB para usar como réplica

1. Crie uma nova Base de Dados Azure para o servidor MariaDB (por exemplo, replica.mariadb.database.azure.com). O servidor é o servidor de réplica sinuoso na Replicação de Dados.

    Para saber mais sobre a criação do servidor, consulte [Criar uma Base de Dados Azure para o servidor MariaDB utilizando o portal Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > Deve criar a Base de Dados Azure para o servidor MariaDB nos níveis de preços otimizados para fins gerais ou memória.

2. Criar contas de utilizador idênticas e privilégios correspondentes.
    
    As contas dos utilizadores não são replicadas do servidor principal para o servidor de réplicas. Para fornecer acesso ao utilizador ao servidor de réplicas, deve criar manualmente todas as contas e privilégios correspondentes na recém-criada Base de Dados Azure para o servidor MariaDB.

## <a name="configure-the-master-server"></a>Configure o servidor principal

Os seguintes passos preparam e configuram o servidor MariaDB hospedado no local, num VM, ou num serviço de base de dados em nuvem para replicação de Dados. O servidor MariaDB é o mestre em Replicação de Dados.

1. Ligue a exploração binária.
    
    Para ver se a exploração de madeira binária está ativada no mestre, introduza o seguinte comando:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se a [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) variável `ON`devolver o valor, o registo binário está ativado no seu servidor.

   Se `log_bin` devolver `OFF`o valor, edite o ficheiro `log_bin=ON` **my.cnf** de modo a que se transforme na exploração madeireira binária. Reiniciar o servidor para fazer a alteração produzir efeito.

2. Configure as definições do servidor principal.

    A replicação de dados requer `lower_case_table_names` que o parâmetro seja consistente entre os servidores mestre e réplica. O `lower_case_table_names` parâmetro está `1` definido por padrão na Base de Dados Azure para MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Crie um novo papel de replicação e crie permissões.

   Crie uma conta de utilizador no servidor principal que esteja configurada com privilégios de replicação. Pode criar uma conta utilizando comandos SQL ou bancada de trabalho MySQL. Se planeia replicar-se com o SSL, tem de especificar isto quando criar a conta de utilizador.
   
   Para saber adicionar contas de utilizador no seu servidor principal, consulte a [documentação MariaDB](https://mariadb.com/kb/en/library/create-user/).

   Utilizando os seguintes comandos, a nova função de replicação pode aceder ao mestre a partir de qualquer máquina, e não apenas da máquina que acolhe o próprio mestre. Para este acesso, **especifique\@o síncope '%'** no comando para criar um utilizador.
   
   Para saber mais sobre a documentação do MariaDB, consulte [a especificação dos nomes das contas.](https://mariadb.com/kb/en/library/create-user/#account-names)

   **Comando SQL**

   - Replicação com SSL

       Para exigir SSL para todas as ligações do utilizador, introduza o seguinte comando para criar um utilizador:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replicação sem SSL

       Se o SSL não for necessário para todas as ligações, introduza o seguinte comando para criar um utilizador:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Para criar a função de replicação na Bancada de Trabalho MySQL, no painel **de Gestão,** selecione **Utilizadores e Privilégios.** Em seguida, selecione **Adicionar Conta**.
 
   ![Utilizadores e Privilégios](./media/howto-data-in-replication/users_privileges.png)

   Introduza um nome de utilizador no campo **'Nome de login'.**

   ![Utilizador sincronizado](./media/howto-data-in-replication/syncuser.png)
 
   Selecione o painel **De Papéis Administrativos** e, em seguida, na lista de **Privilégios Globais,** selecione **Replication Slave**. Selecione **Aplicar** para criar a função de replicação.

   ![Escravo da Replicação](./media/howto-data-in-replication/replicationslave.png)


4. Detete o servidor principal para o modo de leitura.

   Antes de despejar uma base de dados, o servidor deve ser colocado no modo de leitura. Enquanto estiver em modo de leitura, o mestre não pode processar nenhuma transação de escrita. Para ajudar a evitar o impacto do negócio, agende a janela apenas para leitura durante um período fora do pico.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Obtenha o nome atual do ficheiro de registo binário e offset.

   Para determinar o nome de ficheiro binário atual [`show master status`](https://mariadb.com/kb/en/library/show-master-status/)e offset, executar o comando .
    
   ```sql
   show master status;
   ```
   Os resultados devem ser semelhantes ao quadro seguinte:
   
   ![Resultados do Estado Mestre](./media/howto-data-in-replication/masterstatus.png)

   Note o nome do ficheiro binário, porque será usado em etapas posteriores.
   
6. Obtenha a posição GTID (opcional, necessária para replicação com GTID).

   Executar a [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) função para obter a posição GTID para o nome e offset correspondentes do ficheiro binlog.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>Despejar e restaurar o servidor principal

1. Despeje todas as bases de dados do servidor principal.

   Use mysqldump para despejar todas as bases de dados do servidor principal. Não é necessário despejar a biblioteca mySQL e a biblioteca de testes.

    Para mais informações, consulte [Despejar e restaurar](howto-migrate-dump-restore.md).

2. Detete o servidor principal para ler/escrever o modo.

   Depois de a base de dados ter sido despejada, mude o servidor master MariaDB de volta para o modo de leitura/escrita.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Restaure o ficheiro de despejo para o novo servidor.

   Restaure o ficheiro de despejo para o servidor criado na Base de Dados Azure para o serviço MariaDB. Consulte [dump & Restaurar](howto-migrate-dump-restore.md) para como restaurar um ficheiro de despejo para um servidor MariaDB.

   Se o ficheiro de despejo for grande, faça o upload para um VM em Azure na mesma região que o seu servidor de réplicas. Restaurá-lo na Base de Dados Azure para o servidor MariaDB a partir do VM.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>Ligue os servidores mestre e réplica para iniciar a Replicação data-in

1. Desloque o servidor principal.

   Todas as funções de replicação de Dados são feitas por procedimentos armazenados. Pode encontrar todos os procedimentos nos [Procedimentos Armazenados de Replicação de Dados](reference-data-in-stored-procedures.md). Os procedimentos armazenados podem ser executados na concha MySQL ou na bancada de trabalho MySQL.

   Para ligar dois servidores e iniciar a replicação, inicie o sessão no servidor de réplica do alvo no serviço Azure DB para o serviço MariaDB. Em seguida, detete a instância `mysql.az_replication_change_master` externa `mysql.az_replication_change_master_with_gtid` como servidor principal utilizando o procedimento ou armazenado no servidor Azure DB para o servidor MariaDB.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   ou
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: nome de anfitrião do servidor principal
   - master_user: nome de utilizador para o servidor principal
   - master_password: senha para o servidor principal
   - master_log_file: nome de ficheiro de registo binário de execução`show master status`
   - master_log_pos: posição de registo binário de execução`show master status`
   - master_gtid_pos: Posição GTID de correr`select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: contexto do certificado CA. Se não estiver a usar SSL, passe numa corda vazia.
    
    
    *Recomendamos a passagem do parâmetro master_ssl_ca como variável. Para mais informações, consulte os seguintes exemplos.

   **Exemplos**

   - Replicação com SSL

       Criar a `@cert` variável executando os seguintes comandos:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       A replicação com SSL é configurada entre um servidor principal hospedado no domínio companya.com, e um servidor de réplica hospedado na Base de Dados Azure para MariaDB. Este procedimento armazenado é executado na réplica.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Replicação sem SSL

       A replicação sem SSL é configurada entre um servidor principal hospedado no domínio companya.com, e um servidor de réplica hospedado na Base de Dados Azure para MariaDB. Este procedimento armazenado é executado na réplica.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Começa a replicação.

   Ligue `mysql.az_replication_start` para o procedimento armazenado para iniciar a replicação.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Verifique o estado da replicação.

   Ligue [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) para o comando do servidor de réplica para ver o estado de replicação.
    
   ```sql
   show slave status;
   ```

   Se `Slave_IO_Running` `Slave_SQL_Running` e estiverem `yes`no estado `Seconds_Behind_Master` , `0`e o valor do é , a replicação está a funcionar. `Seconds_Behind_Master`indica a hora da réplica. Se o valor `0`não for, então a réplica está a processar atualizações.

4. Atualize as variáveis correspondentes do servidor para tornar a replicação de dados mais segura (necessária apenas para replicação sem GTID).
    
    Devido a uma limitação de replicação [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) nativa [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) em MariaDB, você deve definir e variáveis na replicação sem o cenário GTID.

    Verifique as variáveis `sync_master_info` `sync_relay_log_info` e variáveis do seu servidor de escravos para se `1`certificar de que a replicação de dados é estável e definir as variáveis para .
    
## <a name="other-stored-procedures"></a>Outros procedimentos armazenados

### <a name="stop-replication"></a>Parar replicação

Para parar a replicação entre o servidor principal e o servidor de réplicas, utilize o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Remover a relação de replicação

Para remover a relação entre o servidor principal e o servidor de réplicas, utilize o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Ignore o erro de replicação

Para saltar um erro de replicação e permitir a replicação, utilize o seguinte procedimento armazenado:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a replicação de Dados](concepts-data-in-replication.md) para base de dados Azure para MariaDB.
