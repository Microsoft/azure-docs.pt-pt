---
title: Configure a replicação de dados para replicar os dados na base de dados do Azure para MySQL.
description: Este artigo descreve como configurar a dados na replicação da base de dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/31/2018
ms.openlocfilehash: 7d56d7f8fcbd53d4f69863d260591ef80f3d7188
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102946"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Como configurar a base de dados do Azure para a replicação de dados MySQL

Neste artigo, aprenderá como configurar a replicação de dados na base de dados do Azure para o serviço MySQL ao configurar os servidores mestre e de réplica. Replicação de dados-in permite-lhe sincronizar dados a partir de um servidor MySQL mestre em execução no local, nas máquinas virtuais ou serviços de base de dados hospedados por outros fornecedores de cloud para uma réplica da base de dados do Azure para o serviço MySQL. 

Este artigo pressupõe que tem pelo menos alguma experiência anterior com servidores MySQL e bases de dados.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Criar um servidor MySQL para ser utilizado como réplica

1. Criar uma nova base de dados do Azure para o servidor MySQL

   Criar um novo servidor do MySQL (ex. "replica.mysql.database.azure.com"). Consulte a [criar uma base de dados do Azure para o servidor MySQL com o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md) para a criação do servidor. Esse servidor é o servidor de "réplica" na replicação de dados-in.

   > [!IMPORTANT]
   > A base de dados do Azure para o servidor MySQL têm de ser criado nos escalões de preços para fins gerais ou com otimização de memória.
   > 

2. Criar as mesmas contas de usuário e privilégios correspondentes

   Contas de utilizador não são replicadas a partir do servidor mestre para o servidor de réplica. Se pretender oferecer aos usuários acesso ao servidor de réplica, terá de criar manualmente todas as contas e privilégios correspondentes no recentemente criada a base de dados do Azure para o servidor MySQL.

## <a name="configure-the-master-server"></a>Configurar o servidor mestre
Os seguintes passos prepararem e configurar o MySQL server alojado no local, uma máquina virtual ou o serviço de base de dados hospedado por outros fornecedores de cloud para a replicação de dados. Esse servidor é o "mestre" na replicação de dados-in. 

1. Ativar o registo binário

   Verifica se o registo binário tenha sido ativado no mestre de executando o seguinte comando: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se a variável [ `log_bin` ](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) é devolvido com o valor "ON", o registo binário está ativado no seu servidor. 

   Se `log_bin` é devolvido com o valor "OFF", ativar o binário registo ao editar o ficheiro de cnf então que `log_bin=ON` e reinicie o servidor para que a alteração tenha efeito.

