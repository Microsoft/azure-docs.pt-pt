---
title: Configurar a replicação de dados na base de dados do Azure para MariaDB | Documentos da Microsoft
description: Este artigo descreve como configurar dados de replicação na base de dados do Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 21e8a88cc6f03b4d54a6c5299b0b6be36cc32d6d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444793"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Configurar a replicação de dados na base de dados do Azure para MariaDB

Este artigo descreve como configurar dados de replicação na base de dados do Azure para MariaDB ao configurar os servidores mestre e de réplica. Este artigo pressupõe que tem alguma experiência anterior com MariaDB servidores e bases de dados.

Para criar uma réplica da base de dados do Azure para o serviço de MariaDB, replicação de dados sincroniza os dados de um principal MariaDB server no local, em máquinas virtuais (VMs) ou nos serviços de base de dados de cloud.

> [!NOTE]
> Se o servidor mestre é uma versão mais recente ou 10.2, recomendamos que defina a replicação de dados-in usando [ID de transação Global](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Criar um servidor de MariaDB para utilizar como uma réplica

1. Crie uma nova base de dados do Azure para MariaDB servidor (por exemplo, replica.mariadb.database.azure.com). O servidor é o servidor de réplica na replicação de dados-in.

    Para saber mais sobre a criação do servidor, consulte [criar uma base de dados do Azure para o servidor da MariaDB com o portal do Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > Tem de criar a base de dados do Azure para MariaDB server nos escalões de preços para fins gerais ou com otimização de memória.

2. Crie contas de utilizador idênticos e privilégios correspondentes.
    
    Contas de utilizador não são replicadas a partir do servidor principal para o servidor de réplica. Para fornecer acesso de utilizador para o servidor de réplica, tem de criar manualmente todas as contas e privilégios correspondentes na base de dados do Azure recentemente criado para o servidor de MariaDB.

## <a name="configure-the-master-server"></a>Configurar o servidor mestre

Os passos seguintes prepararem e configurar a MariaDB server alojado no local, numa VM ou num serviço cloud da base de dados para a replicação de dados. O servidor de MariaDB é o mestre de replicação de dados.

1. Ative o registo binário.
    
    Para ver se o registo binário está ativado no mestre de, introduza o seguinte comando:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se a variável [ `log_bin` ](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) devolve o valor `ON`, registo binário está ativado no seu servidor.

   Se `log_bin` devolve o valor `OFF`, edite a **cnf** ficheiro para que `log_bin=ON` ativa o registo binário. Reinicie o servidor para fazer a alteração tenha efeito.

2. Configure as definições do servidor mestre.

    Replicação de dados requer que o parâmetro `lower_case_table_names` para ser consistente entre os servidores mestre e de réplica. O `lower_case_table_names` parâmetro estiver definido como `1` por predefinição na base de dados do Azure para MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Criar uma nova função de replicação e configurar permissões.

   Crie uma conta de utilizador no servidor principal que está configurado com privilégios de replicação. Pode criar uma conta com comandos SQL ou MySQL Workbench. Se pretender replicar com SSL, tem de especificar isso ao criar a conta de utilizador.
   
   Para saber como adicionar contas de utilizador no seu servidor mestre, veja a [MariaDB documentação](https://mariadb.com/kb/en/library/create-user/).

   Ao utilizar os seguintes comandos, a nova função de replicação pode aceder o mestre de qualquer máquina, não apenas a máquina que aloja o mestre em si. Para este acesso, especifique **syncuser\@'%'** no comando para criar um utilizador.
   
   Para saber mais sobre a documentação da MariaDB, veja [especificando os nomes das contas](https://mariadb.com/kb/en/library/create-user/#account-names).

   **Comando SQL**

   - Replicação com SSL

       Para exigir SSL para todas as ligações de utilizador, introduza o seguinte comando para criar um utilizador:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replicação sem SSL

       Se o SSL não é necessário para todas as ligações, introduza o seguinte comando para criar um utilizador:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Para criar a função de replicação no MySQL Workbench, no **gerenciamento** painel, selecione **utilizadores e os privilégios**. Em seguida, selecione **adicionar conta**.
 
   ![Os utilizadores e privilégios](./media/howto-data-in-replication/users_privileges.png)

   Introduza um nome de utilizador a **nome de início de sessão** campo.

   ![Sincronizar o utilizador](./media/howto-data-in-replication/syncuser.png)
 
   Selecione o **funções administrativas** painel e, em seguida, na lista de **privilégios Global**, selecione **subordinado de replicação**. Selecione **aplicar** para criar a função de replicação.

   ![Replicação subordinado](./media/howto-data-in-replication/replicationslave.png)


4. Defina o servidor mestre para o modo só de leitura.

   Antes de despeja um banco de dados, o servidor deve ser colocado no modo só de leitura. No modo só de leitura, o mestre não consegue processar quaisquer transações de escrita. Para ajudar a evitar o impacto comercial, agende a janela de só de leitura durante uma hora de ponta.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Obtenha o nome de ficheiro de registo binário atual e o deslocamento.

   Para determinar o nome de ficheiro de registo binário atual e o deslocamento, execute o comando [ `show master status` ](https://mariadb.com/kb/en/library/show-master-status/).
    
   ```sql
   show master status;
   ```
   Os resultados devem ser semelhantes a tabela seguinte:
   
   ![Resultados de estado do mestre](./media/howto-data-in-replication/masterstatus.png)

   Tome nota do nome de ficheiro binário, uma vez que vai ser utilizada em passos posteriores.
   
6. Obter a posição de GTID (opcional, que são necessários para a replicação com GTID).

   Para executar a função [ `BINLOG_GTID_POS` ](https://mariadb.com/kb/en/library/binlog_gtid_pos/) para obter a posição de GTID para o nome de ficheiro binlog correspondente e o deslocamento.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>Cópia de segurança e restaurar o servidor mestre

1. Despeja todas as bases de dados do servidor principal.

   Utilize mysqldump para Despejar todos os bancos de dados do servidor principal. Não é necessário para a biblioteca MySQL de cópia de segurança e a biblioteca de teste.

    Para obter mais informações, consulte [captura e restauro](howto-migrate-dump-restore.md).

2. Defina o servidor mestre de modo de leitura/escrita.

   Depois da base de dados foram colocado, alterar o mestre MariaDB back de servidor para o modo de leitura/escrita.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Restaure o ficheiro de informação para o novo servidor.

   Restaure o ficheiro de informação para o servidor criado na base de dados do Azure para MariaDB serviço. Ver [capturar e restaurar](howto-migrate-dump-restore.md) para saber como restaurar um arquivo de despejo para um servidor de MariaDB.

   Se o ficheiro de informação for grande, carregue-a uma VM no Azure na mesma região que o seu servidor de réplica. Restaurá-la para a base de dados do Azure para MariaDB server da VM.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>Vincule os servidores mestre e de réplica para iniciar a replicação de dados

1. Defina o servidor mestre.

   Todas as funções de replicação de dados-in são efetuadas pelo procedimentos armazenados. Pode encontrar todos os procedimentos em [dados na replicação procedimentos armazenados](reference-data-in-stored-procedures.md). Procedimentos armazenados podem ser executados no shell do MySQL ou o MySQL Workbench.

   Para ligar os dois servidores e iniciar a replicação, inicie sessão no servidor de réplica de destino no DB do Azure para MariaDB serviço. Em seguida, definir a instância externa como o servidor mestre com o `mysql.az_replication_change_master` ou `mysql.az_replication_change_master_with_gtid` procedimento armazenado no banco de dados do Azure para MariaDB server.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   ou
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: nome de anfitrião do servidor mestre
   - master_user: nome de utilizador para o servidor mestre
   - master_password: palavra-passe para o servidor mestre
   - master_log_file: nome de ficheiro de registo binário a execução `show master status`
   - master_log_pos: posição do registo binário a execução `show master status`
   - master_gtid_pos: Posição de GTID a execução `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: Contexto de certificado de AC. Se não estiver a utilizar SSL, passar um string.* vazio
    
    
    \* Recomendamos aprovação no parâmetro master_ssl_ca como uma variável. Para obter mais informações, consulte os exemplos seguintes.

   **Exemplos**

   - Replicação com SSL

       Crie a variável `@cert` ao executar os comandos seguintes:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       Replicação com o SSL é configurada entre um servidor mestre hospedado em companya.com o domínio e um servidor de réplica alojado na base de dados do Azure para MariaDB. Este procedimento armazenado é executado na réplica.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Replicação sem SSL

       Replicação sem SSL é configurada entre um servidor mestre hospedado em companya.com o domínio e um servidor de réplica alojado na base de dados do Azure para MariaDB. Este procedimento armazenado é executado na réplica.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Inicie a replicação.

   Chamar o `mysql.az_replication_start` armazenados procedimento para iniciar a replicação.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Verificar o estado de replicação.

   Chamar o [ `show slave status` ](https://mariadb.com/kb/en/library/show-slave-status/) comando no servidor de réplica para ver o estado de replicação.
    
   ```sql
   show slave status;
   ```

   Se `Slave_IO_Running` e `Slave_SQL_Running` estiverem no estado `yes`e o valor de `Seconds_Behind_Master` é `0`, replicação está a funcionar. `Seconds_Behind_Master` Indica a réplica como tardia é. Se o valor não é `0`, em seguida, a réplica está a processar atualizações.

4. Atualize as variáveis de servidor correspondente para tornar os dados na replicação mais segura (necessária apenas para a replicação sem GTID).
    
    Devido a uma limitação de replicação nativo no MariaDB, tem de definir [ `sync_master_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) e [ `sync_relay_log_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) variáveis em replicação sem o cenário GTID.

    Verifique o seu servidor de subordinado `sync_master_info` e `sync_relay_log_info` variáveis para se certificar de que a replicação de dados-in é estável e definir as variáveis `1`.
    
## <a name="other-stored-procedures"></a>Outros procedimentos armazenados

### <a name="stop-replication"></a>Parar a replicação

Para parar a replicação entre o servidor mestre e de réplica, utilize o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Remova a relação de replicação

Para remover a relação entre o servidor mestre e de réplica, utilize o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Ignorar o erro de replicação

Para ignorar um erro de replicação e permitir que a replicação, utilize o seguinte procedimento armazenado:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [replicação de dados](concepts-data-in-replication.md) para base de dados do Azure para MariaDB.
