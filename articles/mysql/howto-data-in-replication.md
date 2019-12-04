---
title: Configurar o data-in Replication-banco de dados do Azure para MySQL
description: Este artigo descreve como configurar o Replicação de Dados para o banco de dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: eaebcf50084223e1c1f4df30294bece96cffda6d
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774301"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Como configurar o banco de dados do Azure para MySQL Replicação de Dados

Neste artigo, você aprenderá a configurar Replicação de Dados no banco de dados do Azure para o serviço MySQL Configurando os servidores mestre e de réplica. Replicação de Dados permite sincronizar dados de um servidor MySQL mestre em execução local, em máquinas virtuais ou em serviços de banco de dados hospedados por outros provedores de nuvem em uma réplica no serviço de banco de dados do Azure para MySQL. 

Este artigo pressupõe que você tenha pelo menos alguma experiência anterior com servidores e bancos de dados MySQL.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Criar um servidor MySQL a ser usado como réplica

1. Criar um novo banco de dados do Azure para servidor MySQL

   Criar um novo servidor MySQL (ex. "replica.mysql.database.azure.com"). Consulte [criar um banco de dados do Azure para servidor MySQL usando o portal do Azure para a](quickstart-create-mysql-server-database-using-azure-portal.md) criação do servidor. Este servidor é o servidor de "réplica" no Replicação de Dados.

   > [!IMPORTANT]
   > O banco de dados do Azure para servidor MySQL deve ser criado nos tipos de preço Uso Geral ou com otimização de memória.
   > 

2. Criar as mesmas contas de usuário e privilégios correspondentes

   As contas de usuário não são replicadas do servidor mestre para o servidor de réplica. Se você planeja fornecer aos usuários acesso ao servidor de réplica, você precisa criar manualmente todas as contas e privilégios correspondentes nesse banco de dados do Azure recém-criado para o servidor MySQL.

## <a name="configure-the-master-server"></a>Configurar o servidor mestre
As etapas a seguir preparam e configuram o servidor MySQL hospedado localmente, em uma máquina virtual ou em um serviço de banco de dados hospedado por outros provedores de nuvem para Replicação de Dados. Esse servidor é o "mestre" na replicação de dados. 

1. Ativar log binário

   Verifique se o log binário foi habilitado no mestre executando o seguinte comando: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se a variável [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) for retornada com o valor "on", o log binário será habilitado em seu servidor. 

   Se `log_bin` for retornado com o valor "OFF", ative o log binário editando o arquivo My. cnf para que `log_bin=ON` e reinicie o servidor para que a alteração entre em vigor.

