---
title: Usar comandos e recursos de bloco de anotações internos no Azure Cosmos DB (versão prévia)
description: Saiba como usar comandos e recursos internos para realizar operações comuns usando blocos de anotações internos do Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: dech
ms.openlocfilehash: 61d46bbf0ccdeb5cd2e95e36e19f1aa81cfeeb48
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513404"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-preview"></a>Usar comandos e recursos de bloco de anotações internos no Azure Cosmos DB (versão prévia)

Os blocos de anotações internos do Jupyter no Azure Cosmos DB permitem que você analise e visualize os dados do portal do Azure. Este artigo descreve como utilizar as funcionalidades e os comandos de bloco de notas integrados para realizar operações comuns.

## <a name="install-a-new-package"></a>Instalar um novo pacote
Depois de habilitar o suporte ao Notebook para suas contas do Azure Cosmos, você pode abrir um novo bloco de anotações e instalar um pacote.

Em uma nova célula de código, insira e execute o código a seguir, substituindo ``PackageToBeInstalled`` pelo pacote do Python desejado.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Este pacote estará disponível para uso em qualquer notebook no espaço de trabalho da conta do Azure Cosmos. 

> [!TIP]
> Se o seu notebook exigir um pacote personalizado, recomendamos que você adicione uma célula no bloco de anotações para instalar o pacote, pois os pacotes são removidos se você [redefinir o espaço de trabalho](#reset-notebooks-workspace).  

## <a name="run-a-sql-query"></a>Executar uma consulta SQL

Você pode usar o comando ``%%sql`` mágica para executar uma [consulta SQL](sql-query-getting-started.md) em qualquer contêiner em sua conta. Use a sintaxe:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Substitua ``{database_id}`` e ``{container_id}`` pelo nome do banco de dados e do contêiner em sua conta do cosmos. Se os argumentos ``--database`` e ``--container`` não forem fornecidos, a consulta será executada no banco de [dados e no contêiner padrão](#set-default-database-for-queries).
- Você pode executar qualquer consulta SQL que seja válida no Azure Cosmos DB. O texto da consulta deve estar em uma nova linha.

Por exemplo: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Execute ```%%sql?``` em uma célula para ver a documentação de ajuda do comando mágico do SQL no bloco de anotações.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Executar uma consulta SQL e uma saída para um dataframe do pandas

Você pode gerar os resultados de uma consulta ``%%sql`` em um [Dataframe do pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe). Use a sintaxe: 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Substitua ``{database_id}`` e ``{container_id}`` pelo nome do banco de dados e do contêiner em sua conta do cosmos. Se os argumentos ``--database`` e ``--container`` não forem fornecidos, a consulta será executada no banco de [dados e no contêiner padrão](#set-default-database-for-queries).
- Substitua ``{outputDataFrameVar}`` pelo nome da variável dataframe que conterá os resultados.
- Você pode executar qualquer consulta SQL que seja válida no Azure Cosmos DB. O texto da consulta deve estar em uma nova linha. 

Por exemplo:

```bash
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```bash
df_cosmos.head(10)

    Action  ItemRevenue Country Item
0   Viewed  9.00    Tunisia Black Tee
1   Viewed  19.99   Antigua and Barbuda Flannel Shirt
2   Added   3.75    Guinea-Bissau   Socks
3   Viewed  3.75    Guinea-Bissau   Socks
4   Viewed  55.00   Czech Republic  Rainjacket
5   Viewed  350.00  Iceland Cosmos T-shirt
6   Added   19.99   Syrian Arab Republic    Button-Up Shirt
7   Viewed  19.99   Syrian Arab Republic    Button-Up Shirt
8   Viewed  33.00   Tuvalu  Red Top
9   Viewed  14.00   Cape Verde  Flip Flop Shoes
```
## <a name="upload-json-items-to-a-container"></a>Carregar itens JSON em um contêiner
Você pode usar o comando ``%%upload`` mágica para carregar dados de um arquivo JSON para um contêiner Cosmos do Azure especificado. Use o seguinte comando para carregar os itens:

```bash
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Substitua ``{database_id}`` e ``{container_id}`` pelo nome do banco de dados e do contêiner em sua conta do Azure Cosmos. Se os argumentos ``--database`` e ``--container`` não forem fornecidos, a consulta será executada no banco de [dados e no contêiner padrão](#set-default-database-for-queries).
- Substitua ``{url_location_of_file}`` pelo local do seu arquivo JSON. O arquivo deve ser uma matriz de objetos JSON válidos e deve ser acessível pela Internet pública.

Por exemplo:

```bash
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```bash
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
Com as estatísticas de saída, você pode calcular os RU/s efetivos usados para carregar os itens. Por exemplo, se 25.000 RUs foram consumidos em até 38 segundos, a RU/s efetiva é 25.000 RUs/38 segundos = 658 RU/s.

## <a name="set-default-database-for-queries"></a>Definir banco de dados padrão para consultas
Você pode definir o banco de dados padrão ```%%sql``` comandos serão usados para o bloco de anotações. Substitua ```{database_id}``` pelo nome do seu banco de dados.

```bash
%database {database_id}
```
Execute ```%database?``` em uma célula para ver a documentação no bloco de anotações.

## <a name="set-default-container-for-queries"></a>Definir contêiner padrão para consultas
Você pode definir o contêiner padrão ```%%sql``` comandos serão usados para o bloco de anotações. Substitua ```{container_id}``` pelo nome do seu contêiner.

```bash
%container {container_id}
```
Execute ```%container?``` em uma célula para ver a documentação no bloco de anotações.

## <a name="use-built-in-nteract-data-explorer"></a>Usar o nteract data Explorer interno
Você pode usar o [nteract data Explorer](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) interno para filtrar e visualizar um dataframe. Para habilitar esse recurso, defina a opção ``pd.options.display.html.table_schema`` como ``True`` e ``pd.options.display.max_rows`` para o valor desejado (você pode definir ``pd.options.display.max_rows`` como ``None`` para mostrar todos os resultados).

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
![Gerenciador de dados do nteract](media/use-notebook-features-and-commands/nteract-built-in-chart.png)

## <a name="use-the-built-in-python-sdk"></a>Usar o SDK do Python interno
A versão 4 do [SDK Azure Cosmos DB Python para API do SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) é instalada e incluída no ambiente do notebook para a conta do Azure Cosmos.

Use a instância de ``cosmos_client`` interna para executar qualquer operação do SDK. 

Por exemplo:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
Consulte [exemplos do SDK do Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples). 

> [!IMPORTANT]
> O SDK do Python interno só tem suporte para contas da API do SQL (núcleo). Para outras APIs, será necessário [instalar o driver Python relevante](#install-a-new-package) que corresponde à API. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Criar uma instância personalizada do ``cosmos_client``
Para obter mais flexibilidade, você pode criar uma instância personalizada do ``cosmos_client`` para:

- Personalizar a [política de conexão](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- Executar operações em uma conta do Azure Cosmos diferente daquela em que você está

Você pode acessar a cadeia de conexão e a chave primária da conta atual por meio das [variáveis de ambiente](#access-the-account-endpoint-and-primary-key-env-variables). 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=os.environ["COSMOS_ENDPOINT"], credential=os.environ["COSMOS_KEY"], connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Acessar as variáveis de ponto de extremidade de conta e chave primária env
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> As variáveis de ambiente ``COSMOS_ENDPOINT`` e ``COSMOS_KEY`` só são aplicáveis à API do SQL. Para outras APIs, localize o ponto de extremidade e a chave na folha **cadeias de conexão** ou **chaves** em sua conta do Azure Cosmos.  

## <a name="reset-notebooks-workspace"></a>Redefinir espaço de trabalho de blocos de anotações
Para redefinir o espaço de trabalho de blocos de anotações para as configurações padrão, selecione **redefinir espaço de trabalho** na barra de comandos. Isso removerá todos os pacotes instalados personalizados e reiniciará o servidor Jupyter. Seus blocos de anotações, arquivos e recursos de Cosmos do Azure não serão afetados.  

![Redefinir espaço de trabalho de blocos de anotações](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os benefícios do [Azure Cosmos DB notebooks Jupyter](cosmosdb-jupyter-notebooks.md)
- Saiba mais sobre o [SDK do Azure Cosmos DB Python para API do SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
