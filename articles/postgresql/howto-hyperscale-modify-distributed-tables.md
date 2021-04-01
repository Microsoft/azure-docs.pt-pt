---
title: Modificar tabelas distribuídas - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Comandos SQL para criar e modificar tabelas distribuídas - Hiperescala (Citus) usando o portal Azure
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/10/2020
ms.openlocfilehash: cf9f9ca5b8690a38c6e5aa6f519378c0a2e3a4f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026442"
---
# <a name="distribute-and-modify-tables"></a>Distribuir e modificar tabelas

## <a name="distributing-tables"></a>Distribuir tabelas

Para criar uma tabela distribuída, é necessário definir primeiro o esquema de mesa. Para tal, pode definir uma tabela utilizando a declaração [CREATE TABLE](http://www.postgresql.org/docs/current/static/sql-createtable.html) da mesma forma que faria com uma tabela PostgreSQL regular.

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    actor jsonb,
    org jsonb,
    created_at timestamp
);
```

Em seguida, pode utilizar a função de \_ tabela distribuída para \_ especificar a coluna de distribuição da tabela e criar os fragmentos do trabalhador.

```sql
SELECT create_distributed_table('github_events', 'repo_id');
```

A chamada de função informa a Hyperscale (Citus) que a tabela de eventos do github \_ deve ser distribuída na coluna de repo \_ id (hashing o valor da coluna). A função também cria fragmentos nos nós do trabalhador utilizando os valores de configuração do fator de \_ replicação citus.shard e citus.shard. \_ \_

Cria um número total de citus.shard \_ count número de fragmentos, onde cada fragmento possui uma parte de um espaço de haxixe e é replicado com base no valor de configuração do fator de replicação padrão citus.shard. \_ \_ As réplicas de fragmentos criadas no trabalhador têm as mesmas definições de tabela, índice e restrição que a tabela no coordenador. Uma vez criadas as réplicas, a função guarda todos os metadados distribuídos no coordenador.

Cada fragmento criado é atribuído um ID de fragmento único e todas as suas réplicas têm o mesmo ID de fragmento. Os fragmentos são representados no nó do trabalhador como tabelas postgresQL regulares nomeadas \' shardid de nome de mesa \_ onde o nome de mesa é o nome da mesa \' distribuída, e o ID de fragmento é o ID único atribuído. Pode ligar-se aos pós-casos dos trabalhadores para visualizar ou executar comandos em fragmentos individuais.

Está agora pronto para inserir dados na tabela distribuída e executar consultas nele. Também pode saber mais sobre o UDF usado nesta secção na tabela e referência [DDL de fragmento.](reference-hyperscale-functions.md#table-and-shard-ddl)

### <a name="reference-tables"></a>Tabelas de Referência

O método acima distribui tabelas em múltiplos fragmentos horizontais.  Outra possibilidade é distribuir mesas num único fragmento e replicar o fragmento a cada nó de trabalhador. As tabelas distribuídas desta forma são chamadas *tabelas de referência.* São usados para armazenar dados que precisam de ser acedidos frequentemente por múltiplos nós num cluster.

Os candidatos comuns para as tabelas de referência incluem:

-   Mesas mais pequenas que precisam de se juntar a mesas distribuídas maiores.
-   Tabelas em aplicativos multi-inquilinos que não têm uma coluna de identificação de inquilino ou que não estão \' associados a um inquilino. (Ou, durante a migração, mesmo para algumas mesas associadas a um inquilino.)
-   Tabelas que precisam de constrangimentos únicos em várias colunas e são pequenas o suficiente.

Por exemplo, suponha que um site de eCommerce multi-inquilinos precise calcular o imposto de venda para transações em qualquer uma das suas lojas. A informação fiscal não é \' específica para qualquer inquilino. Faz sentido colocá-lo numa mesa partilhada. Uma tabela de referência centrada nos EUA pode ser assim:

```postgresql
-- a reference table

CREATE TABLE states (
  code char(2) PRIMARY KEY,
  full_name text NOT NULL,
  general_sales_tax numeric(4,3)
);

-- distribute it to all workers