2. Configurações do servidor mestre

   Replicação de Dados exige que o parâmetro `lower_case_table_names` seja consistente entre os servidores mestre e de réplica. Esse parâmetro é 1 por padrão no banco de dados do Azure para MySQL. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Criar uma nova função de replicação e configurar a permissão

   Crie uma conta de usuário no servidor mestre que está configurada com privilégios de replicação. Isso pode ser feito por meio de comandos SQL ou por uma ferramenta como o MySQL Workbench. Considere se você planeja replicar com SSL, pois isso deverá ser especificado ao criar o usuário. Consulte a documentação do MySQL para entender como [Adicionar contas de usuário](https://dev.mysql.com/doc/refman/5.7/en/adding-users.html) em seu servidor mestre. 

   Nos comandos abaixo, a nova função de replicação criada é capaz de acessar o mestre de qualquer computador, não apenas o computador que hospeda o próprio mestre. Isso é feito especificando "syncuser@ '% '" no comando Create User. Consulte a documentação do MySQL para saber mais sobre como [especificar nomes de conta](https://dev.mysql.com/doc/refman/5.7/en/account-names.html).

   **Comando SQL**

   *Replicação com SSL*

   Para exigir o SSL para todas as conexões de usuário, use o seguinte comando para criar um usuário: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replicação sem SSL*

   Se o SSL não for necessário para todas as conexões, use o seguinte comando para criar um usuário:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   Para criar a função de replicação no MySQL Workbench, abra o painel **usuários e privilégios** no painel de **Gerenciamento** . Em seguida, clique em **adicionar conta**. 
 
   ![Usuários e privilégios](./media/howto-data-in-replication/users_privileges.png)

   Digite o nome de usuário no campo **nome de logon** . 

   ![Sincronizar usuário](./media/howto-data-in-replication/syncuser.png)
 
   Clique no painel **funções administrativas** e selecione escravo de **replicação** na lista de **privilégios globais**. Em seguida, clique em **aplicar** para criar a função de replicação.

   ![Escravo de replicação](./media/howto-data-in-replication/replicationslave.png)


4. Definir o servidor mestre como modo somente leitura

   Antes de começar a despejar o banco de dados, o servidor precisa ser colocado no modo somente leitura. Enquanto estiver no modo somente leitura, o mestre não poderá processar nenhuma transação de gravação. Avalie o impacto para seus negócios e agende a janela somente leitura em um horário fora de pico, se necessário.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Obter o nome e o deslocamento do arquivo de log binário

   Execute o comando [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) para determinar o nome e o deslocamento do arquivo de log binário atual.
    
   ```sql
   show master status;
   ```
   Os resultados devem ser como a seguir. Certifique-se de anotar o nome do arquivo binário, pois ele será usado em etapas posteriores.

   ![Resultados do status mestre](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>Despejar e restaurar o servidor mestre

1. Despejar todos os bancos de dados do servidor mestre

   Você pode usar o mysqldump para despejar bancos de dados do seu mestre. Para obter detalhes, consulte [despejar & restaurar](concepts-migrate-dump-restore.md). É desnecessário despejar biblioteca MySQL e biblioteca de teste.

2. Definir o servidor mestre para o modo de leitura/gravação

   Depois que o banco de dados tiver sido despejado, altere o servidor MySQL mestre de volta para o modo de leitura/gravação.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Restaurar arquivo de despejo para o novo servidor

   Restaure o arquivo de despejo para o servidor criado no banco de dados do Azure para o serviço MySQL. Consulte [despejar & restaurar](concepts-migrate-dump-restore.md) para saber como restaurar um arquivo de despejo para um servidor MySQL. Se o arquivo de despejo for grande, carregue-o em uma máquina virtual no Azure na mesma região que o servidor de réplica. Restaure-o para o servidor de banco de dados do Azure para MySQL da máquina virtual.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Vincular servidores mestre e de réplica para iniciar Replicação de Dados

1. Definir servidor mestre

   Todas as funções de Replicação de Dados são feitas por procedimentos armazenados. Você pode encontrar todos os procedimentos em [replicação de dados procedimentos armazenados](reference-data-in-stored-procedures.md). Os procedimentos armazenados podem ser executados no Shell do MySQL ou no MySQL Workbench. 

   Para vincular dois servidores e iniciar a replicação, faça logon no servidor de réplica de destino no serviço BD do Azure para MySQL e defina a instância externa como o servidor mestre. Isso é feito usando o procedimento armazenado `mysql.az_replication_change_master` no banco de BD do Azure para o servidor MySQL.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: nome do host do servidor mestre
   - master_user: nome de usuário para o servidor mestre
   - master_password: senha para o servidor mestre
   - master_log_file: o nome do arquivo de log binário de ser executado `show master status`
   - master_log_pos: a posição do log binário de ser executada `show master status`
   - master_ssl_ca: contexto do certificado de autoridade de certificação. Se não estiver usando SSL, passe uma cadeia de caracteres vazia.
       - É recomendável passar esse parâmetro em como uma variável. Consulte os exemplos a seguir para obter mais informações.

> [!NOTE]
> Se o servidor mestre estiver hospedado em uma VM do Azure, defina "permitir acesso aos serviços do Azure" como "ativado" para permitir que os servidores mestre e de réplica se comuniquem entre si. Essa configuração pode ser alterada das opções de **segurança de conexão** . Consulte [gerenciar regras de firewall usando o portal](howto-manage-firewall-using-portal.md) para obter mais informações.

   **Exemplos**

   *Replicação com SSL*

   A variável `@cert` é criada executando os seguintes comandos do MySQL: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   A replicação com SSL é configurada entre um servidor mestre hospedado no domínio "companya.com" e um servidor de réplica hospedado no banco de dados do Azure para MySQL. Esse procedimento armazenado é executado na réplica. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *Replicação sem SSL*

   A replicação sem SSL é configurada entre um servidor mestre hospedado no domínio "companya.com" e um servidor de réplica hospedado no banco de dados do Azure para MySQL. Esse procedimento armazenado é executado na réplica.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

1. Iniciar a replicação

   Chame o procedimento armazenado `mysql.az_replication_start` para iniciar a replicação.

   ```sql
   CALL mysql.az_replication_start;
   ```

1. Verificar status de replicação

   Chame o comando [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) no servidor de réplica para exibir o status de replicação.
    
   ```sql
   show slave status;
   ```

   Se o estado de `Slave_IO_Running` e `Slave_SQL_Running` forem "Sim" e o valor de `Seconds_Behind_Master` for "0", a replicação estará funcionando bem. `Seconds_Behind_Master` indica o quanto tarde a réplica é. Se o valor não for "0", significa que a réplica está processando atualizações. 

## <a name="other-stored-procedures"></a>Outros procedimentos armazenados

### <a name="stop-replication"></a>Parar replicação

Para interromper a replicação entre o servidor mestre e de réplica, use o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Remover relação de replicação

Para remover a relação entre o servidor mestre e de réplica, use o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Ignorar erro de replicação

Para ignorar um erro de replicação e permitir que a replicação Continue, use o seguinte procedimento armazenado:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o [replicação de dados](concepts-data-in-replication.md) para o banco de dados do Azure para MySQL. 
