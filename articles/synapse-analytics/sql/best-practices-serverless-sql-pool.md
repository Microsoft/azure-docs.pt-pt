---
title: Melhores práticas para piscina SQL sem servidor
description: Recomendações e boas práticas para trabalhar com piscina SQL sem servidor.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: dcd48354372a196ea903c335e5e22caf20e25996
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219659"
---
# <a name="best-practices-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Melhores práticas para piscina SQL sem servidor em Azure Synapse Analytics

Neste artigo, você encontrará uma coleção de boas práticas para usar a piscina SQL sem servidor. Serverless SQL pool é um recurso em Azure Synapse Analytics.

O pool SQL sem servidor permite-lhe consultar ficheiros nas suas contas de armazenamento Azure. Não tem capacidades locais de armazenamento ou ingestão. Assim, todos os ficheiros que os alvos de consulta são externos ao pool SQL sem servidor. Tudo relacionado com a leitura de ficheiros de armazenamento pode ter um impacto no desempenho da consulta.

Algumas orientações genéricas são:
- Certifique-se de que as aplicações do seu cliente são collocadas com a piscina SQL sem servidor.
  - Se estiver a utilizar aplicações de clientes fora do Azure (por exemplo, Power BI Desktop, SSMS, ADS), certifique-se de que está a utilizar a piscina sem servidor numa região próxima do computador do cliente.
