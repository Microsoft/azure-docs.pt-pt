---
title: Criar um banco de dados multilocatário com o banco de dados do Azure para PostgreSQL – tutorial de Citus (hiperescala)
description: Este tutorial mostra como criar, popular e consultar tabelas distribuídas no banco de dados do Azure para PostgreSQL Citus (hiperescala).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 130c3e9f5abb24ffcc4e0c4ad6b96af5fca62090
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496531"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Tutorial: criar um banco de dados multilocatário usando o banco de dados do Azure para PostgreSQL – Citus (hiperescala)

Neste tutorial, você usa o banco de dados do Azure para PostgreSQL-Citus (hiperescala) para aprender a:

> [!div class="checklist"]
> * Criar um grupo do servidor Hyperscale (Citus)
> * Usar o utilitário psql para criar um esquema
> * Tabelas de fragmento entre nós
> * Ingerir dados de exemplo
> * Consultar dados de locatário
> * Compartilhar dados entre locatários
> * Personalizar o esquema por locatário

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Usar o utilitário psql para criar um esquema

Uma vez conectado ao banco de dados do Azure para PostgreSQL-Citus (hiperescala) usando psql, você pode concluir algumas tarefas básicas. Este tutorial orienta você pela criação de um aplicativo Web que permite que os anunciantes acompanhem suas campanhas.

Várias empresas podem usar o aplicativo, portanto, vamos criar uma tabela para manter as empresas e outra para suas campanhas. No console do psql, execute estes comandos:

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

Cada campanha será paga para executar anúncios. Adicione uma tabela para anúncios também, executando o seguinte código em psql após o código acima:

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

Por fim, acompanharemos as estatísticas sobre cliques e impressões para cada anúncio:

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

Você pode ver as tabelas recém-criadas na lista de tabelas agora em psql executando:

```postgres
\dt
```

Os aplicativos multilocatários podem impor exclusividade apenas por locatário, motivo pelo qual todas as chaves primárias e estrangeiras incluem a ID da empresa.

## <a name="shard-tables-across-nodes"></a>Tabelas de fragmento entre nós

Uma implantação de hiperescala armazena linhas de tabela em nós diferentes com base no valor de uma coluna designada pelo usuário. Essa "coluna de distribuição" marca qual locatário possui quais linhas.

Vamos definir a coluna de distribuição como ID de\_da empresa, o identificador do locatário. No psql, execute estas funções:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## <a name="ingest-sample-data"></a>Ingerir dados de exemplo

Fora do psql Now, na linha de comando normal, baixe os conjuntos de dados de exemplo:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

De volta dentro do psql, carregue os dados em massa. Certifique-se de executar psql no mesmo diretório em que você baixou os arquivos de dados.

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Esses dados agora serão distribuídos entre os nós de trabalho.

## <a name="query-tenant-data"></a>Consultar dados de locatário

Quando o aplicativo solicita dados para um único locatário, ele pode executar a consulta em um único nó de trabalho. Consultas de locatário único filtram por uma única ID de locatário. Por exemplo, a consulta a seguir filtra `company_id = 5` para anúncios e impressões. Tente executá-lo em psql para ver os resultados.

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

## <a name="share-data-between-tenants"></a>Compartilhar dados entre locatários

Até agora, todas as tabelas foram distribuídas por `company_id`, mas alguns dados não se "pertencem naturalmente" a nenhum locatário em particular e podem ser compartilhados. Por exemplo, todas as empresas na plataforma de anúncios de exemplo podem querer obter informações geográficas para seu público-alvo com base em endereços IP.

Crie uma tabela para armazenar informações geográficas compartilhadas. Execute os seguintes comandos no psql:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Em seguida, faça `geo_ips` uma "tabela de referência" para armazenar uma cópia da tabela em cada nó de trabalho.

```sql
SELECT create_reference_table('geo_ips');
```

Carregue-o com dados de exemplo. Lembre-se de executar esse comando em psql de dentro do diretório em que você baixou o conjunto de os.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

Ingressar na tabela de cliques com os IPs de\_geográfica é eficiente em todos os nós.
Aqui está uma junção para encontrar os locais de todas as pessoas que clicaram no AD
290. Tente executar a consulta no psql.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Personalizar o esquema por locatário

Cada locatário pode precisar armazenar informações especiais não necessárias para outras pessoas. No entanto, todos os locatários compartilham uma infraestrutura comum com um esquema de banco de dados idêntico. Onde os dados adicionais podem ir?

Um truque é usar um tipo de coluna aberta como o JSONB do PostgreSQL.  Nosso esquema tem um campo JSONB em `clicks` chamado `user_data`.
Uma empresa (por exemplo, a empresa cinco) pode usar a coluna para controlar se o usuário está em um dispositivo móvel.

Aqui está uma consulta para encontrar quem clica mais: dispositivos móveis ou visitantes tradicionais.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

Podemos otimizar essa consulta para uma única empresa criando um [índice parcial](https://www.postgresql.org/docs/current/static/indexes-partial.html).

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

Em geral, podemos criar [índices iniciar](https://www.postgresql.org/docs/current/static/gin-intro.html) em cada chave e valor dentro da coluna.

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

Nas etapas anteriores, você criou recursos do Azure em um grupo de servidores. Se você não espera precisar desses recursos no futuro, exclua o grupo de servidores. Pressione o botão *excluir* na página *visão geral* do seu grupo de servidores. Quando solicitado em uma página pop-up, confirme o nome do grupo de servidores e clique no botão *excluir* final.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a provisionar um grupo de servidores de hiperescala (Citus). Você se conectou a ele com psql, criou um esquema e distribuiu dados. Você aprendeu a consultar dados dentro e entre locatários e para personalizar o esquema por locatário.

Em seguida, saiba mais sobre os conceitos de hiperescala.
> [!div class="nextstepaction"]
> [Tipos de nó de hiperescala](https://aka.ms/hyperscale-concepts)
