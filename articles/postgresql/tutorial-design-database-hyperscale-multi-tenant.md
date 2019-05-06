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
ms.date: 05/06/2019
ms.openlocfilehash: b135baf73e21cd524b6e8fad35452362f36cf0c0
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080808"
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

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Criar uma Base de Dados do Azure para o PostgreSQL

Siga estes passos para criar uma Base de Dados do Azure para o servidor PostgreSQL:
1. Clique em **Criar um recurso**, no canto superior esquerdo do portal do Azure.
2. Selecione **Bases de Dados**, na página **Nova**, e selecione **Base de Dados do Azure para o PostgreSQL**, na página **Bases de Dados**.
3. Para a opção de implementação, clique nas **Create** botão em **grupo de servidores de grande escala (Citus) - pré-visualização.**
4. Preencha o formulário com os detalhes do novo servidor com as seguintes informações:
   - Grupo de recursos: clique nas **criar novo** link abaixo da caixa de texto para este campo. Introduza um nome, tal como **myresourcegroup**.
   - Nome do grupo de servidor: introduza um nome exclusivo para o novo grupo de servidor, que também irá ser utilizado para um subdomínio de servidor.
   - Nome de utilizador administrador: introduza um nome de utilizador exclusivo, será utilizado mais tarde para ligar à base de dados.
   - Palavra-passe: tem de ter, pelo menos, oito carateres de comprimento e conter carateres de três das seguintes categorias – em inglês letras maiúsculas, em minúscula inglesas, números (0-9) e carateres não alfanuméricos (!, $, #, %, etc.)
   - Localização: Utilize a localização mais próxima dos seus utilizadores para lhes dar o acesso mais rápido aos dados.

   > [!IMPORTANT]
   > O início de sessão e a palavra-passe de administrador de servidor que especificar aqui serão necessários para iniciar sessão no servidor e nas respetivas bases de dados mais adiante neste tutorial. Lembre-se ou grave estas informações para utilização posterior.

5. Clique em **configurar grupo de servidor**. Deixe as definições, em que secção inalterada e clique em **guardar**.
6. Clique em **rever + criar** e, em seguida **criar** para aprovisionar o servidor. O aprovisionamento demora alguns minutos.
7. A página irá redirecionar para monitorizar a implementação. Quando o estado em direto é alterado de **sua implementação está em curso** para **sua implementação está completa**, clique nas **saídas** item de menu no lado esquerdo da página.
8. A página de saídas irá conter um nome de anfitrião do coordenador com um botão junto ao mesmo para copiar o valor para a área de transferência. Registe estas informações para uso posterior.

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor

O serviço Base de Dados do Azure para PostgreSQL utiliza uma firewall ao nível do servidor. Por predefinição, o firewall impede que todas as aplicações e ferramentas externas estabeleçam uma ligação ao servidor e a quaisquer bases de dados no servidor. Podemos tem de adicionar uma regra para abrir a firewall para um intervalo de endereços IP específico.

1. Do **saídas** secção onde copiou anteriormente o nome de anfitrião do nó de coordenador, clique em volta para o **descrição geral** item de menu.

2. Encontrar o grupo de dimensionamento para a sua implementação na lista de recursos e clique no mesmo. (O nome terá o prefixo "sg-".)

3. Clique em **Firewall** sob **segurança** no menu da esquerda.

4. Clique no link **+ Adicionar regra de firewall para o endereço IP de cliente atual**. Por último, clique a **guardar** botão.

5. Clique em **Guardar**.

   > [!NOTE]
   > O servidor PostgreSQL do Azure comunica através da porta 5432. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 5432 poderá não ser permitido pela firewall da rede. Se assim for, não poderá ligar ao servidor da Base de Dados SQL do Azure, a menos que o departamento de TI abra a porta 5432.
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Ligar à base de dados com o psql no Cloud Shell

Agora, vamos utilizar o utilitário da linha de comandos [psql](https://www.postgresql.org/docs/current/app-psql.html) para ligar ao servidor da Base de Dados do Azure para PostgreSQL.
1. Inicie o Azure Cloud Shell através do ícone de terminal no painel de navegação superior.

   ![Base de Dados de Azure para o PostgreSQL – ícone do terminal do Azure Cloud Shell](./media/tutorial-design-database-hyperscale-multi-tenant/psql-cloud-shell.png)

2. O Azure Cloud Shell é aberto no browser, onde poderá escrever os comandos bash.

   ![Base de Dados do Azure para o PostgreSQL – Linha de Comandos Bash do Azure Shell](./media/tutorial-design-database-hyperscale-multi-tenant/psql-bash.png)

3. Na linha de comandos do Cloud Shell, estabeleça ligação à Base de Dados do Azure para o servidor PostgreSQL com os comandos psql. O formato seguinte é utilizado para estabelecer ligação a uma Base de Dados do Azure para o servidor PostgreSQL com o utilitário [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Por exemplo, o comando seguinte liga à base de dados predefinido chamado **citus** no servidor PostgreSQL **mydemoserver.postgres.database.azure.com** com as credenciais de acesso. Quando lhe for pedido, introduza a palavra-passe de administrador do servidor.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

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

Crie uma tabela para conter informações geográficas partilhadas. Executá-lo na psql:

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

Carregá-lo com dados de exemplo. Não se esqueça de executá-lo na psql de dentro do diretório onde transferiu o conjunto de dados.

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
