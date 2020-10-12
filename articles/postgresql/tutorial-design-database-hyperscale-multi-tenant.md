---
title: 'Tutorial: Conceber uma base de dados multi-inquilinos - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL'
description: Este tutorial mostra como alimentar uma aplicação multi-inquilino escalável com Base de Dados Azure para Hiperescala PósgreSQL (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: ef0f542a27f378b6132d8ef19b55f386d4102d53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90895258"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Tutorial: desenhe uma base de dados multi-inquilinos utilizando a Base de Dados Azure para PostgreSQL – Hyperscale (Citus)

Neste tutorial, você usa a Base de Dados Azure para PostgreSQL - Hiperescala (Citus) para aprender a:

> [!div class="checklist"]
> * Criar um grupo do servidor Hyperscale (Citus)
> * Use a utilidade psql para criar um esquema
> * Mesas de caco em todos os nosdes
> * Ingerir dados de exemplo
> * Dados do inquilino de consulta
> * Partilhar dados entre inquilinos
> * Personalize o esquema por inquilino

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Use a utilidade psql para criar um esquema

Uma vez ligado à Base de Dados Azure para PostgreSQL - Hiperescala (Citus) utilizando o psql, pode completar algumas tarefas básicas. Este tutorial acompanha-o através da criação de uma aplicação web que permite aos anunciantes acompanhar as suas campanhas.

Várias empresas podem usar a app, por isso vamos criar uma mesa para manter empresas e outra para as suas campanhas. Na consola psql, executar estes comandos:

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

Cada campanha pagará para publicar anúncios. Adicione também uma tabela para anúncios, executando o seguinte código em psql após o código acima:

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

Pode ver as tabelas recém-criadas na lista de tabelas agora no PSQL, correndo:

```postgres
\dt
```

As aplicações multi-arrendadas podem impor a singularidade apenas por inquilino, razão pela qual todas as chaves primárias e estrangeiras incluem a identificação da empresa.

## <a name="shard-tables-across-nodes"></a>Mesas de caco em todos os nosdes

Uma implantação de hiperescala armazena linhas de mesa em diferentes nós com base no valor de uma coluna designada pelo utilizador. Esta "coluna de distribuição" marca que o inquilino possui que rema.

Vamos definir a coluna de distribuição para ser identificação da \_ empresa, o identificador do inquilino. No PSQL, executar estas funções:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

## <a name="ingest-sample-data"></a>Ingerir dados de exemplo

Fora do psql agora, na linha de comando normal, descarregue conjuntos de dados de amostras:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

De volta ao PSQL, a granel carregue os dados. Certifique-se de executar psql no mesmo diretório onde descarregou os ficheiros de dados.

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Estes dados serão agora distribuídos pelos nós dos trabalhadores.

## <a name="query-tenant-data"></a>Dados do inquilino de consulta

Quando o pedido solicita dados para um único inquilino, a base de dados pode executar a consulta num único nó de trabalhador. Consultas de inquilino único filtram por uma única identificação de inquilino. Por exemplo, os seguintes filtros de consulta `company_id = 5` para anúncios e impressões. Tente executá-lo no PSQL para ver os resultados.

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

Até agora, todas as tabelas foram distribuídas por `company_id` , mas alguns dados naturalmente não "pertencem" a qualquer inquilino em particular, e podem ser partilhados. Por exemplo, todas as empresas da plataforma de anúncios de exemplo podem querer obter informações geográficas para o seu público com base em endereços IP.

Crie uma tabela para conter informações geográficas partilhadas. Executar os seguintes comandos em psql:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Em seguida, faça `geo_ips` uma "tabela de referência" para armazenar uma cópia da tabela em cada nó de trabalhador.

```sql
SELECT create_reference_table('geo_ips');
```

Carregue-o com dados de exemplo. Lembre-se de executar este comando no PSQL a partir de dentro do diretório onde descarregou o conjunto de dados.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

Juntar a tabela de cliques com geo \_ ips é eficiente em todos os nós.
Aqui está uma união para encontrar as localizações de todos os que clicaram no anúncio
290. Tente executar a consulta em PSQL.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Personalize o esquema por inquilino

Cada inquilino pode precisar de armazenar informações especiais não necessárias por outros. No entanto, todos os inquilinos partilham uma infraestrutura comum com um esquema de base de dados idêntico. Para onde podem ir os dados extras?

Um dos truques é usar um tipo de coluna aberta como o JSONB do PostgreSQL.  O nosso esquema tem um campo JSONB `clicks` `user_data` chamado.
Uma empresa (digamos empresa cinco), pode usar a coluna para saber se o utilizador está num dispositivo móvel.

Aqui está uma consulta para descobrir quem clica mais: mobile, ou visitantes tradicionais.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

Podemos otimizar esta consulta para uma única empresa criando um [índice parcial.](https://www.postgresql.org/docs/current/static/indexes-partial.html)

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

De uma forma mais geral, podemos criar [índices gin em](https://www.postgresql.org/docs/current/static/gin-intro.html) cada chave e valor dentro da coluna.

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

## <a name="clean-up-resources"></a>Limpar os recursos

Nos passos anteriores, criou recursos Azure num grupo de servidores. Se não espera precisar destes recursos no futuro, elimine o grupo de servidores. Prima o botão *Eliminar* na página *'Vista Geral'* para o seu grupo de servidor. Quando solicitado numa página pop-up, confirme o nome do grupo de servidor e clique no botão final *eliminar.*

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a providenciar um grupo de servidores Hyperscale (Citus). Ligou-se a ele com o PSQL, criou um esquema e distribuiu dados. Aprendeu a consultar dados dentro e entre inquilinos, e a personalizar o esquema por inquilino.

- Saiba mais sobre [os tipos de nó de](https://aka.ms/hyperscale-concepts) grupo de servidor
- Determine o melhor [tamanho inicial](howto-hyperscale-scaling.md#picking-initial-size) para o seu grupo de servidor
