---
title: Configure replicação de dados - Base de dados Azure para MariaDB
description: Este artigo descreve como configurar a replicação de dados na Base de Dados Azure para MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 01/18/2021
ms.openlocfilehash: 67e4da13d6954342b9979eb57a35c812cb63bb3e
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539988"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Configure a replicação de dados na base de dados Azure para MariaDB

Este artigo descreve como configurar a [replicação de dados na](concepts-data-in-replication.md) Base de Dados Azure para MariaDB configurando os servidores de origem e réplica. Este artigo pressupõe que você tem alguma experiência anterior com servidores e bases de dados MariaDB.

Para criar uma réplica na Base de Dados Azure para o serviço MariaDB, [a replicação de dados](concepts-data-in-replication.md) sincroniza os dados de um servidor MariaDB de origem no local, em máquinas virtuais (VMs) ou em serviços de base de dados em nuvem. A Replicação de Dados de Entrada é baseada na replicação baseada na posição dos ficheiros de registo binário (binlog) nativo para o MariaDB. Para saber mais sobre a replicação do binlog, consulte a [visão geral](https://mariadb.com/kb/en/library/replication-overview/)da replicação do binlog .

Reveja as [limitações e requisitos](concepts-data-in-replication.md#limitations-and-considerations) da replicação de data-in antes de executar os passos neste artigo.

> [!NOTE]
> Se o seu servidor de origem for a versão 10.2 ou mais recente, recomendamos que crie a Replicação de Dados utilizando [o ID de Transação Global](https://mariadb.com/kb/en/library/gtid/).

> [!NOTE]
> Este artigo contém referências ao termo _escravo_, um termo que a Microsoft já não utiliza. Quando o termo for removido do software, vamos removê-lo deste artigo.

## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Crie um servidor MariaDB para usar como réplica

1. Crie uma nova Base de Dados Azure para o servidor MariaDB (por exemplo, replica.mariadb.database.azure.com). O servidor é o servidor de réplica na replicação de dados.

    Para saber mais sobre a criação de servidores, consulte [criar uma Base de Dados Azure para servidor MariaDB utilizando o portal Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > Tem de criar a Base de Dados Azure para o servidor MariaDB nos níveis de preços otimizados para fins gerais ou memória.

2. Crie contas de utilizador idênticas e privilégios correspondentes.

    As contas dos utilizadores não são replicadas do servidor de origem para o servidor de réplicas. Para fornecer acesso do utilizador ao servidor de réplicas, tem de criar manualmente todas as contas e privilégios correspondentes na recém-criada Base de Dados Azure para o servidor MariaDB.

3. Adicione o endereço IP do servidor de origem às regras de firewall da réplica. 

   Atualize as regras de firewall com o [portal do Azure](howto-manage-firewall-portal.md) ou a [CLI do Azure](howto-manage-firewall-cli.md).

## <a name="configure-the-source-server"></a>Configure o servidor de origem

Os passos seguintes preparam e configuram o servidor MariaDB alojado no local, num VM ou num serviço de base de dados em nuvem para replicação de dados. O servidor MariaDB é a fonte na Replicação de Dados.

1. Reveja os [requisitos do servidor primário](concepts-data-in-replication.md#requirements) antes de prosseguir. 

2. Certifique-se de que o servidor de origem permite o tráfego de entrada e saída na porta 3306 e que o servidor de origem tem um **endereço IP público,** o DNS é acessível ao público ou tem um nome de domínio totalmente qualificado (FQDN). 

   Teste a conectividade ao servidor de origem tentando ligar a partir de uma ferramenta como a linha de comando MySQL hospedada em outra máquina ou a partir da [Azure Cloud Shell](../cloud-shell/overview.md) disponível no portal Azure.

   Se a sua organização tiver políticas de segurança rigorosas e não permitir que todos os endereços IP no servidor de origem habiliem a comunicação do Azure ao seu servidor de origem, pode potencialmente utilizar o comando abaixo para determinar o endereço IP da sua Base de Dados Azure para o servidor MariaDB.

   1. Inscreva-se na sua Base de Dados Azure para MariaDB utilizando uma ferramenta como a linha de comando MySQL.
   2. Execute a consulta abaixo.

      ```bash
      mysql> SELECT @@global.redirect_server_host;
      ```

      Abaixo está alguma saída de amostra:

      ```bash
      +-----------------------------------------------------------+
      | @@global.redirect_server_host                             |
      +-----------------------------------------------------------+
      | e299ae56f000.tr1830.westus1-a.worker.database.windows.net |
       +-----------------------------------------------------------+
      ```

   3. Saia da linha de comando MySQL.
   4. Execute o seguinte no utilitário ping para obter o endereço IP.

      ```bash
      ping <output of step 2b>
      ```

      Por exemplo:

      ```bash
      C:\Users\testuser> ping e299ae56f000.tr1830.westus1-a.worker.database.windows.net
      Pinging tr1830.westus1-a.worker.database.windows.net (**11.11.111.111**) 56(84) bytes of data.
      ```

   5. Configure as regras de firewall do seu servidor de origem para incluir o endereço IP de saída do passo anterior na porta 3306.

   > [!NOTE]
   > Este endereço IP pode ser alterado devido a operações de manutenção/implantação. Este método de conectividade destina-se apenas a clientes que não têm dinheiro para permitir todo o endereço IP na porta 3306.

3. Ligue a exploração madeireira binária.

    Para ver se a exploração madeireira binária está ativada na primária, insira o seguinte comando:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se a variável [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) devolver o `ON` valor, a sessão de registo binário está ativada no seu servidor.

   Se `log_bin` devolver o `OFF` valor, edite o ficheiro **my.cnf** de modo a ligar `log_bin=ON` a registo binário. Reinicie o servidor para que a alteração faça efeito.

4. Configurar as definições do servidor de origem.

    A replicação de dados requer que o parâmetro `lower_case_table_names` seja consistente entre os servidores de origem e réplica. O `lower_case_table_names` parâmetro é definido por `1` padrão na Base de Dados Azure para MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

5. Crie uma nova função de replicação e crie permissões.

   Crie uma conta de utilizador no servidor de origem configurada com privilégios de replicação. Pode criar uma conta utilizando comandos SQL ou bancada MySQL Workbench. Se pretender replicar-se com SSL, tem de especificar isto quando criar a conta de utilizador.

   Para saber como adicionar contas de utilizador no seu servidor de origem, consulte a [documentação MariaDB](https://mariadb.com/kb/en/library/create-user/).

   Utilizando os seguintes comandos, a nova função de replicação pode aceder à fonte a partir de qualquer máquina, e não apenas à máquina que hospeda a própria fonte. Para este acesso, especifique o **sincronizador \@ '%'** no comando para criar um utilizador.

   Para saber mais sobre a documentação do MariaDB, consulte [especificar os nomes das contas.](https://mariadb.com/kb/en/library/create-user/#account-names)

   **Comando SQL**

   - Replicação com SSL

       Para exigir SSL para todas as ligações do utilizador, insira o seguinte comando para criar um utilizador:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replicação sem SSL

       Se o SSL não for necessário para todas as ligações, insira o seguinte comando para criar um utilizador:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Para criar o papel de replicação na bancada MySQL Workbench, no painel **de Gestão,** selecione **Utilizadores e Privilégios.** Em seguida, **selecione Adicionar Conta**.

   ![Utilizadores e Privilégios](./media/howto-data-in-replication/users_privileges.png)

   Introduza um nome de utilizador no campo Nome de Início de **Sessão.**

   ![Utilizador sincronizado](./media/howto-data-in-replication/syncuser.png)

   Selecione o painel **De Funções Administrativas** e, em seguida, na lista de **Privilégios Globais,** selecione **Replication Slave**. **Selecione Aplicar** para criar a função de replicação.

   ![Escravo da Replicação](./media/howto-data-in-replication/replicationslave.png)

6. Desajuste o servidor de origem para o modo apenas de leitura.

   Antes de despejar uma base de dados, o servidor deve ser colocado apenas no modo de leitura. No modo apenas de leitura, a fonte não pode processar quaisquer transações de escrita. Para ajudar a evitar o impacto do negócio, agende a janela apenas de leitura durante um período fora do pico.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

7. Obtenha o nome do ficheiro de registo binário atual e offset.

   Para determinar o nome do ficheiro de registo binário atual e o offset, executar o comando [`show master status`](https://mariadb.com/kb/en/library/show-master-status/) .

   ```sql
   show master status;
   ```

   Os resultados devem ser semelhantes ao quadro seguinte:

   ![Resultados do Estado-Mestre](./media/howto-data-in-replication/masterstatus.png)

   Note o nome do ficheiro binário, porque será usado em etapas posteriores.

8. Obtenha a posição GTID (opcional, necessária para a replicação com GTID).

   Executar a função [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) para obter a posição GTID para o nome de ficheiro binlog correspondente e compensar.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```

## <a name="dump-and-restore-the-source-server"></a>Despeje e restaure o servidor de origem

1. Despeje todas as bases de dados do servidor de origem.

   Use o mysqldump para despejar todas as bases de dados do servidor de origem. Não é necessário despejar a biblioteca MySQL e a biblioteca de testes.

    Para mais informações, consulte [o Despejo e o restauro.](howto-migrate-dump-restore.md)

2. Descreva o servidor de origem para o modo de leitura/escrita.

   Depois de a base de dados ter sido despejada, altere o servidor MariaDB de origem de volta para o modo de leitura/escrita.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Restaurar o ficheiro de despejo para o novo servidor.

   Restaurar o ficheiro de despejo para o servidor criado na Base de Dados Azure para o serviço MariaDB. Consulte [o Dump & Restaurar](howto-migrate-dump-restore.md) como restaurar um ficheiro de despejo num servidor MariaDB.

   Se o ficheiro de despejo for grande, faça o upload para um VM em Azure dentro da mesma região que o seu servidor de réplica. Restaure-o para a Base de Dados Azure para o servidor MariaDB a partir do VM.

## <a name="link-the-source-and-replica-servers-to-start-data-in-replication"></a>Ligue os servidores de origem e réplica para iniciar a replicação de dados

1. Desa parte do servidor de origem.

   Todas as funções de replicação de dados são feitas por procedimentos armazenados. Pode encontrar todos os procedimentos nos [procedimentos armazenados de replicação de dados.](reference-stored-procedures.md) Os procedimentos armazenados podem ser executados na concha MySQL ou na bancada MySQL Workbench.

   Para ligar dois servidores e iniciar a replicação, inicie sedumento no servidor de réplica alvo no DB Azure para o serviço MariaDB. Em seguida, descreva a instância externa como o servidor de origem utilizando o `mysql.az_replication_change_master` procedimento ou o procedimento armazenado no `mysql.az_replication_change_master_with_gtid` Azure DB para o servidor MariaDB.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   ou

   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: nome de anfitrião do servidor de origem
   - master_user: nome de utilizador para o servidor de origem
   - master_password: palavra-passe para o servidor de origem
   - master_log_file: nome de ficheiro de registo binário da execução `show master status`
   - master_log_pos: posição de log binário de correr `show master status`
   - master_gtid_pos: posição GTID de correr `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: contexto do certificado de CA. Se não estiver a usar SSL, passe numa corda vazia.*


    *Recomendamos a passagem do parâmetro master_ssl_ca como variável. Para mais informações, consulte os seguintes exemplos.

   **Exemplos**

   - Replicação com SSL

       Criar a variável `@cert` executando os seguintes comandos:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE\'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       A replicação com SSL é configurada entre um servidor de origem alojado no domínio companya.com, e um servidor de réplica alojado na Base de Dados Azure para MariaDB. Este procedimento armazenado é executado na réplica.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```

   - Replicação sem SSL

       A replicação sem SSL é configurada entre um servidor de origem alojado no domínio companya.com, e um servidor de réplica alojado na Base de Dados Azure para MariaDB. Este procedimento armazenado é executado na réplica.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Começar a replicação.

   Ligue para o `mysql.az_replication_start` procedimento armazenado para iniciar a replicação.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Verifique o estado da replicação.

   Ligue para o [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) comando no servidor de réplica para ver o estado de replicação.

   ```sql
   show slave status;
   ```

   Se `Slave_IO_Running` e estiver no estado , e o valor do é , a `Slave_SQL_Running` `yes` `Seconds_Behind_Master` `0` replicação está a funcionar. `Seconds_Behind_Master` indica quão tarde é a réplica. Se o valor não `0` for, então a réplica está a processar atualizações.

4. Atualize as variáveis de servidor correspondentes para tornar a replicação de dados mais segura (necessária apenas para replicação sem GTID).

    Devido a uma limitação de replicação nativa em MariaDB, você deve definir  [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) e [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) variáveis na replicação sem o cenário GTID.

    Verifique as variáveis e variáveis do seu servidor de réplica `sync_master_info` para se certificar de que a `sync_relay_log_info` replicação de dados é estável e dedifice as variáveis para `1` .

## <a name="other-stored-procedures"></a>Outros procedimentos armazenados

### <a name="stop-replication"></a>Parar replicação

Para parar a replicação entre o servidor de origem e réplica, utilize o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Remover a relação de replicação

Para remover a relação entre o servidor de origem e réplica, utilize o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Ignore o erro de replicação

Para ignorar um erro de replicação e permitir a replicação, utilize o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a replicação de dados](concepts-data-in-replication.md) para a base de dados Azure para MariaDB.
