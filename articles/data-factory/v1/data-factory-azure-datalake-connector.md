---
title: Copiar dados de e para Azure Data Lake Storage Gen1
description: Saiba como copiar dados de e para a Data Lake Store utilizando a Azure Data Factory
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
ms.custom: devx-track-csharp
robots: noindex
ms.openlocfilehash: 8bdfceff562ae2501a9d95774f7134669e929b7e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379408"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Copiar dados de e para data lake storage Gen1 usando data factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-azure-datalake-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-data-lake-store.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector Azure Data Lake Storage Gen1 em V2](../connector-azure-data-lake-store.md).

Este artigo explica como usar a Copy Activity in Azure Data Factory para mover dados de e para Azure Data Lake Storage Gen1 (anteriormente conhecido como Azure Data Lake Store). Baseia-se no artigo de atividades de [movimento de dados,](data-factory-data-movement-activities.md) uma visão geral do movimento de dados com a Copy Activity.

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **da Azure Data Lake Store** para as seguintes lojas de dados:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados das seguintes lojas de dados **para a Azure Data Lake Store:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Crie uma conta Data Lake Store antes de criar um pipeline com a Copy Activity. Para mais informações, consulte [Começar com a Azure Data Lake Store.](../../data-lake-store/data-lake-store-get-started-portal.md)

## <a name="supported-authentication-types"></a>Tipos de autenticação suportados
O conector Data Lake Store suporta estes tipos de autenticação:
* Autenticação do principal de serviço
* Autenticação credencial do utilizador (OAuth)

