---
title: Parâmetros do servidor - Base de Dados Azure para MySQL
description: Este tópico fornece diretrizes para configurar parâmetros do servidor na Base de Dados Azure para o MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/5/2020
ms.openlocfilehash: f78e6969c98545ec0b9b3e0a0822d4b9ae35903a
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84562008"
---
# <a name="server-parameters-in-azure-database-for-mysql"></a>Parâmetros do servidor na Base de Dados Azure para o MySQL

Este artigo fornece considerações e diretrizes para configurar parâmetros do servidor na Base de Dados Azure para o MySQL.

## <a name="what-are-server-parameters"></a>Quais são os parâmetros do servidor? 

O motor MySQL fornece muitas variáveis/parâmetros de servidor diferentes que podem ser usados para configurar e afinar o comportamento do motor. Alguns parâmetros podem ser definidos dinamicamente durante o tempo de funcionamento, enquanto outros são "estáticos", exigindo um reinício do servidor para se aplicar.

A Azure Database for MySQL expõe a capacidade de alterar o valor de vários parâmetros do servidor MySQL utilizando o [portal Azure](./howto-server-parameters.md), [Azure CLI](./howto-configure-server-parameters-using-cli.md)e [PowerShell](./howto-configure-server-parameters-using-powershell.md) para corresponder às necessidades da sua carga de trabalho.

## <a name="configurable-server-parameters"></a>Parâmetros de servidor configuráveis

A lista de parâmetros suportados do servidor está em constante crescimento. Utilize o separador parâmetros do servidor no portal Azure para visualizar a lista completa e configurar os valores dos parâmetros do servidor.

Consulte as seguintes secções abaixo para saber mais sobre os limites dos vários parâmetros do servidor comumente atualizados. Os limites são determinados pelo nível de preços e vCores do servidor.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Reveja a documentação do [MySQL](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) para saber mais sobre este parâmetro.

#### <a name="servers-supporting-up-to-4-tb-storage"></a>Servidores que suportam até 4 armazenamento de TB

|**Nível de preços**|**vCore(s)**|**Valor predefinido (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Básica|1|872415232|134217728|872415232|
|Básica|2|2684354560|134217728|2684354560|
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

|**Nível de preços**|**vCore(s)**|**Valor predefinido (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Básica|1|872415232|134217728|872415232|
|Básica|2|2684354560|134217728|2684354560|
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
> `innodb_file_per_table`só podem ser atualizados nos níveis de preços otimizados para fins gerais e memória.

O MySQL armazena a tabela InnoDB em diferentes espaços de mesa com base na configuração que forneceu durante a criação da tabela. O [espaço de mesa do sistema](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) é a área de armazenamento do dicionário de dados InnoDB. Um [espaço de tabela de ficheiros por tabela](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) contém dados e índices para uma única tabela InnoDB, e é armazenado no sistema de ficheiros no seu próprio ficheiro de dados. Este comportamento é controlado pelo parâmetro do `innodb_file_per_table` servidor. A definição `innodb_file_per_table` faz com que o `OFF` InnoDB crie tabelas no espaço de tabela do sistema. Caso contrário, o InnoDB cria tabelas em espaços de mesa de ficheiros por mesa.

A Azure Database for MySQL suporta no maior, **1 TB,** num único ficheiro de dados. Se o tamanho da sua base de dados for superior a 1 TB, deverá criar a tabela no [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) espaço de mesa. Se tiver um tamanho de mesa maior que 1 TB, deve utilizar a mesa de partição.

### <a name="join_buffer_size"></a>join_buffer_size

Reveja a documentação do [MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) para saber mais sobre este parâmetro.

|**Nível de preços**|**vCore(s)**|**Valor predefinido (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Básica|1|Não configurável no nível básico|N/D|N/D|
|Básica|2|Não configurável no nível básico|N/D|N/D|
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

|**Nível de preços**|**vCore(s)**|**Valor predefinido**|**Valor mínimo**|**Valor máximo**|
|---|---|---|---|---|
|Básica|1|50|10|50|
|Básica|2|100|10|100|
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

### <a name="max_heap_table_size"></a>max_heap_table_size

Reveja a documentação do [MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) para saber mais sobre este parâmetro.

|**Nível de preços**|**vCore(s)**|**Valor predefinido (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Básica|1|Não configurável no nível básico|N/D|N/D|
|Básica|2|Não configurável no nível básico|N/D|N/D|
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

|**Nível de preços**|**vCore(s)**|**Valor predefinido (bytes)**|**Valor mínimo (bytes)**|**Valor máximo **|
|---|---|---|---|---|
|Básica|1|Não configurável no nível básico|N/D|N/D|
|Básica|2|Não configurável no nível básico|N/D|N/D|
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

### <a name="sort_buffer_size"></a>sort_buffer_size

Reveja a documentação do [MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) para saber mais sobre este parâmetro.

|**Nível de preços**|**vCore(s)**|**Valor predefinido (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Básica|1|Não configurável no nível básico|N/D|N/D|
|Básica|2|Não configurável no nível básico|N/D|N/D|
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

|**Nível de preços**|**vCore(s)**|**Valor predefinido (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|
|Básica|1|Não configurável no nível básico|N/D|N/D|
|Básica|2|Não configurável no nível básico|N/D|N/D|
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

As tabelas do fuso horário podem ser povoadas chamando o `mysql.az_load_timezone` procedimento armazenado a partir de uma ferramenta como a linha de comando MySQL ou a bancada mySQL Workbench. Consulte o [portal Azure](howto-server-parameters.md#working-with-the-time-zone-parameter) ou os artigos [do Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) para saber como ligar para o procedimento armazenado e definir os fusos horários globais ou ao nível da sessão.

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