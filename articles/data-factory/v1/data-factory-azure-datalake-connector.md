---
title: Copiar dados de e para azure Data Lake Storage Gen1
description: Saiba copiar dados de e para data lake store usando a Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b6a60536bae6fbedf01eda7aa340e90ced58e004
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281603"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Copiar dados de e para Data Lake Storage Gen1 utilizando data factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-azure-datalake-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-data-lake-store.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector Azure Data Lake Storage Gen1 em V2](../connector-azure-data-lake-store.md).

Este artigo explica como usar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de e para o Azure Data Lake Storage Gen1 (anteriormente conhecido como Azure Data Lake Store). Baseia-se no artigo de atividades do [movimento Data,](data-factory-data-movement-activities.md) uma visão geral do movimento de dados com a Atividade de Cópia.

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **da Azure Data Lake Store** para as seguintes lojas de dados:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados das seguintes lojas de dados **para a Azure Data Lake Store:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Crie uma conta Data Lake Store antes de criar um pipeline com copy activity. Para mais informações, consulte [Start start with Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Tipos de autenticação suportado
O conector Data Lake Store suporta estes tipos de autenticação:
* Autenticação do principal de serviço
* Autenticação credencial do utilizador (OAuth)

Recomendamos que utilize a autenticação principal do serviço, especialmente para uma cópia de dados agendada. O comportamento de expiração do token pode ocorrer com a autenticação credencial do utilizador. Para mais detalhes de configuração, consulte a secção de propriedades de [serviço Linked.](#linked-service-properties)

## <a name="get-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de/para uma Loja de Lagos De Dados Azure utilizando diferentes ferramentas/APIs.

A forma mais fácil de criar um pipeline para copiar dados é utilizar o **Assistente de Cópia**. Para um tutorial sobre a criação de um pipeline utilizando o Assistente de Cópia, consulte [Tutorial: Crie um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md).

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Estúdio Visual,** **Azure PowerShell,** **Modelo de Gestor de Recursos Azure,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de sumidouro:

1. Criar uma fábrica de **dados.** Uma fábrica de dados pode conter um ou mais oleodutos.
2. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados. Por exemplo, se estiver a copiar dados de um armazenamento de blob Azure para uma Loja de Lagos De Dados Azure, cria dois serviços ligados para ligar a sua conta de armazenamento Azure e a loja Azure Data Lake à sua fábrica de dados. Para propriedades de serviço ligadas específicas à Azure Data Lake Store, consulte a secção de [propriedades de serviço ligadas.](#linked-service-properties)
2. Crie **conjuntos** de dados para representar dados de entrada e saída para a operação de cópia. No exemplo mencionado no último passo, cria-se um conjunto de dados para especificar o recipiente e a pasta blob que contém os dados de entrada. E cria outro conjunto de dados para especificar a pasta e o caminho de ficheiros na loja Data Lake que detém os dados copiados do armazenamento de blob. Para propriedades de conjunto de dados específicas da Azure Data Lake Store, consulte a secção de propriedades do conjunto de [dados.](#dataset-properties)
3. Crie um **pipeline** com uma atividade de cópia que tome um conjunto de dados como entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, utiliza o BlobSource como fonte e o AzureDataLakeStoreSink como um lavatório para a atividade da cópia. Da mesma forma, se estiver a copiar da Azure Data Lake Store para o Armazenamento De Blob Azure, utiliza o AzureDataLakeStoreSource e o BlobSink na atividade da cópia. Para propriedades de atividade de cópia específicas da Azure Data Lake Store, consulte a secção de propriedades de atividade de [cópia.](#copy-activity-properties) Para obter mais informações sobre como utilizar uma loja de dados como fonte ou pia, clique no link na secção anterior para a sua loja de dados.

Quando utiliza o assistente, as definições jSON para estas entidades da Fábrica de Dados (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Fábrica de Dados utilizando o formato JSON. Para amostras com definições JSON para entidades da Fábrica de Dados que são usadas para copiar dados de/para uma Loja de Lagos De Dados Azure, consulte a secção de exemplos da [JSON](#json-examples-for-copying-data-to-and-from-data-lake-store) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades jSON que são usadas para definir entidades data Factory específicas para data lake store.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos
Um serviço ligado liga uma loja de dados a uma fábrica de dados. Cria um serviço ligado do tipo **AzureDataLakeStore** para ligar os dados da Data Lake Store à sua fábrica de dados. A tabela seguinte descreve elementos JSON específicos dos serviços ligados à Data Lake Store. Pode escolher entre o diretor de serviço e a autenticação credencial do utilizador.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| **tipo** | A propriedade tipo deve ser definida para **AzureDataLakeStore**. | Sim |
| **dataLakeStoreUri** | Informações sobre a conta Azure Data Lake Store. Esta informação tem um dos `https://[accountname].azuredatalakestore.net/webhdfs/v1` `adl://[accountname].azuredatalakestore.net/`seguintes formatos: ou . | Sim |
| **subscriptionId** | Id de subscrição Azure a que pertence a conta Data Lake Store. | Obrigatório para afundar |
| **resourceGroupName** | Nome de grupo de recursos Azure a que pertence a conta Data Lake Store. | Obrigatório para afundar |

### <a name="service-principal-authentication-recommended"></a>Autenticação do principal de serviço (recomendado)
Para utilizar a autenticação principal do serviço, registe uma entidade de aplicação no Azure Ative Directory (Azure AD) e conceda-lhe o acesso à Data Lake Store. Para obter passos detalhados, consulte a [autenticação serviço-a-serviço](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Tome nota dos seguintes valores, que utiliza para definir o serviço vinculado:
* ID da aplicação
* Chave de aplicação
* ID do inquilino

> [!IMPORTANT]
> Certifique-se de que concede a permissão adequada ao principal do serviço na Azure Data Lake Store:
>- Para utilizar a **Data Lake Store como fonte,** conceda pelo menos ler + **executar** permissão de acesso a dados para listar e copiar o conteúdo de uma pasta, ou **ler** permissão para copiar um único ficheiro. Sem requisitos no controlo de acesso ao nível da conta.
>- Para utilizar a **Data Lake Store como pia,** conceda pelo menos write + **Execute** a permissão de acesso a dados para criar itens infantis na pasta. E se utilizar o Azure IR para capacitar a cópia (tanto a fonte como a pia estão na nuvem), de modo a permitir que a Data Factory detete a região da Data Lake Store, conceda pelo menos o papel **de Leitor** no controlo de acesso à conta (IAM). Se pretender evitar esta função IAM, [especifique a execuçãoLocalização](data-factory-data-movement-activities.md#global) com a localização da sua Data Lake Store na atividade de cópia.
>- Se utilizar o **Copy Wizard para obter pipelines**de autor, conceda pelo menos o papel do **Leitor** no controlo de acesso à conta (IAM). Além disso, conceda pelo menos **ler + Executar** permissão para a raiz da data Lake Store ("/") e seus filhos. Caso contrário, poderá ver a mensagem "As credenciais fornecidas são inválidas".

Utilize a autenticação principal do serviço especificando as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| **serviçoPrincipalId** | Especifique a identificação do cliente do pedido. | Sim |
| **serviçoPrincipalKey** | Especifique a chave da aplicação. | Sim |
| **inquilino** | Especifique as informações do arrendatário (nome de domínio ou ID do inquilino) sob a qual reside a sua candidatura. Pode recuperá-lo pairando sobre o rato no canto superior direito do portal Azure. | Sim |

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
Em alternativa, pode utilizar a autenticação credencial do utilizador para copiar de ou para data lake Store, especificando as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| **autorização** | Clique no botão **Autorizar** no Editor da Fábrica de Dados e introduza a sua credencial que atribui o URL de autorização autogerado a esta propriedade. | Sim |
| **sessionId** | OAuth session ID da sessão de autorização da OAuth. Cada id da sessão é único e só pode ser usado uma vez. Esta definição é gerada automaticamente quando utiliza o Editor da Fábrica de Dados. | Sim |

> [!IMPORTANT]
> Certifique-se de que concede ao utilizador a permissão adequada na Loja de Lagos De Dados Do Azure:
>- Para utilizar a **Data Lake Store como fonte,** conceda pelo menos ler + **executar** permissão de acesso a dados para listar e copiar o conteúdo de uma pasta, ou **ler** permissão para copiar um único ficheiro. Sem requisitos no controlo de acesso ao nível da conta.
>- Para utilizar a **Data Lake Store como pia,** conceda pelo menos write + **Execute** a permissão de acesso a dados para criar itens infantis na pasta. E se utilizar o Azure IR para capacitar a cópia (tanto a fonte como a pia estão na nuvem), de modo a permitir que a Data Factory detete a região da Data Lake Store, conceda pelo menos o papel **de Leitor** no controlo de acesso à conta (IAM). Se pretender evitar esta função IAM, [especifique a execuçãoLocalização](data-factory-data-movement-activities.md#global) com a localização da sua Data Lake Store na atividade de cópia.
>- Se utilizar o **Copy Wizard para obter pipelines**de autor, conceda pelo menos o papel do **Leitor** no controlo de acesso à conta (IAM). Além disso, conceda pelo menos **ler + Executar** permissão para a raiz da data Lake Store ("/") e seus filhos. Caso contrário, poderá ver a mensagem "As credenciais fornecidas são inválidas".

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

#### <a name="token-expiration"></a>Expiração do token
O código de autorização que gera utilizando o botão **Autorizar** expira após um certo período de tempo. A seguinte mensagem significa que o símbolo de autenticação expirou:

Erro de operação credencial: invalid_grant - AADSTS70002: Erro validando credenciais. AADSTS70008: A subvenção de acesso fornecida é expirada ou revogada. Identificação do traço: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlation ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21-09-31Z.

O quadro seguinte mostra os prazos de validade de diferentes tipos de contas de utilizador:

| Tipo de utilizador | Expira depois |
|:--- |:--- |
| Contas de utilizador *não* geridas pelo Azure @hotmail.com Ative @live.comDirectory (por exemplo, ou ) |12 horas |
| Contas de utilizadores geridas pelo Azure Ative Directory |14 dias após a última corrida de fatias <br/><br/>90 dias, se uma fatia baseada num serviço ligado baseado em OAuth funciona pelo menos uma vez a cada 14 dias |

Se alterar a sua palavra-passe antes do tempo de validade do token, o token expira imediatamente. Verá a mensagem mencionada anteriormente nesta secção.

Pode reautorizar a conta utilizando o botão **Autorizar** quando o token expirar para recolocar o serviço ligado. Também pode gerar valores para as propriedades **sessionId** e **autorização** programáticamente utilizando o seguinte código:


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
Para mais detalhes sobre as classes Data Factory utilizadas no código, consulte a [classe AzureDataLakeStoreLinkedService,](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx) [AzureDataLakeAnalyticsLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)e os tópicos da [Classe AuthorizationSessionGetResponse.](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) Adicione uma referência `2.9.10826.1824` `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` à `WindowsFormsWebAuthenticationDialog` versão para a classe utilizada no código.

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

**Sintoma:** Ao copiar dados **para** a Azure Data Lake Store, se a sua atividade de cópia falhar com o seguinte erro:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Causa da raiz:** Existem 2 possíveis razões:

1. `subscriptionId` O `resourceGroupName` serviço ligado à Loja de Lagos De Dados Azure está incorreto;
2. O utilizador ou o diretor de serviço não tem a permissão necessária.

**Resolução:**

1. Certifique-se `subscriptionId` `resourceGroupName` de que o `typeProperties` e especifica no serviço ligado são, de facto, aqueles a que a sua conta de data lake pertence.

2. Certifique-se de que concede pelo menos o papel **de Leitor** ao utilizador ou diretor de serviço na conta data lake. Eis como fazê-lo:

    1. Vá ao portal Azure -> sua conta data lake store
    2. Clique no controlo de **acesso (IAM)** na lâmina da Data Lake Store
    3. Clique em **Adicionar atribuição de funções**
    4. Definir **Função** como **Leitor**, e selecione o utilizador ou o principal de serviço que usa para copiar para conceder acesso

3. Se não quiser conceder o papel de **Leitor** ao utilizador ou ao diretor de serviço, alternativa é [especificar explicitamente um local](data-factory-data-movement-activities.md#global) de execução na atividade de cópia com a localização da sua Data Lake Store. Exemplo:

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
Para especificar um conjunto de dados para representar dados de entrada numa Data Lake Store, você definiu a propriedade **tipo** do conjunto de dados para **AzureDataLakeStore**. Detete a propriedade **linkedServiceName** do conjunto de dados para o nome do serviço ligado à Data Lake Store. Para obter uma lista completa das secções e propriedades da JSON disponíveis para definir conjuntos de dados, consulte o artigo Criação de conjuntos de [dados.](data-factory-create-datasets.md) As secções de um conjunto de dados na JSON, tais como **estrutura,** **disponibilidade,** e **política,** são semelhantes para todos os tipos de conjuntos de dados (base de dados Azure SQL, blob Azure e tabela Azure, por exemplo). A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações como localização e formato dos dados na loja de dados.

A secção **TypeProperties** para um conjunto de dados do tipo **AzureDataLakeStore** contém as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| **folderPath** |Caminho para o recipiente e pasta na Data Lake Store. |Sim |
| **fileName** |Nome do ficheiro na Loja do Lago De dados Azure. A propriedade **fileName** é opcional e sensível a casos. <br/><br/>Se especificar o nome do **ficheiro,** a atividade (incluindo a Cópia) funciona no ficheiro específico.<br/><br/>Quando o nome do **ficheiro** não é especificado, a Cópia inclui todos os ficheiros na **pastaPath** no conjunto de dados de entrada.<br/><br/>Quando o nome do **ficheiro** não é especificado para um conjunto de dados de saída e preservar `Data._Guid_.txt`a **hierarquia** não é especificado no sumidouro de atividade, o nome do ficheiro gerado está no formato . Por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |Não |
| **divididoBy** |A propriedade **dividida Por** é opcional. Pode usá-lo para especificar um caminho dinâmico e nome de ficheiro para dados da série de tempo. Por exemplo, **a pastaO caminho** pode ser parametrizado para cada hora de dados. Para mais detalhes e exemplos, consulte a propriedade partitionedBy. |Não |
| **formato** | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat**e **ParquetFormat**. Desloque a propriedade **tipo** em **formato** a um destes valores. Para mais informações, consulte as secções de [formato Texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato JSON,](data-factory-supported-file-and-compression-formats.md#json-format) [formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [formato ORC](data-factory-supported-file-and-compression-formats.md#orc-format)e [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) nos [formatos de Ficheiro e compressão suportados pelo artigo da Azure Data Factory.](data-factory-supported-file-and-compression-formats.md) <br><br> Se pretender copiar ficheiros "as-is" entre lojas baseadas em `format` ficheiros (cópia binária), ignore a secção nas definições de conjunto de dados de entrada e de saída. |Não |
| **compressão** | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são **GZip,** **Deflate,** **BZip2**e **ZipDeflate**. Os níveis suportados são **Ideais** e **Mais Rápidos.** Para mais informações, consulte [formatos de Ficheiro e compressão suportados pela Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

### <a name="the-partitionedby-property"></a>A propriedade divididaPor
Pode especificar propriedades dinâmicas de **pastasPath** e **fileName** para dados de séries de tempo com a propriedade **divididaBy,** funções data Factory e variáveis do sistema. Para mais detalhes, consulte o [artigo Azure Data Factory - funções e variáveis](data-factory-functions-variables.md) do sistema.


No exemplo seguinte, `{Slice}` é substituído pelo valor da `SliceStart` variável do`yyyyMMddHH`sistema Data Factory no formato especificado (). O `SliceStart` nome refere-se à hora de início da fatia. A `folderPath` propriedade é diferente para `wikidatagateway/wikisampledataout/2014100103` cada `wikidatagateway/wikisampledataout/2014100104`fatia, como em ou .

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

No exemplo seguinte, o ano, mês, `SliceStart` dia e hora são extraídos em `folderPath` `fileName` variáveis separadas que são usadas pelas propriedades e propriedades:
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
Para mais detalhes sobre conjuntos de dados, agendamento e fatias da série de tempo, consulte os dados da Fábrica de [Dados azure](data-factory-create-datasets.md) e artigos de [programação e execução da Fábrica](data-factory-scheduling-and-execution.md) de Dados.


## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo Criação de [pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e a política estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na secção **tipoPropriedades** de uma atividade variam com cada tipo de atividade. Para uma atividade de cópia, variam dependendo dos tipos de fontes e pias.

**AzureDataLakeStoreSource** suporta a seguinte propriedade na secção **TypeProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| **recursivo** |Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. |Verdadeiro (valor predefinido), Falso |Não |

**AzureDataLakeStoreSink** suporta as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| **copiarComportamento** |Especifica o comportamento da cópia. |<b>PreserveHierarchy</b>: Preserva a hierarquia dos ficheiros na pasta-alvo. O caminho relativo do ficheiro fonte para a pasta fonte é idêntico ao caminho relativo do ficheiro alvo para a pasta-alvo.<br/><br/><b>Hierarquia plana</b>: Todos os ficheiros da pasta fonte são criados no primeiro nível da pasta-alvo. Os ficheiros-alvo são criados com nomes autogerados.<br/><br/><b>MergeFiles</b>: Funda todos os ficheiros da pasta de origem para um ficheiro. Se o nome do ficheiro ou da bolha for especificado, o nome do ficheiro fundido é o nome especificado. Caso contrário, o nome do ficheiro é autogerado. |Não |

### <a name="recursive-and-copybehavior-examples"></a>exemplos recursivos e copyBehavior
Esta secção descreve o comportamento resultante da operação Copy para diferentes combinações de valores recursivos e copyBehavior.

| recursivo | copiarComportamento | Comportamento resultante |
| --- | --- | --- |
| true |preservar Hierarquia |Para uma pasta de origem Pasta 1 com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5<br/><br/>a pasta-alvo Pasta 1 é criada com a mesma estrutura que a fonte<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5. |
| true |achatar a hierarquia |Para uma pasta de origem Pasta 1 com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5<br/><br/>a pasta-alvo 1 é criada com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| true |mergeFiles |Para uma pasta de origem Pasta 1 com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5<br/><br/>a pasta-alvo 1 é criada com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + Ficheiro 5 conteúdos são fundidos num só ficheiro com nome de ficheiro gerado automaticamente |
| false |preservar Hierarquia |Para uma pasta de origem Pasta 1 com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5<br/><br/>a pasta-alvo Pasta 1 é criada com a seguinte estrutura<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/><br/>Subpasta1 com File3, File4 e File5 não são captadas. |
| false |achatar a hierarquia |Para uma pasta de origem Pasta 1 com a seguinte estrutura:<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5<br/><br/>a pasta-alvo Pasta 1 é criada com a seguinte estrutura<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/><br/>Subpasta1 com File3, File4 e File5 não são captadas. |
| false |mergeFiles |Para uma pasta de origem Pasta 1 com a seguinte estrutura:<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5<br/><br/>a pasta-alvo Pasta 1 é criada com a seguinte estrutura<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos File1 + File2 são fundidos num ficheiro com nome de ficheiro gerado automaticamente. nome gerado automaticamente para File1<br/><br/>Subpasta1 com File3, File4 e File5 não são captadas. |

## <a name="supported-file-and-compression-formats"></a>Formatos de ficheiro e de compressão suportados
Para mais detalhes, consulte os [formatos de Arquivo e compressão no artigo da Azure Data Factory.](data-factory-supported-file-and-compression-formats.md)

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>Exemplos jSON para copiar dados de e para data lake store
Os seguintes exemplos fornecem definições JSON de amostra. Pode utilizar estas definições de amostra para criar um pipeline utilizando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [o Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Os exemplos mostram como copiar dados de e para data Lake Store e armazenamento Azure Blob. No entanto, os dados podem ser copiados _diretamente_ de qualquer uma das fontes para qualquer uma das pias suportadas. Para mais informações, consulte a secção "Lojas e formatos de dados suportados" nos dados do Movimento utilizando o artigo [Copy Activity.](data-factory-data-movement-activities.md)

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Exemplo: Copiar dados do Armazenamento de Blob Azure para a Loja de Lagos De Dados Azure
O código de exemplo nesta secção mostra:

* Um serviço ligado do tipo [AzureStorage.](data-factory-azure-blob-connector.md#linked-service-properties)
* Um serviço ligado do tipo [AzureDataLakeStore.](#linked-service-properties)
* Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureDataLakeStore](#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza [blobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [AzureDataLakeStoreSink](#copy-activity-properties).

Os exemplos mostram como os dados da série de tempo do Armazenamento De Blob Azure são copiados para data lake store a cada hora.

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

**Serviço ligado à Azure Data Lake Store**

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
> Para mais detalhes de configuração, consulte a secção de propriedades de [serviço Linked.](#linked-service-properties)
>

**Conjunto de dados de entrada do blobs do Azure**

No exemplo seguinte, os dados são recolhidos a`"frequency": "Hour", "interval": 1`partir de uma nova bolha a cada hora ( ). O caminho da pasta e o nome do ficheiro para a bolha são avaliados dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza o ano, mês e parte do dia da hora de início. O nome do ficheiro usa a parte de hora da hora de início. A `"external": true` definição informa o serviço data Factory de que a tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

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

**Conjunto de dados de saída da Azure Data Lake Store**

O exemplo seguinte copia dados para data lake store. Novos dados são copiados para data lake store a cada hora.

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

**Copiar atividade em um oleoduto com uma fonte blob e um lavatório data lake store**

No exemplo seguinte, o pipeline contém uma atividade de cópia configurada para utilizar os conjuntos de dados de entrada e saída. A atividade de cópia está programada para funcionar a cada hora. Na definição JSON `source` do gasoduto, `BlobSource`o `sink` tipo está `AzureDataLakeStoreSink`definido para .

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

* Um serviço ligado do tipo [AzureDataLakeStore.](#linked-service-properties)
* Um serviço ligado do tipo [AzureStorage.](data-factory-azure-blob-connector.md#linked-service-properties)
* Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureDataLakeStore](#dataset-properties).
* Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza [O AzureDataLakeStoreSource](#copy-activity-properties) e [o BlobSink.](data-factory-azure-blob-connector.md#copy-activity-properties)

O código copia dados da série de tempo da Data Lake Store para uma bolha Azure a cada hora.

**Serviço ligado à Azure Data Lake Store**

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
> Para mais detalhes de configuração, consulte a secção de propriedades de [serviço Linked.](#linked-service-properties)
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

Neste exemplo, `"external"` a `true` definição para informar o serviço data Factory de que a tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

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
**Conjunto de dados de saída de blob azure**

No exemplo seguinte, os dados são escritos para`"frequency": "Hour", "interval": 1`uma nova bolha a cada hora ( ). O caminho da pasta para a bolha é avaliado dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza o ano, mês, dia e horas da hora de início.

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

**Uma atividade de cópia em um oleoduto com uma fonte azure Data Lake Store e um lavatório blob**

No exemplo seguinte, o pipeline contém uma atividade de cópia configurada para utilizar os conjuntos de dados de entrada e saída. A atividade de cópia está programada para funcionar a cada hora. Na definição JSON `source` do gasoduto, `AzureDataLakeStoreSource`o `sink` tipo está `BlobSink`definido para .

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

Na definição de atividade de cópia, também pode mapear colunas do conjunto de dados de origem para colunas no conjunto de dados do lavatório. Para mais detalhes, consulte [mapeiar colunas de conjuntos](data-factory-map-columns.md)de dados na Azure Data Factory .

## <a name="performance-and-tuning"></a>Desempenho e otimização
Para conhecer os fatores que afetam o desempenho da Atividade de Cópia e como otimizá-lo, consulte o artigo de [performance e afinação](data-factory-copy-activity-performance.md) da Atividade de Cópia.
