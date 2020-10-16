---
title: Latência de replicação de resolução de problemas - Base de Dados Azure para MySQL
description: Aprenda a resolver a latência da replicação de problemas utilizando a Base de Dados Azure para as réplicas de leitura mySQL.
keywords: mysql, resolução de problemas, latência de replicação em segundos
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.openlocfilehash: cb02b29c100da7b8d63f214acc78906a757344c0
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096101"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>Latência de replicação de resolução de problemas na Base de Dados Azure para o MySQL

A funcionalidade [de réplica de leitura](concepts-read-replicas.md) permite-lhe replicar dados de uma Base de Dados Azure para o servidor MySQL para um servidor de réplica apenas de leitura. Pode reduzir as cargas de trabalho, encaminhando as consultas de leitura e relatório da aplicação para os servidores de réplicas. Esta configuração reduz a pressão no servidor de origem. Também melhora o desempenho geral e a latência da aplicação à medida que escala. 

As réplicas são atualizadas assíncronamente utilizando a tecnologia de replicação baseada na posição de ficheiro (binlog) do motor MySQL. Para obter mais informações, consulte [a visão geral da configuração de replicação baseada na posição do ficheiro MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

O atraso de replicação nas réplicas de leitura secundária depende de vários fatores. Estes fatores incluem, mas não se limitam a: 

- Latência da rede.
- Volume de transação no servidor de origem.
- Nível de cálculo do servidor de origem e servidor de réplica de leitura secundária.
- Consultas em execução no servidor de origem e servidor secundário. 

Neste artigo, você vai aprender a resolver problemas de latência de replicação na Base de Dados Azure para MySQL. Também compreenderá algumas causas comuns de aumento da latência da replicação nos servidores de réplicas.

## <a name="replication-concepts"></a>Conceitos de replicação

Quando um registo binário é ativado, o servidor de origem escreve transações comprometidas no registo binário. O tronco binário é utilizado para a replicação. É ligado por padrão para todos os servidores recém-abastados que suportam até 16 TB de armazenamento. Nos servidores de réplicas, dois fios são executados em cada servidor de réplica. Um fio é o *fio IO,* e o outro é o *fio SQL:*

- A linha IO liga-se ao servidor de origem e solicita registos binários atualizados. Este fio recebe as atualizações de registo binário. Essas atualizações são guardadas num servidor de réplica, num registo local chamado *registo de retransmissão*.
- O fio SQL lê o registo do retransmissor e aplica as alterações de dados nos servidores de réplicas.

## <a name="monitoring-replication-latency"></a>Monitorização da latência da replicação

A base de dados Azure para o MySQL fornece a métrica para o lag de replicação em segundos no [Azure Monitor](concepts-monitoring.md). Esta métrica está disponível apenas em servidores de réplicas de leitura. É calculado pela métrica seconds_behind_master que está disponível no MySQL. 

Para compreender a causa do aumento da latência da replicação, ligue-se ao servidor de réplica utilizando a [bancada MySQL Workbench](connect-workbench.md) ou [a Azure Cloud Shell](https://shell.azure.com). Em seguida, corra seguindo o comando.

>[!NOTE] 
> No seu código, substitua os valores de exemplo pelo nome de utilizador do seu servidor de réplica e nome de utilizador de administração. O nome de utilizador de administração requer `@\<servername>` uma base de dados Azure para o MySQL.

```azurecli-interactive
mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
```

Eis como a experiência parece no terminal Cloud Shell:

```
Requesting a Cloud Shell.Succeeded.
Connecting terminal...

Welcome to Azure Cloud Shell

Type "az" to use Azure CLI
Type "help" to learn about Cloud Shell

user@Azure:~$mysql -h myreplicademoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 64796
Server version: 5.6.42.0 Source distribution

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
```

No mesmo terminal Cloud Shell, executar o seguinte comando:

```
mysql> SHOW SLAVE STATUS;
```

Aqui está uma saída típica:
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="Monitorização da latência da replicação&quot;:::


A saída contém muita informação. Normalmente, é preciso concentrar-se apenas nas linhas que a tabela seguinte descreve.

|Métrica|Descrição|
|---|---|
|Slave_IO_State| Representa o estado atual do fio IO. Normalmente, o estado é &quot;Esperar que o mestre envie o evento&quot; se o servidor de origem (master) estiver a sincronizar. Um estado como &quot;Ligar a dominar" indica que a réplica perdeu a ligação ao servidor de origem. Certifique-se de que o servidor de origem está em funcionamento ou verifique se uma firewall está a bloquear a ligação.|
|Master_Log_File| Representa o ficheiro de registo binário ao qual o servidor de origem está a escrever.|
|Read_Master_Log_Pos| Indica onde o servidor de origem está a escrever no ficheiro de registo binário.|
|Relay_Master_Log_File| Representa o ficheiro de registo binário que o servidor de réplica está a ler a partir do servidor de origem.|
|Slave_IO_Running| Indica se a linha IO está em funcionamento. O valor deve `Yes` ser. Se o valor `NO` for, então a replicação é provavelmente quebrada.|
|Slave_SQL_Running| Indica se a linha SQL está em funcionamento. O valor deve `Yes` ser. Se o valor `NO` for, então a replicação é provavelmente quebrada.|
|Exec_Master_Log_Pos| Indica a posição do Relay_Master_Log_File que a réplica está a aplicar. Se houver latência, esta sequência de posição deve ser menor do que Read_Master_Log_Pos.|
|Relay_Log_Space|Indica o limite superior do tamanho do registo do relé. Pode verificar o tamanho consultando `SHOW GLOBAL VARIABLES` como `relay_log_space_limit` .|
|Seconds_Behind_Master| Apresenta latência de replicação em segundos.|
|Last_IO_Errno|Apresenta o código de erro do fio IO, se houver. Para obter mais informações sobre estes códigos, consulte a [referência de mensagem de erro do servidor MySQL](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).|
|Last_IO_Error| Apresenta a mensagem de erro do fio IO, se houver.|
|Last_SQL_Errno|Apresenta o código de erro de fio SQL, se houver. Para obter mais informações sobre estes códigos, consulte a [referência de mensagem de erro do servidor MySQL](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).|
|Last_SQL_Error|Apresenta a mensagem de erro do fio SQL, se houver.|
|Slave_SQL_Running_State| Indica o estado atual do fio SQL. Neste estado, `System lock` é normal. Também é normal ver um estado `Waiting for dependent transaction to commit` de. Este estado indica que a réplica está à espera que o servidor de origem atualize transações comprometidas.|

Se Slave_IO_Running é `Yes` e Slave_SQL_Running `Yes` está, então a replicação está a correr bem. 

Em seguida, verifique Last_IO_Errno, Last_IO_Error, Last_SQL_Errno e Last_SQL_Error.  Estes campos exibem o número de erro e a mensagem de erro do erro mais recente que fez com que a linha SQL parasse. Um número de erro `0` e uma mensagem vazia significa que não há erro. Investigue qualquer valor de erro não zero verificando o código de erro na referência de mensagem de erro do [servidor MySQL](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).

## <a name="common-scenarios-for-high-replication-latency"></a>Cenários comuns para a elevada latência de replicação

As seguintes secções abordam cenários em que a alta latência de replicação é comum.

### <a name="network-latency-or-high-cpu-consumption-on-the-source-server"></a>Latência da rede ou elevado consumo de CPU no servidor de origem

Se vir os seguintes valores, então a latência de replicação é provavelmente causada por alta latência da rede ou elevado consumo de CPU no servidor de origem. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

Neste caso, o fio IO está em execução e está à espera no servidor de origem. O servidor de origem já escreveu para o ficheiro de registo binário número 20. A réplica só recebeu até ao ficheiro número 10. Os principais fatores para a alta latência de replicação neste cenário são a velocidade da rede ou alta utilização do CPU no servidor de origem.  

Em Azure, a latência da rede dentro de uma região pode normalmente ser medida milissegundos. Em todas as regiões, a latência varia de milissegundos a segundos. 

Na maioria dos casos, o atraso de ligação entre as linhas IO e o servidor de origem é causado por uma alta utilização do CPU no servidor de origem. Os fios IO são processados lentamente. Pode detetar este problema utilizando o Azure Monitor para verificar a utilização do CPU e o número de ligações simultâneas no servidor de origem.

Se não vir uma alta utilização do CPU no servidor de origem, o problema pode ser a latência da rede. Se a latência da rede for subitamente anormalmente elevada, verifique se a página de estado do [Azure](https://status.azure.com/status) tem problemas ou interrupções conhecidas. 

### <a name="heavy-bursts-of-transactions-on-the-source-server"></a>Fortes explosões de transações no servidor de origem

Se vir os seguintes valores, então uma forte explosão de transações no servidor de origem está provavelmente a causar a latência da replicação. 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

A saída mostra que a réplica pode recuperar o registo binário atrás do servidor de origem. Mas a réplica IO thread indica que o espaço de registo de retransmissão já está cheio. 

A velocidade da rede não está a causar o atraso. A réplica está a tentar pôr a conversa em dia. Mas o tamanho do tronco binário atualizado excede o limite superior do espaço de registo do relé. 

Para resolver este problema, ative o [registo de consulta lenta](concepts-server-logs.md) no servidor de origem. Utilize registos de consulta lenta para identificar transações de longa duração no servidor de origem. Em seguida, afina as consultas identificadas para reduzir a latência no servidor. 

A latência de replicação deste tipo é geralmente causada pela carga de dados no servidor de origem. Quando os servidores de origem têm cargas de dados semanais ou mensais, a latência de replicação é infelizmente inevitável. Os servidores de réplica eventualmente alcançam depois de termina a carga de dados no servidor de origem.


### <a name="slowness-on-the-replica-server"></a>Lentidão no servidor de réplica

Se observar os seguintes valores, então o problema pode estar no servidor de réplicas. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: The binary log file sequence equals to Relay_Master_Log_File, e.g. mysql-bin.000191
Read_Master_Log_Pos: The position of master server written to the above file is larger than Relay_Log_Pos, e.g. 103978138
Relay_Master_Log_File: mysql-bin.000191
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Exec_Master_Log_Pos: The position of slave reads from master binary log file is smaller than Read_Master_Log_Pos, e.g. 13468882
Seconds_Behind_Master: There is latency and the value here is greater than 0
```

Neste cenário, a saída mostra que tanto o fio IO como o fio SQL estão a funcionar bem. A réplica lê o mesmo ficheiro de registo binário que o servidor de origem escreve. No entanto, alguma latência no servidor de réplicas reflete a mesma transação a partir do servidor de origem. 

As seguintes secções descrevem causas comuns deste tipo de latência.

#### <a name="no-primary-key-or-unique-key-on-a-table"></a>Nenhuma chave primária ou chave única em uma mesa

A base de dados Azure para o MySQL utiliza replicação baseada em linha. O servidor de origem escreve eventos para o registo binário, registando alterações em linhas de tabela individuais. O fio SQL replica então essas alterações nas linhas de tabela correspondentes no servidor de réplica. Quando uma tabela carece de uma chave primária ou única, o fio SQL digitaliza todas as linhas da tabela alvo para aplicar as alterações. Este scan pode causar latência de replicação.

No MySQL, a chave primária é um índice associado que garante um desempenho de consulta rápida porque não pode incluir valores NUOS. Se utilizar o motor de armazenamento InnoDB, os dados da tabela estão fisicamente organizados para fazer pesquisas e tipos ultrarrápidos com base na chave primária. 

Recomendamos que adicione uma chave primária nas tabelas do servidor de origem antes de criar o servidor de réplica. Adicione as teclas primárias no servidor de origem e, em seguida, re-crie réplicas de leitura para ajudar a melhorar a latência da replicação.

Utilize a seguinte consulta para saber quais as tabelas que faltam uma chave primária no servidor de origem:

```sql 
select tab.table_schema as database_name, tab.table_name 
from information_schema.tables tab left join 
information_schema.table_constraints tco 
on tab.table_schema = tco.table_schema 
and tab.table_name = tco.table_name 
and tco.constraint_type = 'PRIMARY KEY' 
where tco.constraint_type is null 
and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') 
and tab.table_type = 'BASE TABLE' 
order by tab.table_schema, tab.table_name;

```

#### <a name="long-running-queries-on-the-replica-server"></a>Consultas de longa duração no servidor de réplicas

A carga de trabalho no servidor de réplica pode fazer com que o fio SQL fique atrás da linha IO. Consultas de longa duração no servidor de réplicas são uma das causas comuns da alta latência de replicação. Para resolver este problema, ative o [registo de consulta lenta](concepts-server-logs.md) no servidor de réplica. 

Consultas lentas podem aumentar o consumo de recursos ou abrandar o servidor para que a réplica não possa alcançar o servidor de origem. Neste cenário, afina as perguntas lentas. Consultas mais rápidas impedem o bloqueio do fio SQL e melhoram significativamente a latência de replicação.


#### <a name="ddl-queries-on-the-source-server"></a>Consultas DDL no servidor de origem
No servidor de origem, um comando de linguagem de definição de dados (DDL) [`ALTER TABLE`](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) como pode demorar muito tempo. Enquanto o comando DDL está em execução, milhares de outras consultas podem estar a funcionar em paralelo no servidor de origem. 

Quando o DDL é replicado, para garantir a consistência da base de dados, o motor MySQL executa o DDL num único fio de replicação. Durante esta tarefa, todas as outras consultas replicadas estão bloqueadas e devem aguardar até que a operação DDL termine no servidor de réplicas. Mesmo as operações online da DDL causam este atraso. As operações DDL aumentam a latência da replicação.

Se ativar o [registo de consulta lenta](concepts-server-logs.md) no servidor de origem, pode detetar este problema de latência verificando um comando DDL que funcionasse no servidor de origem. Através da queda do índice, do renomeamento e da criação, pode utilizar o algoritmo INPLACE para a TABELA ALTER. Pode ser necessário copiar os dados da tabela e reconstruir a tabela. 

Tipicamente, o DML simultâneo é suportado para o algoritmo INPLACE. Mas pode fazer um bloqueio exclusivo de metadados na mesa quando preparar e executar a operação. Assim, para a declaração DO ÍNDICE CREATE, pode utilizar as cláusulas ALGORITMO e LOCK para influenciar o método de cópia da tabela e o nível de concordância para a leitura e escrita. Pode ainda impedir as operações de DML adicionando um índice FULLTEXT ou um índice ESPACIAL. 

O exemplo a seguir cria um índice utilizando cláusulas DE ALGORITMO e BLOQUEIO.

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

Infelizmente, para uma declaração dDL que requer um bloqueio, não pode evitar a latência da replicação. Para reduzir os efeitos potenciais, faça este tipo de operações de DDL durante as horas de ponta, por exemplo durante a noite.

#### <a name="downgraded-replica-server"></a>Servidor de réplica desclassificado

Na Base de Dados Azure para o MySQL, as réplicas de leitura utilizam a mesma configuração do servidor que o servidor de origem. Pode alterar a configuração do servidor de réplica depois de ter sido criada. 

Se o servidor de réplica for desclassificado, a carga de trabalho pode consumir mais recursos, o que por sua vez pode levar à latência de replicação. Para detetar este problema, utilize o Azure Monitor para verificar o CPU e o consumo de memória do servidor de réplica. 

Neste cenário, recomendamos que mantenha a configuração do servidor de réplica em valores iguais ou superiores aos valores do servidor de origem. Esta configuração permite que a réplica acompanhe o servidor de origem.

#### <a name="improving-replication-latency-by-tuning-the-source-server-parameters"></a>Melhorar a latência da replicação ajustando os parâmetros do servidor de origem

Na Base de Dados Azure para o MySQL, por padrão, a replicação é otimizada para ser executada com fios paralelos em réplicas. Quando as cargas de trabalho de alta concordância no servidor de origem fazem com que o servidor de réplica fique para trás, pode melhorar a latência da replicação configurando o parâmetro binlog_group_commit_sync_delay no servidor de origem. 

O parâmetro binlog_group_commit_sync_delay controla quantos microsegundos o registo binário se compromete antes de sincronizar o ficheiro de registo binário. O benefício deste parâmetro é que, em vez de aplicar imediatamente todas as transações comprometidas, o servidor de origem envia as atualizações de registo binário a granel. Este atraso reduz a IO na réplica e ajuda a melhorar o desempenho. 

Pode ser útil definir o parâmetro binlog_group_commit_sync_delay para 1000 ou mais. Em seguida, monitorize a latência de replicação. Desconfiem este parâmetro com cautela e utilize-o apenas para cargas de trabalho de alta concordância. 

Para cargas de trabalho de baixa concordância que incluem muitas transações singleton, a definição de binlog_group_commit_sync_delay pode aumentar a latência. A latência pode aumentar porque o fio IO aguarda atualizações de registos binários a granel, mesmo que apenas algumas transações sejam cometidas. 

## <a name="next-steps"></a>Passos seguintes
Consulte a visão geral da [replicação do binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).
