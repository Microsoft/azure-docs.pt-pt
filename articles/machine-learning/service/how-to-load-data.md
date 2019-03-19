---
title: 'Carga: o SDK de Python de preparação de dados'
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre a carregar dados com o SDK do Azure Machine Learning Data Prep. Pode carregar diferentes tipos de dados de entrada, especificar os tipos de ficheiro de dados e parâmetros ou utilizar a funcionalidade de leitura inteligente do SDK para detetar automaticamente o tipo de ficheiro.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 2/22/2019
ms.custom: seodec18
ms.openlocfilehash: fe676cbba89a99a3dbd29609f181274062b37d86
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58000280"
---
# <a name="load-and-read-data-with-azure-machine-learning"></a>Carregar e ler dados com o Azure Machine Learning

Neste artigo, vai aprender diferentes métodos de carregamento de dados com o SDK do Azure Machine Learning Data Prep. Para ver a documentação de referência para o SDK, consulte a [descrição geral](https://aka.ms/data-prep-sdk). O SDK oferece suporte a vários recursos de ingestão de dados, incluindo:

* Carregar a partir de muitos tipos de ficheiro com a inferência de tipos de parâmetro (codificação, separador, cabeçalhos) de análise
* Conversão de tipo usando inferência durante o carregamento de ficheiro
* Suporte de ligação para o MS SQL Server e o armazenamento do Azure Data Lake

A tabela seguinte mostra uma seleção de funções usadas para carregar dados de tipos de ficheiros comuns.

| Tipo de ficheiro | Função | Ligação de referência |
|-------|-------|-------|
|Qualquer|`auto_read_file()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#auto-read-file-path--filepath--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|Texto|`read_lines()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-lines-path--filepath--header--azureml-dataprep-api-engineapi-typedefinitions-promoteheadersmode----promoteheadersmode-none--0---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---skip-rows--int---0--skip-mode--azureml-dataprep-api-engineapi-typedefinitions-skipmode----skipmode-none--0---comment--str---none--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|CSV|`read_csv()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-csv-path--filepath--separator--str--------header--azureml-dataprep-api-engineapi-typedefinitions-promoteheadersmode----promoteheadersmode-constantgrouped--3---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---quoting--bool---false--inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--skip-mode--azureml-dataprep-api-engineapi-typedefinitions-skipmode----skipmode-none--0---comment--str---none--include-path--bool---false--archive-options--azureml-dataprep-api--archiveoption-archiveoptions---none-----azureml-dataprep-api-dataflow-dataflow)|
|Excel|`read_excel()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-excel-path--filepath--sheet-name--str---none--use-column-headers--bool---false--inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|Largura fixa|`read_fwf()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-fwf-path--filepath--offsets--typing-list-int---header--azureml-dataprep-api-engineapi-typedefinitions-promoteheadersmode----promoteheadersmode-constantgrouped--3---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--skip-mode--azureml-dataprep-api-engineapi-typedefinitions-skipmode----skipmode-none--0---include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|JSON|`read_json()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-json-path--filepath--encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---flatten-nested-arrays--bool---false--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|

## <a name="load-data-automatically"></a>Carregar dados automaticamente

Para carregar dados automaticamente sem especificar o tipo de ficheiro, utilize o `auto_read_file()` função. O tipo de ficheiro e os argumentos necessários para lê-lo são inferidos automaticamente.

```python
import azureml.dataprep as dprep

dflow = dprep.auto_read_file(path='./data/any-file.txt')
```

Esta função é útil para detetar automaticamente o tipo de ficheiro, codificação de mensagens em fila e outros argumentos análise tudo a partir de ponto de entrada conveniente uma. A função também automaticamente executa os seguintes passos normalmente executados quando o carregamento de dados delimitados:

* Inferindo e definindo o delimitador
* A ignorar registos vazios na parte superior do ficheiro
* Inferindo e definir a linha de cabeçalho

Em alternativa, se souber o ficheiro, escreva antecipadamente e pretende controlar explicitamente a forma como ela é analisada, use as funções de ficheiro específicos.

## <a name="load-text-line-data"></a>Carregar dados de linha de texto

Para ler dados de texto simples num fluxo de dados, utilize o `read_lines()` sem especificar parâmetros opcionais.

```python
dflow = dprep.read_lines(path='./data/text_lines.txt')
dflow.head(5)
```

||Linha|
|----|-----|
|0|Data \| \| temperatura mínima \| \| temperatura máximo|
|1|2015-07-1 \| \| -4.1 \| \| 10.0|
|2|2015-07-2 \| \| -0.8 \| \| 10.8|


Depois dos dados são ingeridos, execute o seguinte código para converter o objeto de fluxo de dados para um Pandas dataframe.

```python
pandas_df = dflow.to_pandas_dataframe()
```

## <a name="load-csv-data"></a>Carregar dados do CSV

Ao ler arquivos delimitados, o tempo de execução subjacente pode inferir os parâmetros de análise (separador, codificação, se pretende utilizar cabeçalhos, etc.). Execute o seguinte código ao tentar ler um ficheiro CSV especificando apenas a respetiva localização.

```python
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|ALABAMA|1|101710|CONDADO Hale|10171002158| |
|2|ALABAMA|1|101710|CONDADO Hale|10171002162| |


Para excluir linhas durante o carregamento, definir o `skip_rows` parâmetro. Este parâmetro irá ignorar linhas de carregamento descendente no ficheiro CSV (usando um índice baseado num).

```python
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1)
dflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|
|0|ALABAMA|1|101710|CONDADO Hale|10171002158|29|
|1|ALABAMA|1|101710|CONDADO Hale|10171002162|40 |

