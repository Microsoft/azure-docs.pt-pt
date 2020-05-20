---
title: Limitações - Base de Dados Azure para MySQL
description: Este artigo descreve limitações na Base de Dados Azure para o MySQL, tais como o número de opções de motor de ligação e armazenamento.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 9cf5c958a0dd9a19e6b976ff36a18c45e062f604
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659925"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Limitações na Base de Dados Azure para MySQL
As seguintes secções descrevem capacidade, suporte do motor de armazenamento, suporte privilegiado, suporte à declaração de manipulação de dados e limites funcionais no serviço de base de dados. Consulte também [as limitações gerais](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) aplicáveis ao motor de base de dados MySQL.

## <a name="server-parameters"></a>Parâmetros do servidor

Os valores mínimos e máximos de vários parâmetros populares do servidor são determinados pelo nível de preços e vCores. Consulte as tabelas abaixo para limites.

### <a name="max_connections"></a>max_connections

|**Nível de Preços**|**vCore(s)**|**Valor por defeito**|**Valor do min**|**Valor máximo**|
|---|---|---|---|---|
|Básico|1|50|10|50|
|Básico|2|100|10|100|
|Fins Gerais|2|300|10|600|
|Fins Gerais|4|625|10|1250|
|Fins Gerais|8|1250|10|2500|
|Fins Gerais|16|2500|10|5000|
|Fins Gerais|32|5000|10|10000|
|Fins Gerais|64|10000|10|20 000|
|Otimizada para Memória|2|600|10|800|
|Otimizada para Memória|4|1250|10|2500|
|Otimizada para Memória|8|2500|10|5000|
|Otimizada para Memória|16|5000|10|10000|
|Otimizada para Memória|32|10000|10|20 000|

Quando as ligações excederem o limite, poderá receber o seguinte erro:
> ERRO 1040 (08004): Demasiadas ligações

> [!IMPORTANT]
> Para uma melhor experiência, recomendamos que utilize um pooler de ligação como proxySQL para gerir eficientemente as ligações.

A criação de novas ligações de clientes ao MySQL leva tempo e uma vez estabelecidas, estas ligações ocupam recursos de base de dados, mesmo quando estão inativas. A maioria das aplicações solicita muitas ligações de curta duração, o que compõe esta situação. O resultado é menos recursos disponíveis para a sua carga de trabalho real, levando a uma diminuição do desempenho. Um pooler de ligação que diminui as ligações inativas e reutiliza as ligações existentes ajudará a evitar isso. Para aprender sobre a configuração do ProxySQL, visite o nosso post de [blog.](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)

### <a name="query_cache_size"></a>query_cache_size

A cache de consulta é desligada por defeito. Para ativar a cache de consulta, configure o `query_cache_type` parâmetro. 

Reveja a [documentação mySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) para saber mais sobre este parâmetro.

> [!NOTE]
> A cache de consulta é depreciada a partir de MySQL 5.7.20 e foi removida em MySQL 8.0

|**Nível de Preços**|**vCore(s)**|**Valor por defeito**|**Valor do min**|**Valor máximo**|
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

### <a name="sort_buffer_size"></a>sort_buffer_size

Reveja a [documentação mySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) para saber mais sobre este parâmetro.

|**Nível de Preços**|**vCore(s)**|**Valor por defeito**|**Valor do min**|**Valor máximo**|
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

### <a name="join_buffer_size"></a>join_buffer_size

Reveja a [documentação mySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) para saber mais sobre este parâmetro.

|**Nível de Preços**|**vCore(s)**|**Valor por defeito**|**Valor do min**|**Valor máximo**|
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

### <a name="max_heap_table_size"></a>max_heap_table_size

Reveja a [documentação mySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) para saber mais sobre este parâmetro.

|**Nível de Preços**|**vCore(s)**|**Valor por defeito**|**Valor do min**|**Valor máximo**|
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

### <a name="tmp_table_size"></a>tmp_table_size

Reveja a [documentação mySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) para saber mais sobre este parâmetro.

|**Nível de Preços**|**vCore(s)**|**Valor por defeito**|**Valor do min**|**Valor máximo**|
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

