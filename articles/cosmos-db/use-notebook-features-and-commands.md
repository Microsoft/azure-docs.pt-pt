---
title: Usar comandos e recursos de bloco de anotações internos no Azure Cosmos DB
description: Saiba como usar comandos e recursos internos para realizar operações comuns usando blocos de anotações internos do Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: dech
ms.openlocfilehash: 4a9bd554e0858024d656dbf35d6fb00995e6f4bd
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672496"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db"></a>Usar comandos e recursos de bloco de anotações internos no Azure Cosmos DB

Os blocos de anotações internos do Jupyter no Azure Cosmos DB permitem que você analise e visualize os dados do portal do Azure. Este artigo descreve como usar os recursos e comandos de bloco de anotações internos para realizar operações comuns.

## <a name="install-a-new-package"></a>Instalar um novo pacote
Depois de habilitar o suporte ao Notebook para suas contas do Azure Cosmos, você pode abrir um novo bloco de anotações e instalar um pacote.

Em uma nova célula de código, insira e execute o código a seguir ``PackageToBeInstalled`` , substituindo pelo pacote do Python desejado.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Este pacote estará disponível para uso em qualquer notebook no espaço de trabalho da conta do Azure Cosmos. 

> [!TIP]
> Se o seu notebook exigir um pacote personalizado, recomendamos que você adicione uma célula no bloco de anotações para instalar o pacote, pois os pacotes são removidos se você [redefinir o espaço de trabalho](#reset-notebooks-workspace).  

## <a name="run-a-sql-query"></a>Executar uma consulta SQL

Você pode usar o ``%%sql`` comando mágico para executar uma [consulta SQL](sql-query-getting-started.md) em qualquer contêiner em sua conta. Use a sintaxe:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Substitua ``{database_id}`` e``{container_id}`` pelo nome do banco de dados e do contêiner em sua conta do cosmos. Se os ``--database`` argumentos ``--container`` e não forem fornecidos, a consulta será executada no banco de [dados e no contêiner padrão](#set-default-database-for-queries).
- Você pode executar qualquer consulta SQL que seja válida no Azure Cosmos DB. O texto da consulta deve estar em uma nova linha.

Por exemplo: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Execute ```%%sql?``` em uma célula para ver a documentação de ajuda do comando mágico do SQL no bloco de anotações.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Executar uma consulta SQL e uma saída para um dataframe do pandas

Você pode gerar os resultados de uma ``%%sql`` consulta em um [dataframe do pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe). Use a sintaxe: 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Substitua ``{database_id}`` e``{container_id}`` pelo nome do banco de dados e do contêiner em sua conta do cosmos. Se os ``--database`` argumentos ``--container`` e não forem fornecidos, a consulta será executada no banco de [dados e no contêiner padrão](#set-default-database-for-queries).
- Substituir ``{outputDataFrameVar}`` pelo nome da variável dataframe que conterá os resultados.
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

## <a name="set-default-database-for-queries"></a>Definir banco de dados padrão para consultas
Você pode definir os comandos de ```%%sql``` banco de dados padrão que usarão para o bloco de anotações. Substitua ```{database_id}``` pelo nome do seu banco de dados.

```bash
%database {database_id}
```
Execute ```%database?``` em uma célula para ver a documentação no bloco de anotações.

## <a name="set-default-container-for-queries"></a>Definir contêiner padrão para consultas
Você pode definir que os comandos ```%%sql``` de contêiner padrão usarão para o bloco de anotações. Substitua ```{container_id}``` pelo nome do seu contêiner.

```bash
%container {container_id}
```
Execute ```%container?``` em uma célula para ver a documentação no bloco de anotações.

## <a name="use-the-built-in-python-sdk"></a>Usar o SDK do Python interno
A versão 4 do [SDK Azure Cosmos DB Python para API do SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) é instalada e incluída no ambiente do notebook para a conta Cosmos.

Use a ``cosmos_client`` instância interna para executar qualquer operação do SDK. 

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

## <a name="create-a-custom-instance-of-cosmos_client"></a>Criar uma instância personalizada do``cosmos_client``
Para obter mais flexibilidade, você pode criar uma instância personalizada ``cosmos_client`` do para:

- Personalizar a [política de conexão](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- Executar operações em uma conta Cosmos diferente daquela em que você está

Você pode acessar a cadeia de conexão e a chave primária da conta atual por meio das [variáveis de ambiente](#access-the-account-endpoint-and-primary-key-env-variables). 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(os.environ["COSMOS_ENDPOINT"], {'masterKey': os.environ["COSMOS_KEY"]}, connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Acessar as variáveis de ponto de extremidade de conta e chave primária env
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> As ``COSMOS_ENDPOINT`` variáveis ``COSMOS_KEY`` de ambiente e são aplicáveis somente para a API do SQL. Para outras APIs, localize o ponto de extremidade e a chave na folha **cadeias de conexão** ou **chaves** na sua conta do cosmos.  

## <a name="reset-notebooks-workspace"></a>Redefinir espaço de trabalho de blocos de anotações
Para redefinir o espaço de trabalho de blocos de anotações para as configurações padrão, selecione **redefinir espaço de trabalho** na barra de comandos. Isso removerá todos os pacotes instalados personalizados e reiniciará o servidor Jupyter. Seus blocos de anotações, arquivos e recursos de Cosmos não serão afetados.  

![Redefinir espaço de trabalho de blocos de anotações](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os benefícios do [Azure Cosmos DB notebooks Jupyter](cosmosdb-jupyter-notebooks.md)
- Saiba mais sobre o [SDK do Azure Cosmos DB Python para API do SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