Execute o seguinte código para exibir os tipos de dados de coluna.

```python
dflow.dtypes
```
Saída:

    stnam                     object
    fipst                     object
    leaid                     object
    leanm10                   object
    ncessch                   object
    schnam10                  object
    MAM_MTH00numvalid_1011    object
    dtype: object

Por predefinição, o SDK do Azure Machine Learning Data Prep não altera o tipo de dados. A origem de dados que está a ler a partir do é um arquivo de texto, para que o SDK lê todos os valores como cadeias de caracteres. Neste exemplo, as colunas numéricas devem ser analisadas como números. Definir o `inference_arguments` parâmetro `InferenceArguments.current_culture()` para inferir e converter os tipos de coluna durante o arquivo lido automaticamente.

```
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dflow.dtypes
```
Saída:

    stnam                      object
    fipst                     float64
    leaid                     float64
    leanm10                    object
    ncessch                   float64
    schnam10                   object
    ALL_MTH00numvalid_1011    float64
    dtype: object


Várias das colunas corretamente foram detetadas como numérico e seu tipo está definido como `float64`.

## <a name="use-excel-data"></a>Utilizar dados do Excel

O SDK inclui um `read_excel()` função para carregar ficheiros do Excel. Por predefinição, a função será carregada a primeira planilha na pasta de trabalho. Para definir uma folha de cálculo específica para carregar, definir o `sheet_name` parâmetro com o valor da cadeia do nome da folha.

```python
dflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dflow.head(5)
```

| |Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8| | |
|-|-------|-------|-------|-------|-------|-------|-------|-------|-|-|
|0|Nenhuma|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhuma| |
|1|Nenhuma|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhuma| |
|2|Nenhuma|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhuma| |
|3|classificação|Cargo|Studio|Mundial|Nacionais / %|Column1|Coletividade / %|Column2|Ano ^| |
|4|1|Avatar|Fox|2788|760.5|0.273|2027.5|0.727|2009 ^|5|

O resultado mostra que os dados na folha de segundo tinham três linhas vazias antes dos cabeçalhos. O `read_excel()` função contém parâmetros opcionais para ignorar linhas e o uso de cabeçalhos. Execute o seguinte código para ignorar as primeiras três linhas e utilizar a quarta linha como cabeçalhos.

```python
dflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_column_headers=True, skip_rows=3)
```

||classificação|Cargo|Studio|Mundial|Nacionais / %|Column1|Coletividade / %|Column2|Ano ^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Avatar|Fox|2788|760.5|0.273|2027.5|0.727|2009 ^|
|1|2|Titanic|Par.|2186.8|658.7|0.301|1528.1|0.699|1997 ^|

## <a name="load-fixed-width-data-files"></a>Carregar ficheiros de dados de largura fixa

Para carregar arquivos de largura fixa, especifique uma lista de desvios de caráter. A primeira coluna é sempre pressuposta para iniciar o deslocamento de zero.

```python
dflow = dprep.read_fwf('./data/fixed_width_file.txt', offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dflow.head(5)
```

||010000|99999|NORUEGA FALSAS|NO|NO_1|ENRS|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|NORUEGA FALSAS|NO|NO|ENSO||||
|1|010010|99999|JAN MAYEN|NO|JN|ENJA|+70933|-008667|+00090|


Para evitar a detecção de cabeçalho e analisar os dados corretos, passar `PromoteHeadersMode.NONE` para o `header` parâmetro.

```python
dflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)
```

||Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|NORUEGA FALSAS|NO|NO_1|ENRS|Column7|Column8|Column9|
|1|010003|99999|NORUEGA FALSAS|NO|NO|ENSO||||


## <a name="load-sql-data"></a>Carregar dados SQL

