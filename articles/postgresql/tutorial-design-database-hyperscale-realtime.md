---
title: Criar um dashboard em tempo real com a base de dados do Azure para PostgreSQL – Hiperescala (Citus) (pré-visualização) tutorial
description: Este tutorial mostra como criar, preencher e consultar tabelas distribuídas na base de dados do Azure para PostgreSQL Hiperescala (Citus) (pré-visualização).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: 7324ab1d7aa6e42100c9c6760c17b0ea6445f21d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080745"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>Tutorial: Criar um dashboard de análise em tempo real através da utilização da base de dados do Azure para PostgreSQL – Hiperescala (Citus) (pré-visualização)

Neste tutorial, vai utilizar base de dados do Azure para PostgreSQL - Hiperescala (Citus) (pré-visualização) para saber como:

> [!div class="checklist"]
> * Criar um grupo de servidores de grande escala (Citus)
> * Utilizar o utilitário psql para criar um esquema
> * Tabelas de partição horizontal em todos os nós
> * Gerar dados de exemplo
> * Executar rollups
> * Consultar dados não processados e agregados
> * Expirar dados

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
   - Nome do grupo de servidor: **mydemoserver** (nome de um servidor, que mapeia para o nome DNS e é necessário para ser globalmente exclusivo).
   - Nome de utilizador administrador: **myadmin** (ele será usado posteriormente para ligar à base de dados).
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

   ![Base de Dados de Azure para o PostgreSQL – ícone do terminal do Azure Cloud Shell](./media/tutorial-design-database-hyperscale-realtime/psql-cloud-shell.png)

2. O Azure Cloud Shell é aberto no browser, onde poderá escrever os comandos bash.

   ![Base de Dados do Azure para o PostgreSQL – Linha de Comandos Bash do Azure Shell](./media/tutorial-design-database-hyperscale-realtime/psql-bash.png)

3. Na linha de comandos do Cloud Shell, estabeleça ligação à Base de Dados do Azure para o servidor PostgreSQL com os comandos psql. O formato seguinte é utilizado para estabelecer ligação a uma Base de Dados do Azure para o servidor PostgreSQL com o utilitário [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Por exemplo, o comando seguinte liga à base de dados predefinido chamado **citus** no servidor PostgreSQL **mydemoserver.postgres.database.azure.com** com as credenciais de acesso. Quando lhe for pedido, introduza a palavra-passe de administrador do servidor.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="use-psql-utility-to-create-a-schema"></a>Utilizar o utilitário psql para criar um esquema

Assim que estiver ligado à base de dados do Azure para PostgreSQL - Hiperescala (Citus) (pré-visualização) com psql, pode concluir algumas tarefas básicas. Este tutorial explica-lhe ingerir dados de tráfego de análise da web, em seguida, acumular os dados para fornecer dashboards em tempo real com base nesses dados.

Vamos criar uma tabela que irá consumir todos os nossos dados de tráfego web não processados. Execute os seguintes comandos no psql terminal:

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

Também vamos criar uma tabela que irá conter o nosso agregados por minuto e uma tabela que mantém a posição do nosso último rollup. Execute o seguinte também psql:

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

Pode ver as tabelas recém-criado na lista de tabelas agora com o comando psql:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Tabelas de partição horizontal em todos os nós

Uma implementação de hiperescala armazena linhas de tabela em nós diferentes com base no valor de uma coluna designado pelo usuário. Este "coluna de distribuição" marca como os dados são em partição horizontal em todos os nós.

Vamos definir a coluna de distribuição para que seja o site\_id, a chave de partição horizontal. No psql, execute estas funções:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>Gerar dados de exemplo

Agora, nosso grupo de servidor deve estar pronto para a ingestão de alguns dados. Podemos executar o seguinte localmente a partir de nossa `psql` ligação continuamente inserir dados.

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('http://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

A consulta adiciona uma linha aproximadamente a cada trimestre em segundo lugar. As linhas são armazenadas em nós de trabalho diferentes, conforme indicado pela coluna de distribuição, `site_id`.

   > [!NOTE]
   > Deixe a consulta de geração de dados em execução e abrir uma segunda ligação psql para os comandos restantes neste tutorial.
   >

## <a name="query"></a>Consulta

A opção de alojamento de hiperescala permite que vários nós processar consultas em paralelo para a velocidade. Por exemplo, a base de dados calcula as agregações, como soma e CONTAGEM em nós de trabalho e combina os resultados numa resposta final.

Eis uma consulta para a contagem de solicitações da web por minuto, juntamente com algumas estatísticas.
Tente executá-lo no psql e observe os resultados.

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="performing-rollups"></a>Rollups de execução

A consulta acima funciona bem nos estágios iniciais, mas o desempenho cairá, já que o dimensionamento de dados. Mesmo com distribuído o processamento é mais rápido computar previamente estes dados que recalcular isso repetidamente.

Podemos garantir que o nosso dashboard permanece rápida, acumular regularmente os dados não processados para uma tabela de agregação. Neste caso, faz o rollup para nossa tabela de agregação de um minuto, mas também poderia ter agregações de 5 minutos, 15 minutos, uma hora e assim por diante.

Uma vez que iremos executar continuamente este rollup, vamos criar uma função de executá-la. Executar estes comandos psql para criar o `rollup_http_request` função.

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

Com a nossa função in-loco, executá-lo para os dados de agregação:

```sql
SELECT rollup_http_request();
```

E com nossos dados num formulário pré-agregados pode consultar a tabela de agregação para obter o mesmo relatório como anteriormente. Execute o seguinte:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Expirar dados antigos

Os rollups efetuam consultas mais rápido, mas ainda temos de expirar dados antigos para evitar os custos de armazenamento não vinculado. Simplesmente decida quanto pretende manter os dados para cada granularidade e utilize consultas padrão para eliminar dados expirados. No exemplo a seguir, decidimos de manter os dados não processados de um dia e por minuto agregações durante um mês:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

Na produção, poderia encapsular essas consultas numa função e chamá-lo a cada minuto numa tarefa cron.

## <a name="clean-up-resources"></a>Limpar recursos

Nos passos anteriores, criou os recursos do Azure num grupo de servidor. Se não espera precisa destes recursos no futuro, elimine o grupo de servidor. Prima a *eliminar* botão no *descrição geral* página para o seu grupo de servidor. Quando lhe for pedido numa página de pop-up, confirme o nome do grupo de servidor e clique no último *eliminar* botão.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu como aprovisionar um grupo de servidores de grande escala (Citus). Conectados a ele com psql, criou um esquema e dados distribuídos. Aprendeu a consultar dados de ambos no formato não processado, regularmente agregar dados, consultar as tabelas agregadas e expirar os dados antigos.

Em seguida, saiba mais sobre os conceitos de hiperescala.
> [!div class="nextstepaction"]
> [Tipos de nós de Hiperescala](https://aka.ms/hyperscale-concepts)