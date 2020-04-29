---
title: 'Tutorial: Designar uma base de dados multi-inquilinos - Hyperscale (Citus) - Base de Dados Azure para PostgreSQL'
description: Este tutorial mostra como criar, povoar e consultar mesas distribuídas na Base de Dados Azure para hiperescala PostgreSQL (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 17ac29de243f4abfff1cfc83fc6424799978bf0e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74978156"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Tutorial: conceber uma base de dados multi-inquilinoutilizando a Base de Dados Azure para PostgreSQL – Hyperscale (Citus)

Neste tutorial, utiliza a Base de Dados Azure para PostgreSQL - Hyperscale (Citus) para aprender a:

> [!div class="checklist"]
> * Criar um grupo do servidor Hyperscale (Citus)
> * Use a utilidade psql para criar um esquema
> * Mesas de caco em todos os nódosos
> * Ingerir dados de exemplo
> * Dados do inquilino consulta
> * Partilhar dados entre inquilinos
> * Personalize o esquema por inquilino

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Use a utilidade psql para criar um esquema

Uma vez ligado à Base de Dados Azure para PostgreSQL - Hiperescala (Citus) utilizando o psql, pode completar algumas tarefas básicas. Este tutorial acompanha-o através da criação de uma aplicação web que permite aos anunciantes acompanhar em suas campanhas.

Várias empresas podem usar a app, por isso vamos criar uma tabela para manter empresas e outra para as suas campanhas. Na consola psql, execute estes comandos:

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

Cada campanha vai pagar para publicar anúncios. Adicione também uma tabela para anúncios, executando o seguinte código em psql após o código acima:

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

Finalmente, vamos rastrear estatísticas sobre cliques e impressões para cada anúncio:

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

Pode ver as tabelas recém-criadas na lista de tabelas agora no PSQL, executando:

```postgres
\dt
```

As aplicações multi-arrendatárias só podem impor a singularidade por inquilino, razão pela qual todas as chaves primárias e estrangeiras incluem o ID da empresa.

## <a name="shard-tables-across-nodes"></a>Mesas de caco em todos os nódosos

Uma implantação de hiperescala armazena linhas de mesa em diferentes nódosos com base no valor de uma coluna designada pelo utilizador. Esta "coluna de distribuição" marca qual o inquilino que possui quais as filas.

Vamos definir a coluna de\_distribuição para ser id da empresa, o identificador inquilino. No psql, executar estas funções:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## <a name="ingest-sample-data"></a>Ingerir dados de exemplo

Fora do PSQL agora, na linha de comando normal, descarregue os conjuntos de dados da amostra:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

De volta ao psql, carregue a granel os dados. Certifique-se de executar psql no mesmo diretório onde descarregou os ficheiros de dados.

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Estes dados serão agora distribuídos pelos nós dos trabalhadores.

## <a name="query-tenant-data"></a>Dados do inquilino consulta

Quando o pedido solicita dados para um único inquilino, a base de dados pode executar a consulta num único nó de trabalhador. Consultas de inquilino único filtram por uma única identificação de inquilino. Por exemplo, os filtros `company_id = 5` de consulta seguintes para anúncios e impressões. Tente executá-lo no PSQL para ver os resultados.

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

Até agora, todas as `company_id`tabelas foram distribuídas por, mas alguns dados não naturalmente "pertencem" a qualquer inquilino em particular, e podem ser partilhados. Por exemplo, todas as empresas da plataforma de anúncios exemplo podem querer obter informações geográficas para o seu público com base em endereços IP.

Crie uma tabela para guardar informações geográficas partilhadas. Executar os seguintes comandos em psql:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Em `geo_ips` seguida, faça uma "mesa de referência" para armazenar uma cópia da mesa em cada nó dos trabalhadores.

```sql
SELECT create_reference_table('geo_ips');
```

Carregue-o com dados de exemplo. Lembre-se de executar este comando em psql de dentro do diretório onde descarregou o conjunto de dados.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

Juntar a tabela de\_cliques com geo ips é eficiente em todos os nós.
Aqui está uma junta para encontrar as localizações de todos os que clicaram em anúncio
290. Tente fazer a consulta no PSQL.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Personalize o esquema por inquilino

Cada inquilino pode ter de armazenar informações especiais não necessárias por outros. No entanto, todos os inquilinos partilham uma infraestrutura comum com um esquema de base de dados idêntico. Para onde podem ir os dados extras?

Um truque é usar um tipo de coluna aberta como o JSONB do PostgreSQL.  O nosso esquema tem um campo `clicks` JSONB chamado `user_data`.
Uma empresa (digamos empresa cinco), pode usar a coluna para rastrear se o utilizador está num dispositivo móvel.

Aqui está uma consulta para encontrar quem clica mais: visitantes móveis ou tradicionais.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

Podemos otimizar esta consulta para uma única empresa através da criação de um [índice parcial.](https://www.postgresql.org/docs/current/static/indexes-partial.html)

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

De uma forma mais geral, podemos criar um [gin índices](https://www.postgresql.org/docs/current/static/gin-intro.html) em cada chave e valor dentro da coluna.

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

Nos passos anteriores, criou os recursos do Azure num grupo de servidores. Se não espera precisar destes recursos no futuro, elimine o grupo de servidores. Prima o botão *Eliminar* na página *'Visão Geral'* para o seu grupo de servidores. Quando solicitado numa página pop-up, confirme o nome do grupo do servidor e clique no *botão* Eliminar final.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a fornecer um grupo de servidores de Hiperescala (Citus). Ligaste-lhe com o PSQL, criaste um esquema e distribuíste dados. Aprendeu a consultar dados dentro e entre inquilinos, e a personalizar o esquema por inquilino.

Em seguida, aprenda sobre os conceitos de hiperescala.
> [!div class="nextstepaction"]
> [Tipos de nó de hiperescala](https://aka.ms/hyperscale-concepts)