- Certifique-se de que o armazenamento (Azure Data Lake, Cosmos DB) e a piscina SQL sem servidor estão na mesma região.
- Tente [otimizar](#prepare-files-for-querying) o layout de armazenamento utilizando a partição e mantendo os seus ficheiros entre 100 MB e 10 GB.
- Se estiver a devolver um grande número de resultados, certifique-se de que está a utilizar SSMS ou ADS e não o Synapse Studio. O Synapse Studio é uma ferramenta web que não é projetada para grandes conjuntos de resultados. 
- Se estiver a filtrar os resultados por coluna de cordas, tente utilizar alguma `BIN2_UTF8` colisão.
- Tente cache os resultados do lado do cliente utilizando o modo de importação de Power BI ou serviços de análise Azure, e refresque-os periodicamente. As piscinas SQL sem servidor não podem fornecer experiência interativa no modo de consulta direta Power BI se estiver a utilizar consultas complexas ou a processar uma grande quantidade de dados.

## <a name="client-applications-and-network-connections"></a>Aplicações de clientes e ligações de rede

Certifique-se de que a sua aplicação ao cliente está ligada ao espaço de trabalho sinapse mais próximo possível com a ligação ideal.
- Apor uma aplicação de cliente com o espaço de trabalho Synapse. Se estiver a utilizar aplicações como Power BI ou Azure Analysis Service, certifique-se de que estão na mesma região onde colocou o seu espaço de trabalho Synapse. Se necessário, crie os espaços de trabalho separados que estejam emparelhados com as aplicações do seu cliente. A colocação de uma aplicação de clientes e o espaço de trabalho da Sinapse em diferentes regiões pode causar maior latência e streaming mais lento de resultados.
- Se estiver a ler dados da sua aplicação no local, certifique-se de que o espaço de trabalho da Synapse se encontra na região que se encontra perto da sua localização.
- Certifique-se de que não tem alguns problemas de largura de banda de rede enquanto lê uma grande quantidade de dados.
- Não utilize o estúdio Synapse para devolver uma grande quantidade de dados. O estúdio Synapse é uma ferramenta web que utiliza o protocolo HTTPS para transferir dados. Utilize o Azure Data Studio ou o SQL Server Management Studio para ler uma grande quantidade de dados.

## <a name="storage-and-content-layout"></a>Armazenamento e disposição de conteúdo

### <a name="colocate-your-storage-and-serverless-sql-pool"></a>Coloque o seu armazenamento e piscina SQL sem servidor

Para minimizar a latência, coloque a sua conta de armazenamento Azure ou o armazenamento analítico CosmosDB e o seu ponto final de piscina SQL sem servidor. As contas de armazenamento e os pontos finais a provisionados durante a criação do espaço de trabalho situam-se na mesma região.

Para obter um melhor desempenho, se aceder a outras contas de armazenamento com piscina SQL sem servidor, certifique-se de que estão na mesma região. Se não estiverem na mesma região, haverá um aumento da latência para a transferência de rede de dados entre a região remota e a região do ponto final.

### <a name="azure-storage-throttling"></a>Estrangulamento de armazenamento Azure

Várias aplicações e serviços podem aceder à sua conta de armazenamento. O estrangulamento de armazenamento ocorre quando o IOPS combinado ou a produção gerada por aplicações, serviços e carga de trabalho de piscina SQL sem servidor excedem os limites da conta de armazenamento. Como resultado, você vai experimentar um efeito negativo significativo no desempenho da consulta.

Quando o estrangulamento é detetado, a piscina SQL sem servidor tem manuseamento incorporado para resolvê-lo. A piscina SQL sem servidor fará pedidos de armazenamento a um ritmo mais lento até que o estrangulamento seja resolvido.

> [!TIP]
> Para uma execução ótima de consultas, não exlinhe a conta de armazenamento com outras cargas de trabalho durante a execução de consultas.

### <a name="azure-ad-pass-through-performance"></a>Performance de passagem AZURE AD

O pool SQL sem servidor permite-lhe aceder a ficheiros armazenados utilizando credenciais de acesso ao Azure Ative Directory (Azure AD) ou sas. Você pode experimentar um desempenho mais lento com Azure AD Pass-through do que você iria com SAS.

Se precisar de um melhor desempenho, tente usar credenciais SAS para aceder ao armazenamento.

### <a name="prepare-files-for-querying"></a>Preparar ficheiros para consulta

Se possível, pode preparar ficheiros para um melhor desempenho:

- Converta o CSV e o JSON grandes em Parquet. Parquet é um formato colunar. Por ser comprimido, os seus tamanhos de ficheiro são menores do que os ficheiros CSV ou JSON que contêm os mesmos dados. A piscina SQL sem servidor é capaz de saltar as colunas e linhas que não são necessárias em consulta se estiver a ler ficheiros Parquet. A piscina SQL sem servidor necessitará de menos tempo e menos pedidos de armazenamento para lê-lo.
- Se uma consulta tiver como alvo um único ficheiro grande, beneficiará de o dividir em vários ficheiros menores.
- Tente manter o tamanho do ficheiro CSV entre 100 MB e 10 GB.
- É melhor ter ficheiros igualmente dimensionados para um único caminho OPENROWSET ou uma LOCALIZAÇÃO DE tabela externa.
- Partition seus dados armazenando divisórias em diferentes pastas ou nomes de ficheiros. Consulte [o nome de ficheiro e as funções de filepa para direcionar as divisórias específicas](#use-filename-and-filepath-functions-to-target-specific-partitions).

### <a name="colocate-your-cosmosdb-analytical-storage-and-serverless-sql-pool"></a>Coloque o seu armazenamento analítico CosmosDB e piscina SQL sem servidor

Certifique-se de que o seu armazenamento analítico CosmosDB é colocado na mesma região que o espaço de trabalho da Synapse. Consultas entre regiões podem causar enormes latências. Utilize propriedade da região na cadeia de ligação para especificar explicitamente a região onde a loja analítica é colocada (ver [consulta CosmosDb usando piscina SQL sem servidor):](query-cosmos-db-analytical-store.md#overview)

```
'account=<database account name>;database=<database name>;region=<region name>'
```

## <a name="csv-optimizations"></a>Otimizações do CSV

### <a name="use-parser_version-20-to-query-csv-files"></a>Utilize PARSER_VERSION 2.0 para consultar ficheiros CSV

Pode utilizar um analisador otimizado para o desempenho quando consultar ficheiros CSV. Para mais detalhes, consulte [PARSER_VERSION.](develop-openrowset.md)

### <a name="manually-create-statistics-for-csv-files"></a>Criar manualmente estatísticas para ficheiros CSV

O pool SQL sem servidor baseia-se em estatísticas para gerar planos de execução de consultas ideais. As estatísticas serão automaticamente criadas para colunas em ficheiros Parquet quando necessário. Neste momento, as estatísticas não são automaticamente criadas para colunas em ficheiros CSV e deve criar estatísticas manualmente para colunas que utilize em consultas, particularmente as utilizadas em DISTINCT, JOIN, WHERE, ORDER BY e GROUP BY. Consulte [as estatísticas na piscina SQL sem servidor](develop-tables-statistics.md#statistics-in-serverless-sql-pool) para obter detalhes.


## <a name="data-types"></a>Tipos de dados

### <a name="use-appropriate-data-types"></a>Utilize tipos de dados apropriados

Os tipos de dados que utiliza na sua consulta afetam o desempenho. Pode obter um melhor desempenho se seguir estas orientações: 

- Utilize o menor tamanho de dados que acomodará o maior valor possível.
  - Se o comprimento máximo do valor do caracteres for de 30 caracteres, utilize um tipo de dado de caracteres de comprimento 30.
  - Se todos os valores da coluna de caracteres forem de tamanho fixo, utilize **char** ou **nchar**. Caso contrário, utilize **varchar** ou **nvarchar**.
  - Se o valor máximo da coluna inteiro for de 500, use **a pequena tonalidade** porque é o menor tipo de dados que pode acomodar este valor. Pode encontrar gamas de dados inteiros [neste artigo.](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- Se possível, utilize **varchar** e **char** em vez de **nvarchar** e **nchar**.
- Utilize tipos de dados inteiros, se possível. As operações SORT, JOIN e GROUP BY completam-se mais rapidamente em inteiros do que em dados de caracteres.
- Se estiver a utilizar inferência de esquema, [verifique os tipos de dados inferidos](#check-inferred-data-types).

### <a name="check-inferred-data-types"></a>Verificar tipos de dados inferidos

[A inferência de Schema](query-parquet-files.md#automatic-schema-inference) ajuda-o a escrever rapidamente consultas e a explorar dados sem conhecer esquemas de ficheiros. O custo desta conveniência é que os tipos de dados inferidos podem ser maiores do que os tipos reais de dados. Isto acontece quando não há informação suficiente nos ficheiros de origem para garantir que o tipo de dados apropriado é usado. Por exemplo, os ficheiros Parquet não contêm metadados sobre o comprimento máximo da coluna de caracteres. Assim, a piscina SQL sem servidor infere-o como varchar (8000).

Pode utilizar [sp_describe_first_results_set](/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true) para verificar os tipos de dados resultantes da sua consulta.

O exemplo a seguir mostra como pode otimizar tipos de dados inferidos. Este procedimento é utilizado para mostrar os tipos de dados inferidos: 
```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

Aqui está o conjunto de resultados:

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|data 2(7)|8|
|0|3|passenger_count|int|4|

Depois de conhecer os tipos de dados inferidos para a consulta, pode especificar os tipos de dados apropriados:

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="filter-optimization"></a>Otimização de filtros

### <a name="push-wildcards-to-lower-levels-in-the-path"></a>Empurre os wildcards para níveis mais baixos no caminho

Pode utilizar wildcards no seu caminho para [consultar vários ficheiros e pastas](query-data-storage.md#query-multiple-files-or-folders). A piscina SQL sem servidor lista ficheiros na sua conta de armazenamento, a partir do primeiro * utilizando a API de armazenamento. Elimina ficheiros que não correspondem ao caminho especificado. A redução da lista inicial de ficheiros pode melhorar o desempenho se houver muitos ficheiros que correspondam ao caminho especificado até ao primeiro wildcard.

### <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>Use funções de nome de ficheiro e de filepa para direcionar as divisórias específicas

Os dados são muitas vezes organizados em divisórias. Pode instruir a piscina SQL sem servidor para consultar pastas e ficheiros específicos. Ao fazê-lo, reduzirá o número de ficheiros e a quantidade de dados que a consulta necessita para ler e processar. Um bónus adicional é que conseguirá um melhor desempenho.

Para obter mais informações, leia sobre as funções [de nome de ficheiros](query-data-storage.md#filename-function) e [de filepas](query-data-storage.md#filepath-function) e consulte os exemplos para [consultar ficheiros específicos](query-specific-files.md).

> [!TIP]
> Elenco sempre os resultados das funções de datata de ficheiro e nome de ficheiros para os tipos de dados apropriados. Se utilizar tipos de dados de caracteres, certifique-se de que utiliza o comprimento adequado.

> [!NOTE]
> As funções utilizadas para a eliminação de divisórias, filepath e filename, não são atualmente suportadas para tabelas externas, além das criadas automaticamente para cada tabela criada em Apache Spark para Azure Synapse Analytics.

Se os seus dados armazenados não forem divididos, considere parti-os. Desta forma, pode usar estas funções para otimizar consultas que visam esses ficheiros. Quando consulta [as tabelas De Apache Spark para Azure Synapse](develop-storage-files-spark-tables.md) a partir de uma piscina SQL sem servidor, a consulta destinar-se-á automaticamente apenas aos ficheiros necessários.

### <a name="use-proper-collation-to-utilize-predicate-pushdown-for-character-columns"></a>Use a colagem adequada para utilizar o pushdown predicado para colunas de caracteres

Os dados no ficheiro parquet são organizados em grupos de linha. O pool SQL sem servidor salta grupos de linha com base em predicado especificado na cláusula WHERE e, assim, reduz a IO, o que resulta num aumento do desempenho da consulta. 

Por favor, note que o pushdown predicado para colunas de caracteres em ficheiros parquet é suportado apenas para Latin1_General_100_BIN2_UTF8 colagem. Pode especificar a colagem para uma coluna específica utilizando a cláusula COM. Se não especificar esta colagem utilizando a cláusula COM, será utilizada a colagem de base de dados.

## <a name="optimize-repeating-queries"></a>Otimize consultas repetindo

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Use o CETAS para melhorar o desempenho da consulta e junta-se

[O CETAS](develop-tables-cetas.md) é uma das funcionalidades mais importantes disponíveis na piscina SQL sem servidor. O CETAS é uma operação paralela que cria metadados de mesa externos e exporta os resultados da consulta SELECT para um conjunto de ficheiros na sua conta de armazenamento.

Pode utilizar o CETAS para materializar partes frequentemente utilizadas de consultas, como tabelas de referência juntas, a um novo conjunto de ficheiros. Em seguida, pode juntar-se a esta única tabela externa em vez de repetir junções comuns em múltiplas consultas.

À medida que o CETAS gera ficheiros Parquet, as estatísticas serão criadas automaticamente quando a primeira consulta visa esta tabela externa, resultando num melhor desempenho para consultas subsequentes que direcionam a tabela gerada com CETAS.

## <a name="next-steps"></a>Passos seguintes

Reveja o artigo [de resolução de problemas](resources-self-help-sql-on-demand.md) para encontrar soluções para problemas comuns. Se você está trabalhando com piscina SQL dedicada em vez de piscina SQL sem servidor, consulte [as melhores práticas para piscinas SQL dedicadas](best-practices-dedicated-sql-pool.md) para orientação específica.
