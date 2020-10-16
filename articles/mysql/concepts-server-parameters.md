---
title: Parâmetros do servidor - Base de Dados Azure para MySQL
description: Este tópico fornece diretrizes para configurar parâmetros do servidor na Base de Dados Azure para o MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: 5415446e0211618cfbee917d0df91213d68b7097
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91627351"
---
# <a name="server-parameters-in-azure-database-for-mysql"></a>Parâmetros do servidor na Base de Dados Azure para o MySQL

Este artigo fornece considerações e diretrizes para configurar parâmetros do servidor na Base de Dados Azure para o MySQL.

## <a name="what-are-server-parameters"></a>Quais são os parâmetros do servidor? 

O motor MySQL fornece muitas variáveis/parâmetros de servidor diferentes que podem ser usados para configurar e afinar o comportamento do motor. Alguns parâmetros podem ser definidos dinamicamente durante o tempo de funcionamento, enquanto outros são "estáticos", exigindo um reinício do servidor para se aplicar.

A Azure Database for MySQL expõe a capacidade de alterar o valor de vários parâmetros do servidor MySQL utilizando o [portal Azure](./howto-server-parameters.md), [Azure CLI](./howto-configure-server-parameters-using-cli.md)e [PowerShell](./howto-configure-server-parameters-using-powershell.md) para corresponder às necessidades da sua carga de trabalho.

## <a name="configurable-server-parameters"></a>Parâmetros de servidor configuráveis

A lista de parâmetros suportados do servidor está em constante crescimento. Utilize o separador parâmetros do servidor no portal Azure para visualizar a lista completa e configurar os valores dos parâmetros do servidor.

Consulte as seguintes secções abaixo para saber mais sobre os limites dos vários parâmetros do servidor comumente atualizados. Os limites são determinados pelo nível de preços e vCores do servidor.

### <a name="thread-pools"></a>Piscinas de rosca

O MySQL atribui tradicionalmente um fio para cada ligação ao cliente. À medida que o número de utilizadores simultâneos aumenta, há uma diminuição correspondente no desempenho. Muitos fios ativos podem impactar significativamente o desempenho devido ao aumento da comutação de contexto, contenção de fios e má localidade para caches de CPU.

As piscinas de rosca, que é uma característica lateral do servidor e distinta da ligação, maximizam o desempenho introduzindo um conjunto dinâmico de fios de trabalhador que podem ser usados para limitar o número de fios ativos que correm no servidor e minimizar o churn de rosca. Isto ajuda a garantir que uma explosão de ligações não fará com que o servidor fique sem recursos ou se despenhe com um erro de memória. As piscinas de roscas são mais eficientes para consultas curtas e cargas de trabalho intensivas da CPU, por exemplo, cargas de trabalho OLTP.

Para saber mais sobre piscinas de fios, consulte a [Introdução de piscinas de fios na Base de Dados Azure para o MySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/introducing-thread-pools-in-azure-database-for-mysql-service/ba-p/1504173)

> [!NOTE]
> A função de piscina thread não é suportada para a versão MySQL 5.6. 

### <a name="configuring-the-thread-pool"></a>Configurar a piscina de fios
Para ativar a piscina de fios, atualize o parâmetro do `thread_handling` servidor para "pool-of-threads". Por predefinição, este parâmetro é definido para `one-thread-per-connection` , o que significa que o MySQL cria um novo fio para cada nova ligação. Por favor, note que este é um parâmetro estático e requer que um servidor reinicie para aplicar.

Também pode configurar o número máximo e mínimo de fios na piscina definindo os seguintes parâmetros do servidor: 
- `thread_pool_max_threads`: Este valor garante que não haverá mais do que este número de fios na piscina.
- `thread_pool_min_threads`: Este valor define o número de fios que serão reservados mesmo após o fecho das ligações.

Para melhorar as questões de desempenho de consultas curtas na piscina de fios, a Base de Dados Azure para o MySQL permite-lhe ativar a execução do lote onde, em vez de voltar à piscina de fios imediatamente após a execução de uma consulta, os fios manter-se-ão ativos por um curto período de tempo para aguardar a próxima consulta através desta ligação. Em seguida, o fio executa a consulta rapidamente e uma vez concluída, aguarda a próxima, até que o consumo total de tempo deste processo exceda um limiar. O comportamento de execução do lote é determinado utilizando os seguintes parâmetros do servidor:  

-  `thread_pool_batch_wait_timeout`: Este valor especifica o tempo que um fio espera que outra consulta processe.
- `thread_pool_batch_max_time`: Este valor determina o tempo máximo que um fio repetirá o ciclo de execução de consultas e aguarda a próxima consulta.

> [!IMPORTANT]
> Por favor, teste a piscina de rosca antes de ligá-la em produção. 

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