O SDK também pode carregar dados a partir de uma origem SQL. Atualmente, apenas o Microsoft SQL Server é suportado. Para ler dados a partir de um SQL server, crie uma [ `MSSQLDataSource` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.mssqldatasource?view=azure-dataprep-py) objeto que contém os parâmetros de ligação. O parâmetro de palavra-passe de `MSSQLDataSource` aceita um [ `Secret` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#register-secret-value--str--id--str---none-----azureml-dataprep-api-engineapi-typedefinitions-secret) objeto. Pode criar um objeto de segredo de duas formas:

* Registe o segredo e o respetivo valor com o motor de execução.
* Criar o segredo com apenas um `id` (se o valor secreto já está registado no ambiente de execução) usando `dprep.create_secret("[SECRET-ID]")`.

```python
secret = dprep.register_secret(value="[SECRET-PASSWORD]", id="[SECRET-ID]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=secret)
```

Depois de criar um objeto de origem de dados, pode avançar para ler dados a partir do resultado da consulta.

```python
dflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dflow.head(5)
```

| |ProductID|Nome|ProductNumber|Cor|StandardCost|ListPrice|Tamanho|Peso|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|ThumbNailPhoto|ThumbnailPhotoFileName|ROWGUID|ModifiedDate| |
|-|---------|----|-------------|-----|------------|---------|----|------|-----------------|--------------|-------------|-----------|----------------|--------------|----------------------|-------|------------|-|
|0|680|Estrada hl - preto, 58|FR-R92B-58|Preto|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00 + 00:00|Nenhuma|Nenhuma|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000 + 00:00|
|1|706|Estrada hl - vermelho, 58|FR-R92R-58|Vermelho|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00 + 00:00|Nenhuma|Nenhuma|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000 + 00:00|
|2|707|Capacete desportivo 100, vermelho|HL-U509-R|Vermelho|13.0863|34.99|Nenhuma|Nenhuma|35|33|2005-07-01 00:00:00 + 00:00|Nenhuma|Nenhuma|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000 + 00:00|


## <a name="use-azure-data-lake-storage"></a>Utilizar o armazenamento do Azure Data Lake

Existem duas formas do SDK podem adquirir o token de OAuth necessário para aceder ao armazenamento do Azure Data Lake:

* Obter o token de acesso a partir de uma sessão recente do logon do usuário da CLI do Azure
* Utilizar um principal de serviço (SP) e um certificado como segredo

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>Utilizar um token de acesso a partir de uma sessão de CLI do Azure recente

No seu computador local, execute o seguinte comando.

```azurecli
az login
az account show --query tenantId
dflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dflow.head(5) head
```

> [!NOTE]
> Se a sua conta de utilizador é membro de mais do que um inquilino do Azure, tem de especificar o inquilino sob a forma de nome de anfitrião do URL do AAD.

### <a name="create-a-service-principal-with-the-azure-cli"></a>Criar um principal de serviço com a CLI do Azure

Utilize a CLI do Azure para criar um principal de serviço e o certificado correspondente. Este principal de serviço específico está configurado com o `reader` função, com seu escopo reduzido para apenas a conta de armazenamento do Azure Data Lake 'dpreptestfiles'.

```azurecli
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```

Este comando emite o `appId` e o caminho para o ficheiro de certificado (normalmente, na pasta raiz). O ficheiro. crt contém o certificado público e a chave privada no formato PEM.

```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

Para configurar a ACL para o sistema de ficheiros de armazenamento do Azure Data Lake, utilize o objectId do utilizador. Neste exemplo, o principal de serviço é utilizado.

```azurecli
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```

Para configurar `Read` e `Execute` acesso para o sistema de ficheiros de armazenamento do Azure Data Lake, configurar a ACL para pastas e ficheiros individualmente. Isso é que o modelo de ACL de HDFS subjacente não suporta a herança.

```azurecli
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r-x" --path /
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r--" --path /farmers-markets.csv
```

```
certThumbprint = 'C2:08:9D:9E:D1:74:FC:EB:E9:7E:63:96:37:1C:13:88:5E:B9:2C:84'
certificate = ''
with open('./data/adls-dpreptestfiles.crt', 'rt', encoding='utf-8') as crtFile:
    certificate = crtFile.read()

servicePrincipalAppId = "8dd38f34-1fcb-4ff9-accd-7cd60b757174"
```

### <a name="acquire-an-oauth-access-token"></a>Adquirir um token de acesso de OAuth

Utilize o `adal` pacote (`pip install adal`) para criar um contexto de autenticação no inquilino MSFT e adquirir um token de acesso de OAuth. Para ADLS, o recurso no pedido de token tem de ser para 'https://datalake.azure.net', que é diferente da maioria dos outros recursos do Azure.

```python
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext('https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate('https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dflow = dprep.read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dflow.to_pandas_dataframe().head()
```

||FMID|MarketName|Web site|Rua|city|Concelho|
|----|------|-----|----|----|----|----|
|0|1012063|Caledónia agricultores mercado associação - Danville|https://sites.google.com/site/caledoniafarmers... ||Danville|Caledónia|
|1|1011871|Stearns Homestead agricultores ' mercado|http://Stearnshomestead.com |6975 ridge estrada|Parma|Cuyahoga|
|2|1011878|Mercado de quilómetro 100|https://www.pfcmarkets.com |507 Harrison St|Kalamazoo|Kalamazoo|
|3|1009364|106 S. Main Street agricultores mercado|http://thetownofsixmile.wordpress.com/ |106 S. Main Street|Seis quilómetro|||
|4|1010691|Mercado de agricultores 10th Rua da Comunidade|https://agrimissouri.com/... |Rua 10th e Poplar|Lamar|Barton|

## <a name="next-steps"></a>Passos Seguintes

* Consulte o SDK [descrição geral](https://aka.ms/data-prep-sdk) para padrões de design e exemplos de utilização
* Consulte o SDK do Azure Machine Learning Data Prep [tutorial](tutorial-data-prep.md) para obter um exemplo de resolver um cenário específico