Recomendamos que utilize a autenticação principal do serviço, especialmente para uma cópia de dados agendada. O comportamento de expiração do token pode ocorrer com a autenticação credencial do utilizador. Para mais detalhes de configuração, consulte a secção [de propriedades de serviço Linked.](#linked-service-properties)

## <a name="get-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de/para uma Loja de Lagos de Dados Azure utilizando diferentes ferramentas/APIs.

A forma mais fácil de criar um pipeline para copiar dados é utilizar o **Copy Wizard**. Para obter um tutorial sobre a criação de um oleoduto utilizando o Copy Wizard, consulte [Tutorial: Crie um oleoduto utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md).

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell,** **Azure Resource Manager,** **.NET API** e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Criar uma fábrica de **dados.** Uma fábrica de dados pode conter um ou mais oleodutos.
2. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados. Por exemplo, se estiver a copiar dados de um armazenamento de blob Azure para uma Loja de Data Lake Azure, cria dois serviços ligados para ligar a sua conta de armazenamento Azure e a loja Azure Data Lake à sua fábrica de dados. Para propriedades de serviços ligadas específicas da Azure Data Lake Store, consulte a secção [de propriedades de serviços ligadas.](#linked-service-properties)
2. Crie **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. No exemplo mencionado no último passo, cria-se um conjunto de dados para especificar o recipiente blob e a pasta que contém os dados de entrada. E, cria outro conjunto de dados para especificar a pasta e o caminho do ficheiro na loja Data Lake que detém os dados copiados do armazenamento do blob. Para obter propriedades de conjunto de dados específicas da Azure Data Lake Store, consulte a secção [de propriedades do conjunto de dados.](#dataset-properties)
3. Crie um **pipeline** com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, utiliza o BlobSource como fonte e o AzureDataLakeStoreSink como pia para a atividade da cópia. Da mesma forma, se estiver a copiar da Azure Data Lake Store para o Azure Blob Storage, utilize a AzureDataLakeStoreSource e a BlobSink na atividade da cópia. Para propriedades de atividade de cópia específicas da Azure Data Lake Store, consulte a secção [de propriedades da atividade de cópia.](#copy-activity-properties) Para obter mais informações sobre como utilizar uma loja de dados como fonte ou pia, clique no link na secção anterior para a sua loja de dados.

Quando utiliza o assistente, as definições de JSON para estas entidades da Data Factory (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Data Factory utilizando o formato JSON. Para amostras com definições JSON para entidades da Data Factory que são usadas para copiar dados de/para uma Loja de Lagos de Dados Azure, consulte a secção de [exemplos JSON](#json-examples-for-copying-data-to-and-from-data-lake-store) deste artigo.

As secções seguintes fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Data Factory específicas da Data Lake Store.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas
Um serviço ligado liga uma loja de dados a uma fábrica de dados. Cria um serviço ligado do tipo **AzureDataLakeStore** para ligar os dados da Data Lake Store à sua fábrica de dados. A tabela seguinte descreve elementos JSON específicos dos serviços ligados à Data Lake Store. Pode escolher entre o principal serviço e a autenticação credencial do utilizador.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| **tipo** | A propriedade tipo deve ser definida para **AzureDataLakeStore**. | Yes |
| **dataLakeStoreUri** | Informação sobre a conta da Azure Data Lake Store. Esta informação requer um dos seguintes formatos: `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/` . | Yes |
| **subscriptionId** | ID de assinatura Azure a que pertence a conta Data Lake Store. | Necessário para a pia |
| **nome do Grupo de Recursos** | Nome do grupo de recursos Azure a que pertence a conta Data Lake Store. | Necessário para a pia |

### <a name="service-principal-authentication-recommended"></a>Autenticação principal do serviço (recomendado)
Para utilizar a autenticação principal do serviço, registe uma entidade de aplicação no Azure Ative Directory (Azure AD) e conceda-lhe o acesso à Data Lake Store. Para etapas detalhadas, consulte [a autenticação do Serviço ao Serviço.](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md) Tome nota dos seguintes valores, que utiliza para definir o serviço ligado:
* ID da Aplicação
* Chave de aplicação
* ID do inquilino

> [!IMPORTANT]
> Certifique-se de conceder ao chefe de serviço a permissão adequada na Azure Data Lake Store:
>- **Para utilizar a Data Lake Store como fonte**, conceda pelo menos a permissão de acesso a dados leia + **execute** a permissão de acesso aos dados para listar e copiar o conteúdo de uma pasta, ou **Leia** a permissão para copiar um único ficheiro. Sem requisitos no controlo de acesso ao nível da conta.
>- **Para utilizar a Data Lake Store como pia,** conceda pelo menos **a permissão de** acesso a dados para criar itens infantis na pasta. E se utilizar o Azure IR para capacitar a cópia (tanto a fonte como a pia estão na nuvem), de forma a permitir que a Data Factory detete a região da Data Lake Store, conceda pelo menos o papel **do Leitor** no controlo de acesso à conta (IAM). Se quiser evitar esta função IAM, [especifique a execuçãoLocalização](data-factory-data-movement-activities.md#global) com a localização da sua Data Lake Store na atividade de cópia.
>- Se **utilizar o Copy Wizard para autores de pipelines,** conceda pelo menos a função **de Leitor** no controlo de acesso à conta (IAM). Além disso, conceda pelo menos **a leitura + execute** a permissão para a sua raiz data lake store ("/") e seus filhos. Caso contrário, poderá ver a mensagem "As credenciais fornecidas são inválidas".

Utilize a autenticação principal do serviço especificando as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| **servicePrincipalId** | Especifique a identificação do cliente da aplicação. | Yes |
| **servicePrincipalKey** | Especifique a chave da aplicação. | Yes |
| **tenant** | Especifique a informação do inquilino (nome de domínio ou ID do inquilino) sob a qual a sua aplicação reside. Pode recuperá-lo pairando sobre o rato no canto superior direito do portal Azure. | Yes |

**Exemplo: Autenticação principal do serviço**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Autenticação credencial do utilizador
Em alternativa, pode utilizar a autenticação credencial do utilizador para copiar a partir ou para a Data Lake Store, especificando as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| **autorização** | Clique no botão **Authorize** no Editor de Fábrica de Dados e introduza a sua credencial que atribui o URL de autorização autogerado a esta propriedade. | Yes |
| **sessionId** | Identificação da Sessão OAuth da Sessão de Autorização da OAuth. Cada ID de sessão é único e só pode ser usado uma vez. Esta definição é gerada automaticamente quando utiliza o Editor de Fábrica de Dados. | Yes |

> [!IMPORTANT]
> Certifique-se de conceder ao utilizador a devida permissão na Azure Data Lake Store:
>- **Para utilizar a Data Lake Store como fonte**, conceda pelo menos a permissão de acesso a dados leia + **execute** a permissão de acesso aos dados para listar e copiar o conteúdo de uma pasta, ou **Leia** a permissão para copiar um único ficheiro. Sem requisitos no controlo de acesso ao nível da conta.
>- **Para utilizar a Data Lake Store como pia,** conceda pelo menos **a permissão de** acesso a dados para criar itens infantis na pasta. E se utilizar o Azure IR para capacitar a cópia (tanto a fonte como a pia estão na nuvem), de forma a permitir que a Data Factory detete a região da Data Lake Store, conceda pelo menos o papel **do Leitor** no controlo de acesso à conta (IAM). Se quiser evitar esta função IAM, [especifique a execuçãoLocalização](data-factory-data-movement-activities.md#global) com a localização da sua Data Lake Store na atividade de cópia.
>- Se **utilizar o Copy Wizard para autores de pipelines,** conceda pelo menos a função **de Leitor** no controlo de acesso à conta (IAM). Além disso, conceda pelo menos **a leitura + execute** a permissão para a sua raiz data lake store ("/") e seus filhos. Caso contrário, poderá ver a mensagem "As credenciais fornecidas são inválidas".

**Exemplo: Autenticação credencial do utilizador**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>Expiração simbólica
O código de autorização que gera utilizando o botão **Authorize** expira após um certo período de tempo. A seguinte mensagem significa que o token de autenticação expirou:

Erro de funcionamento credencial: invalid_grant - AADSTS70002: Credenciais de validação de erros. AADSTS70008: A subvenção de acesso prestada é caducada ou revogada. Identificação de vestígios: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlation ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21-09-313Z1.

A tabela a seguir mostra os tempos de validade de diferentes tipos de contas de utilizador:

| Tipo de utilizador | Expira depois |
|:--- |:--- |
| Contas de *utilizador não* geridas pelo Azure Ative Directory (por exemplo, ou @hotmail.com @live.com ) |12 horas |
| Contas de utilizadores geridas pelo Azure Ative Directory |14 dias após a última corrida de fatias <br/><br/>90 dias, se uma fatia baseada num serviço ligado com base em OAuth for executado pelo menos uma vez a cada 14 dias |

Se alterar a sua palavra-passe antes do prazo de validade do token, o token expira imediatamente. Verá a mensagem mencionada anteriormente nesta secção.

Pode reautorizar a conta utilizando o botão **Authorize** quando o token expirar para recolocar o serviço ligado. Também pode gerar valores para as propriedades **de sessãoId** e **de autorização** programáticamente utilizando o seguinte código:


```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```
Para obter mais informações sobre as classes data factory utilizadas no código, consulte os tópicos [da Classe AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice), [AzureDataLakeAnalyticsLinkedService Class](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)e [AuthorizationSessionGetResponse Class.](/dotnet/api/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse) Adicione uma referência à versão `2.9.10826.1824` da classe utilizada no `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` `WindowsFormsWebAuthenticationDialog` código.

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

**Sintoma:** Ao copiar dados **para** a Azure Data Lake Store, se a sua atividade de cópia falhar com o seguinte erro:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Causa raiz:** Existem 2 razões possíveis:

1. O `resourceGroupName` serviço `subscriptionId` e/ou especificado no serviço ligado à Azure Data Lake Store está incorreto;
2. O utilizador ou o diretor de serviço não tem a permissão necessária.

**Resolução:**

1. Certifique-se de que o `subscriptionId` serviço e especifica no serviço ligado `resourceGroupName` `typeProperties` são, de facto, os que a sua conta do lago de dados pertence.

2. Certifique-se de que concede pelo menos a função **reader** ao utilizador ou ao principal de serviço na conta do lago de dados. Eis como fazê-lo:

    1. Vá ao portal Azure -> a sua conta Data Lake Store
    2. Clique no **controlo de acesso (IAM)** na lâmina da Data Lake Store
    3. Clique **em Adicionar atribuição de função**
    4. Definir **Função** como **Leitor**, e selecione o utilizador ou o principal de serviço que utiliza para cópia para conceder acesso

3. Se não quiser conceder a função **reader** ao utilizador ou ao principal do serviço, a alternativa é [especificar explicitamente um local de execução](data-factory-data-movement-activities.md#global) em atividade de cópia com a localização da sua Data Lake Store. Exemplo:

    ```json
    {
      "name": "CopyToADLS",
      "type": "Copy",
      ......
      "typeProperties": {
        "source": {
          "type": "<source type>"
        },
        "sink": {
          "type": "AzureDataLakeStoreSink"
        },
        "exeuctionLocation": "West US"
      }
    }
    ```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para especificar um conjunto de dados para representar dados de entrada numa Data Lake Store, define a propriedade **tipo** do conjunto de dados para **a AzureDataLakeStore**. Defina a propriedade **linkedServiceName** do conjunto de dados com o nome do serviço ligado à Data Lake Store. Para obter uma lista completa das secções e propriedades JSON disponíveis para definir conjuntos de dados, consulte o artigo [Criar conjuntos de dados.](data-factory-create-datasets.md) As secções de um conjunto de dados em JSON, tais como **estrutura,** **disponibilidade** e **política,** são semelhantes para todos os tipos de conjuntos de dados (base de dados Azure SQL, blob Azure e tabela Azure, por exemplo). A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações como a localização e o formato dos dados na loja de dados.

A secção **typeProperties** para um conjunto de dados do tipo **AzureDataLakeStore** contém as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| **folderPath** |Caminho para o recipiente e pasta na Data Lake Store. |Yes |
| **fileName** |Nome do ficheiro na Azure Data Lake Store. A **propriedade fileName** é opcional e sensível a casos. <br/><br/>Se especificar **o nome de ficheiro,** a atividade (incluindo copy) funciona no ficheiro específico.<br/><br/>Quando **o data de ficheiro** não é especificado, a Cópia inclui todos os ficheiros na **pastaPa no** conjunto de dados de entrada.<br/><br/>Quando **o nome de ficheiro** não é especificado para um conjunto de dados de saída e **preservarAequia** não é especificado na pia de atividade, o nome do ficheiro gerado está no formato `Data._Guid_.txt` . Por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |No |
| **partitionedBy** |A **propriedade partitionedBy** é opcional. Pode usá-lo para especificar um caminho dinâmico e nome de ficheiro para dados da série de tempo. Por exemplo, **a pastaPath** pode ser parametrizada para cada hora de dados. Para mais detalhes e exemplos, consulte a propriedade PartitionedBy. |No |
| **formato** | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat** e **ParquetFormat**. Desa um destes valores, o **tipo** de propriedade em **formato.** Para mais informações, consulte o [formato Text](data-factory-supported-file-and-compression-formats.md#text-format), [formato JSON,](data-factory-supported-file-and-compression-formats.md#json-format) [formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [formato ORC,](data-factory-supported-file-and-compression-formats.md#orc-format)e secções [De Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) nos [formatos Ficheiro e compressão suportados pelo artigo da Azure Data Factory.](data-factory-supported-file-and-compression-formats.md) <br><br> Se pretender copiar ficheiros "as-is" entre lojas baseadas em ficheiros (cópia binária), ignore a `format` secção nas definições de conjunto de dados de entrada e saída. |No |
| **compressão** | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são **GZip,** **Deflate,** **BZip2** e **ZipDeflate**. Os níveis suportados são **ideais** e **mais rápidos.** Para obter mais informações, consulte [formatos de arquivo e compressão suportados pela Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

### <a name="the-partitionedby-property"></a>A propriedade partitionedBy
Pode especificar as propriedades **dinâmicas do pastaPath** e **do nome de ficheiros** para dados de séries tempotamos com a propriedade **partitionedBy,** funções de Data Factory e variáveis do sistema. Para mais detalhes, consulte a [Azure Data Factory - funções e artigo de variáveis do sistema.](data-factory-functions-variables.md)


No exemplo seguinte, `{Slice}` é substituído pelo valor da variável do sistema data factory no formato especificado ( `SliceStart` `yyyyMMddHH` ). O nome `SliceStart` refere-se à hora de início da fatia. A `folderPath` propriedade é diferente para cada fatia, como em `wikidatagateway/wikisampledataout/2014100103` `wikidatagateway/wikisampledataout/2014100104` ou.

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

No exemplo seguinte, o ano, mês, dia e hora de `SliceStart` serem extraídos em variáveis separadas que são utilizadas pelas `folderPath` `fileName` propriedades:
```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Para obter mais detalhes sobre conjuntos de dados, agendamentos e fatias de séries de tempo, consulte os [Datasets na Azure Data Factory](data-factory-create-datasets.md) e [na Data Factory, agendando e executando](data-factory-scheduling-and-execution.md) artigos.


## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Criar oleodutos.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e política estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na secção **de tipoProperties** de uma atividade variam com cada tipo de atividade. Para uma atividade de cópia, variam dependendo dos tipos de fontes e pias.

**A AzureDataLakeStoreSource** suporta a seguinte propriedade na secção **typeProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| **recursivo** |Indica se os dados são lidos novamente a partir das sub-dobradeiras ou apenas a partir da pasta especificada. |Verdadeiro (valor predefinido), Falso |No |

**A AzureDataLakeStoreSink** suporta as seguintes propriedades na secção **typeProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| **copyOportundo** |Especifica o comportamento da cópia. |<b>Preservar A Outra:</b>Preserva a hierarquia dos ficheiros na pasta alvo. O percurso relativo do ficheiro de origem para a pasta de origem é idêntico ao caminho relativo do ficheiro alvo para a pasta alvo.<br/><br/><b>FlattenHierarchy</b>: Todos os ficheiros da pasta de origem são criados no primeiro nível da pasta alvo. Os ficheiros-alvo são criados com nomes autogerados.<br/><br/><b>MergeFiles</b>: Funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro ou do blob for especificado, o nome do ficheiro fundido é o nome especificado. Caso contrário, o nome do ficheiro é autogerado. |No |

### <a name="recursive-and-copybehavior-examples"></a>recursivo e copySA examples de comportamento
Esta secção descreve o comportamento resultante da operação Copy para diferentes combinações de valores recursivos e de cópias.

| recursivo | copyOportundo | Comportamento resultante |
| --- | --- | --- |
| true |preservarHierarquia |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta-alvo1 é criada com a mesma estrutura que a fonte<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. |
| true |achatamento |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta-alvo1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| true |fusõesFilias |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta-alvo1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + O conteúdo do Ficheiro 5 é fundido num ficheiro com nome de ficheiro gerado automaticamente |
| false |preservarHierarquia |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta-alvo1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/><br/>A sub-dobra1 com File3, File4 e File5 não são recolhidas. |
| false |achatamento |Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta-alvo1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/><br/>A sub-dobra1 com File3, File4 e File5 não são recolhidas. |
| false |fusõesFilias |Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sub-página1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta-alvo1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos do Ficheiro1 + Ficheiro2 são fundidos num único ficheiro com o nome de ficheiro gerado automaticamente. nome gerado automaticamente para File1<br/><br/>A sub-dobra1 com File3, File4 e File5 não são recolhidas. |

## <a name="supported-file-and-compression-formats"></a>Formatos de ficheiro e de compressão suportados
Para mais detalhes, consulte os [formatos De Arquivo e compressão no artigo da Azure Data Factory.](data-factory-supported-file-and-compression-formats.md)

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>Exemplos JSON para copiar dados de e para Data Lake Store
Os exemplos a seguir fornecem definições JSON de amostra. Pode utilizar estas definições de amostra para criar um oleoduto utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Os exemplos mostram como copiar dados de e para data lake store e armazenamento Azure Blob. No entanto, os dados podem ser copiados _diretamente_ de qualquer uma das fontes para qualquer um dos lavatórios suportados. Para obter mais informações, consulte a secção "Lojas de dados suportadas e formatos" nos dados do Movimento utilizando o artigo [Copy Activity.](data-factory-data-movement-activities.md)

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Exemplo: Copiar dados do Azure Blob Storage para a Azure Data Lake Store
O código de exemplo nesta secção mostra:

* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um serviço ligado do tipo [AzureDataLakeStore](#linked-service-properties).
* Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureDataLakeStore](#dataset-properties).
* Um [oleoduto](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [AzureDataLakeStoreStoreSink.](#copy-activity-properties)

Os exemplos mostram como os dados da série de tempo do Azure Blob Storage são copiados para a Data Lake Store a cada hora.

**Serviço ligado do Storage do Azure**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Serviço ligado à Loja de Dados Azure**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> Para mais detalhes de configuração, consulte a secção [de propriedades de serviço Linked.](#linked-service-properties)
>

**Conjunto de dados de entrada do blobs do Azure**

No exemplo seguinte, os dados são recolhidos a partir de uma nova bolha a cada hora `"frequency": "Hour", "interval": 1` . O caminho da pasta e o nome do ficheiro para a bolha são avaliados dinamicamente com base na hora de início da fatia que está a ser processada. O percurso da pasta utiliza a parte do ano, mês e dia da hora de início. O nome do ficheiro utiliza a parte da hora da hora de início. A `"external": true` definição informa o serviço Data Factory de que a tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Conjunto de dados de produção da Azure Data Lake Store**

O exemplo a seguir copia dados para a Data Lake Store. Novos dados são copiados para a Data Lake Store a cada hora.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Copiar atividade em um oleoduto com uma fonte de bolha e um lavatório Data Lake Store**

No exemplo seguinte, o pipeline contém uma atividade de cópia que está configurada para utilizar os conjuntos de dados de entrada e saída. A atividade de cópia está programada para ser executada a cada hora. Na definição JSON do gasoduto, o `source` tipo é definido para , e o tipo é definido para `BlobSource` `sink` `AzureDataLakeStoreSink` .

```json
{
    "name":"SamplePipeline",
    "properties":
    {
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [
            {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureDataLakeStoreOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Exemplo: Copiar dados da Azure Data Lake Store para uma bolha Azure
O código de exemplo nesta secção mostra:

* Um serviço ligado do tipo [AzureDataLakeStore](#linked-service-properties).
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureDataLakeStore](#dataset-properties).
* Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [oleoduto](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza [a AzureDataLakeStoreSource](#copy-activity-properties) e [a BlobSink.](data-factory-azure-blob-connector.md#copy-activity-properties)

O código copia os dados das séries temporizadas da Data Lake Store para uma bolha Azure a cada hora.

**Serviço ligado à Loja de Dados Azure**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> Para mais detalhes de configuração, consulte a secção [de propriedades de serviço Linked.](#linked-service-properties)
>

**Serviço ligado do Storage do Azure**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de dados de entrada do Lago de Dados Azure**

Neste exemplo, a definição `"external"` de `true` informação ao serviço data Factory que a tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

```json
{
    "name": "AzureDataLakeStoreInput",
    "properties":
    {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
**Conjunto de dados de saída de blob Azure**

No exemplo seguinte, os dados são escritos para uma nova bolha a cada hora `"frequency": "Hour", "interval": 1` . O caminho da pasta para a bolha é avaliado dinamicamente com base na hora de início da fatia que está a ser processada. O percurso da pasta utiliza a parte do ano, mês, dia e horas da hora de início.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Uma atividade de cópia em um oleoduto com uma fonte Azure Data Lake Store e um lavatório blob**

No exemplo seguinte, o pipeline contém uma atividade de cópia que está configurada para utilizar os conjuntos de dados de entrada e saída. A atividade de cópia está programada para ser executada a cada hora. Na definição JSON do gasoduto, o `source` tipo é definido para , e o tipo é definido para `AzureDataLakeStoreSource` `sink` `BlobSink` .

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureDataLakeStoreInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

Na definição de atividade de cópia, também é possível mapear colunas do conjunto de dados de origem para colunas no conjunto de dados do lavatório. Para mais informações, consulte [as colunas mapping dataset na Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e otimização
Para conhecer os fatores que afetam o desempenho da Copy Activity e como otimizá-lo, consulte o artigo de [performance da Atividade de Cópia e afinação.](data-factory-copy-activity-performance.md)