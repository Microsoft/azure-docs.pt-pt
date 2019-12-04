---
title: Configurar o data-in Replication-banco de dados do Azure para MariaDB
description: Este artigo descreve como configurar Replicação de Dados no banco de dados do Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 0dbbc9b09d5d4770296223db9dc909c17f574fe8
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767029"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Configurar Replicação de Dados no banco de dados do Azure para MariaDB

Este artigo descreve como configurar o Replicação de Dados no banco de dados do Azure para MariaDB Configurando os servidores mestre e de réplica. Este artigo pressupõe que você tenha alguma experiência anterior com servidores e bancos de dados MariaDB.

Para criar uma réplica no banco de dados do Azure para o serviço MariaDB, Replicação de Dados sincroniza dados de um servidor mestre MariaDB no local, em VMs (máquinas virtuais) ou em serviços de banco de dados de nuvem.

> [!NOTE]
> Se o servidor mestre for da versão 10,2 ou mais recente, recomendamos que você configure Replicação de Dados usando a [ID de transação global](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Criar um servidor MariaDB para usar como uma réplica

1. Crie um novo banco de dados do Azure para o servidor MariaDB (por exemplo, replica.mariadb.database.azure.com). O servidor é o servidor de réplica em Replicação de Dados.

    Para saber mais sobre a criação do servidor, consulte [criar um banco de dados do Azure para o servidor MariaDB usando o portal do Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > Você deve criar o banco de dados do Azure para o servidor MariaDB nos tipos de preço Uso Geral ou com otimização de memória.

2. Crie contas de usuário idênticas e privilégios correspondentes.
    
    As contas de usuário não são replicadas do servidor mestre para o servidor de réplica. Para fornecer acesso de usuário ao servidor de réplica, você deve criar manualmente todas as contas e privilégios correspondentes no banco de dados do Azure recém-criado para o servidor MariaDB.

## <a name="configure-the-master-server"></a>Configurar o servidor mestre

As etapas a seguir preparam e configuram o servidor MariaDB hospedado localmente, em uma VM ou em um serviço de banco de dados de nuvem para Replicação de Dados. O servidor MariaDB é o mestre no Replicação de Dados.

1. Ative o registro em log binário.
    
    Para ver se o log binário está habilitado no mestre, digite o seguinte comando:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se a variável [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) retornar o valor `ON`, o log binário será habilitado no servidor.

   Se `log_bin` retornar o valor `OFF`, edite o arquivo **My. cnf** para que `log_bin=ON` ative o registro em log binário. Reinicie o servidor para fazer a alteração entrar em vigor.

2. Defina as configurações do servidor mestre.

    Replicação de Dados requer que o parâmetro `lower_case_table_names` seja consistente entre os servidores mestre e de réplica. O parâmetro `lower_case_table_names` é definido como `1` por padrão no banco de dados do Azure para MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Crie uma nova função de replicação e configure as permissões.

   Crie uma conta de usuário no servidor mestre que está configurada com privilégios de replicação. Você pode criar uma conta usando comandos SQL ou MySQL Workbench. Se você planeja replicar com SSL, você deve especificar isso ao criar a conta de usuário.
   
   Para saber como adicionar contas de usuário em seu servidor mestre, consulte a [documentação do MariaDB](https://mariadb.com/kb/en/library/create-user/).

   Usando os comandos a seguir, a nova função de replicação pode acessar o mestre de qualquer computador, não apenas o computador que hospeda o próprio mestre. Para esse acesso, especifique **syncuser\@'% '** no comando para criar um usuário.
   
   Para saber mais sobre a documentação do MariaDB, consulte [especificando nomes de conta](https://mariadb.com/kb/en/library/create-user/#account-names).

   **Comando SQL**

   - Replicação com SSL

       Para exigir o SSL para todas as conexões de usuário, digite o seguinte comando para criar um usuário:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replicação sem SSL

       Se o SSL não for necessário para todas as conexões, digite o seguinte comando para criar um usuário:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Para criar a função de replicação no MySQL Workbench, no painel **Gerenciamento** , selecione **usuários e privilégios**. Em seguida, selecione **adicionar conta**.
 
   ![Usuários e privilégios](./media/howto-data-in-replication/users_privileges.png)

   Insira um nome de usuário no campo **nome de logon** .

   ![Sincronizar usuário](./media/howto-data-in-replication/syncuser.png)
 
   Selecione o painel **funções administrativas** e, em seguida, na lista de **privilégios globais**, selecione **replicação escravo**. Selecione **aplicar** para criar a função de replicação.

   ![Escravo de replicação](./media/howto-data-in-replication/replicationslave.png)


4. Defina o servidor mestre como modo somente leitura.

   Antes de despejar um banco de dados, o servidor deve ser colocado no modo somente leitura. No modo somente leitura, o mestre não pode processar nenhuma transação de gravação. Para ajudar a evitar o impacto nos negócios, Agende a janela somente leitura fora do horário de pico.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Obter o nome e o deslocamento do arquivo de log binário atual.

   Para determinar o nome e o deslocamento do arquivo de log binário atual, execute o comando [`show master status`](https://mariadb.com/kb/en/library/show-master-status/).
    
   ```sql
   show master status;
   ```
   Os resultados devem ser semelhantes à tabela a seguir:
   
   ![Resultados do status mestre](./media/howto-data-in-replication/masterstatus.png)

   Observe o nome do arquivo binário, pois ele será usado em etapas posteriores.
   
6. Obtenha a posição GTID (opcional, necessária para replicação com GTID).

   Execute a função [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) para obter a posição GTID para o nome do arquivo binlog correspondente e o deslocamento.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>Despejar e restaurar o servidor mestre

1. Despeje todos os bancos de dados do servidor mestre.

   Use mysqldump para despejar todos os bancos de dados do servidor mestre. Não é necessário despejar a biblioteca do MySQL e a biblioteca de teste.

    Para obter mais informações, consulte [dump and Restore](howto-migrate-dump-restore.md).

2. Defina o servidor mestre como modo de leitura/gravação.

   Depois que o banco de dados tiver sido despejado, altere o servidor MariaDB mestre de volta para o modo de leitura/gravação.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Restaure o arquivo de despejo para o novo servidor.

   Restaure o arquivo de despejo para o servidor criado no banco de dados do Azure para o serviço MariaDB. Consulte [despejar & restaurar](howto-migrate-dump-restore.md) para saber como restaurar um arquivo de despejo para um servidor MariaDB.

   Se o arquivo de despejo for grande, carregue-o em uma VM no Azure na mesma região que o servidor de réplica. Restaure-o no banco de dados do Azure para o servidor MariaDB da VM.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>Vincular os servidores mestre e de réplica para iniciar o Replicação de Dados

1. Defina o servidor mestre.

   Todas as funções de Replicação de Dados são feitas por procedimentos armazenados. Você pode encontrar todos os procedimentos em [replicação de dados procedimentos armazenados](reference-data-in-stored-procedures.md). Os procedimentos armazenados podem ser executados no Shell do MySQL ou no MySQL Workbench.

   Para vincular dois servidores e iniciar a replicação, entre no servidor de réplica de destino no banco de BD do Azure para o serviço MariaDB. Em seguida, defina a instância externa como o servidor mestre usando o `mysql.az_replication_change_master` ou `mysql.az_replication_change_master_with_gtid` procedimento armazenado no Azure DB para MariaDB Server.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   ou
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: nome do host do servidor mestre
   - master_user: nome de usuário para o servidor mestre
   - master_password: senha para o servidor mestre
   - master_log_file: o nome do arquivo de log binário de ser executado `show master status`
   - master_log_pos: a posição do log binário de ser executada `show master status`
   - master_gtid_pos: GTID posição da execução `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: contexto do certificado de autoridade de certificação. Se você não estiver usando SSL, passe uma cadeia de caracteres vazia. *
    
    
    \* Recomendamos passar o parâmetro master_ssl_ca como uma variável. Para obter mais informações, consulte os exemplos a seguir.

   **Exemplos**

   - Replicação com SSL

       Crie a variável `@cert` executando os seguintes comandos:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       A replicação com SSL é configurada entre um servidor mestre hospedado no domínio companya.com e um servidor de réplica hospedado no banco de dados do Azure para MariaDB. Esse procedimento armazenado é executado na réplica.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Replicação sem SSL

       A replicação sem SSL é configurada entre um servidor mestre hospedado no domínio companya.com e um servidor de réplica hospedado no banco de dados do Azure para MariaDB. Esse procedimento armazenado é executado na réplica.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Inicie a replicação.

   Chame o procedimento armazenado `mysql.az_replication_start` para iniciar a replicação.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Verifique o status de replicação.

   Chame o comando [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) no servidor de réplica para exibir o status de replicação.
    
   ```sql
   show slave status;
   ```

   Se `Slave_IO_Running` e `Slave_SQL_Running` estiverem no estado `yes`e o valor de `Seconds_Behind_Master` for `0`, a replicação estará funcionando. `Seconds_Behind_Master` indica o quanto tarde a réplica é. Se o valor não for `0`, a réplica estará processando atualizações.

4. Atualize as variáveis de servidor correspondentes para tornar a replicação de dados em segurança (necessária somente para replicação sem GTID).
    
    Devido a uma limitação de replicação nativa no MariaDB, você deve definir [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) e [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) variáveis na replicação sem o cenário de GTID.

    Verifique as variáveis de `sync_master_info` e `sync_relay_log_info` do servidor subordinado para certificar-se de que a replicação de dados está estável e defina as variáveis como `1`.
    
## <a name="other-stored-procedures"></a>Outros procedimentos armazenados

### <a name="stop-replication"></a>Parar replicação

Para interromper a replicação entre o servidor mestre e de réplica, use o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Remover o relacionamento de replicação

Para remover a relação entre o servidor mestre e de réplica, use o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Ignorar o erro de replicação

Para ignorar um erro de replicação e permitir a replicação, use o seguinte procedimento armazenado:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o [replicação de dados](concepts-data-in-replication.md) para o banco de dados do Azure para MariaDB.
