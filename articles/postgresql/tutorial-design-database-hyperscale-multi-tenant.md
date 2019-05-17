---
title: Conceber uma base de dados do multi-inquilino com a base de dados do Azure para PostgreSQL – Hiperescala (Citus) (pré-visualização) tutorial
description: Este tutorial mostra como criar, preencher e consultar tabelas distribuídas na base de dados do Azure para PostgreSQL Hiperescala (Citus) (pré-visualização).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 73d7aebf3dbff59320e0ef92cbd54811503c71b4
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792261"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>Tutorial: conceber uma base de dados do multi-inquilino através da utilização da base de dados do Azure para PostgreSQL – Hiperescala (Citus) (pré-visualização)

Neste tutorial, vai utilizar base de dados do Azure para PostgreSQL - Hiperescala (Citus) (pré-visualização) para saber como:

> [!div class="checklist"]
> * Criar um grupo de servidores de grande escala (Citus)
> * Utilizar o utilitário psql para criar um esquema
> * Tabelas de partição horizontal em todos os nós
> * Ingerir dados de exemplo
> * Consultar dados de inquilino
> * Partilhar dados entre inquilinos
> * Personalizar o esquema por inquilino

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Utilizar o utilitário psql para criar um esquema

Assim que estiver ligado à base de dados do Azure para PostgreSQL - Hiperescala (Citus) (pré-visualização) com psql, pode concluir algumas tarefas básicas. Este tutorial explica como criar uma aplicação web que permite anunciantes controlar as suas campanhas.

Várias empresas podem utilizar a aplicação, por isso, vamos criar uma tabela para conter as empresas e outro para as suas campanhas. Na consola do psql, execute estes comandos:

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);
```

Cada campanha pagará a execução de anúncios. Adicione uma tabela de anúncios, executando o seguinte código no psql depois do código acima:

```sql
CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);
```

Por fim, podemos irão controlar estatísticas sobre cliques e as impressões para cada anúncio:

```sql
CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

Pode ver as tabelas recém-criado na lista de tabelas agora no psql ao executar:

```postgres
\dt
```

Aplicações com multi-inquilinos podem impor a exclusividade apenas por inquilino, que é por isso que todas as chaves primárias e estrangeiras incluem o ID da empresa.

## <a name="shard-tables-across-nodes"></a>Tabelas de partição horizontal em todos os nós

Uma implementação de hiperescala armazena linhas de tabela em nós diferentes com base no valor de uma coluna designado pelo usuário. Este marcas de "coluna de distribuição" qual o inquilino é o proprietário que linhas.

Vamos definir a coluna de distribuição para ser empresa\_id, o identificador do inquilino. No psql, execute estas funções:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## <a name="ingest-sample-data"></a>Ingerir dados de exemplo

Fora do psql agora, na linha de comandos normal, transferir os conjuntos de dados de exemplo:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

Novamente dentro de psql, em massa carregar os dados. Certifique-se de que execute o psql no mesmo diretório onde transferiu os ficheiros de dados.

```sql
\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Estes dados agora se espalham por nós de trabalho.

## <a name="query-tenant-data"></a>Consultar dados de inquilino

Quando o aplicativo solicita dados de um único inquilino, a base de dados pode executar a consulta num nó de trabalho única. Consultas de inquilino único filtrar por um ID de inquilino único. Por exemplo, a seguinte consulta filtros `company_id = 5` para anúncios e as impressões. Tente executá-lo no psql para ver os resultados.

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## <a name="share-data-between-tenants"></a>Partilhar dados entre inquilinos

Até agora todas as tabelas foram distribuídas pelo `company_id`, mas alguns dados não naturalmente "pertencem" para qualquer inquilino em particular e podem ser partilhados. Por exemplo, todas as empresas na plataforma do ad de exemplo deve-se obter informações geográficas para o público-alvo com base em endereços IP.

Crie uma tabela para conter informações geográficas partilhadas. Execute os seguintes comandos no psql:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Em seguida certifique `geo_ips` "tabela de referência" para armazenar uma cópia da tabela em cada nó de trabalho.

```sql
SELECT create_reference_table('geo_ips');
```

Carregá-lo com dados de exemplo. Lembre-se executar este comando no psql de dentro do diretório onde transferiu o conjunto de dados.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

Associar a tabela de cliques com georreplicação\_ips é eficiente em todos os nós.
Aqui está uma associação para encontrar os locais de todas as pessoas que Cliquei no ad
290. Tente executar a consulta no psql.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Personalizar o esquema por inquilino

Cada inquilino poderá ter de armazenar informações especiais não são necessárias por outras pessoas. No entanto, todos os inquilinos partilham uma infraestrutura comum com um esquema de base de dados idênticos. Onde podem aceder os dados Extras?

Um truque consiste em utilizar um tipo de coluna aberta como do PostgreSQL JSONB.  Nosso esquema tem um campo JSONB `clicks` chamado `user_data`.
Uma empresa (Digamos que a empresa cinco), pode utilizar a coluna para controlar se o utilizador está num dispositivo móvel.

Eis uma consulta para encontrar o que clica mais: visitantes móveis ou tradicionais.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

Que podemos otimizar esta consulta para uma única empresa ao criar um [índice parcial](https://www.postgresql.org/docs/current/static/indexes-partial.html).

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

Geralmente, podemos criar uma [índices GIN](https://www.postgresql.org/docs/current/static/gin-intro.html) em cada chave e valor dentro da coluna.

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```

## <a name="clean-up-resources"></a>Limpar recursos

Nos passos anteriores, criou os recursos do Azure num grupo de servidor. Se não espera precisa destes recursos no futuro, elimine o grupo de servidor. Prima a *eliminar* botão no *descrição geral* página para o seu grupo de servidor. Quando lhe for pedido numa página de pop-up, confirme o nome do grupo de servidor e clique no último *eliminar* botão.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu como aprovisionar um grupo de servidores de grande escala (Citus). Conectados a ele com psql, criou um esquema e dados distribuídos. Aprendeu a consultar dados dentro e entre inquilinos e para personalizar o esquema por inquilino.

Em seguida, saiba mais sobre os conceitos de hiperescala.
> [!div class="nextstepaction"]
> [Tipos de nós de Hiperescala](https://aka.ms/hyperscale-concepts)
