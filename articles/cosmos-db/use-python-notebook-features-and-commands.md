---
title: Utilize comandos e funcionalidades de cadernos incorporados nos cadernos Db Python do Azure Cosmos (pré-visualização)
description: Aprenda a usar comandos e funcionalidades incorporados para fazer operações comuns usando os cadernos Python incorporados da Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: dech
ms.openlocfilehash: e5d73ef1f41337bce593fdd4def3721d95db5392
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664103"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-python-notebooks-preview"></a>Utilize comandos e funcionalidades de cadernos incorporados nos cadernos Db Python do Azure Cosmos (pré-visualização)

Os cadernos Jupyter incorporados em Azure Cosmos DB permitem-lhe analisar e visualizar os seus dados a partir do portal Azure. Este artigo descreve como usar comandos e funcionalidades de cadernos incorporados para fazer operações comuns em cadernos Python.

## <a name="install-a-new-package"></a>Instale um novo pacote
Depois de ativar suporte portátil para as suas contas Azure Cosmos, pode abrir um novo caderno e instalar um pacote.

Numa nova célula de código, insira e execute o seguinte código, ``PackageToBeInstalled`` substituindo-o pelo pacote Python desejado.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Este pacote estará disponível para utilização a partir de qualquer caderno no espaço de trabalho da conta Azure Cosmos. 