Na Base de Dados Azure para o MySQL, os registos binários estão sempre ativados (isto `log_bin` é, está definido para ON). Caso pretenda utilizar gatilhos, terá um erro semelhante ao *de si não ter o privilégio SUPER e a exploração madeireira binária está ativada (é possível utilizar a variável menos `log_bin_trust_function_creators` segura)*. 

O formato de registo binário é sempre **ROW** e todas as ligações ao servidor utilizam **sempre** o registo binário baseado na linha. Com a exploração binária baseada em linha, os problemas de segurança não existem e a exploração madeireira binária não pode quebrar, pelo que pode definir com segurança [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) para **TRUE**.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Reveja a documentação do [MySQL](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) para saber mais sobre este parâmetro.

#### <a name="servers-supporting-up-to-4-tb-storage"></a>Servidores que suportam até 4 armazenamento de TB

|**Escalão de Preço**|**vCore(s)**|**Valor predefinido (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Básico|1|872415232|134217728|872415232|
|Básico|2|2684354560|134217728|2684354560|
|Fins Gerais|2|3758096384|134217728|3758096384|
|Fins Gerais|4|8053063680|134217728|8053063680|
|Fins Gerais|8|16106127360|134217728|16106127360|
|Fins Gerais|16|32749125632|134217728|32749125632|
|Fins Gerais|32|66035122176|134217728|66035122176|
|Fins Gerais|64|132070244352|134217728|132070244352|
|Otimizada para Memória|2|7516192768|134217728|7516192768|
|Otimizada para Memória|4|16106127360|134217728|16106127360|
|Otimizada para Memória|8|32212254720|134217728|32212254720|
|Otimizada para Memória|16|65498251264|134217728|65498251264|
|Otimizada para Memória|32|132070244352|134217728|132070244352|

#### <a name="servers-support-up-to-16-tb-storage"></a>Servidores suportam até 16 armazenamento de TB

|**Escalão de Preço**|**vCore(s)**|**Valor predefinido (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Básico|1|872415232|134217728|872415232|
|Básico|2|2684354560|134217728|2684354560|
|Fins Gerais|2|7516192768|134217728|7516192768|
|Fins Gerais|4|16106127360|134217728|16106127360|
|Fins Gerais|8|32212254720|134217728|32212254720|
|Fins Gerais|16|65498251264|134217728|65498251264|
|Fins Gerais|32|132070244352|134217728|132070244352|
|Fins Gerais|64|264140488704|134217728|264140488704|
|Otimizada para Memória|2|15032385536|134217728|15032385536|
|Otimizada para Memória|4|32212254720|134217728|32212254720|
|Otimizada para Memória|8|64424509440|134217728|64424509440|
|Otimizada para Memória|16|130996502528|134217728|130996502528|
|Otimizada para Memória|32|264140488704|134217728|264140488704|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

> [!NOTE]
> `innodb_file_per_table` só podem ser atualizados nos níveis de preços otimizados para fins gerais e memória.

O MySQL armazena a tabela InnoDB em diferentes espaços de mesa com base na configuração que forneceu durante a criação da tabela. O [espaço de mesa do sistema](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) é a área de armazenamento do dicionário de dados InnoDB. Um [espaço de tabela de ficheiros por tabela](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) contém dados e índices para uma única tabela InnoDB, e é armazenado no sistema de ficheiros no seu próprio ficheiro de dados. Este comportamento é controlado pelo parâmetro do `innodb_file_per_table` servidor. A definição `innodb_file_per_table` faz com que o `OFF` InnoDB crie tabelas no espaço de tabela do sistema. Caso contrário, o InnoDB cria tabelas em espaços de mesa de ficheiros por mesa.

A Azure Database for MySQL suporta no maior, **1 TB,** num único ficheiro de dados. Se o tamanho da sua base de dados for superior a 1 TB, deverá criar a tabela no [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) espaço de mesa. Se tiver um tamanho de mesa maior que 1 TB, deve utilizar a mesa de partição.

### <a name="join_buffer_size"></a>join_buffer_size

Reveja a documentação do [MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) para saber mais sobre este parâmetro.

|**Escalão de Preço**|**vCore(s)**|**Valor predefinido (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Básico|1|Não configurável no nível básico|N/D|N/D|
|Básico|2|Não configurável no nível básico|N/D|N/D|
|Fins Gerais|2|262144|128|268435455|
|Fins Gerais|4|262144|128|536870912|
|Fins Gerais|8|262144|128|1073741824|
|Fins Gerais|16|262144|128|2147483648|
|Fins Gerais|32|262144|128|4294967295|
|Fins Gerais|64|262144|128|4294967295|
|Otimizada para Memória|2|262144|128|536870912|
|Otimizada para Memória|4|262144|128|1073741824|
|Otimizada para Memória|8|262144|128|2147483648|
|Otimizada para Memória|16|262144|128|4294967295|
|Otimizada para Memória|32|262144|128|4294967295|

### <a name="max_connections"></a>max_connections

|**Escalão de Preço**|**vCore(s)**|**Valor predefinido**|**Valor mínimo**|**Valor máximo**|
|---|---|---|---|---|
|Básico|1|50|10|50|
|Básico|2|100|10|100|
|Fins Gerais|2|300|10|600|
|Fins Gerais|4|625|10|1250|
|Fins Gerais|8|1250|10|2500|
|Fins Gerais|16|2500|10|5000|
|Fins Gerais|32|5000|10|10000|
|Fins Gerais|64|10000|10|20 000|
|Otimizada para Memória|2|625|10|1250|
|Otimizada para Memória|4|1250|10|2500|
|Otimizada para Memória|8|2500|10|5000|
|Otimizada para Memória|16|5000|10|10000|
|Otimizada para Memória|32|10000|10|20 000|

Quando as ligações excederem o limite, poderá receber o seguinte erro:
> ERRO 1040 (08004): Demasiadas ligações

> [!IMPORTANT]
> Para uma melhor experiência, recomendamos que utilize um pooler de conexão como o ProxySQL para gerir eficientemente as ligações.

Criar novas ligações de clientes ao MySQL leva tempo e uma vez estabelecidos, estas ligações ocupam recursos de base de dados, mesmo quando inativos. A maioria das aplicações solicita muitas ligações de curta duração, o que agrava esta situação. O resultado é menos recursos disponíveis para a sua carga de trabalho real, levando à diminuição do desempenho. Um pooler de ligação que diminui as ligações ociosas e reutiliza as ligações existentes ajudará a evitar isso. Para saber sobre a criação do ProxySQL, visite o nosso [blog post.](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)

>[!Note]
>ProxySQL é uma ferramenta comunitária de código aberto. É suportado pela Microsoft numa base de melhor esforço. Para obter apoio de produção com orientação autoritária, você pode avaliar e contactar o suporte do [produto ProxySQL](https://proxysql.com/services/support/).

### <a name="max_heap_table_size"></a>max_heap_table_size

Reveja a documentação do [MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) para saber mais sobre este parâmetro.

|**Escalão de Preço**|**vCore(s)**|**Valor predefinido (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Básico|1|Não configurável no nível básico|N/D|N/D|
|Básico|2|Não configurável no nível básico|N/D|N/D|
|Fins Gerais|2|16777216|16384|268435455|
|Fins Gerais|4|16777216|16384|536870912|
|Fins Gerais|8|16777216|16384|1073741824|
|Fins Gerais|16|16777216|16384|2147483648|
|Fins Gerais|32|16777216|16384|4294967295|
|Fins Gerais|64|16777216|16384|4294967295|
|Otimizada para Memória|2|16777216|16384|536870912|
|Otimizada para Memória|4|16777216|16384|1073741824|
|Otimizada para Memória|8|16777216|16384|2147483648|
|Otimizada para Memória|16|16777216|16384|4294967295|
|Otimizada para Memória|32|16777216|16384|4294967295|

### <a name="query_cache_size"></a>query_cache_size

A cache de consulta é desligada por defeito. Para ativar a cache de consulta, configuure o `query_cache_type` parâmetro. 

Reveja a documentação do [MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) para saber mais sobre este parâmetro.

> [!NOTE]
> A cache de consulta é depreciada a partir de MySQL 5.7.20 e foi removida no MySQL 8.0

|**Escalão de Preço**|**vCore(s)**|**Valor predefinido (bytes)**|**Valor mínimo (bytes)**|**Valor máximo **|
|---|---|---|---|---|
|Básico|1|Não configurável no nível básico|N/D|N/D|
|Básico|2|Não configurável no nível básico|N/D|N/D|
|Fins Gerais|2|0|0|16777216|
|Fins Gerais|4|0|0|33554432|
|Fins Gerais|8|0|0|67108864|
|Fins Gerais|16|0|0|134217728|
|Fins Gerais|32|0|0|134217728|
|Fins Gerais|64|0|0|134217728|
|Otimizada para Memória|2|0|0|33554432|
|Otimizada para Memória|4|0|0|67108864|
|Otimizada para Memória|8|0|0|134217728|
|Otimizada para Memória|16|0|0|134217728|
|Otimizada para Memória|32|0|0|134217728|

### <a name="lower_case_table_names"></a>lower_case_table_names

O lower_case_table_name está definido para 1 por padrão e pode atualizar este parâmetro no MySQL 5.6 e No MySQL 5.7

Reveja a documentação do [MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_lower_case_table_names) para saber mais sobre este parâmetro.

> [!NOTE]
> No MySQL 8.0, o lower_case_table_name está definido para 1 por padrão e não é possível alterá-lo.

### <a name="innodb_strict_mode"></a>innodb_strict_mode

Se receber um erro semelhante ao "Tamanho da linha demasiado grande (> 8126)" então é melhor desligar o parâmetro **innodb_strict_mode**. O parâmetro do servidor **innodb_strict_mode** não é permitido ser modificado globalmente ao nível do servidor porque se o tamanho dos dados de linha for superior a 8k, os dados serão truncados sem um erro que conduza à perda de dados potenciais. Recomendamos modificar o esquema para se ajustar ao limite de tamanho da página. 

Este parâmetro pode ser definido a um nível de sessão utilizando `init_connect` . Para definir **innodb_strict_mode** ao nível da sessão, consulte o [parâmetro de definição não listado](https://docs.microsoft.com/azure/mysql/howto-server-parameters#setting-parameters-not-listed).

> [!NOTE]
> Se tiver um servidor de réplica de leitura, a definição **innodb_strict_mode** para OFF ao nível da sessão num servidor de origem quebrará a replicação. Sugerimos manter o parâmetro definido para OFF se tiver lido réplicas.

### <a name="sort_buffer_size"></a>sort_buffer_size

Reveja a documentação do [MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) para saber mais sobre este parâmetro.

|**Escalão de Preço**|**vCore(s)**|**Valor predefinido (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Básico|1|Não configurável no nível básico|N/D|N/D|
|Básico|2|Não configurável no nível básico|N/D|N/D|
|Fins Gerais|2|524288|32768|4194304|
|Fins Gerais|4|524288|32768|8388608|
|Fins Gerais|8|524288|32768|16777216|
|Fins Gerais|16|524288|32768|33554432|
|Fins Gerais|32|524288|32768|33554432|
|Fins Gerais|64|524288|32768|33554432|
|Otimizada para Memória|2|524288|32768|8388608|
|Otimizada para Memória|4|524288|32768|16777216|
|Otimizada para Memória|8|524288|32768|33554432|
|Otimizada para Memória|16|524288|32768|33554432|
|Otimizada para Memória|32|524288|32768|33554432|

### <a name="tmp_table_size"></a>tmp_table_size

Reveja a documentação do [MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) para saber mais sobre este parâmetro.

|**Escalão de Preço**|**vCore(s)**|**Valor predefinido (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Básico|1|Não configurável no nível básico|N/D|N/D|
|Básico|2|Não configurável no nível básico|N/D|N/D|
|Fins Gerais|2|16777216|1024|67108864|
|Fins Gerais|4|16777216|1024|134217728|
|Fins Gerais|8|16777216|1024|268435456|
|Fins Gerais|16|16777216|1024|536870912|
|Fins Gerais|32|16777216|1024|1073741824|
|Fins Gerais|64|16777216|1024|1073741824|
|Otimizada para Memória|2|16777216|1024|134217728|
|Otimizada para Memória|4|16777216|1024|268435456|
|Otimizada para Memória|8|16777216|1024|536870912|
|Otimizada para Memória|16|16777216|1024|1073741824|
|Otimizada para Memória|32|16777216|1024|1073741824|

### <a name="time_zone"></a>time_zone

Após a implementação inicial, um servidor Azure para o MySQL inclui tabelas de sistemas para informações de fuso horário, mas estas tabelas não são povoadas. As tabelas do fuso horário podem ser povoadas chamando o `mysql.az_load_timezone` procedimento armazenado a partir de uma ferramenta como a linha de comando MySQL ou a bancada mySQL Workbench. Consulte o [portal Azure](howto-server-parameters.md#working-with-the-time-zone-parameter) ou os artigos [do Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) para saber como ligar para o procedimento armazenado e definir os fusos horários globais ou ao nível da sessão.

## <a name="non-configurable-server-parameters"></a>Parâmetros de servidor não configuráveis

Os parâmetros do servidor abaixo não são configuráveis no serviço:

|**Parâmetro**|**Valor fixo**|
| :------------------------ | :-------- |
|innodb_file_per_table no nível básico|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256MB|
|innodb_log_files_in_group|2|

Outras variáveis não listadas aqui são definidas para os valores padrão MySQL fora da caixa. Consulte os docs MySQL para versões [8.0](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html), [5.7](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)e [5.6](https://dev.mysql.com/doc/refman/5.6/en/server-system-variables.html) para os valores predefinidos. 

## <a name="next-steps"></a>Passos seguintes

- Saiba como [configurar parâmetros de corte utilizando o portal Azure](./howto-server-parameters.md)
- Saiba como [configurar parâmetros de corte utilizando o Azure CLI](./howto-configure-server-parameters-using-cli.md)
- Saiba como [configurar parâmetros de corte usando PowerShell](./howto-configure-server-parameters-using-powershell.md)
