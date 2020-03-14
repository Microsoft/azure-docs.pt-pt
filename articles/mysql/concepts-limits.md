---
title: Limitações - Base de Dados Azure para MySQL
description: Este artigo descreve as limitações na base de dados do Azure para MySQL, como o número de ligação e opções de motor de armazenamento.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 6954f306e0d0a346bd8f39776d987af99f7574dd
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299095"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Limitações na base de dados do Azure para MySQL
As secções seguintes descrevem a capacidade, suporte ao mecanismo de armazenamento, o suporte de privilégio, manipulação de dados de suporte de instrução e limites funcionais no serviço de base de dados. Consulte também [as limitações gerais](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) aplicáveis ao motor de base de dados MySQL.

## <a name="server-parameters"></a>Parâmetros do servidor

Os valores mínimos e máximos de vários parâmetros populares do servidor são determinados pelo nível de preços e vCores. Consulte as tabelas abaixo para limites.

### <a name="max_connections"></a>max_connections

|**Escalão de Preço**|**vCore(s)**|**Default value** (Valor predefinido)|**Valor do min**|**Valor máximo**|
|---|---|---|---|---|
|Básica|1|50|10|50|
|Básica|2|100|10|100|
|Fins Gerais|2|300|10|600|
|Fins Gerais|4|625|10|1250|
|Fins Gerais|8|1250|10|2500|
|Fins Gerais|16|2500|10|5000|
|Fins Gerais|32|5000|10|10000|
|Fins Gerais|64|10000|10|20000|
|Memória Otimizada|2|600|10|800|
|Memória Otimizada|4|1250|10|2500|
|Memória Otimizada|8|2500|10|5000|
|Memória Otimizada|16|5000|10|10000|
|Memória Otimizada|32|10000|10|20000|

Quando as ligações excederem o limite, poderá receber o erro seguinte:
> Erro 1040 (08004): Demasiadas ligações

> [!IMPORTANT]
> Para uma melhor experiência, recomendamos que utilize um pooler de ligação como proxySQL para gerir eficientemente as ligações.

A criação de novas ligações de clientes ao MySQL leva tempo e uma vez estabelecidas, estas ligações ocupam recursos de base de dados, mesmo quando estão inativas. A maioria das aplicações solicita muitas ligações de curta duração, o que compõe esta situação. O resultado é menos recursos disponíveis para a sua carga de trabalho real, levando a uma diminuição do desempenho. Um pooler de ligação que diminui as ligações inativas e reutiliza as ligações existentes ajudará a evitar isso. Para aprender sobre a configuração do ProxySQL, visite o nosso post de [blog.](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)

## <a name="query_cache_size"></a>query_cache_size

A cache de consulta é desligada por defeito. Para ativar a cache de consulta, configure o parâmetro `query_cache_type`. 

Reveja a [documentação mySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) para saber mais sobre este parâmetro.

> [!NOTE]
> A cache de consulta é depreciada a partir de MySQL 5.7.20 e foi removida em MySQL 8.0

|**Escalão de Preço**|**vCore(s)**|**Default value** (Valor predefinido)|**Valor do min**|**Valor máximo**|
|---|---|---|---|---|
|Básica|1|Não configurável no nível básico|N/A|N/A|
|Básica|2|Não configurável no nível básico|N/A|N/A|
|Fins Gerais|2|0|0|16777216|
|Fins Gerais|4|0|0|33554432|
|Fins Gerais|8|0|0|67108864|
|Fins Gerais|16|0|0|134217728|
|Fins Gerais|32|0|0|134217728|
|Fins Gerais|64|0|0|134217728|
|Memória Otimizada|2|0|0|33554432|
|Memória Otimizada|4|0|0|67108864|
|Memória Otimizada|8|0|0|134217728|
|Memória Otimizada|16|0|0|134217728|
|Memória Otimizada|32|0|0|134217728|

## <a name="sort_buffer_size"></a>sort_buffer_size

Reveja a [documentação mySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) para saber mais sobre este parâmetro.

|**Escalão de Preço**|**vCore(s)**|**Default value** (Valor predefinido)|**Valor do min**|**Valor máximo**|
|---|---|---|---|---|
|Básica|1|Não configurável no nível básico|N/A|N/A|
|Básica|2|Não configurável no nível básico|N/A|N/A|
|Fins Gerais|2|524288|32768|4194304|
|Fins Gerais|4|524288|32768|8388608|
|Fins Gerais|8|524288|32768|16777216|
|Fins Gerais|16|524288|32768|33554432|
|Fins Gerais|32|524288|32768|33554432|
|Fins Gerais|64|524288|32768|33554432|
|Memória Otimizada|2|524288|32768|8388608|
|Memória Otimizada|4|524288|32768|16777216|
|Memória Otimizada|8|524288|32768|33554432|
|Memória Otimizada|16|524288|32768|33554432|
|Memória Otimizada|32|524288|32768|33554432|

## <a name="join_buffer_size"></a>join_buffer_size

Reveja a [documentação mySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) para saber mais sobre este parâmetro.

|**Escalão de Preço**|**vCore(s)**|**Default value** (Valor predefinido)|**Valor do min**|**Valor máximo**|
|---|---|---|---|---|
|Básica|1|Não configurável no nível básico|N/A|N/A|
|Básica|2|Não configurável no nível básico|N/A|N/A|
|Fins Gerais|2|262144|128|268435455|
|Fins Gerais|4|262144|128|536870912|
|Fins Gerais|8|262144|128|1073741824|
|Fins Gerais|16|262144|128|2147483648|
|Fins Gerais|32|262144|128|4294967295|
|Fins Gerais|64|262144|128|4294967295|
|Memória Otimizada|2|262144|128|536870912|
|Memória Otimizada|4|262144|128|1073741824|
|Memória Otimizada|8|262144|128|2147483648|
|Memória Otimizada|16|262144|128|4294967295|
|Memória Otimizada|32|262144|128|4294967295|