> [!TIP]
> Se o seu caderno necessitar de um pacote personalizado, recomendamos que adicione uma célula no seu caderno para instalar o pacote, uma vez que os pacotes são removidos se [redefinir o espaço](#reset-notebooks-workspace)de trabalho .  

## <a name="run-a-sql-query"></a>Executar uma consulta SQL

Pode usar o ``%%sql`` comando mágico para fazer uma consulta [SQL](sql-query-getting-started.md) contra qualquer recipiente da sua conta. Utilize a sintaxe:

```python
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Substitua ``{database_id}`` e com o nome da base de dados e do recipiente na sua conta ``{container_id}`` Cosmos. Se os ``--database`` argumentos e os argumentos não ``--container`` forem fornecidos, a consulta será executada na base de dados e no contentor por [defeito](#set-default-database-for-queries).
- Você pode executar qualquer consulta SQL que seja válida em Azure Cosmos DB. O texto de consulta deve estar numa nova linha.

Por exemplo: 
```python
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Corra ```%%sql?``` numa cela para ver a documentação de ajuda para o comando mágico sql no caderno.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Executar uma consulta e saída SQL para um DataFrame pandas

Pode ser divulgado os resultados de uma ``%%sql`` consulta num [DataFrame pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe). Utilize a sintaxe: 

```python
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Substitua ``{database_id}`` e com o nome da base de dados e do recipiente na sua conta ``{container_id}`` Cosmos. Se os ``--database`` argumentos e os argumentos não ``--container`` forem fornecidos, a consulta será executada na base de dados e no contentor por [defeito](#set-default-database-for-queries).
- ``{outputDataFrameVar}``Substitua-a pelo nome da variável DataFrame que conterá os resultados.
- Você pode executar qualquer consulta SQL que seja válida em Azure Cosmos DB. O texto de consulta deve estar numa nova linha. 

Por exemplo:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```python
df_cosmos.head(10)

    Action    ItemRevenue    Country    Item
0    Viewed    9.00    Tunisia    Black Tee
1    Viewed    19.99    Antigua and Barbuda    Flannel Shirt
2    Added    3.75    Guinea-Bissau    Socks
3    Viewed    3.75    Guinea-Bissau    Socks
4    Viewed    55.00    Czech Republic    Rainjacket
5    Viewed    350.00    Iceland    Cosmos T-shirt
6    Added    19.99    Syrian Arab Republic    Button-Up Shirt
7    Viewed    19.99    Syrian Arab Republic    Button-Up Shirt
8    Viewed    33.00    Tuvalu    Red Top
9    Viewed    14.00    Cape Verde    Flip Flop Shoes
```
## <a name="set-default-database-for-queries"></a>Definir base de dados padrão para consultas
Pode definir os comandos de base de dados ```%%sql``` predefinidos utilizados para o caderno. ```{database_id}```Substitua-a pelo nome da sua base de dados.

```python
%database {database_id}
```
Corra ```%database?``` numa cela para ver documentação no caderno.

## <a name="set-default-container-for-queries"></a>Definir recipiente predefinido para consultas
Pode definir os comandos de contentores ```%%sql``` predefinidos utilizados para o caderno. ```{container_id}```Substitua-o pelo nome do seu recipiente.

```python
%container {container_id}
```
Corra ```%container?``` numa cela para ver documentação no caderno.

## <a name="upload-json-items-to-a-container"></a>Faça upload de itens JSON para um recipiente
Pode utilizar o ``%%upload`` comando mágico para fazer o upload de dados de um ficheiro JSON para um recipiente específico do Azure Cosmos. Utilize o seguinte comando para carregar os itens:

```python
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Substitua ``{database_id}`` e com o nome da base de dados e do recipiente na sua conta ``{container_id}`` Azure Cosmos. Se os ``--database`` argumentos e os argumentos não ``--container`` forem fornecidos, a consulta será executada na base de dados e no contentor por [defeito](#set-default-database-for-queries).
- ``{url_location_of_file}``Substitua-a com a localização do seu ficheiro JSON. O ficheiro deve ser um conjunto de objetos JSON válidos e deve ser acessível através da Internet pública.

Por exemplo:

```python
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
Com as estatísticas de saída, pode calcular os RU/s eficazes utilizados para fazer o upload dos itens. Por exemplo, se 25.000 RUs foram consumidos ao longo de 38 segundos, o RU/s eficaz é de 25.000 RUs / 38 segundos = 658 RU/s.

## <a name="run-another-notebook-in-current-notebook"></a>Executar outro caderno no caderno atual 
Pode usar o ``%%run`` comando mágico para executar outro caderno no seu espaço de trabalho a partir do seu caderno atual. Utilize a sintaxe:

```python
%%run ./path/to/{notebookName}.ipynb
```
Substitua ``{notebookName}`` pelo nome do caderno que pretende executar. O caderno deve estar no seu espaço de trabalho "Os meus cadernos". 

## <a name="use-built-in-nteract-data-explorer"></a>Utilize o explorador de dados nteract incorporado
Pode utilizar o explorador de [dados nteract](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) incorporado para filtrar e visualizar um DataFrame. Para ativar esta funcionalidade, detete a opção para ``pd.options.display.html.table_schema`` e para o valor desejado ``True`` ``pd.options.display.max_rows`` (pode definir para mostrar todos ``pd.options.display.max_rows`` os ``None`` resultados).

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
![explorador de dados nteract](media/use-notebook-features-and-commands/nteract-built-in-chart.png)

## <a name="use-the-built-in-python-sdk"></a>Use o Python SDK embutido
A versão 4 do [Azure Cosmos DB Python SDK para SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) está instalada e incluída no ambiente de caderno para a conta Azure Cosmos.

Utilize a instância incorporada ``cosmos_client`` para executar qualquer operação SDK. 

Por exemplo:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
Consulte [as amostras de Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples). 

> [!IMPORTANT]
> O Python SDK incorporado só é suportado para contas SQL (Core) API. Para outras APIs, terá de [instalar o controlador Python relevante](#install-a-new-package) que corresponde à API. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Criar uma instância personalizada de``cosmos_client``
Para obter mais flexibilidade, pode criar uma instância personalizada ``cosmos_client`` para:

- Personalize a política de [conexão](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- Faça operações contra uma conta azure cosmos diferente daquela em que está

Pode aceder à cadeia de ligação e à chave primária da conta corrente através das [variáveis ambientais](#access-the-account-endpoint-and-primary-key-env-variables). 

```python
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=COSMOS.ENDPOINT, credential=COSMOS.KEY, connection_policy=custom_connection_policy)

```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Aceda às variáveis env chave da conta e primárias
Pode aceder ao ponto final incorporado e à chave da conta em que o seu caderno se encontra.

```python
endpoint = COSMOS.ENDPOINT
primary_key = COSMOS.KEY
```
> [!IMPORTANT]
> As ``COSMOS.ENDPOINT`` ``COSMOS.KEY`` variáveis e variáveis só são aplicáveis para a API SQL. Para outras APIs, encontre o ponto final e a chave nas cordas de **ligação** ou na lâmina **de teclas** na sua conta Azure Cosmos.  

## <a name="reset-notebooks-workspace"></a>Redefinir cadernos espaço de trabalho
Para redefinir o espaço de trabalho dos cadernos para as definições predefinidas, selecione **Reset Workspace** na barra de comando. Isto removerá quaisquer pacotes instalados personalizados e reiniciará o servidor Jupyter. Os seus cadernos, ficheiros e recursos da Azure Cosmos não serão afetados.  

![Redefinir cadernos espaço de trabalho](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>Passos seguintes

- Conheça os benefícios dos [cadernos Azure Cosmos DB Jupyter](cosmosdb-jupyter-notebooks.md)
- Conheça o [Azure Cosmos DB Python SDK para API SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
