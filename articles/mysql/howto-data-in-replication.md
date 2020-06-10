---
title: Configure a replicação de dados - Base de Dados Azure para o MySQL
description: Este artigo descreve como configurar a replicação de dados para a base de dados Azure para o MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: b4bc57bd896eb8d250975ec8e9300d0498d70835
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84604159"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Como configurar a base de dados Azure para a replicação de dados do MySQL

Este artigo descreve como configurar a replicação de dados na Base de Dados Azure para o MySQL, configurando os servidores master e replica. Este artigo pressupõe que tem alguma experiência anterior com servidores e bases de dados do MySQL.

> [!NOTE]
> Comunicação sem preconceitos
>
> A Microsoft suporta um ambiente diversificado e inclusão. Este artigo contém referências à palavra _escravo._ O guia de estilo da Microsoft [para comunicação sem preconceitos](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) reconhece isto como uma palavra de exclusão. A palavra é usada neste artigo para consistência porque atualmente é a palavra que aparece no software. Quando o software for atualizado para remover a palavra, este artigo será atualizado para estar em alinhamento.
>

Para criar uma réplica na Base de Dados Azure para o serviço MySQL, a replicação de dados sincroniza os dados de um servidor MySQL no local, em máquinas virtuais (VMs) ou em serviços de base de dados em nuvem.