SELECT create_reference_table('states');
```

Agora, consultas como um imposto de cálculo para um carrinho de compras podem juntar-se à `states` mesa sem sobrecarga de rede, e podem adicionar uma chave estrangeira ao código do Estado para uma melhor validação.

Além de distribuir uma tabela como um único fragmento replicado, o `create_reference_table` UDF marca-o como uma tabela de referência nas tabelas de metadados Hyperscale (Citus). A hiperescala (Citus) executa automaticamente compromissos em duas fases ([2PC](https://en.wikipedia.org/wiki/Two-phase_commit_protocol)) para modificações nas tabelas marcadas desta forma, o que proporciona fortes garantias de consistência.

Se tiver uma tabela distribuída com uma contagem de fragmentos de uma, pode atualizá-la para ser uma tabela de referência reconhecida como esta:

```postgresql
SELECT upgrade_to_reference_table('table_name');
```

Para outro exemplo de utilização de tabelas de referência, consulte o tutorial da [base de dados de vários inquilinos.](tutorial-design-database-hyperscale-multi-tenant.md)

### <a name="distributing-coordinator-data"></a>Distribuição de Dados do Coordenador

Se uma base de dados PostgreSQL existente for convertida no nó coordenador de um cluster De Hiperescala (Citus), os dados nas suas tabelas podem ser distribuídos de forma eficiente e com a mínima interrupção para uma aplicação.

A `create_distributed_table` função descrita trabalhos anteriores em tabelas vazias e não vazias, e para esta última distribui automaticamente linhas de mesa por todo o cluster. Saberá se copia dados pela presença da mensagem, \" AVISO: Copiar dados da tabela local \. .. \" Por exemplo:

```postgresql
CREATE TABLE series AS SELECT i FROM generate_series(1,1000000) i;
SELECT create_distributed_table('series', 'i');
NOTICE:  Copying data from local table...
 create_distributed_table
 --------------------------

 (1 row)
```

As gravações na tabela são bloqueadas enquanto os dados são migrados, e as gravações pendentes são tratadas como consultas distribuídas uma vez que a função se compromete. (Se a função falhar, as consultas tornam-se locais novamente.) As leituras podem continuar normalmente e tornar-se-ão consultas distribuídas assim que a função se comprometer.

Ao distribuir as tabelas A e B, onde A tem uma chave estrangeira para B, distribua primeiro a tabela de destino-chave B. Fazê-lo na ordem errada causará um erro:

```
ERROR:  cannot create foreign key constraint
DETAIL:  Referenced table must be a distributed table or a reference table.
```

Se não for possível distribuir na ordem correta, largue as teclas estrangeiras, distribua as tabelas e recrie as teclas estrangeiras.

Ao migrar dados de uma base de dados externa, como da Amazon RDS para a Nuvem De Hiperescala (Citus), primeiro criar as tabelas distribuídas hyperscale (Citus) através `create_distributed_table` , em seguida, copiar os dados para a tabela.
Copiar em mesas distribuídas evita ficar sem espaço no nó coordenador.

## <a name="colocating-tables"></a>Mesas de colocação

Colocação significa colocar informações relacionadas nas mesmas máquinas. Permite consultas eficientes, aproveitando ao mesmo tempo a escalabilidade horizontal para todo o conjunto de dados. Para mais informações, consulte [a colocação.](concepts-hyperscale-colocation.md)

As mesas estão em grupos. Para controlar manualmente a atribuição do grupo de colocação de uma mesa, utilize o parâmetro opcional `colocate_with` de `create_distributed_table` . Se não se \' importa com a colocação de uma \' mesa, omita este parâmetro. Predefini o valor `'default'` , que agru como a tabela com qualquer outra tabela de colocação predefinitiva com o mesmo tipo de coluna de distribuição, contagem de fragmentos e fator de replicação. Se quiser quebrar ou atualizar esta colocação implícita, pode usar `update_distributed_table_colocation()` .

```postgresql
-- these tables are implicitly co-located by using the same
-- distribution column type and shard count with the default
-- co-location group

SELECT create_distributed_table('A', 'some_int_col');
SELECT create_distributed_table('B', 'other_int_col');
```

Quando uma nova tabela não estiver relacionada com outras pessoas do seu grupo de colocação implícito, especifique `colocated_with => 'none'` .

```postgresql
-- not co-located with other tables

SELECT create_distributed_table('A', 'foo', colocate_with => 'none');
```

Dividir mesas não relacionadas nos seus próprios grupos de colocação melhorará o desempenho [do reequilíbrio de fragmentos,](howto-hyperscale-scale-rebalance.md) porque os fragmentos do mesmo grupo têm de ser movidos em conjunto.

Quando as tabelas estão efetivamente relacionadas (por exemplo, quando serão aderidas), pode fazer sentido alistá-las explicitamente. Os ganhos de colocação adequados são mais importantes do que qualquer reequilíbrio.

Para colocar explicitamente várias tabelas, distribua uma e, em seguida, coloque as outras no seu grupo de colocação. Por exemplo:

```postgresql
-- distribute stores
SELECT create_distributed_table('stores', 'store_id');

