---
title: Configure a replicação de dados para replicar os dados na base de dados do Azure para MariaDB.
description: Este artigo descreve como configurar a dados na replicação da base de dados do Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 39c5efee0958fdfc8fa647f5acaf929f559f7bf7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065657"
---
# <a name="how-to-configure-azure-database-for-mariadb-data-in-replication"></a>Como configurar a base de dados do Azure para a replicação de dados de MariaDB

Neste artigo, aprenderá como configurar a replicação de dados na base de dados do Azure para MariaDB serviço ao configurar os servidores mestre e de réplica. Replicação de dados-in permite-lhe sincronizar dados a partir de um servidor MariaDB mestre em execução no local, nas máquinas virtuais ou serviços de base de dados hospedados por outros fornecedores de cloud para uma réplica da base de dados do Azure para MariaDB serviço. Podemos recommanded, configurar a replicação de dados com [ID de transação Global](https://mariadb.com/kb/en/library/gtid/) quando a versão do seu servidor mestre é 10.2 ou superior.

Este artigo pressupõe que tem pelo menos alguma experiência anterior com MariaDB servidores e bases de dados.

## <a name="create-a-mariadb-server-to-be-used-as-replica"></a>Criar um servidor de MariaDB a utilizar como réplica

1. Criar uma nova base de dados do Azure para o servidor de MariaDB

   Criar um novo servidor de MariaDB (ex. "replica.mariadb.database.azure.com"). Consulte a [criar uma base de dados do Azure para o servidor da MariaDB com o portal do Azure](quickstart-create-mariadb-server-database-using-azure-portal.md) para a criação do servidor. Esse servidor é o servidor de "réplica" na replicação de dados-in.

   > [!IMPORTANT]
   > A base de dados do Azure para MariaDB server têm de ser criado nos escalões de preços para fins gerais ou com otimização de memória.
   > 

2. Criar as mesmas contas de usuário e privilégios correspondentes

   Contas de utilizador não são replicadas a partir do servidor mestre para o servidor de réplica. Se pretender oferecer aos usuários acesso ao servidor de réplica, terá de criar manualmente todas as contas e privilégios correspondentes no recentemente criada a base de dados do Azure para MariaDB server.

## <a name="configure-the-master-server"></a>Configurar o servidor mestre
Os seguintes passos prepararem e configurar a MariaDB server alojado no local, uma máquina virtual ou o serviço de base de dados hospedado por outros fornecedores de cloud para a replicação de dados. Esse servidor é o "mestre" na replicação de dados-in. 

1. Ativar o registo binário

   Verifica se o registo binário tenha sido ativado no mestre de executando o seguinte comando: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se a variável [ `log_bin` ](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) é devolvido com o valor "ON", o registo binário está ativado no seu servidor. 

   Se `log_bin` é devolvido com o valor "OFF", ativar o binário registo ao editar o ficheiro de cnf então que `log_bin=ON` e reinicie o servidor para que a alteração tenha efeito.

2. Definições do servidor mestre

   A replicação de dados-in requer o parâmetro `lower_case_table_names` para ser consistente entre os servidores mestre e de réplica. Este parâmetro é 1 por predefinição na base de dados do Azure para MariaDB. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Criar uma nova função de replicação e configurar a permissão

   Crie uma conta de utilizador no servidor principal que está configurado com privilégios de replicação. Isso pode ser feito por meio de comandos SQL ou uma ferramenta como o MySQL Workbench. Considere se pretender replicar com SSL como isto terá de ser especificado quando criar o utilizador. Consulte a documentação da MariaDB para compreender como [adicionar contas de utilizador](https://mariadb.com/kb/en/library/create-user/) no seu servidor mestre. 

   Os comandos abaixo, a nova função de replicação criada é capaz de acessar o mestre de qualquer máquina, não apenas a máquina que aloja o mestre em si. Isso é feito ao especificar "syncuser\@'%'" no comando de utilizador de criar. Consulte a documentação da MariaDB para saber mais sobre [especificando os nomes das contas](https://mariadb.com/kb/en/library/create-user/#account-names).

   **Comando SQL**

   *Replicação com SSL*

   Para exigir SSL para todas as ligações de utilizador, utilize o seguinte comando para criar um utilizador: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replicação sem SSL*

   Se não é necessário para todas as ligações SSL, utilize o seguinte comando para criar um utilizador:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   Para criar a função de replicação no MySQL Workbench, abra a **utilizadores e os privilégios** painel da **gestão** painel. Em seguida, clique em **adicionar conta**. 
 
   ![Os utilizadores e privilégios](./media/howto-data-in-replication/users_privileges.png)

   Escreva o nome de utilizador para o **nome de início de sessão** campo. 

   ![Sincronizar o utilizador](./media/howto-data-in-replication/syncuser.png)
 
   Clique nas **funções administrativas** painel e, em seguida, selecione **subordinado de replicação** da lista de **privilégios Global**. Em seguida, clique em **aplicar** para criar a função de replicação.

   ![Replicação subordinado](./media/howto-data-in-replication/replicationslave.png)


4. Definir o servidor mestre para o modo só de leitura

   Antes de iniciar Despejar a base de dados, o servidor tem de ser colocadas em modo só de leitura. No modo só de leitura, o mestre será não é possível processar quaisquer transações de escrita. Avaliar o impacto para o seu negócio e agendar a janela de só de leitura num horário fora de pico, se necessário.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Obter o nome de ficheiro de registo binário e deslocamento

   Executar o [ `show master status` ](https://mariadb.com/kb/en/library/show-master-status/) comando para determinar o nome de ficheiro de registo binário atual e o deslocamento.
    
   ```sql
   show master status;
   ```
   Os resultados devem ser como se segue. Lembre-se de que tome nota do nome de ficheiro binário que será utilizada em passos posteriores.

   ![Resultados de estado do mestre](./media/howto-data-in-replication/masterstatus.png)
   
6. Obter a posição de GTID (opcional, necessário para replicação com GTID)

   Para executar a função [ `BINLOG_GTID_POS` ](https://mariadb.com/kb/en/library/binlog_gtid_pos/) comando para obter a posição de GTID para o nome de ficheiro de binlog correspond e o desvio.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-master-server"></a>Cópia de segurança e restaurar o servidor mestre

1. Despeja todas as bases de dados do servidor mestre

   Pode utilizar mysqldump para bases de dados de informação do seu principal. Para obter detalhes, consulte [capturar e restaurar](howto-migrate-dump-restore.md). Não é necessário para a biblioteca MySQL de cópia de segurança e a biblioteca de teste.

2. Servidor mestre do conjunto de modo de leitura/escrita

   Assim que a base de dados foram colocado, alterar o mestre MariaDB back de servidor para o modo de leitura/escrita.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Restaurar o ficheiro de informação para novo servidor

   Restaure o ficheiro de informação para o servidor criado na base de dados do Azure para MariaDB serviço. Consulte a [capturar e restaurar](howto-migrate-dump-restore.md) para saber como restaurar um arquivo de despejo para um servidor de MariaDB. Se o ficheiro de informação for grande, carregue-a uma máquina virtual no Azure na mesma região que o seu servidor de réplica. Restaurá-la para a base de dados do Azure para MariaDB server da máquina virtual.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Ligar servidores mestre e de réplica para iniciar a replicação de dados

1. Servidor mestre do conjunto

   Todas as funções de replicação de dados-in são efetuadas pelo procedimentos armazenados. Pode encontrar todos os procedimentos em [dados na replicação procedimentos armazenados](reference-data-in-stored-procedures.md). Os procedimentos armazenados podem ser executados no shell do MySQL ou o MySQL Workbench.

   Para ligar os dois servidores e iniciar a replicação, inicie sessão no servidor de réplica de destino no DB do Azure para MariaDB serviço e definir a instância externa como o servidor mestre. Isso é feito utilizando o `mysql.az_replication_change_master` ou `mysql.az_replication_change_master_with_gtid` procedimento armazenado no banco de dados do Azure para MariaDB server.

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
   - master_ssl_ca: Contexto de certificado de AC. Se não utilizar SSL, passe na cadeia de caracteres vazia.
       - Recomenda-se para transmitir este parâmetro no como uma variável. Veja os exemplos seguintes para obter mais informações.

   **Exemplos**

   *Replicação com SSL*

   A variável `@cert` é criada executando os seguintes comandos: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   Replicação com o SSL é configurada entre um servidor mestre hospedado no domínio "companya.com" e um servidor de réplica alojado na base de dados do Azure para MariaDB. Este procedimento armazenado é executado na réplica. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
   ```
   *Replicação sem SSL*

   Replicação sem SSL é configurada entre um servidor mestre hospedado no domínio "companya.com" e um servidor de réplica alojado na base de dados do Azure para MariaDB. Este procedimento armazenado é executado na réplica.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
   ```

2. Iniciar a replicação

   Chamar o `mysql.az_replication_start` armazenados procedimento para iniciar a replicação.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Verificar estado de replicação

   Chamar o [ `show slave status` ](https://mariadb.com/kb/en/library/show-slave-status/) comando no servidor de réplica para ver o estado de replicação.
    
   ```sql
   show slave status;
   ```

   Se o estado dos `Slave_IO_Running` e `Slave_SQL_Running` são "Sim" e o valor de `Seconds_Behind_Master` é "0", a replicação está a funcionar bem. `Seconds_Behind_Master` Indica a réplica como tardia é. Se o valor não é "0", significa que a réplica está a processar atualizações. 

4. Atualização correspondem variáveis de servidor para tornar os dados na replicação mais segura (apenas necessária para a replicação sem GTID)
    
    Devido à limitação de replicação nativo MariaDB, terá de configuração [ `sync_master_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) e [ `sync_relay_log_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) variáveis em replicação sem o cenário de GTID. Podemos recommand verificar o seu servidor de subordinado `sync_master_info` e `sync_relay_log_info` variáveis e alterá-los utras `1` se pretender certificar-se de que a replicação de dados-in esteja estável.
    
## <a name="other-stored-procedures"></a>Outros procedimentos armazenados

### <a name="stop-replication"></a>Parar a replicação

Para parar a replicação entre o servidor mestre e de réplica, utilize o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Remova a relação de replicação

Para remover a relação entre o servidor mestre e de réplica, utilize o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Ignorar erro de replicação

Para ignorar um erro de replicação e permitir que a replicação continuar, utilize o seguinte procedimento armazenado:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [replicação de dados](concepts-data-in-replication.md) para base de dados do Azure para MariaDB.
