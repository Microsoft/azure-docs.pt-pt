---
title: Configure Replicação de Dados - Base de Dados Azure para MySQL
description: Este artigo descreve como configurar a replicação de dados para a base de dados Azure para o MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 04/08/2021
ms.openlocfilehash: 5f418867a2f22a16304d16c8889fff9a27a37ab3
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312166"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Como configurar a base de dados Azure para a replicação de dados do MySQL

Este artigo descreve como configurar a [replicação de dados na](concepts-data-in-replication.md) Base de Dados Azure para o MySQL configurando os servidores de origem e réplica. Este artigo pressupõe que tem alguma experiência anterior com servidores e bases de dados do MySQL.

> [!NOTE]
> Este artigo contém referências ao termo _escravo_, um termo que a Microsoft já não utiliza. Quando o termo for removido do software, vamos removê-lo deste artigo.
>

Para criar uma réplica na Base de Dados Azure para o serviço MySQL, [a replicação de dados](concepts-data-in-replication.md) sincroniza os dados de uma fonte do servidor MySQL nas instalações, em máquinas virtuais (VMs) ou em serviços de base de dados em nuvem. A replicação de dados baseia-se na posição de registo binário (binlog) baseada na posição de ficheiro ou na replicação baseada em GTID nativa do MySQL. Para saber mais sobre a replicação do binlog, consulte a visão geral da [replicação do binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Reveja as [limitações e requisitos](concepts-data-in-replication.md#limitations-and-considerations) da replicação de data-in antes de executar os passos neste artigo.

## <a name="create-an-azure-database-for-mysql-single-server-instance-to-use-as-a-replica"></a>Crie uma base de dados Azure para a instância do MySQL Single Server para usar como uma réplica

1. Crie uma nova instância de Azure Database para MySQL Single Server (ex. "replica.mysql.database.azure.com"). Consulte a [Criação de uma Base de Dados Azure para o servidor MySQL utilizando o portal Azure](quickstart-create-mysql-server-database-using-azure-portal.md) para a criação de servidores. Este servidor é o servidor "replica" para replicação de dados.

   > [!IMPORTANT]
   > A Base de Dados Azure para o servidor MySQL deve ser criada nos níveis de preços otimizados para fins gerais ou memória, uma vez que a replicação de dados só é suportada nestes níveis.

2. Crie as mesmas contas de utilizador e privilégios correspondentes.

   As contas dos utilizadores não são replicadas do servidor de origem para o servidor de réplicas. Se planeia fornecer aos utilizadores acesso ao servidor de réplicas, tem de criar manualmente todas as contas e privilégios correspondentes neste recém-criado servidor Azure Database para o servidor MySQL.

3. Adicione o endereço IP do servidor de origem às regras de firewall da réplica.

   Atualize as regras de firewall com o [portal do Azure](howto-manage-firewall-using-portal.md) ou a [CLI do Azure](howto-manage-firewall-using-cli.md).

4. **Opcional** - Se pretender utilizar a [replicação baseada em GTID](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html) do servidor de origem para o servidor de réplicaS Azure Para o servidor de réplicas MySQL, terá de ativar os seguintes parâmetros do servidor na Base de Dados Azure para o servidor MySQL, tal como mostrado na imagem do portal abaixo:

   :::image type="content" source="./media/howto-data-in-replication/enable-gtid.png" alt-text="Ativar o GTID na Base de Dados Azure para o servidor MySQL":::

## <a name="configure-the-source-mysql-server"></a>Configure o servidor MySQL de origem

Os passos seguintes preparam e configuram o servidor MySQL alojado no local, numa máquina virtual ou serviço de base de dados hospedado por outros fornecedores de nuvem para replicação de dados. Este servidor é a "fonte" para a replicação de data-in.

1. Reveja os requisitos do [servidor de origem](concepts-data-in-replication.md#requirements) antes de prosseguir.

2. Certifique-se de que o servidor de origem permite o tráfego de entrada e saída na porta 3306, e que tem um **endereço IP público,** o DNS é acessível ao público ou que tem um nome de domínio totalmente qualificado (FQDN).

   Teste a conectividade ao servidor de origem tentando ligar a partir de uma ferramenta como a linha de comando MySQL hospedada em outra máquina ou a partir da [Azure Cloud Shell](../cloud-shell/overview.md) disponível no portal Azure.

   Se a sua organização tiver políticas de segurança rigorosas e não permitir que todos os endereços IP no servidor de origem habiliem a comunicação do Azure ao seu servidor de origem, pode potencialmente utilizar o comando abaixo para determinar o endereço IP do seu servidor MySQL.

   1. Inscreva-se na base de dados Azure para o servidor MySQL utilizando uma ferramenta como a linha de comando MySQL.

   2. Execute a seguinte consulta.

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
   4. Para obter o endereço IP, execute o seguinte comando no utilitário ping:

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

   Verifique se a exploração madeireira binária foi ativada na fonte executando o seguinte comando:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se a variável [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) for devolvida com o valor "ON", a sessão binária está ativada no seu servidor.

   Se `log_bin` for devolvido com o valor "OFF" e o seu servidor de origem estiver a funcionar no local ou em máquinas virtuais onde pode aceder ao ficheiro de configuração (my.cnf), pode seguir os passos abaixo:
   1. Localize o seu ficheiro de configuração MySQL (my.cnf) no servidor de origem. Por exemplo: /etc/my.cnf
   2. Abra o ficheiro de configuração para editá-lo e localize a secção **mysqld** no ficheiro.
   3. Na secção mysqld, adicione a seguinte linha:

       ```bash
       log-bin=mysql-bin.log
       ```

   4. Reinicie o servidor de origem MySQL para que as alterações produzam efeitos.
   5. Depois de reiniciar o servidor, verifique se a sessão binária é ativada executando a mesma consulta que antes:

      ```sql
      SHOW VARIABLES LIKE 'log_bin';
      ```

4. Configurar as definições do servidor de origem.

   A replicação de dados requer que o parâmetro `lower_case_table_names` seja consistente entre os servidores de origem e réplica. Este parâmetro é 1 por padrão na Base de Dados Azure para o MySQL.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

   **Opcional** - Se desejar utilizar a [replicação baseada em GTID,](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html)terá de verificar se o GTID está ativado no servidor de origem. Pode executar o comando seguinte contra o servidor MySQL da sua fonte para ver se gtid_mode está ON.

   ```sql
   show variables like 'gtid_mode';
   ```

   >[!IMPORTANT]
   > Todos os servidores têm gtid_mode definidos para o valor padrão OFF. Não é necessário ativar o GTID no servidor MySQL de origem especificamente para configurar a Replicação de Dados. Se o GTID já estiver ativado no servidor de origem, pode utilizar opcionalmente a replicação baseada em GTID para configurar a replicação de dados também com a Base de Dados Azure para o MySQL Single Server. Pode utilizar a replicação baseada em ficheiros para configurar a replicação de dados para todos os servidores, independentemente da configuração gitd_mode no servidor de origem.

5. Crie uma nova função de replicação e crie permissão.

   Crie uma conta de utilizador no servidor de origem que esteja configurada com privilégios de replicação. Isto pode ser feito através de comandos SQL ou de uma ferramenta como a bancada MySQL Workbench. Considere se planeia replicar com SSL, uma vez que isso terá de ser especificado ao criar o utilizador. Consulte a documentação do MySQL para entender como [adicionar contas](https://dev.mysql.com/doc/refman/5.7/en/user-names.html) de utilizador no seu servidor de origem.

   Nos seguintes comandos, a nova função de replicação criada pode aceder à fonte a partir de qualquer máquina, e não apenas à máquina que acolhe a própria fonte. Isto é feito especificando "syncuser@'%'" no comando do utilizador create. Consulte a documentação do MySQL para saber mais sobre [a especificação dos nomes das contas.](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)

   **Comando SQL**

   *Replicação com SSL*

   Para exigir SSL para todas as ligações do utilizador, utilize o seguinte comando para criar um utilizador:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replicação sem SSL*

   Se o SSL não for necessário para todas as ligações, utilize o seguinte comando para criar um utilizador:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   Para criar o papel de replicação na bancada mySQL Workbench, abra o painel **de Utilizadores e Privilégios** a partir do painel de **Gestão** e, em seguida, selecione **Add Account**.

   :::image type="content" source="./media/howto-data-in-replication/users_privileges.png" alt-text="Utilizadores e Privilégios":::

   Digite o nome de utilizador no campo Nome de Início de **Sessão.**

   :::image type="content" source="./media/howto-data-in-replication/syncuser.png" alt-text="Utilizador sincronizado":::

   Selecione o painel **De Funções Administrativas** e, em seguida, selecione **Replication Slave** da lista de **Privilégios Globais**. Em seguida, **selecione Aplicar** para criar a função de replicação.

   :::image type="content" source="./media/howto-data-in-replication/replicationslave.png" alt-text="Escravo da Replicação":::

6. Desajuste o servidor de origem para o modo apenas de leitura.

   Antes de começar a despejar a base de dados, o servidor tem de ser colocado apenas no modo de leitura. No modo apenas de leitura, a fonte não poderá processar quaisquer transações de escrita. Avalie o impacto para o seu negócio e agende a janela apenas de leitura num horário fora do pico, se necessário.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

7. Obtenha o nome do ficheiro de registo binário e offset.

   Executar o [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) comando para determinar o nome do ficheiro de registo binário atual e compensar.

   ```sql
    show master status;
   ```
   Os resultados devem ser semelhantes aos seguintes. Certifique-se de que regista o nome do ficheiro binário para utilização em etapas posteriores.

   :::image type="content" source="./media/howto-data-in-replication/masterstatus.png" alt-text="Resultados do Estado-Mestre":::

## <a name="dump-and-restore-the-source-server"></a>Despeje e restaure o servidor de origem

1. Determine quais bases de dados e tabelas pretende replicar na Base de Dados Azure para o MySQL e execute a lixeira a partir do servidor de origem.

    Pode usar o meu "mysqldump" para despejar bases de dados do seu servidor primário. Para mais informações, consulte o [Dump & Restore](concepts-migrate-dump-restore.md). É desnecessário despejar a biblioteca MySQL e a biblioteca de testes.

2. **Opcional** - Se desejar utilizar a [replicação à base de gtid,](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html)terá de identificar o GTID da última transação executada na primária. Pode utilizar o seguinte comando para observar o GTID da última transação executada no servidor principal.

   ```sql
   show global variables like 'gtid_executed';
   ```

3. Descreva o servidor de origem para o modo de leitura/escrita.

   Depois de a base de dados ter sido despejada, altere o servidor MySQL de origem de volta para o modo de leitura/escrita.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

4. Restaurar o ficheiro de despejo para o novo servidor.

   Restaurar o ficheiro de despejo para o servidor criado na Base de Dados Azure para o serviço MySQL. Consulte o [Dump & Restore](concepts-migrate-dump-restore.md) para saber como restaurar um ficheiro de despejo num servidor MySQL. Se o ficheiro de despejo for grande, faça o upload para uma máquina virtual em Azure dentro da mesma região que o seu servidor de réplica. Restaure-o para a Base de Dados Azure para o servidor MySQL a partir da máquina virtual.

5. **Opcional** - Note o GTID do servidor restaurado na Base de Dados Azure para o MySQL para garantir que é igual ao servidor primário. Pode utilizar o seguinte comando para observar o GTID do valor purgado GTID na Base de Dados Azure para o servidor de réplica MySQL. O valor da gtid_purged deve ser o mesmo que gtid_executed no mestrado anotado no passo 2 para a replicação baseada em GTID funcionar.

   ```sql
   show global variables like 'gtid_purged';
   ```

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>Link fonte e servidores de réplica para iniciar a replicação de dados

1. Desa parte do servidor de origem.

   Todas as funções de replicação de dados são feitas por procedimentos armazenados. Pode encontrar todos os procedimentos nos [procedimentos armazenados de replicação de dados.](./reference-stored-procedures.md) Os procedimentos armazenados podem ser executados na concha MySQL ou na bancada MySQL Workbench.

   Para ligar dois servidores e iniciar a replicação, inicie sessão no servidor de réplica alvo no DB Azure para o serviço MySQL e decreva a instância externa como o servidor de origem. Isto é feito utilizando o `mysql.az_replication_change_master` procedimento armazenado no Azure DB para o servidor MySQL.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', <master_port>, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   **Opcional** - Se desejar utilizar a [replicação à base de gtid,](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html)terá de utilizar o seguinte comando para ligar os dois servidores

    ```sql
   call mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', <master_port>, '<master_ssl_ca>');
   ```

   - master_host: nome de anfitrião do servidor de origem
   - master_user: nome de utilizador para o servidor de origem
   - master_password: palavra-passe para o servidor de origem
   - master_port: número de porta em que o servidor de origem está a ouvir as ligações. (3306 é a porta predefinido em que o MySQL está a ouvir)
   - master_log_file: nome de ficheiro de registo binário da execução `show master status`
   - master_log_pos: posição de log binário de correr `show master status`
   - master_ssl_ca: contexto do certificado de CA. Se não utilizar sSl, passe em corda vazia.

     É recomendado passar este parâmetro como uma variável. Para mais informações, consulte os seguintes exemplos.

   > [!NOTE]
   > Se o servidor de origem estiver hospedado num VM Azure, desajuste "Permitir o acesso aos serviços Azure" para "ON" para permitir que os servidores de origem e réplica se comuniquem entre si. Esta definição pode ser alterada a partir das opções de **segurança De Ligação.** Para obter mais informações, consulte [as regras de firewall utilizando o portal](howto-manage-firewall-using-portal.md) .

   **Exemplos**

   *Replicação com SSL*

   A variável `@cert` é criada executando os seguintes comandos MySQL:

      ```sql
      SET @cert = '-----BEGIN CERTIFICATE-----
      PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
      -----END CERTIFICATE-----'
      ```

   A replicação com SSL é configurada entre um servidor de origem alojado no domínio "companya.com" e um servidor de réplica alojado na Base de Dados Azure para o MySQL. Este procedimento armazenado é executado na réplica.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
      ```

   *Replicação sem SSL*

   A replicação sem SSL é configurada entre um servidor de origem alojado no domínio "companya.com" e um servidor de réplica alojado na Base de Dados Azure para o MySQL. Este procedimento armazenado é executado na réplica.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
      ```

2. Preparar a filtragem.

   Se pretender saltar a replicação de algumas tabelas do seu mestre, atualize o parâmetro do `replicate_wild_ignore_table` servidor no seu servidor de réplica. Pode fornecer mais de um padrão de mesa usando uma lista separada por vírgulas.

   Reveja a documentação do [MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table) para saber mais sobre este parâmetro.

   Para atualizar o parâmetro, pode utilizar o [portal Azure](howto-server-parameters.md) ou [O Azure CLI](howto-configure-server-parameters-using-cli.md).

3. Começar a replicação.

   Ligue para o `mysql.az_replication_start` procedimento armazenado para iniciar a replicação.

   ```sql
   CALL mysql.az_replication_start;
   ```

4. Verifique o estado da replicação.

   Ligue para o [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) comando no servidor de réplica para ver o estado de replicação.

   ```sql
   show slave status;
   ```

   Se o estado `Slave_IO_Running` de e `Slave_SQL_Running` são "sim" e o valor de `Seconds_Behind_Master` "0", a replicação está a funcionar bem. `Seconds_Behind_Master` indica quão tarde é a réplica. Se o valor não for "0", significa que a réplica está a processar atualizações.

## <a name="other-useful-stored-procedures-for-data-in-replication-operations"></a>Outros procedimentos úteis armazenados para operações de replicação de dados

### <a name="stop-replication"></a>Parar replicação

Para parar a replicação entre o servidor de origem e réplica, utilize o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Remover a relação de replicação

Para remover a relação entre o servidor de origem e réplica, utilize o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Erro de replicação de skip

Para ignorar um erro de replicação e permitir que a replicação continue, utilize o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_skip_counter;
```

 **Opcional** - Se pretender utilizar a [replicação à base de gtid,](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-gtids-concepts.html)utilize o seguinte procedimento armazenado para saltar uma transação

```sql
call mysql. az_replication_skip_gtid_transaction(‘<transaction_gtid>’)
```

O procedimento pode ignorar a transação para o GTID dado. Se o formato GTID não estiver certo ou a transação GTID já tiver sido executada, o procedimento deixará de ser executado. O GTID para uma transação pode ser determinado analisando o registo binário para verificar os eventos de transação. O MySQL fornece um [mysqlbinlog](https://dev.mysql.com/doc/refman/5.7/en/mysqlbinlog.html) utilitário para analisar registos binários e exibir os seus conteúdos em formato de texto, que pode ser usado para identificar GTID da transação.

Para saltar a próxima transação após a posição de replicação atual, utilize o seguinte comando para identificar o GTID da próxima transação, como mostrado abaixo.

```sql
SHOW BINLOG EVENTS [IN 'log_name'] [FROM pos][LIMIT [offset,] row_count]
```

  :::image type="content" source="./media/howto-data-in-replication/show-binary-log.png" alt-text="Mostrar resultados de registo binário":::

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a replicação de dados](concepts-data-in-replication.md) para a base de dados Azure para o MySQL.
