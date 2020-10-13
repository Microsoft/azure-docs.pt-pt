---
title: Latência de replicação de resolução de problemas - Base de Dados Azure para MySQL
description: Saiba como resolver a latência da replicação de problemas com a Base de Dados Azure para o MySQL ler réplicas
keywords: mysql, resolução de problemas, latência de replicação em segundos
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.openlocfilehash: 16a502a53b4441faf68ea342e0bc865731d38b1a
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877111"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>Latência de replicação de resolução de problemas na Base de Dados Azure para o MySQL

A funcionalidade [de réplica de leitura](concepts-read-replicas.md) permite-lhe replicar dados de uma Base de Dados Azure para o servidor MySQL para um servidor de réplica apenas de leitura. As réplicas de leitura são usadas para reduzir a carga de trabalho através do encaminhamento de consultas de leitura/relatório da aplicação para servidores de réplicas. Isto reduz a pressão sobre o servidor primário e melhora o desempenho geral e a latência da aplicação à medida que escala. As réplicas são atualizadas de forma assíncrona com a tecnologia de replicação baseada na posição dos ficheiros de registo binário nativo (binlog) do motor MySQL. Para saber mais sobre a replicação do binlog, consulte a visão geral da [replicação do binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

O atraso de replicação nas réplicas de leitura secundária depende do número de fatores, incluindo, mas não se limitando a 

- Latência de rede
- Volume de transação no servidor de origem
- Nível de cálculo do servidor de réplica de leitura secundária
- Consultas a decorrer no servidor primário e secundário. 

Neste documento, você aprenderá sobre como resolver a latência de replicação de problemas na Base de Dados Azure para o MySQL. Além disso, também irá compreender algumas das causas comuns do aumento da latência da replicação nos servidores de réplicas.

## <a name="replication-concepts"></a>Conceitos de replicação

Quando o registo binário está ativado, o servidor de origem escreve transação comprometida no registo binário, que é usado para replicação. O registo binário é ligado por padrão para todos os servidores recém-abastados que suportam até 16 armazenamentos de TB. Nos servidores de réplicas, existem dois fios em execução por servidor de réplica, um chamado fio IO e o outro chamado thread SQL.

- A **linha IO** liga-se ao servidor de origem e solicita registos binários atualizados. Depois de este fio receber as atualizações de registo binário, são guardadas num servidor de réplica, num registo local chamado registo de retransmissão.
- A **linha SQL** lê o registo do retransmissor e aplica a(s) alteração de dados em servidores de réplicas.

## <a name="monitoring-replication-latency"></a>Monitorização da latência da replicação

A base de dados Azure para o MySQL fornece o lag de replicação em segundos métrica no [Azure Monitor](concepts-monitoring.md). Esta métrica está disponível apenas em servidores de réplicas de leitura. Esta métrica é calculada usando a métrica seconds_behind_master disponível no MySQL. Para compreender a causa raiz do aumento da latência da replicação, ligue-se ao servidor de réplica usando a bancada [MySQL Workbench](connect-workbench.md) ou [a concha da Nuvem Azure](https://shell.azure.com) e execute o seguinte comando:

 Substitua os valores pelo nome do servidor de réplica real e pelo nome de login do utilizador administrativo. O nome de utilizador de administração requer '@ \<servername> ' para Azure Database for MySQL:

  ```azurecli-interactive
  mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  Eis como a experiência se parece no terminal Cloud Shell
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
  No mesmo terminal Azure Cloud Shell, execute o comando seguinte

  ```
  mysql> SHOW SLAVE STATUS;
  ```

  Uma saída típica será como:
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="Monitorização da latência da replicação&quot;:::


A saída contém muita informação, mas normalmente só é importante concentrarmo-nos nas seguintes colunas:

|Métrica|Descrição|
|---|---|
|Slave_IO_State| Estado atual da linha IO. Normalmente, o estado é &quot;Esperar que o mestre envie o evento&quot; se estiver a sincronizar. No entanto, se vir um estado como &quot;Ligar-se ao mestre&quot;, então a réplica perdeu a ligação ao servidor principal. Verifique se o mestre está a funcionar ou se uma firewall está a bloquear a ligação.|
|Master_Log_File| O ficheiro binário de registos para o qual o mestre está a escrever.|
|Read_Master_Log_Pos| Representa a posição no ficheiro de log binário acima, no qual o mestre está a escrever.|
|Relay_Master_Log_File| Indicado representa o ficheiro de registo binário que o servidor de réplica está a ler a partir do mestre.|
|Slave_IO_Running| Indica se a linha IO está em funcionamento. Devia ser &quot;Sim&quot;. Se &quot;NÃO&quot;, então é provável que a replicação esteja quebrada.|
|Slave_SQL_Running| Indica se a linha SQL está em funcionamento. Devia ser &quot;Sim&quot;. Se &quot;NÃO&quot;, então é provável que a replicação esteja quebrada.|
|Exec_Master_Log_Pos| Visualiza a posição do Relay_Master_Log_File a réplica está a aplicar-se. Se houver latência, esta sequência de posição deve ser menor do que Read_Master_Log_Pos.|
|Relay_Log_Space|Apresenta o limite superior do tamanho do registo do relé. Você pode verificar o tamanho consultando séries de variáveis globais como &quot;relay_log_space_limit&quot;.|
|Seconds_Behind_Master| Apresenta latência de replicação em segundos.|
|Last_IO_Errno|Apresenta o código de erro do fio IO, se houver. Para obter mais informações sobre estes códigos, consulte [a documentação do MySQL.](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html)|
|Last_IO_Error| Apresenta a mensagem de erro do fio IO, se houver.|
|Last_SQL_Errno|Apresenta o código de erro de fio SQL, se houver. Para obter mais informações sobre estes códigos, consulte [a documentação do MySQL.](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html)|
|Last_SQL_Error|Apresenta a mensagem de erro do fio SQL, se houver.|
|Slave_SQL_Running_State| Indica o estado atual do fio SQL. Note que &quot;Bloqueio de sistema" mostrado neste estado é um comportamento normal. É normal ver o estatuto de "Esperar que a transação dependente se comprometa". Indica que a réplica está à espera que o mestre atualize transações comprometidas.|

Se Slave_IO_Running é Sim e Slave_SQL_Running é Sim, então a replicação está a correr bem. 

Em seguida, tens de verificar Last_IO_Errno, Last_IO_Error, Last_SQL_Errno e Last_SQL_Error.  Estes campos detêm o número de erro e a mensagem de erro do erro mais recente que fez com que a linha SQL parasse. Um erro número 0 e uma mensagem vazia significam que não há erro. Um valor não zero no erro deve ser investigado mais aprofundadamente, procurando o código de erro na [documentação mySQL](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html).

## <a name="common-scenarios-for-high-replication-latency"></a>Cenários comuns para a elevada latência de replicação

### <a name="network-latency-or-high-cpu-on-source-server"></a>Latência da rede ou cpu elevado no servidor de origem

Se observar os seguintes valores, a causa mais comum da latência de replicação é a alta latência da rede ou o elevado consumo de cpu no servidor de origem. Neste caso, o fio IO está correndo e esperando o mestre. O mestre (servidor de origem) já escreveu para o ficheiro de registo binário #20, enquanto a réplica recebida apenas para arquivar #10. Os principais fatores que contribuem para a elevada latência de replicação neste cenário são a velocidade da rede ou a alta utilização do CPU no servidor de origem.  Em Azure, a latência da rede dentro de uma região normalmente varia em milissegundos e em toda a região pode ir até segundos. Na maioria dos casos, o atraso na linha IO para ligar ao servidor de origem é causado devido à alta utilização do cpu no servidor de origem, fazendo com que o processamento da linha IO seja lento. Isto pode ser detetado monitorizando a utilização do CPU e observando o número de ligações simultâneas no servidor de origem utilizando o monitor Azure.

Se não vir uma utilização elevada do CPU no servidor de origem, as causas possíveis podem ser a latência da rede. se vir uma alta latência de rede de repente, recomendamos que verifique a página de estado do [Azure](https://status.azure.com/status) para garantir que existem problemas ou interrupções não conhecidas. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

### <a name="heavy-burst-of-transactions-on-source-server"></a>Explosão pesada de transações no servidor de origem

Se observar os seguintes valores, a causa mais comum da latência de replicação é a explosão pesada de transações no servidor de origem. Na saída abaixo, embora a réplica possa recuperar o log binário atrás do mestre, a réplica IO thread indica que o espaço de registo do relé já está cheio. A velocidade da rede não está a causar o atraso, porque a réplica já está a tentar recuperar o mais rápido possível. Em vez disso, o tamanho do log binário atualizado excede o limite superior do espaço de registo do relé. Para resolver ainda mais este problema, o [registo de consultas lentas](concepts-server-logs.md) deve ser ativado no servidor principal. Os registos de consulta lenta permitem identificar transações de longa duração no servidor de origem. As consultas identificadas precisam de ser sintonizadas para reduzir a latência no servidor. 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

Seguem-se as causas comuns da latência nesta categoria:

#### <a name="replication-latency-due-to-data-load-on-source-server"></a>Latência de replicação devido à carga de dados no servidor de origem
Em alguns casos, existem cargas de dados semanais ou mensais nos servidores de origem. Infelizmente, a latência da replicação é inevitável neste caso. Neste cenário, os servidores de réplica acabam por ser apanhados após a conclusão da carga de dados no servidor de origem.


### <a name="slowness-on-the-replica-server"></a>Lentidão no servidor de réplica

Se observar os seguintes valores, a causa mais comum pode ser algum problema no servidor de réplica que precisa de uma investigação mais aprofundada. Neste cenário, como se pode ver na saída, tanto os fios IO como o SQL estão a funcionar bem e a réplica está a ler o mesmo ficheiro de log binário que o mestre escreve. No entanto, ocorre alguma latência no servidor de réplica para refletir a mesma transação a partir do servidor de origem. 

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

Seguem-se as causas comuns da latência nesta categoria:

#### <a name="no-primary-or-unique-key-on-a-table"></a>Nenhuma chave primária ou única em uma mesa

A base de dados Azure para o MySQL utiliza replicação baseada em linha. Com a replicação baseada em linha, o servidor principal escreve eventos para o registo binário sobre a mudança individual da fila de tabela. O SQL Thread executa essas alterações nas linhas de tabela correspondentes no servidor de réplicas. Nenhuma chave primária ou única numa mesa é uma das causas comuns da latência da replicação. A falta de teclas primárias ou únicas leva à digitalização de todas as linhas da tabela alvo por thread SQL para aplicar as alterações.

No MySQL a chave primária é um índice associado que garante um desempenho de consulta rápida, uma vez que não pode incluir valores NUOS. Com o motor de armazenamento InnoDB, os dados da tabela são fisicamente organizados para fazer pesquisas e tipos ultrarrápidos com base na chave primária. Por isso, recomenda-se adicionar uma chave primária nas tabelas do servidor de origem antes de criar o servidor de réplica. Neste cenário, é necessário adicionar teclas primárias no servidor de origem e recriar réplicas de leitura para ajudar a melhorar a latência da replicação.

Pode utilizar a seguinte consulta para determinar as tabelas com a chave primária em falta no servidor de origem:

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

#### <a name="replication-latency-due-to-long-running-queries-on-replica-server"></a>Latência de replicação devido a longas consultas no servidor de réplicas

É possível que a carga de trabalho no servidor de réplicas possa impedir que o fio SQL se mantenha com o fio IO. Esta é uma das causas comuns de alta latência de replicação se houver uma longa consulta no servidor de réplica. Neste caso, o [registo de consultas lentas](concepts-server-logs.md) deve ser ativado no servidor de réplica para ajudar a resolver o problema. Consultas lentas podem aumentar os consumos de recursos ou abrandar o servidor, assim a réplica não será capaz de recuperar com o mestre. Neste cenário, é preciso afinar consultas lentas. Consultas mais rápidas impedem o bloqueio do fio SQL e melhoram significativamente a latência da replicação.


#### <a name="replication-latency-due-to-ddl-queries-on-source-server"></a>Latência de replicação devido a consultas de DDL no servidor de origem
Se houver um comando DDL de longa duração como [ALTER TABLE](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) executado no servidor de origem e dizer que demorou 1 hora a ser executado. Durante esse tempo, pode haver milhares de outras consultas em paralelo no servidor de origem. Quando o DDL é replicado na réplica, para garantir a consistência da base de dados, o motor MySQL tem de executar o DDL num único fio de replicação. Assim, todas as outras consultas replicadas serão bloqueadas e terão de esperar uma hora ou mais até que a operação DDL esteja concluída no servidor de réplicas. Isto é verdade independentemente da operação DDL online ou não. Com as operações de DDL, espera-se que a replicação veja uma latência de replicação aumentada.

Se tiver [um registo de consulta lenta](concepts-server-logs.md) ativado no servidor de origem, este cenário pode ser detetado olhando para os registos de consulta lenta para ver se um comando DDL foi executado no servidor de origem. Embora a queda do Índice, o renomeamento e a criação devam utilizar o algoritmo INPLACE para a TABELA ALTER, pode envolver a cópia de dados da tabela e reconstruir a tabela. Normalmente para algoritmo INPLACE simultânea DML é suportado, mas um bloqueio exclusivo de metadados na tabela pode ser tomado brevemente durante as fases de preparação e execução da operação. Como tal, para a declaração CREATE INDEX, as cláusulas ALGORITMO e LOCK podem ser usadas para influenciar o método de cópia da tabela e o nível de concordância para a leitura e escrita, no entanto, adicionar um índice FULLTEXT ou SPATIAL ainda impedirá as operações de DML. Veja abaixo um exemplo de criação de um índice com cláusulas DE ALGORITMO e LOCK:

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

Infelizmente, para a declaração do DDL que requer um bloqueio, a latência de replicação não pode ser evitada, em vez disso, estes tipos de operações DDL devem ser realizados durante as horas de ponta, por exemplo durante a noite para reduzir o impacto potencial.

#### <a name="replication-latency-due-to-replica-server-lower-sku"></a>Latência de replicação devido ao servidor de réplica sKU inferior

Na Base de Dados Azure para as réplicas de leitura MySQL são criadas com a mesma configuração do servidor principal que o servidor principal. A configuração do servidor de réplica pode ser alterada depois de ter sido criada. No entanto, se o servidor de réplica for desclassificado, a carga de trabalho pode causar um maior consumo de recursos que, por sua vez, pode levar à latência de replicação. Isto pode ser observado monitorizando o consumo de CPU e Memória do servidor de réplica a partir do Azure Monitor. Neste cenário, recomenda-se que a configuração do servidor de réplica seja mantida em valores iguais ou superiores à fonte para garantir que a réplica é capaz de acompanhar o mestre.

#### <a name="improving-replication-latency-using-server-parameter-tuning-on-source-server"></a>Melhorar a latência da replicação usando afinação do parâmetro do servidor no servidor de origem

Na Base de Dados Azure para o MySQL, a replicação é otimizada para ser executada com fios paralelos em réplicas por padrão. Para cargas de trabalho de alta concordância no servidor de origem onde o servidor de réplicas não está a conseguir recuperar, a latência de replicação pode melhorar configurando binlog_group_commit_sync_delay de parâmetros no servidor de origem. Este parâmetro controla quantos microsegundos o log binário se compromete antes de sincronizar o ficheiro de registo binário. O benefício é que, em vez de aplicar imediatamente todas as transações cometidas, o mestre envia as atualizações de registo binário a granel. Isto reduz o IO na réplica e ajuda a melhorar o desempenho. Neste cenário, pode ser útil definir binlog_group_commit_sync_delay a 1000 ou mais e monitorizar a latência da replicação. Este parâmetro deve ser definido com cautela e alavancado apenas para cargas de trabalho elevadas e simultâneas. Para um cenário de baixa concordância com muitas transações singleton, a definição de binlog_group_commit_sync_delay pode aumentar a latência porque o fio IO está à espera de atualizações de registos binários a granel enquanto apenas poucas transações podem ser comprometidas. 

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a visão geral da replicação do binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).