Reveja as [limitações e requisitos](concepts-data-in-replication.md#limitations-and-considerations) da replicação de data-in antes de executar os passos neste artigo.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Crie um servidor MySQL para ser usado como réplica

1. Criar uma nova Base de Dados Azure para o servidor MySQL

   Criar um novo servidor MySQL (ex. "replica.mysql.database.azure.com"). Consulte a [Criação de uma Base de Dados Azure para o servidor MySQL utilizando o portal Azure](quickstart-create-mysql-server-database-using-azure-portal.md) para a criação de servidores. Este servidor é o servidor "replica" na replicação de dados.

   > [!IMPORTANT]
   > A Base de Dados Azure para o servidor MySQL deve ser criada nos níveis de preços otimizados para fins gerais ou memória.
   > 

2. Criar as mesmas contas de utilizador e privilégios correspondentes

   As contas do utilizador não são replicadas do servidor principal para o servidor de réplica. Se planeia fornecer aos utilizadores acesso ao servidor de réplicas, tem de criar manualmente todas as contas e privilégios correspondentes neste recém-criado servidor Azure Database para o servidor MySQL.

3. Adicione o endereço IP do servidor principal às regras de firewall da réplica. 

   Atualize as regras de firewall com o [portal do Azure](howto-manage-firewall-using-portal.md) ou a [CLI do Azure](howto-manage-firewall-using-cli.md).

## <a name="configure-the-master-server"></a>Configure o servidor principal
Os passos seguintes preparam e configuram o servidor MySQL alojado no local, numa máquina virtual ou serviço de base de dados hospedado por outros fornecedores de nuvem para replicação de dados. Este servidor é o "mestre" na replicação de datas.


1. Reveja os [requisitos](concepts-data-in-replication.md#requirements) do servidor principal antes de prosseguir. 

   Por exemplo, certifique-se de que o servidor principal permite o tráfego de entrada e saída na porta 3306 e que o servidor principal tem um **endereço IP público,** o DNS é acessível ao público ou tem um nome de domínio totalmente qualificado (FQDN). 
   
   Teste a conectividade ao servidor principal tentando ligar a partir de uma ferramenta como a linha de comando MySQL hospedada em outra máquina ou a partir da [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) disponível no portal Azure.

2. Ligue a exploração madeireira binária

   Verifique se a exploração madeireira binária foi ativada no comandante executando o seguinte comando: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se a variável [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) for devolvida com o valor "ON", a sessão binária está ativada no seu servidor. 

   Se `log_bin` for devolvido com o valor "OFF", ligue a sessão binária editando o seu ficheiro my.cnf de modo a que e `log_bin=ON` reinicie o seu servidor para que a alteração entre em vigor.

3. Definições do servidor principal

   A replicação de dados requer que o parâmetro `lower_case_table_names` seja consistente entre os servidores mestre e réplica. Este parâmetro é 1 por padrão na Base de Dados Azure para o MySQL. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. Criar uma nova função de replicação e configurar a permissão

   Crie uma conta de utilizador no servidor principal que esteja configurada com privilégios de replicação. Isto pode ser feito através de comandos SQL ou de uma ferramenta como a MySQL Workbench. Considere se planeia replicar com SSL uma vez que isso terá de ser especificado ao criar o utilizador. Consulte a documentação do MySQL para entender como [adicionar contas](https://dev.mysql.com/doc/refman/5.7/en/user-names.html) de utilizador no seu servidor principal. 

   Nos comandos abaixo, a nova função de replicação criada é capaz de aceder ao mestre a partir de qualquer máquina, e não apenas a máquina que acolhe o próprio mestre. Isto é feito especificando "syncuser@'%'" no comando do utilizador create. Consulte a documentação do MySQL para saber mais sobre [a especificação dos nomes das contas.](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)

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

   Para criar o papel de replicação na bancada mySQL Workbench, abra o painel **de Utilizadores e Privilégios** do painel de **Gestão.** Em seguida, clique na **Conta Adicionar.** 
 
   ![Utilizadores e Privilégios](./media/howto-data-in-replication/users_privileges.png)

   Digite o nome de utilizador no campo Nome de Início de **Sessão.** 

   ![Utilizador sincronizado](./media/howto-data-in-replication/syncuser.png)
 
   Clique no painel **De Funções Administrativas** e, em seguida, selecione **Replication Slave** da lista de **Privilégios Globais**. Em seguida, clique em **Aplicar** para criar o papel de replicação.

   ![Escravo da Replicação](./media/howto-data-in-replication/replicationslave.png)


5. Desajuste o servidor principal para o modo apenas de leitura

   Antes de começar a despejar a base de dados, o servidor tem de ser colocado apenas no modo de leitura. Durante o modo apenas de leitura, o mestre não poderá processar quaisquer transações de escrita. Avalie o impacto para o seu negócio e agende a janela apenas de leitura num horário fora do pico, se necessário.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. Obtenha o nome do ficheiro de registo binário e offset

   Executar o [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) comando para determinar o nome do ficheiro de registo binário atual e compensar.
    
   ```sql
   show master status;
   ```
   Os resultados devem ser como seguir. Certifique-se de que regista o nome do ficheiro binário, uma vez que será utilizado em etapas posteriores.

   ![Resultados do Estado-Mestre](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>Despeje e restaure o servidor principal

1. Despeje todas as bases de dados do servidor principal

   Pode usar o meu "mysqldump" para despejar bases de dados do seu mestre. Para mais informações, consulte o [Dump & Restore](concepts-migrate-dump-restore.md). É desnecessário despejar a biblioteca MySQL e a biblioteca de testes.

2. Definir o servidor principal para o modo de leitura/escrita

   Uma vez que a base de dados tenha sido despejada, mude o servidor MySQL mestre de volta para o modo de leitura/escrita.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Restaurar o ficheiro de despejo para o novo servidor

   Restaurar o ficheiro de despejo para o servidor criado na Base de Dados Azure para o serviço MySQL. Consulte o [Dump & Restore](concepts-migrate-dump-restore.md) para saber como restaurar um ficheiro de despejo num servidor MySQL. Se o ficheiro de despejo for grande, faça o upload para uma máquina virtual em Azure dentro da mesma região que o seu servidor de réplica. Restaure-o para a Base de Dados Azure para o servidor MySQL a partir da máquina virtual.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Link master e servidores de réplica para iniciar a replicação de dados

1. Definir servidor principal

   Todas as funções de replicação de dados são feitas por procedimentos armazenados. Pode encontrar todos os procedimentos nos [procedimentos armazenados de replicação de dados.](reference-data-in-stored-procedures.md) Os procedimentos armazenados podem ser executados na concha MySQL ou na bancada MySQL Workbench. 

   Para ligar dois servidores e iniciar a replicação, inicie sessão no servidor de réplica alvo no DB Azure para o serviço MySQL e descreva a instância externa como o servidor principal. Isto é feito utilizando o `mysql.az_replication_change_master` procedimento armazenado no Azure DB para o servidor MySQL.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: nome de anfitrião do servidor principal
   - master_user: nome de utilizador para o servidor principal
   - master_password: palavra-passe para o servidor principal
   - master_log_file: nome de ficheiro de registo binário da execução`show master status`
   - master_log_pos: posição de log binário de correr`show master status`
   - master_ssl_ca: contexto do certificado de CA. Se não utilizar sSl, passe em corda vazia.
       - Recomenda-se passar este parâmetro como uma variável. Consulte os seguintes exemplos para obter mais informações.

> [!NOTE]
> Se o servidor principal estiver hospedado num VM Azure, desajuste "Permitir o acesso aos serviços Azure" a "ON" para permitir que os servidores de mestre e réplica se comuniquem entre si. Esta definição pode ser alterada a partir das opções de **segurança De Ligação.** Consulte para [gerir as regras de firewall usando o portal](howto-manage-firewall-using-portal.md) para obter mais informações.

   **Exemplos**

   *Replicação com SSL*

   A variável `@cert` é criada executando os seguintes comandos MySQL: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   A replicação com SSL é configurada entre um servidor principal alojado no domínio "companya.com" e um servidor de réplica alojado na Base de Dados Azure para o MySQL. Este procedimento armazenado é executado na réplica. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *Replicação sem SSL*

   A replicação sem SSL é configurada entre um servidor principal alojado no domínio "companya.com" e um servidor de réplica alojado na Base de Dados Azure para o MySQL. Este procedimento armazenado é executado na réplica.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

1. Iniciar a replicação

   Ligue para o `mysql.az_replication_start` procedimento armazenado para iniciar a replicação.

   ```sql
   CALL mysql.az_replication_start;
   ```

1. Verifique o estado da replicação

   Ligue para o [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) comando no servidor de réplica para ver o estado de replicação.
    
   ```sql
   show slave status;
   ```

   Se o estado `Slave_IO_Running` de e `Slave_SQL_Running` são "sim" e o valor de `Seconds_Behind_Master` "0", a replicação está a funcionar bem. `Seconds_Behind_Master`indica quão tarde é a réplica. Se o valor não for "0", significa que a réplica está a processar atualizações. 

## <a name="other-stored-procedures"></a>Outros procedimentos armazenados

### <a name="stop-replication"></a>Parar replicação

Para parar a replicação entre o servidor principal e o servidor de réplica, utilize o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Remover a relação de replicação

Para remover a relação entre o servidor principal e o servidor de réplicas, utilize o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Erro de replicação de skip

Para ignorar um erro de replicação e permitir a replicação, utilize o seguinte procedimento armazenado:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Próximos passos
- Saiba mais sobre [a replicação de dados](concepts-data-in-replication.md) para a base de dados Azure para o MySQL. 