-- add to the same group as stores
SELECT create_distributed_table('orders', 'store_id', colocate_with => 'stores');
SELECT create_distributed_table('products', 'store_id', colocate_with => 'stores');
```

As informações sobre os grupos de cosão são armazenadas na tabela [pg_dist_colocation,](reference-hyperscale-metadata.md#colocation-group-table) enquanto [pg_dist_partition](reference-hyperscale-metadata.md#partition-table) revela quais as tabelas atribuídas aos grupos.

## <a name="dropping-tables"></a>Mesas de queda

Pode utilizar o comando padrão PostgreSQL DROP TABLE para remover as suas tabelas distribuídas. Tal como acontece com as tabelas regulares, o DROP TABLE remove quaisquer índices, regras, gatilhos e constrangimentos que existam para a tabela alvo. Além disso, também deixa cair os fragmentos nos nós dos trabalhadores e limpa os seus metadados.

```sql
DROP TABLE github_events;
```

## <a name="modifying-tables"></a>Modificação de tabelas

A hiperescala (Citus) propaga automaticamente muitos tipos de declarações de DDL.
A modificação de uma tabela distribuída no nó coordenador também atualizará os fragmentos dos trabalhadores. Outras declarações de DDL requerem propagação manual, e algumas outras são proibidas, como qualquer uma que modificaria uma coluna de distribuição.
A tentativa de funcionamento do DDL que não é elegível para a propagação automática levantará um erro e deixará as tabelas no nó coordenador inalteradas.

Aqui está uma referência das categorias de declarações DDL que se propagam.
A propagação automática pode ser ativada ou desativada com um [parâmetro de configuração](reference-hyperscale-parameters.md#citusenable_ddl_propagation-boolean)

### <a name="addingmodifying-columns"></a>Adicionar/Modificar colunas

A hiperescala (Citus) propaga a maioria dos comandos [ALTER TABLE](https://www.postgresql.org/docs/current/static/ddl-alter.html) automaticamente. Adicionar colunas ou alterar os seus valores predefinidos funciona como numa base de dados PostgreSQL de uma única máquina:

```postgresql
-- Adding a column

ALTER TABLE products ADD COLUMN description text;

-- Changing default value

ALTER TABLE products ALTER COLUMN price SET DEFAULT 7.77;
```

Alterações significativas numa coluna existente como renomeá-la ou alterar o seu tipo de dados também são boas. No entanto, o tipo de dados da [coluna de distribuição](concepts-hyperscale-nodes.md#distribution-column) não pode ser alterado.
Esta coluna determina como os dados de tabelas se distribuem através do cluster Hyperscale (Citus) e modificar o seu tipo de dados exigiria mover os dados.

Tentar fazê-lo causa um erro:

```postgres
-- assumining store_id is the distribution column
-- for products, and that it has type integer

ALTER TABLE products
ALTER COLUMN store_id TYPE text;

/*
ERROR:  XX000: cannot execute ALTER TABLE command involving partition column
LOCATION:  ErrorIfUnsupportedAlterTableStmt, multi_utility.c:2150
*/
```

### <a name="addingremoving-constraints"></a>Adição/Remoção de Restrições

A utilização da Hyperscale (Citus) permite-lhe continuar a usufruir da segurança de uma base de dados relacional, incluindo restrições de base de dados (ver [os docs](https://www.postgresql.org/docs/current/static/ddl-constraints.html)PostgreSQL).
Devido à natureza dos sistemas distribuídos, a Hyperscale (Citus) não cruzará os constrangimentos de singularidade ou a integridade referencial entre nós de trabalhadores.

Para configurar uma chave estrangeira entre as tabelas distribuídas, inclua sempre a coluna de distribuição na chave. Incluir a coluna de distribuição pode envolver a criação do composto-chave.

Podem ser criadas chaves estrangeiras nestas situações:

-   entre duas mesas locais (não distribuídas),
-   entre duas tabelas de referência,
-   entre duas tabelas distribuídas [com posição](concepts-hyperscale-colocation.md) quando a chave inclui a coluna de distribuição, ou
-   como uma tabela distribuída referindo uma [tabela de referência](concepts-hyperscale-nodes.md#type-2-reference-tables)

As teclas estrangeiras das tabelas de referência às tabelas distribuídas não são suportadas.

> [!NOTE]
>
> As chaves primárias e os constrangimentos de singularidade devem incluir a coluna de distribuição. Adicioná-las a uma coluna de não distribuição gerará um erro

Este exemplo mostra como criar chaves primárias e estrangeiras em tabelas distribuídas:

```postgresql
--
-- Adding a primary key
-- --------------------

-- We'll distribute these tables on the account_id. The ads and clicks
-- tables must use compound keys that include account_id.

ALTER TABLE accounts ADD PRIMARY KEY (id);
ALTER TABLE ads ADD PRIMARY KEY (account_id, id);
ALTER TABLE clicks ADD PRIMARY KEY (account_id, id);

