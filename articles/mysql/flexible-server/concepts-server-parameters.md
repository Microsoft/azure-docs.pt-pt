---
title: Parâmetros do servidor - Base de Dados Azure para MySQL - Servidor Flexível
description: Este tópico fornece diretrizes para configurar parâmetros do servidor na Base de Dados Azure para o MySQL - Servidor Flexível.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: f83f743b692ae5a625a4c881b12cbad999f1f606
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105106773"
---
# <a name="server-parameters-in-azure-database-for-mysql---flexible-server"></a>Parâmetros do servidor na Base de Dados Azure para MySQL - Servidor Flexível

> [!IMPORTANT]
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Este artigo fornece considerações e diretrizes para configurar parâmetros do servidor na Base de Dados Azure para o servidor flexível MySQL.

## <a name="what-are-server-variables"></a>O que são variáveis de servidor? 

O motor MySQL fornece muitas [variáveis/parâmetros](https://dev.mysql.com/doc/refman/5.7/en/server-option-variable-reference.html) de servidor diferentes que podem ser usados para configurar e afinar o comportamento do motor. Alguns parâmetros podem ser definidos dinamicamente durante o tempo de funcionamento, enquanto outros são "estáticos", exigindo um reinício do servidor para se aplicar.

A Azure Database for MySQL Flexible Server expõe a capacidade de alterar o valor de vários parâmetros do servidor MySQL utilizando o [portal Azure](./how-to-configure-server-parameters-portal.md) e [O CLI Azure](./how-to-configure-server-parameters-cli.md) para corresponder às necessidades da sua carga de trabalho.

## <a name="configurable-server-parameters"></a>Parâmetros de servidor configuráveis

Pode gerir a base de dados Azure para a configuração do Servidor Flexível MySQL utilizando parâmetros do servidor. Os parâmetros do servidor são configurados com o valor predefinido e recomendado quando cria o servidor. A lâmina do parâmetro do servidor no portal Azure mostra os parâmetros do servidor modificáveis e não modificáveis. Os parâmetros do servidor não modificáveis estão acinzentados.

A lista de parâmetros suportados do servidor está em constante crescimento. Utilize o separador parâmetros do servidor no portal Azure para visualizar a lista completa e configurar os valores dos parâmetros do servidor.

Consulte as seguintes secções abaixo para saber mais sobre os limites dos vários parâmetros do servidor comumente atualizados. Os limites são determinados pelo nível de cálculo e tamanho (vCores) do servidor.

> [!NOTE]
> Se procura modificar um parâmetro do servidor que não é modificável mas que gostaria de ver como um modificável para o seu ambiente, por favor abra um item [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) ou vote se o feedback já existir que nos possa ajudar a priorizar.

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

Na Base de Dados Azure para o MySQL Flexible Server, os registos binários estão sempre ativados (isto é, `log_bin` está definido para ON). Caso pretenda utilizar gatilhos, terá um erro semelhante ao *de si não ter o privilégio SUPER e a exploração madeireira binária está ativada (é possível utilizar a variável menos `log_bin_trust_function_creators` segura)*. 

O formato de registo binário é sempre **ROW** e todas as ligações ao servidor utilizam **sempre** o registo binário baseado na linha. Com a exploração binária baseada em linha, os problemas de segurança não existem e a exploração madeireira binária não pode quebrar, pelo que pode definir com segurança [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) para **TRUE**.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Reveja a documentação do [MySQL](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) para saber mais sobre este parâmetro.

|**Escalão de Preço**|**vCore(s)**|**Tamanho da memória (GiB)**|**Valor predefinido (bytes)**|**Valor mínimo (bytes)**|**Valor máximo (bytes)**|
|---|---|---|---|---|---|
|Rebentado (B1s)|1|1|134217728|33554432|134217728|
|Rebentado (B1ms)|1|2|536870912|134217728|536870912|
|Rebentado|2|4|2147483648|134217728|2147483648|
|Fins Gerais|2|8|6442450944|134217728|6442450944|
|Fins Gerais|4|16|12884901888|134217728|12884901888|
|Fins Gerais|8|32|25769803776|134217728|25769803776|
|Fins Gerais|16|64|51539607552|134217728|51539607552|
|Fins Gerais|32|128|103079215104|134217728|103079215104|
|Fins Gerais|48|192|154618822656|134217728|154618822656|
|Fins Gerais|64|256|206158430208|134217728|206158430208|
|Otimizada para Memória|2|16|12884901888|134217728|12884901888|
|Otimizada para Memória|4|32|25769803776|134217728|25769803776|
|Otimizada para Memória|8|64|51539607552|134217728|51539607552|
|Otimizada para Memória|16|128|103079215104|134217728|103079215104|
|Otimizada para Memória|32|256|206158430208|134217728|206158430208|
|Otimizada para Memória|48|384|309237645312|134217728|309237645312|
|Otimizada para Memória|64|504|405874409472|134217728|405874409472|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

O MySQL armazena a tabela InnoDB em diferentes espaços de mesa com base na configuração que forneceu durante a criação da tabela. O [espaço de mesa do sistema](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) é a área de armazenamento do dicionário de dados InnoDB. Um [espaço de tabela de ficheiros por tabela](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) contém dados e índices para uma única tabela InnoDB, e é armazenado no sistema de ficheiros no seu próprio ficheiro de dados. Este comportamento é controlado pelo parâmetro do `innodb_file_per_table` servidor. A definição `innodb_file_per_table` faz com que o `OFF` InnoDB crie tabelas no espaço de tabela do sistema. Caso contrário, o InnoDB cria tabelas em espaços de mesa de ficheiros por mesa.

A base de dados Azure para o MySQL Flexible Server suporta no maior, **4 TB,** num único ficheiro de dados. Se o tamanho da sua base de dados for superior a 4 TB, deverá criar a tabela no [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) espaço de mesa. Se tiver um tamanho de mesa superior a 4 TB, deve utilizar a tabela de divisórias.

### <a name="max_connections"></a>max_connections

O valor de max_connection é determinado pelo tamanho da memória do servidor. 

|**Escalão de Preço**|**vCore(s)**|**Tamanho da memória (GiB)**|**Valor predefinido**|**Valor mínimo**|**Valor máximo**|
|---|---|---|---|---|---|
|Rebentado (B1s)|1|1|85|10|171|
|Rebentado (B1ms)|1|2|171|10|341|
|Rebentado|2|4|341|10|683|
|Fins Gerais|2|8|683|10|1365|
|Fins Gerais|4|16|1365|10|2731|
|Fins Gerais|8|32|2731|10|5461|
|Fins Gerais|16|64|5461|10|10923|
|Fins Gerais|32|128|10923|10|21845|
|Fins Gerais|48|192|16384|10|32768|
|Fins Gerais|64|256|21845|10|43691|
|Otimizada para Memória|2|16|1365|10|2731|
|Otimizada para Memória|4|32|2731|10|5461|
|Otimizada para Memória|8|64|5461|10|10923|
|Otimizada para Memória|16|128|10923|10|21845|
|Otimizada para Memória|32|256|21845|10|43691|
|Otimizada para Memória|48|384|32768|10|65536|
|Otimizada para Memória|64|504|43008|10|86016|

Quando as ligações excederem o limite, poderá receber o seguinte erro:
> ERRO 1040 (08004): Demasiadas ligações

> [!IMPORTANT]
> Para uma melhor experiência, recomendamos que utilize um pooler de conexão como o ProxySQL para gerir eficientemente as ligações.

Criar novas ligações de clientes ao MySQL leva tempo e uma vez estabelecidos, estas ligações ocupam recursos de base de dados, mesmo quando inativos. A maioria das aplicações solicita muitas ligações de curta duração, o que agrava esta situação. O resultado é menos recursos disponíveis para a sua carga de trabalho real, levando à diminuição do desempenho. Um pooler de ligação que diminui as ligações ociosas e reutiliza as ligações existentes ajudará a evitar isso. Para saber sobre a criação do ProxySQL, visite o nosso [blog post.](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)

>[!Note]
>ProxySQL é uma ferramenta comunitária de código aberto. É suportado pela Microsoft numa base de melhor esforço. Para obter apoio de produção com orientação autoritária, você pode avaliar e contactar o suporte do [produto ProxySQL](https://proxysql.com/services/support/).

### <a name="innodb_strict_mode"></a>innodb_strict_mode

Se receber um erro semelhante ao "Tamanho da linha demasiado grande (> 8126)", é melhor desligar o parâmetro **innodb_strict_mode**. O parâmetro do servidor **innodb_strict_mode** não é permitido ser modificado globalmente ao nível do servidor porque se o tamanho dos dados de linha for superior a 8k, os dados serão truncados sem um erro, o que pode levar à perda de dados potenciais. Recomendamos modificar o esquema para se ajustar ao limite de tamanho da página. 

Este parâmetro pode ser definido a um nível de sessão utilizando `init_connect` . Para definir **innodb_strict_mode** ao nível da sessão, consulte o [parâmetro de definição não listado](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters).

> [!NOTE]
> Se tiver um servidor de réplica de leitura, a definição **innodb_strict_mode** para OFF ao nível da sessão num servidor de origem quebrará a replicação. Sugerimos manter o parâmetro definido para OFF se tiver lido réplicas.

### <a name="time_zone"></a>time_zone

Após a implementação inicial, um Azure for MySQL Flexible Server inclui tabelas de sistemas para informações de fuso horário, mas estas tabelas não são povoadas. As tabelas do fuso horário podem ser povoadas chamando o `mysql.az_load_timezone` procedimento armazenado a partir de uma ferramenta como a linha de comando MySQL ou a bancada mySQL Workbench. Consulte o [portal Azure](./how-to-configure-server-parameters-portal.md#working-with-the-time-zone-parameter) ou os artigos [do Azure CLI](./how-to-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) para saber como ligar para o procedimento armazenado e definir os fusos horários globais ou ao nível da sessão.

## <a name="non-modifiable-server-parameters"></a>Parâmetros de servidor não modificáveis

A lâmina do parâmetro do servidor no portal Azure mostra os parâmetros do servidor modificáveis e não modificáveis. Os parâmetros do servidor não modificáveis estão acinzentados. Se pretender configurar um parâmetro de servidor não modificável ao nível da sessão, consulte o [portal Azure](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters) ou o artigo [do Azure CLI](./how-to-configure-server-parameters-cli.md#setting-non-modifiable-server-parameters) para definir o parâmetro ao nível de ligação utilizando `init_connect` .

## <a name="next-steps"></a>Passos seguintes

- Como configurar [parâmetros do servidor no portal Azure](./how-to-configure-server-parameters-portal.md)
- Como configurar [os parâmetros do servidor no Azure CLI](./how-to-configure-server-parameters-cli.md)
