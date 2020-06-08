---
title: Limitações - Base de Dados Azure para MariaDB
description: Este artigo descreve limitações na Base de Dados Azure para MariaDB, tais como o número de opções de motor de ligação e armazenamento.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/5/2020
ms.openlocfilehash: c99851125552873e63f8199bf7b206c5fad231be
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2020
ms.locfileid: "84484739"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Limitações na Base de Dados Azure para MariaDB
As secções seguintes descrevem capacidade, suporte ao motor de armazenamento, suporte ao privilégio, suporte à declaração de manipulação de dados e limites funcionais no serviço de base de dados.

## <a name="server-parameters"></a>Parâmetros do servidor

Os valores mínimos e máximos de vários parâmetros populares do servidor são determinados pelo nível de preços e vCores. Consulte as tabelas abaixo para obter limites.

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

Criar novas ligações de clientes com a MariaDB leva tempo e uma vez estabelecidos, estas ligações ocupam recursos de base de dados, mesmo quando inativos. A maioria das aplicações solicita muitas ligações de curta duração, o que agrava esta situação. O resultado é menos recursos disponíveis para a sua carga de trabalho real, levando à diminuição do desempenho. Um pooler de ligação que diminui as ligações ociosas e reutiliza as ligações existentes ajudará a evitar isso. Para saber sobre a criação do ProxySQL, visite o nosso [blog post.](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)

### <a name="query_cache_size"></a>query_cache_size

A cache de consulta é desligada por defeito. Para ativar a cache de consulta, configuure o `query_cache_type` parâmetro. 