-- Next distribute the tables

SELECT create_distributed_table('accounts', 'id');
SELECT create_distributed_table('ads',      'account_id');
SELECT create_distributed_table('clicks',   'account_id');

--
-- Adding foreign keys
-- -------------------

-- Note that this can happen before or after distribution, as long as
-- there exists a uniqueness constraint on the target column(s) which
-- can only be enforced before distribution.

ALTER TABLE ads ADD CONSTRAINT ads_account_fk
  FOREIGN KEY (account_id) REFERENCES accounts (id);
ALTER TABLE clicks ADD CONSTRAINT clicks_ad_fk
  FOREIGN KEY (account_id, ad_id) REFERENCES ads (account_id, id);
```

Da mesma forma, inclua a coluna de distribuição em restrições de singularidade:

```postgresql
-- Suppose we want every ad to use a unique image. Notice we can
-- enforce it only per account when we distribute by account id.

ALTER TABLE ads ADD CONSTRAINT ads_unique_image
  UNIQUE (account_id, image_url);
```

Constrangimentos não nulos podem ser aplicados a qualquer coluna (distribuição ou não) porque não requerem procuras entre trabalhadores.

```postgresql
ALTER TABLE ads ALTER COLUMN image_url SET NOT NULL;
```

### <a name="using-not-valid-constraints"></a>Utilização de Restrições NÃO VÁLIDAs

Em algumas situações, pode ser útil impor restrições para novas linhas, permitindo que as linhas existentes não conformes permaneçam inalteradas. A Hiperescala (Citus) suporta esta funcionalidade para restrições de VERIFICAção e teclas estrangeiras, utilizando a designação de restrição NÃO VÁLIDA do PostgreSQL. \' \" \"

Por exemplo, considere uma aplicação que armazena os perfis dos utilizadores numa [tabela de referência.](concepts-hyperscale-nodes.md#type-2-reference-tables)

```postgres
-- we're using the "text" column type here, but a real application
-- might use "citext" which is available in a postgres contrib module

CREATE TABLE users ( email text PRIMARY KEY );
SELECT create_reference_table('users');
```

No decorrer do tempo imaginem que alguns não-endereços entram na mesa.

```postgres
INSERT INTO users VALUES
   ('foo@example.com'), ('hacker12@aol.com'), ('lol');
```

Gostaríamos de validar os endereços, mas o PostgreSQL não nos permite normalmente adicionar uma restrição DE VERIFICA que falha para as linhas existentes. No *entanto,* permite uma restrição marcada não válida:

```postgres
ALTER TABLE users
ADD CONSTRAINT syntactic_email
CHECK (email ~
   '^[a-zA-Z0-9.!#$%&''*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$'
) NOT VALID;
```

Novas linhas estão agora protegidas.

```postgres
INSERT INTO users VALUES ('fake');

/*
ERROR:  new row for relation "users_102010" violates
        check constraint "syntactic_email_102010"
DETAIL:  Failing row contains (fake).
*/
```

Mais tarde, durante horas não de pico, um administrador de base de dados pode tentar corrigir as linhas más e revalidar a restrição.

```postgres
-- later, attempt to validate all rows
ALTER TABLE users
VALIDATE CONSTRAINT syntactic_email;
```

A documentação PostgreSQL tem mais informações sobre NÃO VALIDAÇÃO e VALIDAÇÃO DE RESTRIÇÃO na secção [TABELA ALTER.](https://www.postgresql.org/docs/current/sql-altertable.html)

### <a name="addingremoving-indices"></a>Adicionar/Remover Índices

A hiperescala (Citus) suporta a adição e remoção [de índices:](https://www.postgresql.org/docs/current/static/sql-createindex.html)

```postgresql
-- Adding an index

CREATE INDEX clicked_at_idx ON clicks USING BRIN (clicked_at);

-- Removing an index

DROP INDEX clicked_at_idx;
```

Adicionar um índice requer um bloqueio de escrita, que pode ser indesejável num \" sistema de registo multi-inquilino. \" Para minimizar o tempo de inatividade da aplicação, crie o índice [simultaneamente.](https://www.postgresql.org/docs/current/static/sql-createindex.html#SQL-CREATEINDEX-CONCURRENTLY) Este método requer mais trabalho total do que uma construção de índice padrão e demora mais tempo a ser concluído. No entanto, uma vez que permite que as operações normais continuem enquanto o índice é construído, este método é útil para adicionar novos índices num ambiente de produção.

```postgresql
-- Adding an index without locking table writes

CREATE INDEX CONCURRENTLY clicked_at_idx ON clicks USING BRIN (clicked_at);
```
