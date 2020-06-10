---
title: Configure replicação de dados - Base de dados Azure para MariaDB
description: Este artigo descreve como configurar a replicação de dados na Base de Dados Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/30/2020
ms.openlocfilehash: 5549f9eaf2bc44dfa7e99df04fd7864dd4b655ce
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84610904"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Configure a replicação de dados na base de dados Azure para MariaDB

Este artigo descreve como configurar a replicação de dados na Base de Dados Azure para MariaDB configurando os servidores mestre e réplica. Este artigo pressupõe que você tem alguma experiência anterior com servidores e bases de dados MariaDB.

Para criar uma réplica na Base de Dados Azure para o serviço MariaDB, a replicação de dados sincroniza os dados de um servidor MariaDB no local, em máquinas virtuais (VMs) ou em serviços de base de dados em nuvem.

Reveja as [limitações e requisitos](concepts-data-in-replication.md#limitations-and-considerations) da replicação de data-in antes de executar os passos neste artigo.

> [!NOTE]
> Se o seu servidor principal for a versão 10.2 ou mais recente, recomendamos que crie a Replicação de Dados utilizando [o ID de Transação Global](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Crie um servidor MariaDB para usar como réplica

1. Crie uma nova Base de Dados Azure para o servidor MariaDB (por exemplo, replica.mariadb.database.azure.com). O servidor é o servidor de réplica na replicação de dados.

    Para saber mais sobre a criação de servidores, consulte [criar uma Base de Dados Azure para servidor MariaDB utilizando o portal Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > Tem de criar a Base de Dados Azure para o servidor MariaDB nos níveis de preços otimizados para fins gerais ou memória.

2. Crie contas de utilizador idênticas e privilégios correspondentes.
    
    As contas dos utilizadores não são replicadas do servidor principal para o servidor de réplicas. Para fornecer acesso do utilizador ao servidor de réplicas, tem de criar manualmente todas as contas e privilégios correspondentes na recém-criada Base de Dados Azure para o servidor MariaDB.

3. Adicione o endereço IP do servidor principal às regras de firewall da réplica. 

   Atualize as regras de firewall com o [portal do Azure](howto-manage-firewall-portal.md) ou a [CLI do Azure](howto-manage-firewall-cli.md).

> [!NOTE]
> Comunicação sem preconceitos
>
> A Microsoft suporta um ambiente diversificado e inclusão. Este artigo contém referências à palavra _escravo._ O guia de estilo da Microsoft [para comunicação sem preconceitos](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) reconhece isto como uma palavra de exclusão. A palavra é usada neste artigo para consistência porque atualmente é a palavra que aparece no software. Quando o software for atualizado para remover a palavra, este artigo será atualizado para estar em alinhamento.
>

## <a name="configure-the-master-server"></a>Configure o servidor principal

Os passos seguintes preparam e configuram o servidor MariaDB alojado no local, num VM ou num serviço de base de dados em nuvem para replicação de dados. O servidor MariaDB é o mestre na replicação de dados.

1. Reveja os [requisitos](concepts-data-in-replication.md#requirements) do servidor principal antes de prosseguir. 

   Por exemplo, certifique-se de que o servidor principal permite o tráfego de entrada e saída na porta 3306 e que o servidor principal tem um **endereço IP público,** o DNS é acessível ao público ou tem um nome de domínio totalmente qualificado (FQDN). 
   
   Teste a conectividade ao servidor principal tentando ligar a partir de uma ferramenta como a linha de comando MySQL hospedada em outra máquina ou a partir da [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) disponível no portal Azure.

2. Ligue a exploração madeireira binária.
    
    Para ver se a exploração madeireira binária está ativada no mestre, insira o seguinte comando:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se a variável [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) devolver o `ON` valor, a sessão de registo binário está ativada no seu servidor.

   Se `log_bin` devolver o `OFF` valor, edite o ficheiro **my.cnf** de modo a ligar `log_bin=ON` a registo binário. Reinicie o servidor para que a alteração faça efeito.

3. Configurar as definições do servidor principal.

    A replicação de dados requer que o parâmetro `lower_case_table_names` seja consistente entre os servidores mestre e réplica. O `lower_case_table_names` parâmetro é definido por `1` padrão na Base de Dados Azure para MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. Crie uma nova função de replicação e crie permissões.

   Crie uma conta de utilizador no servidor principal que esteja configurada com privilégios de replicação. Pode criar uma conta utilizando comandos SQL ou bancada MySQL Workbench. Se pretender replicar-se com SSL, tem de especificar isto quando criar a conta de utilizador.
   
   Para aprender a adicionar contas de utilizador no seu servidor principal, consulte a [documentação MariaDB](https://mariadb.com/kb/en/library/create-user/).

   Utilizando os seguintes comandos, a nova função de replicação pode aceder ao mestre a partir de qualquer máquina, e não apenas à máquina que acolhe o próprio mestre. Para este acesso, especifique o **sincronizador \@ '%'** no comando para criar um utilizador.
   
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


5. Desajuste o servidor principal para o modo apenas de leitura.

   Antes de despejar uma base de dados, o servidor deve ser colocado apenas no modo de leitura. Enquanto está em modo apenas de leitura, o mestre não pode processar transações de escrita. Para ajudar a evitar o impacto do negócio, agende a janela apenas de leitura durante um período fora do pico.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. Obtenha o nome do ficheiro de registo binário atual e offset.

   Para determinar o nome do ficheiro de registo binário atual e o offset, executar o comando [`show master status`](https://mariadb.com/kb/en/library/show-master-status/) .
    
   ```sql
   show master status;
   ```
   Os resultados devem ser semelhantes ao quadro seguinte:
   
   ![Resultados do Estado-Mestre](./media/howto-data-in-replication/masterstatus.png)

   Note o nome do ficheiro binário, porque será usado em passos posteriores.
   
7. Obtenha a posição GTID (opcional, necessária para a replicação com GTID).

   Executar a função [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) para obter a posição GTID para o nome de ficheiro binlog correspondente e compensar.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>Despeje e restaure o servidor principal

1. Despeje todas as bases de dados do servidor principal.

   Use o mysqldump para despejar todas as bases de dados do servidor principal. Não é necessário despejar a biblioteca MySQL e a biblioteca de testes.

    Para mais informações, consulte [o Despejo e o restauro.](howto-migrate-dump-restore.md)

2. Descreva o servidor principal para o modo de leitura/escrita.

   Depois de a base de dados ter sido despejada, mude o servidor MariaDB mestre de volta para o modo de leitura/escrita.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Restaurar o ficheiro de despejo para o novo servidor.

   Restaurar o ficheiro de despejo para o servidor criado na Base de Dados Azure para o serviço MariaDB. Consulte [o Dump & Restaurar](howto-migrate-dump-restore.md) como restaurar um ficheiro de despejo num servidor MariaDB.

   Se o ficheiro de despejo for grande, faça o upload para um VM em Azure dentro da mesma região que o seu servidor de réplica. Restaure-o para a Base de Dados Azure para o servidor MariaDB a partir do VM.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>Ligue os servidores master e replica para iniciar a replicação de dados

1. Desemomine o servidor principal.

   Todas as funções de replicação de dados são feitas por procedimentos armazenados. Pode encontrar todos os procedimentos nos [procedimentos armazenados de replicação de dados.](reference-data-in-stored-procedures.md) Os procedimentos armazenados podem ser executados na concha MySQL ou na bancada MySQL Workbench.

   Para ligar dois servidores e iniciar a replicação, inicie sedumento no servidor de réplica alvo no DB Azure para o serviço MariaDB. Em seguida, descreva a instância externa como o servidor principal utilizando o `mysql.az_replication_change_master` procedimento ou o procedimento armazenado no `mysql.az_replication_change_master_with_gtid` Azure DB para o servidor MariaDB.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   ou
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: nome de anfitrião do servidor principal
   - master_user: nome de utilizador para o servidor principal
   - master_password: palavra-passe para o servidor principal
   - master_log_file: nome de ficheiro de registo binário da execução`show master status`
   - master_log_pos: posição de log binário de correr`show master status`
   - master_gtid_pos: posição GTID de correr`select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
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

       A replicação com SSL é configurada entre um servidor principal alojado no domínio companya.com, e um servidor de réplica hospedado na Base de Dados Azure para MariaDB. Este procedimento armazenado é executado na réplica.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Replicação sem SSL

       A replicação sem SSL é configurada entre um servidor principal alojado no domínio companya.com, e um servidor de réplica alojado na Base de Dados Azure para MariaDB. Este procedimento armazenado é executado na réplica.

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

   Se `Slave_IO_Running` e estiver no estado , e o valor do é , a `Slave_SQL_Running` `yes` `Seconds_Behind_Master` `0` replicação está a funcionar. `Seconds_Behind_Master`indica quão tarde é a réplica. Se o valor não `0` for, então a réplica está a processar atualizações.

4. Atualize as variáveis de servidor correspondentes para tornar a replicação de dados mais segura (necessária apenas para replicação sem GTID).
    
    Devido a uma limitação de replicação nativa em MariaDB, você deve definir [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) e [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) variáveis na replicação sem o cenário GTID.

    Verifique as variáveis e variáveis do seu servidor `sync_master_info` `sync_relay_log_info` de escravos para se certificar de que a replicação de dados é estável e dedifica as variáveis para `1` .
    
## <a name="other-stored-procedures"></a>Outros procedimentos armazenados

### <a name="stop-replication"></a>Parar replicação

Para parar a replicação entre o servidor principal e o servidor de réplica, utilize o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Remover a relação de replicação

Para remover a relação entre o servidor principal e o servidor de réplica, utilize o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Ignore o erro de replicação

Para ignorar um erro de replicação e permitir a replicação, utilize o seguinte procedimento armazenado:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre [a replicação de dados](concepts-data-in-replication.md) para a base de dados Azure para MariaDB.
