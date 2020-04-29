---
title: Configure replicação de dados - Base de Dados Azure para MySQL
description: Este artigo descreve como configurar a replicação de Dados para a Base de Dados Azure para o MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: 18c1d8b42dc73951901ec4ae9b79715ddbd47617
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80474037"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Como configurar a Base de Dados Azure para a Replicação de Dados MySQL

Este artigo descreve como configurar a Replicação de Dados na Base de Dados Azure para o MySQL configurando os servidores mestre e réplica. Este artigo assume que tem alguma experiência anterior com servidores e bases de dados MySQL.

Para criar uma réplica na Base de Dados Azure para o serviço MySQL, a Replicação data-in sincroniza os dados de um servidor MySQL mestre no local, em máquinas virtuais (VMs), ou em serviços de base de dados em nuvem.

Reveja as [limitações e requisitos](concepts-data-in-replication.md#limitations-and-considerations) da replicação data-in antes de executar os passos neste artigo.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Criar um servidor MySQL para ser usado como réplica

1. Criar uma nova Base de Dados Azure para servidor MySQL

   Crie um novo servidor MySQL (ex. "replica.mysql.database.azure.com"). Consulte a [Create a Azure Database para o servidor MySQL utilizando o portal Azure](quickstart-create-mysql-server-database-using-azure-portal.md) para a criação do servidor. Este servidor é o servidor "replica" na Replicação de Dados.

   > [!IMPORTANT]
   > A Base de Dados Azure para o servidor MySQL deve ser criada nos níveis de preços otimizados para fins gerais ou memória.
   > 

2. Criar as mesmas contas de utilizador e privilégios correspondentes

   As contas de utilizador não são replicadas do servidor principal para o servidor de réplicas. Se planeia fornecer aos utilizadores acesso ao servidor de réplicas, precisa de criar manualmente todas as contas e privilégios correspondentes neste recém-criado Servidor Debase Azure para o Servidor MySQL.

3. Adicione o endereço IP do servidor principal às regras de firewall da réplica. 

   Atualize as regras de firewall com o [portal do Azure](howto-manage-firewall-using-portal.md) ou a [CLI do Azure](howto-manage-firewall-using-cli.md).

## <a name="configure-the-master-server"></a>Configure o servidor principal
Os seguintes passos preparam e configuram o servidor MySQL hospedado no local, numa máquina virtual ou serviço de base de dados hospedado por outros fornecedores de nuvem para replicação de dados. Este servidor é o "mestre" na replicação de Dados.


1. Reveja os [requisitos do servidor principal](concepts-data-in-replication.md#requirements) antes de prosseguir. 

   Por exemplo, certifique-se de que o servidor principal permite o tráfego de entrada e saída na porta 3306 e que o servidor principal tem um **endereço IP público,** o DNS é acessível ao público ou tem um nome de domínio totalmente qualificado (FQDN). 
   
   Teste a conectividade com o servidor principal tentando ligar-se a partir de uma ferramenta como a linha de comando MySQL alojada noutra máquina ou a partir da [Casca de Nuvem Azure](https://docs.microsoft.com/azure/cloud-shell/overview) disponível no portal Azure.

2. Ligue a exploração madeireira binária

   Verifique se a exploração de madeira binária foi ativada no mestre, executando o seguinte comando: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se a [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) variável for devolvida com o valor "ON", o registo binário está ativado no seu servidor. 

   Se `log_bin` for devolvido com o valor "OFF", ligue a exploração de registobinário editando o seu ficheiro my.cnf de modo a que `log_bin=ON` e reinicie o seu servidor para que a alteração faça efeito.

3. Definições de servidor principal

   A replicação de dados `lower_case_table_names` requer que o parâmetro seja consistente entre os servidores mestre e réplica. Este parâmetro é 1 por padrão na Base de Dados Azure para MySQL. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. Criar um novo papel de replicação e configurar permissão

   Crie uma conta de utilizador no servidor principal que esteja configurada com privilégios de replicação. Isto pode ser feito através de comandos SQL ou de uma ferramenta como a bancada mySQL. Considere se planeia replicar com o SSL, uma vez que esta terá de ser especificada na criação do utilizador. Consulte a documentação mySQL para entender como [adicionar contas](https://dev.mysql.com/doc/refman/5.7/en/user-names.html) de utilizador no seu servidor principal. 

   Nos comandos abaixo, a nova função de replicação criada é capaz de aceder ao mestre a partir de qualquer máquina, e não apenas da máquina que acolhe o próprio mestre. Isto é feito especificando "syncuser@'%'" no comando do utilizador. Consulte a documentação mySQL para saber mais sobre [especificar nomes de conta](https://dev.mysql.com/doc/refman/5.7/en/account-names.html).

   **Comando SQL**

   *Replicação com SSL*

   Para exigir sSL para todas as ligações do utilizador, utilize o seguinte comando para criar um utilizador: 

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

   Para criar a função de replicação na Bancada de Trabalho MySQL, abra o painel **Utilizadores e Privilégios** do painel **de Gestão.** Em seguida, clique na **Conta Adicionar**. 
 
   ![Utilizadores e Privilégios](./media/howto-data-in-replication/users_privileges.png)

   Digite o nome de utilizador no campo **'Nome de login'.** 

   ![Utilizador sincronizado](./media/howto-data-in-replication/syncuser.png)
 
   Clique no painel **De Papéções Administrativas** e, em seguida, selecione **Replication Slave** da lista de **Privilégios Globais**. Em seguida, clique em **Aplicar** para criar a função de replicação.

   ![Escravo da Replicação](./media/howto-data-in-replication/replicationslave.png)


5. Desajuste o servidor principal para o modo de leitura

   Antes de começar a despejar a base de dados, o servidor precisa de ser colocado no modo de leitura. Enquanto está em modo de leitura, o mestre será incapaz de processar quaisquer transações de escrita. Avalie o impacto para o seu negócio e agende a janela apenas para leitura em tempo fora do pico, se necessário.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. Obtenha o nome de ficheiro de registo binário e offset

   Executar [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) o comando para determinar o nome de ficheiro binário atual e offset.
    
   ```sql
   show master status;
   ```
   Os resultados devem ser como seguir. Certifique-se de que nota o nome do ficheiro binário, uma vez que será utilizado em etapas posteriores.

   ![Resultados do Estado Mestre](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>Despejar e restaurar o servidor principal

1. Despeje todas as bases de dados do servidor principal

   Podes usar o mysqldump para despejar bases de dados do teu mestre. Para mais detalhes, consulte o [Dump & Restore](concepts-migrate-dump-restore.md). É desnecessário despejar a biblioteca e biblioteca mySQL.

2. Detete o servidor principal para ler/escrever o modo

   Uma vez que a base de dados tenha sido despejada, mude o servidor Master MySQL de volta para o modo de leitura/escrita.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Restaurar o ficheiro de despejo para o novo servidor

   Restaure o ficheiro de despejo do servidor criado na Base de Dados Azure para o serviço MySQL. Consulte o [Dump & Restaurar](concepts-migrate-dump-restore.md) para como restaurar um ficheiro de despejo para um servidor MySQL. Se o ficheiro de despejo for grande, carregue-o para uma máquina virtual em Azure dentro da mesma região que o seu servidor de réplicas. Restaurá-lo na Base de Dados Azure para o servidor MySQL a partir da máquina virtual.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Link master e servidores de réplica para iniciar replicação de Dados

1. Definir servidor principal

   Todas as funções de replicação de Dados são feitas por procedimentos armazenados. Pode encontrar todos os procedimentos nos [Procedimentos Armazenados de Replicação de Dados](reference-data-in-stored-procedures.md). Os procedimentos armazenados podem ser executados na concha MySQL ou na bancada de trabalho MySQL. 

   Para ligar dois servidores e iniciar a replicação, inicie o login no servidor de réplica do alvo no serviço Azure DB para o serviço MySQL e detetete a instância externa como o servidor principal. Isto é feito `mysql.az_replication_change_master` utilizando o procedimento armazenado no Azure DB para o servidor MySQL.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: nome de anfitrião do servidor principal
   - master_user: nome de utilizador para o servidor principal
   - master_password: senha para o servidor principal
   - master_log_file: nome de ficheiro de registo binário de execução`show master status`
   - master_log_pos: posição de registo binário de execução`show master status`
   - master_ssl_ca: contexto do certificado CA. Se não utilizar o SSL, passe com uma corda vazia.
       - Recomenda-se passar este parâmetro como variável. Consulte os seguintes exemplos para obter mais informações.

> [!NOTE]
> Se o servidor principal estiver hospedado num VM Azure, desempenhe "Permitir o acesso aos serviços Azure" para "ON" para permitir que os servidores mestres e réplicas se comuniquem entre si. Esta definição pode ser alterada a partir das opções de segurança da **Ligação.** Consulte a [gestão](howto-manage-firewall-using-portal.md) das regras de firewall utilizando o portal para obter mais informações.

   **Exemplos**

   *Replicação com SSL*

   A `@cert` variável é criada executando os seguintes comandos MySQL: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   A replicação com SSL é configurada entre um servidor principal hospedado no domínio "companya.com" e um servidor de réplica hospedado na Base de Dados Azure para MySQL. Este procedimento armazenado é executado na réplica. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *Replicação sem SSL*

   A replicação sem SSL é configurada entre um servidor principal hospedado no domínio "companya.com" e um servidor de réplica hospedado na Base de Dados Azure para MySQL. Este procedimento armazenado é executado na réplica.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

1. Iniciar a replicação

   Ligue `mysql.az_replication_start` para o procedimento armazenado para iniciar a replicação.

   ```sql
   CALL mysql.az_replication_start;
   ```

1. Verifique o estado da replicação

   Ligue [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) para o comando do servidor de réplica para ver o estado de replicação.
    
   ```sql
   show slave status;
   ```

   Se o `Slave_IO_Running` estado `Slave_SQL_Running` e o "sim" `Seconds_Behind_Master` e o valor de "0", a replicação está a funcionar bem. `Seconds_Behind_Master`indica a hora da réplica. Se o valor não for "0", significa que a réplica está a processar atualizações. 

## <a name="other-stored-procedures"></a>Outros procedimentos armazenados

### <a name="stop-replication"></a>Parar replicação

Para parar a replicação entre o servidor principal e o servidor de réplicas, utilize o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Remover a relação de replicação

Para remover a relação entre o servidor mestre e o servidor de réplicas, utilize o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Erro de replicação de salto

Para saltar um erro de replicação e permitir que a replicação prossiga, utilize o seguinte procedimento armazenado:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [a replicação de Dados](concepts-data-in-replication.md) para base de dados Azure para MySQL. 