As tabelas de fuso horário podem ser povoadas ligando para o procedimento armazenado a partir de uma ferramenta como a linha de `mysql.az_load_timezone` comando MySQL ou a bancada de trabalho MySQL. Consulte os artigos do [portal Azure](howto-server-parameters.md#working-with-the-time-zone-parameter) ou [do Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) para saber como ligar para o procedimento armazenado e definir os fusos horários globais ou de nível de sessão.

### <a name="innodb_file_per_table"></a>innodb_file_per_table

A MySQL armazena a tabela InnoDB em diferentes espaços de mesa com base na configuração que forneceu durante a criação da tabela. O espaço de mesa do [sistema](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) é a área de armazenamento do dicionário de dados InnoDB. Um [espaço de mesa de arquivo](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) por mesa contém dados e índices para uma única tabela InnoDB, e é armazenado no sistema de ficheiros no seu próprio ficheiro de dados. Este comportamento é controlado pelo parâmetro do `innodb_file_per_table` servidor. A definição `innodb_file_per_table` para fazer com que o `OFF` InnoDB crie tabelas no espaço de mesa do sistema. Caso contrário, o InnoDB cria tabelas em espaços de mesa de arquivo por mesa.

A Base de Dados Azure para MySQL suporta na maior, **1 TB,** num único ficheiro de dados. Se o tamanho da sua base de dados for superior a 1 TB, deverá criar a tabela em [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) espaço de mesa. Se tiver um único tamanho de mesa maior do que 1 TB, deve utilizar a mesa de partição.

## <a name="storage-engine-support"></a>Suporte do motor de armazenamento

### <a name="supported"></a>Suportado
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMÓRIA](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Não suportado
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BURACO NEGRO](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARQUIVO](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERADO](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Apoio ao privilégio

### <a name="unsupported"></a>Não suportado
- Função DBA: Muitos parâmetros e definições do servidor podem inadvertidamente degradar o desempenho do servidor ou anular as propriedades acid do DBMS. Como tal, para manter a integridade do serviço e sLA a nível do produto, este serviço não expõe o papel de DBA. A conta de utilizador predefinida, que é construída quando uma nova instância de base de dados é criada, permite que esse utilizador execute a maioria das declarações de DDL e DML na instância de base de dados gerida. 
- Super privilégio: Igualmente [super privilégio](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) também é restrito.
- DEFINER: Requer super privilégios para criar e é restrito. Se importar dados utilizando uma cópia de segurança, retire os `CREATE DEFINER` comandos manualmente ou utilizando o `--skip-definer` comando ao executar um mysqldump.

## <a name="data-manipulation-statement-support"></a>Suporte à declaração de manipulação de dados

### <a name="supported"></a>Suportado
- `LOAD DATA INFILE`é suportado, mas o `[LOCAL]` parâmetro deve ser especificado e direcionado para um caminho unc (armazenamento Azure montado através de SMB).

### <a name="unsupported"></a>Não suportado
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Limitações funcionais

### <a name="scale-operations"></a>Operações à escala
- Atualmente, a escala dinâmica de e para os níveis de preços básicos não é suportada.
- O tamanho de armazenamento do servidor não é suportado.

### <a name="server-version-upgrades"></a>Atualizações da versão do servidor
- Atualmente, a migração automatizada entre as principais versões do motor de base de dados não é suportada. Se quiser fazer upgrade para a próxima versão principal, [despeje-a e restaure-a](./concepts-migrate-dump-restore.md) para um servidor que foi criado com a nova versão do motor.

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo
- Ao utilizar a função PITR, o novo servidor é criado com as mesmas configurações que o servidor em que se baseia.
- Restaurar um servidor eliminado não é suportado.

### <a name="vnet-service-endpoints"></a>VNet service endpoints (Pontos finais de serviço de VNet)
- O suporte para os pontos finais do serviço VNet destina-se apenas a servidores otimizados para fins gerais e memória.

### <a name="storage-size"></a>Tamanho do armazenamento
- Consulte [os níveis](concepts-pricing-tiers.md) de preços para os limites de tamanho de armazenamento por nível de preços.

## <a name="current-known-issues"></a>Questões conhecidas atuais
- A instância do servidor MySQL apresenta a versão do servidor errada após a ligação ser estabelecida. Para obter a versão correta do motor da instância do servidor, utilize o `select version();` comando.

## <a name="next-steps"></a>Passos seguintes
- [O que está disponível em cada nível de serviço](concepts-pricing-tiers.md)
- [Versões de base de dados MySQL suportadas](concepts-supported-versions.md)