Reveja a [documentação mariaDB](https://mariadb.com/kb/en/server-system-variables/#query_cache_size) para saber mais sobre este parâmetro.

|**Nível de preços**|**vCore(s)**|**Valor predefinido**|**Valor mínimo**|**Valor máximo**|
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

Reveja a [documentação mariaDB](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size) para saber mais sobre este parâmetro.

|**Nível de preços**|**vCore(s)**|**Valor predefinido**|**Valor mínimo**|**Valor máximo**|
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

### <a name="join_buffer_size"></a>join_buffer_size

Reveja a [documentação mariaDB](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size) para saber mais sobre este parâmetro.

|**Nível de preços**|**vCore(s)**|**Valor predefinido**|**Valor mínimo**|**Valor máximo**|
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

### <a name="max_heap_table_size"></a>max_heap_table_size

Reveja a [documentação mariaDB](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size) para saber mais sobre este parâmetro.

|**Nível de preços**|**vCore(s)**|**Valor predefinido**|**Valor mínimo**|**Valor máximo**|
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

### <a name="tmp_table_size"></a>tmp_table_size

Reveja a [documentação mariaDB](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size) para saber mais sobre este parâmetro.

|**Nível de preços**|**vCore(s)**|**Valor predefinido**|**Valor mínimo**|**Valor máximo**|
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

As tabelas do fuso horário podem ser povoadas chamando o `mysql.az_load_timezone` procedimento armazenado a partir de uma ferramenta como a linha de comando MySQL ou a bancada mySQL Workbench. Consulte o [portal Azure](howto-server-parameters.md#working-with-the-time-zone-parameter) ou os artigos [do Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) para saber como ligar para o procedimento armazenado e definir os fusos horários globais ou ao nível da sessão.

### <a name="innodb_file_per_table"></a>innodb_file_per_table

A MariaDB armazena a tabela InnoDB em diferentes espaços de mesa com base na configuração que forneceu durante a criação da tabela. O [espaço de mesa do sistema](https://mariadb.com/kb/en/innodb-system-tablespaces/) é a área de armazenamento do dicionário de dados InnoDB. Um [espaço de tabela de ficheiros por tabela](https://mariadb.com/kb/en/innodb-file-per-table-tablespaces/) contém dados e índices para uma única tabela InnoDB, e é armazenado no sistema de ficheiros no seu próprio ficheiro de dados. Este comportamento é controlado pelo parâmetro do `innodb_file_per_table` servidor. A definição `innodb_file_per_table` faz com que o `OFF` InnoDB crie tabelas no espaço de tabela do sistema. Caso contrário, o InnoDB cria tabelas em espaços de mesa de ficheiros por mesa.

A Azure Database for MariaDB suporta no maior, **1 TB,** num único ficheiro de dados. Se o tamanho da sua base de dados for superior a 1 TB, deverá criar a tabela no [innodb_file_per_table](https://mariadb.com/kb/en/innodb-system-variables/#innodb_file_per_table) espaço de mesa. Se tiver um tamanho de mesa maior que 1 TB, deve utilizar a mesa de partição.

## <a name="storage-engine-support"></a>Suporte ao motor de armazenamento

### <a name="supported"></a>Suportado
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMÓRIA](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Não suportado
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BURACO NEGRO](https://mariadb.com/kb/en/library/blackhole/)
- [ARQUIVO](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Apoio ao privilégio

### <a name="unsupported"></a>Não suportado
- Função DBA: Muitos parâmetros e configurações do servidor podem inadvertidamente degradar o desempenho do servidor ou anular as propriedades acid do DBMS. Como tal, para manter a integridade do serviço e sLA a nível de produto, este serviço não expõe o papel da DBA. A conta de utilizador predefinida, que é construída quando uma nova instância de base de dados é criada, permite que o utilizador execute a maioria das declarações de DDL e DML na instância da base de dados gerida.
- Super privilégio: Igualmente [o privilégio SUPER](https://mariadb.com/kb/en/library/grant/#global-privileges) também é restrito.
- DEFINER: Requer super privilégios para criar e é restrito. Se importar dados utilizando uma cópia de segurança, remova os `CREATE DEFINER` comandos manualmente ou utilizando o `--skip-definer` comando quando efetuar uma mesqldump.

## <a name="data-manipulation-statement-support"></a>Suporte à declaração de manipulação de dados

### <a name="supported"></a>Suportado
- `LOAD DATA INFILE`é suportado, mas o `[LOCAL]` parâmetro deve ser especificado e direcionado para um caminho UNC (armazenamento azul montado através de SMB).

### <a name="unsupported"></a>Não suportado
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Limitações funcionais

### <a name="scale-operations"></a>Operações de escala
- Atualmente, não é suportado um escalão dinâmico de e para os níveis básicos de preços.
- O tamanho de armazenamento do servidor diminui não é suportado.

### <a name="server-version-upgrades"></a>Atualizações da versão do servidor
- Atualmente, a migração automatizada entre as principais versões do motor da base de dados não é suportada.

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo
- Ao utilizar a função PITR, o novo servidor é criado com as mesmas configurações que o servidor em que se baseia.
- Restaurar um servidor eliminado não é suportado.

### <a name="subscription-management"></a>Gestão de subscrições
- Atualmente, os servidores pré-criados em movimento dinâmico em todo o grupo de subscrição e recursos não são suportados.

### <a name="vnet-service-endpoints"></a>VNet service endpoints (Pontos finais de serviço de VNet)
- O suporte para os pontos finais do serviço VNet é apenas para servidores otimizados para fins gerais e memória.

### <a name="storage-size"></a>Tamanho do armazenamento
- Consulte [os níveis de preços](concepts-pricing-tiers.md) para os limites de tamanho de armazenamento por nível de preços.

## <a name="current-known-issues"></a>Questões conhecidas atuais
- A instância do servidor MariaDB exibe a versão incorreta do servidor após a ligação ser estabelecida. Para obter a versão correta do motor de instância do servidor, utilize o `select version();` comando.

## <a name="next-steps"></a>Próximos passos
- [O que está disponível em cada nível de serviço](concepts-pricing-tiers.md)
- [Versões de base de dados MariaDB suportadas](concepts-supported-versions.md)