2. Definições do servidor mestre

   A replicação de dados-in requer o parâmetro `lower_case_table_names` para ser consistente entre os servidores mestre e de réplica. Este parâmetro é 1 por predefinição na base de dados do Azure para MySQL. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Criar uma nova função de replicação e configurar a permissão

   Crie uma conta de utilizador no servidor principal que está configurado com privilégios de replicação. Isso pode ser feito por meio de comandos SQL ou uma ferramenta como o MySQL Workbench. Considere se pretender replicar com SSL como isto terá de ser especificado quando criar o utilizador. Consulte a documentação do MySQL para compreender como [adicionar contas de utilizador](https://dev.mysql.com/doc/refman/5.7/en/adding-users.html) no seu servidor mestre. 

   Os comandos abaixo, a nova função de replicação criada é capaz de acessar o mestre de qualquer máquina, não apenas a máquina que aloja o mestre em si. Isso é feito ao especificar "syncuser@'%'" no comando de utilizador de criar. Consulte a documentação do MySQL para saber mais sobre [especificando os nomes das contas](https://dev.mysql.com/doc/refman/5.7/en/account-names.html).

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

   Executar o [ `show master status` ](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) comando para determinar o nome de ficheiro de registo binário atual e o deslocamento.
    
   ```sql
   show master status;
   ```
   Os resultados devem ser como se segue. Lembre-se de que tome nota do nome de ficheiro binário que será utilizada em passos posteriores.

   ![Resultados de estado do mestre](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>Cópia de segurança e restaurar o servidor mestre

1. Despeja todas as bases de dados do servidor mestre

   Pode utilizar mysqldump para bases de dados de informação do seu principal. Para obter detalhes, consulte [capturar e restaurar](concepts-migrate-dump-restore.md). Não é necessário para a biblioteca MySQL de cópia de segurança e a biblioteca de teste.

2. Servidor mestre do conjunto de modo de leitura/escrita

   Assim que a base de dados foram colocado, alterar o mestre de retorno de servidor do MySQL para o modo de leitura/escrita.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Restaurar o ficheiro de informação para novo servidor

   Restaure o ficheiro de informação para o servidor criado na base de dados do Azure para o serviço MySQL. Consulte a [capturar e restaurar](concepts-migrate-dump-restore.md) para saber como restaurar um arquivo de despejo para um servidor MySQL. Se o ficheiro de informação for grande, carregue-a uma máquina virtual no Azure na mesma região que o seu servidor de réplica. Restaurá-la para a base de dados do Azure para o servidor MySQL da máquina virtual.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Ligar servidores mestre e de réplica para iniciar a replicação de dados

1. Servidor mestre do conjunto

   Todas as funções de replicação de dados-in são efetuadas pelo procedimentos armazenados. Pode encontrar todos os procedimentos em [dados na replicação procedimentos armazenados](reference-data-in-stored-procedures.md). Os procedimentos armazenados podem ser executados no shell do MySQL ou o MySQL Workbench. 

   Para ligar os dois servidores e iniciar a replicação, inicie sessão no servidor de réplica de destino no DB do Azure para o serviço MySQL e definir a instância externa como o servidor mestre. Isso é feito usando o `mysql.az_replication_change_master` procedimento armazenado no Azure DB para o servidor MySQL.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: nome de anfitrião do servidor mestre
   - master_user: nome de utilizador para o servidor mestre
   - master_password: palavra-passe para o servidor mestre
   - master_log_file: nome de ficheiro de registo binário a execução `show master status`
   - master_log_pos: posição do registo binário a execução `show master status`
   - master_ssl_ca: Contexto de certificado de AC. Se não utilizar SSL, passe na cadeia de caracteres vazia.
       - Recomenda-se para transmitir este parâmetro no como uma variável. Veja os exemplos seguintes para obter mais informações.

> [!NOTE]
> Se o servidor mestre está alojado numa VM do Azure, defina a opção "Permitir acesso aos serviços do Azure" como "ON" para permitir que os servidores mestre e de réplica comunicar entre si. Esta definição pode ser alterada do **segurança de ligação** opções. Consulte a [gerir regras de firewall com o portal](howto-manage-firewall-using-portal.md) para obter mais informações.

   **Exemplos**

   *Replicação com SSL*

   A variável `@cert` é criada executando os seguintes comandos do MySQL: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   Replicação com o SSL é configurada entre um servidor mestre hospedado no domínio "companya.com" e um servidor de réplica alojado na base de dados do Azure para MySQL. Este procedimento armazenado é executado na réplica. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *Replicação sem SSL*

   Replicação sem SSL é configurada entre um servidor mestre hospedado no domínio "companya.com" e um servidor de réplica alojado na base de dados do Azure para MySQL. Este procedimento armazenado é executado na réplica.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

1. Iniciar a replicação

   Chamar o `mysql.az_replication_start` armazenados procedimento para iniciar a replicação.

   ```sql
   CALL mysql.az_replication_start;
   ```

1. Verificar estado de replicação

   Chamar o [ `show slave status` ](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) comando no servidor de réplica para ver o estado de replicação.
    
   ```sql
   show slave status;
   ```

   Se o estado dos `Slave_IO_Running` e `Slave_SQL_Running` são "Sim" e o valor de `Seconds_Behind_Master` é "0", a replicação está a funcionar bem. `Seconds_Behind_Master` Indica a réplica como tardia é. Se o valor não é "0", significa que a réplica está a processar atualizações. 

## <a name="other-stored-procedures"></a>Outros procedimentos armazenados

### <a name="stop-replication"></a>Parar replicação

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
- Saiba mais sobre [replicação de dados](concepts-data-in-replication.md) para base de dados do Azure para MySQL. 