## <a name="max_heap_table_size"></a>max_heap_table_size

Reveja a [documentação mySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) para saber mais sobre este parâmetro.

|**Escalão de Preço**|**vCore(s)**|**Default value** (Valor predefinido)|**Valor do min**|**Valor máximo**|
|---|---|---|---|---|
|Básica|1|Não configurável no nível básico|N/A|N/A|
|Básica|2|Não configurável no nível básico|N/A|N/A|
|Fins Gerais|2|16777216|16384|268435455|
|Fins Gerais|4|16777216|16384|536870912|
|Fins Gerais|8|16777216|16384|1073741824|
|Fins Gerais|16|16777216|16384|2147483648|
|Fins Gerais|32|16777216|16384|4294967295|
|Fins Gerais|64|16777216|16384|4294967295|
|Memória Otimizada|2|16777216|16384|536870912|
|Memória Otimizada|4|16777216|16384|1073741824|
|Memória Otimizada|8|16777216|16384|2147483648|
|Memória Otimizada|16|16777216|16384|4294967295|
|Memória Otimizada|32|16777216|16384|4294967295|

## <a name="tmp_table_size"></a>tmp_table_size

Reveja a [documentação mySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) para saber mais sobre este parâmetro.

|**Escalão de Preço**|**vCore(s)**|**Default value** (Valor predefinido)|**Valor do min**|**Valor máximo**|
|---|---|---|---|---|
|Básica|1|Não configurável no nível básico|N/A|N/A|
|Básica|2|Não configurável no nível básico|N/A|N/A|
|Fins Gerais|2|16777216|1024|67108864|
|Fins Gerais|4|16777216|1024|134217728|
|Fins Gerais|8|16777216|1024|268435456|
|Fins Gerais|16|16777216|1024|536870912|
|Fins Gerais|32|16777216|1024|1073741824|
|Fins Gerais|64|16777216|1024|1073741824|
|Memória Otimizada|2|16777216|1024|134217728|
|Memória Otimizada|4|16777216|1024|268435456|
|Memória Otimizada|8|16777216|1024|536870912|
|Memória Otimizada|16|16777216|1024|1073741824|
|Memória Otimizada|32|16777216|1024|1073741824|

## <a name="storage-engine-support"></a>Suporte ao mecanismo de armazenamento

### <a name="supported"></a>Suportadas
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMÓRIA](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Não suportado
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BURACO NEGRO](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARQUIVO](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERADO](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Suporte de privilégio

### <a name="unsupported"></a>Não suportado
- Função DBA: várias definições de parâmetros do servidor e inadvertidamente podem degradar o desempenho do servidor ou negar as propriedades ACID do DBMS. Como tal, para manter a integridade de serviço e o SLA num nível de produto, este serviço não expõe a função DBA. A conta de usuário padrão, é criada quando é criada uma nova instância de base de dados, permite que o utilizador efetuar a maioria das instruções DDL e DML na instância gerida da base de dados. 
- Super privilégio: Igualmente [super privilégio](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) também é restrito.
- DEFINER: Requer super privilégios para criar e é restrito. Se importar dados utilizando uma cópia de segurança, remova o `CREATE DEFINER` comandos manualmente ou utilizando o comando `--skip-definer` ao executar um mysqldump.

## <a name="data-manipulation-statement-support"></a>Suporte de instrução de manipulação de dados

### <a name="supported"></a>Suportadas
- `LOAD DATA INFILE` é suportado, mas o parâmetro `[LOCAL]` deve ser especificado e direcionado para um caminho unc (armazenamento Azure montado através de SMB).

### <a name="unsupported"></a>Não suportado
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Limitações funcionais

### <a name="scale-operations"></a>Operações de dimensionamento
- Dimensionamento dinâmico de e para os escalões de preços básicos não é atualmente suportada.
- Não é suportada a diminuir o tamanho de armazenamento do servidor.

### <a name="server-version-upgrades"></a>Atualização de versão do servidor
- Migração automatizada entre as versões do motor de base de dados principal não é atualmente suportada. Se quiser fazer upgrade para a próxima versão principal, [despeje-a e restaure-a](./concepts-migrate-dump-restore.md) para um servidor que foi criado com a nova versão do motor.

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo
- Quando utilizar a funcionalidade PITR, é criado o novo servidor com as mesmas configurações que o servidor que baseia-se.
- Não é suportado restaurar um servidor foi eliminado.

### <a name="vnet-service-endpoints"></a>Pontos finais de serviço de VNet
- Suporte para pontos finais de serviço da VNet é apenas para fins gerais e memória otimizada de servidores.

### <a name="storage-size"></a>Tamanho do armazenamento
- Consulte [os níveis](concepts-pricing-tiers.md) de preços para os limites de tamanho de armazenamento por nível de preços.

## <a name="current-known-issues"></a>Atuais problemas conhecidos
- Instância do servidor MySQL apresenta a versão de servidor incorreto depois de ligação é estabelecida. Para obter a versão correta do motor da instância do servidor, utilize o comando `select version();`.

## <a name="next-steps"></a>Passos Seguintes
- [O que está disponível em cada nível de serviço](concepts-pricing-tiers.md)
- [Versões de base de dados MySQL suportadas](concepts-supported-versions.md)
