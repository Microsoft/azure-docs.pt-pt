---
title: Use comandos e funcionalidades de cadernos incorporados em cadernos Azure Cosmos DB Python (pré-visualização)
description: Aprenda a usar comandos e funcionalidades incorporados para fazer operações comuns usando os cadernos Python incorporados da Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.openlocfilehash: b89fcf32ed033f359b4db601e36cc69bb899944d
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165829"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-python-notebooks-preview"></a>Use comandos e funcionalidades de cadernos incorporados em cadernos Azure Cosmos DB Python (pré-visualização)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Os cadernos Jupyter incorporados em Azure Cosmos DB permitem-lhe analisar e visualizar os seus dados a partir do portal Azure. Este artigo descreve como usar comandos e funcionalidades de cadernos incorporados para fazer operações comuns em cadernos Python.

## <a name="install-a-new-package"></a>Instale um novo pacote
Depois de ativar o suporte de um portátil para as suas contas Azure Cosmos, pode abrir um novo caderno e instalar um pacote.

Numa nova célula de código, insira e execute o seguinte código, ``PackageToBeInstalled`` substituindo-o pela embalagem Python desejada.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled --user
```
Este pacote estará disponível para usar em qualquer caderno no espaço de trabalho da conta Azure Cosmos. 

> [!TIP]
> Se o seu portátil necessitar de um pacote personalizado, recomendamos que adicione uma célula no seu caderno para instalar a embalagem, uma vez que as embalagens são removidas se [reiniciar o espaço de trabalho](#reset-notebooks-workspace).  

## <a name="run-a-sql-query"></a>Executar uma consulta SQL

Pode utilizar o ``%%sql`` comando mágico para executar uma consulta [SQL](sql-query-getting-started.md) contra qualquer recipiente na sua conta. Utilize a sintaxe:

```python
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Substitua ``{database_id}`` e pelo nome da base de ``{container_id}`` dados e do recipiente na sua conta Cosmos. Se os ``--database`` argumentos e os argumentos não forem ``--container`` apresentados, a consulta será executada na [base de dados e no contentor predefinidos](#set-default-database-for-queries).
- Pode executar qualquer consulta SQL que seja válida em Azure Cosmos DB. O texto de consulta deve estar numa nova linha.

Por exemplo: 
```python
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Corra ```%%sql?``` numa cela para ver a documentação de ajuda para o comando mágico sql no caderno.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Executar uma consulta e saída SQL para um Pandas DataFrame

Pode desaudrar os resultados de uma ``%%sql`` consulta num [DataFrame pandas.](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame) Utilize a sintaxe: 

```python
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Substitua ``{database_id}`` e pelo nome da base de ``{container_id}`` dados e do recipiente na sua conta Cosmos. Se os ``--database`` argumentos e os argumentos não forem ``--container`` apresentados, a consulta será executada na [base de dados e no contentor predefinidos](#set-default-database-for-queries).
- ``{outputDataFrameVar}``Substitua-o pelo nome da variável DataFrame que conterá os resultados.
- Pode executar qualquer consulta SQL que seja válida em Azure Cosmos DB. O texto de consulta deve estar numa nova linha. 

Por exemplo:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```python
df_cosmos.head(10)

    Action    ItemRevenue    Country/Region    Item
0    Viewed    9.00    Tunisia    Black Tee
1    Viewed    19.99    Antigua and Barbuda    Flannel Shirt
2    Added    3.75    Guinea-Bissau    Socks
3    Viewed    3.75    Guinea-Bissau    Socks
4    Viewed    55.00    Czech Republic    Rainjacket
5    Viewed    350.00    Iceland    Cosmos T-shirt
6    Added    19.99    Syrian Arab Republic    Button-Up Shirt
7    Viewed    19.99    Syrian Arab Republic    Button-Up Shirt
8    Viewed    33.00    Tuvalu    Red Top
9    Viewed    14.00    Cabo Verde    Flip Flop Shoes
```
## <a name="set-default-database-for-queries"></a>Definir base de dados predefinido para consultas
Pode definir os comandos de base de ```%%sql``` dados predefinidos que serão utilizados para o portátil. ```{database_id}```Substitua-o pelo nome da sua base de dados.

```python
%database {database_id}
```
Corra ```%database?``` numa cela para ver documentação no caderno.

## <a name="set-default-container-for-queries"></a>Definir recipiente predefinido para consultas
Pode definir os comandos de recipiente ```%%sql``` predefinidos que serão utilizados para o portátil. ```{container_id}```Substitua-o pelo nome do seu recipiente.

```python
%container {container_id}
```
Corra ```%container?``` numa cela para ver documentação no caderno.

## <a name="upload-json-items-to-a-container"></a>Faça o upload de itens JSON para um recipiente
Pode utilizar o ``%%upload`` comando mágico para enviar dados de um ficheiro JSON para um recipiente Azure Cosmos especificado. Utilize o seguinte comando para carregar os itens:

```python
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Substitua ``{database_id}`` e pelo nome da base de ``{container_id}`` dados e do recipiente na sua conta Azure Cosmos. Se os ``--database`` argumentos e os argumentos não forem ``--container`` apresentados, a consulta será executada na [base de dados e no contentor predefinidos](#set-default-database-for-queries).
- ``{url_location_of_file}``Substitua-o pela localização do seu ficheiro JSON. O ficheiro deve ser um conjunto de objetos JSON válidos e deve ser acessível através da Internet pública.

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
Com as estatísticas de saída, pode calcular os RU/s eficazes utilizados para carregar os itens. Por exemplo, se 25.000 RUs foram consumidos ao longo de 38 segundos, o RU/s eficaz é de 25.000 RUs / 38 segundos = 658 RU/s.

Pode guardar ficheiros (como ficheiros CSV ou JSON) para o espaço de trabalho do portátil local. Recomendamos que adicione uma célula no seu caderno para guardar ficheiros. Pode ver estes ficheiros a partir do terminal integrado no ambiente do caderno. Pode utilizar o comando "Ls" para visualizar os ficheiros guardados. No entanto, estes ficheiros são removidos se reiniciar o espaço de trabalho. Assim, é melhor usar armazenamento persistente, como GitHub ou uma conta de armazenamento em vez do espaço de trabalho local.

## <a name="run-another-notebook-in-current-notebook"></a>Executar outro caderno no caderno atual 
Pode utilizar o ``%%run`` comando mágico para executar outro caderno no seu espaço de trabalho a partir do seu caderno atual. Utilize a sintaxe:

```python
%%run ./path/to/{notebookName}.ipynb
```
``{notebookName}``Substitua-o pelo nome do caderno que pretende executar. O caderno deve estar no seu atual espaço de trabalho 'Os Meus Cadernos'. 

## <a name="use-built-in-nteract-data-explorer"></a>Utilize o explorador de dados nteract incorporado
Pode utilizar o explorador de [dados nteract](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) incorporado para filtrar e visualizar um DataFrame. Para ativar esta função, desa estada a opção ``pd.options.display.html.table_schema`` e o valor pretendido ``True`` ``pd.options.display.max_rows`` (pode definir para mostrar todos ``pd.options.display.max_rows`` os ``None`` resultados).

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
:::image type="content" source="media/use-notebook-features-and-commands/nteract-built-in-chart.png" alt-text="explorador de dados nteract":::

## <a name="use-the-built-in-python-sdk"></a>Use o Python SDK incorporado
A versão 4 do [Azure Cosmos DB Python SDK para a SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) está instalada e incluída no ambiente de cadernos para a conta Azure Cosmos.

Utilize o caso incorporado ``cosmos_client`` para executar qualquer operação SDK. 

Por exemplo:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
Consulte [as amostras do Python SDK.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples) 

> [!IMPORTANT]
> O Python SDK incorporado só é suportado para contas API SQL (Core). Para outras APIs, terá de [instalar o controlador Python relevante](#install-a-new-package) que corresponde à API. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Criar uma instância personalizada de ``cosmos_client``
Para uma maior flexibilidade, pode criar uma instância personalizada de ``cosmos_client`` forma a:

- Personalizar a [política de ligação](/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?preserve-view=true&view=azure-python-preview)
- Executar operações contra uma conta Azure Cosmos diferente da que você está em

Pode aceder à cadeia de ligação e à chave primária da conta corrente através das [variáveis ambientais.](#access-the-account-endpoint-and-primary-key-env-variables) 

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
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Aceda ao ponto final da conta e às variáveis env chave primária
Pode aceder ao ponto final incorporado e à chave da conta onde o seu caderno está.

```python
endpoint = COSMOS.ENDPOINT
primary_key = COSMOS.KEY
```
> [!IMPORTANT]
> As ``COSMOS.ENDPOINT`` ``COSMOS.KEY`` variáveis e variáveis só são aplicáveis à API SQL. Para outras APIs, encontre o ponto final e a chave na lâmina **de Ligação ou** **Teclas** na sua conta Azure Cosmos.  

## <a name="reset-notebooks-workspace"></a>Redefinir espaço de trabalho de cadernos
Para redefinir o espaço de trabalho dos blocos de notas para as definições predefinidos, selecione **Reset Workspace** na barra de comando. Isto removerá quaisquer pacotes instalados personalizados e reiniciará o servidor Jupyter. Os seus cadernos, ficheiros e recursos da Azure Cosmos não serão afetados.  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="Redefinir espaço de trabalho de cadernos":::

## <a name="next-steps"></a>Passos seguintes

- Conheça os benefícios dos [cadernos Azure Cosmos DB Jupyter](cosmosdb-jupyter-notebooks.md)
- Conheça o [Azure Cosmos DB Python SDK para a SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)