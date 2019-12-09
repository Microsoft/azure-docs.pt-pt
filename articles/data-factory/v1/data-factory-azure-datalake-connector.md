---
title: Copiar dados de e para Azure Data Lake Storage Gen1
description: Saiba como copiar dados de e para Data Lake Store usando Azure Data Factory
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
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930103"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Copiar dados de e para Data Lake Storage Gen1 usando Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-azure-datalake-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-data-lake-store.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [Azure data Lake Storage Gen1 Connector na v2](../connector-azure-data-lake-store.md).

Este artigo explica como usar a atividade de cópia no Azure Data Factory para mover dados de e para o Azure Data Lake Storage Gen1 (anteriormente conhecido como Azure Data Lake Store). Ele se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , uma visão geral da movimentação de dados com a atividade de cópia.

## <a name="supported-scenarios"></a>Cenários suportados
Você pode copiar dados **de Azure data Lake Store** para os seguintes armazenamentos de dados:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Você pode copiar dados dos seguintes repositórios de dados **para Azure data Lake Store**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Crie uma conta de Data Lake Store antes de criar um pipeline com a atividade de cópia. Para obter mais informações, consulte Introdução [ao Azure data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Tipos de autenticação com suporte
O conector de Data Lake Store dá suporte a esses tipos de autenticação:
* Autenticação do principal de serviço
* Autenticação de credencial de usuário (OAuth)

Recomendamos que você use a autenticação de entidade de serviço, especialmente para uma cópia de dados agendada. O comportamento de expiração do token pode ocorrer com a autenticação de credenciais do usuário. Para obter detalhes de configuração, consulte a seção [Propriedades do serviço vinculado](#linked-service-properties) .

## <a name="get-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que move dados de/para um Azure Data Lake Store usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline para copiar dados é usar o **Assistente de cópia**. Para obter um tutorial sobre como criar um pipeline usando o assistente de cópia, consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md).

Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net**e **API REST**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie um **Data Factory**. Um data factory pode conter um ou mais pipelines.
2. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory. Por exemplo, se você estiver copiando dados de um armazenamento de BLOBs do Azure para um Azure Data Lake Store, crie dois serviços vinculados para vincular sua conta de armazenamento do Azure e Azure Data Lake armazenamento ao seu data factory. Para propriedades do serviço vinculado que são específicas para Azure Data Lake Store, consulte a seção [Propriedades do serviço vinculado](#linked-service-properties) .
2. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia. No exemplo mencionado na última etapa, você cria um conjunto de dados para especificar o contêiner de BLOBs e a pasta que contém o dado de entrada. Além disso, você cria outro conjunto de dados para especificar a pasta e o caminho do arquivo no repositório de Data Lake que contém os dados copiados do armazenamento de BLOBs. Para propriedades do conjunto de informações que são específicas para Azure Data Lake Store, consulte a seção [Propriedades do conjunto](#dataset-properties) de informações.
3. Crie um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída. No exemplo mencionado anteriormente, você usa o Blobname como uma origem e AzureDataLakeStoreSink como um coletor para a atividade de cópia. Da mesma forma, se você estiver copiando de Azure Data Lake Store para o armazenamento de BLOBs do Azure, use AzureDataLakeStoreSource e BlobSink na atividade de cópia. Para propriedades da atividade de cópia que são específicas para Azure Data Lake Store, consulte a seção [Propriedades da atividade de cópia](#copy-activity-properties) . Para obter detalhes sobre como usar um armazenamento de dados como uma origem ou um coletor, clique no link na seção anterior para seu armazenamento de dados.

Quando você usa o assistente, as definições de JSON para essas entidades de Data Factory (serviços vinculados, conjuntos de valores e o Pipeline) são criadas automaticamente para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades de Data Factory usando o formato JSON. Para obter exemplos com definições de JSON para Data Factory entidades usadas para copiar dados de/para um Azure Data Lake Store, consulte a seção [exemplos de JSON](#json-examples-for-copying-data-to-and-from-data-lake-store) deste artigo.

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas para Data Lake Store.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
Um serviço vinculado vincula um armazenamento de dados a um data factory. Você cria um serviço vinculado do tipo **AzureDataLakeStore** para vincular seus dados de data Lake Store ao seu data Factory. A tabela a seguir descreve os elementos JSON específicos para Data Lake Store serviços vinculados. Você pode escolher entre a entidade de serviço e a autenticação de credenciais de usuário.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| **tipo** | A propriedade de tipo deve ser definida como **AzureDataLakeStore**. | Sim |
| **dataLakeStoreUri** | Informações sobre a conta do Azure Data Lake Store. Estas informações recebe um dos seguintes formatos: `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/`. | Sim |
| **subscriptionId** | ID de subscrição do Azure à qual pertence a conta do Data Lake Store. | Necessário para o sink |
| **resourceGroupName** | Nome do grupo de recursos do Azure à qual pertence a conta do Data Lake Store. | Necessário para o sink |

### <a name="service-principal-authentication-recommended"></a>Autenticação de entidade de serviço (recomendada)
Para usar a autenticação de entidade de serviço, registre uma entidade de aplicativo no Azure Active Directory (AD do Azure) e conceda a ela acesso ao Data Lake Store. Para obter passos detalhados, consulte [autenticação serviço a serviço](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Tome nota dos seguintes valores, o que utilizar para definir o serviço ligado:
* ID da aplicação
* Chave da aplicação
* ID do inquilino

> [!IMPORTANT]
> Certifique-se de que conceder a service principal permissão adequada no Azure Data Lake Store:
>- **Para usar data Lake Store como fonte**, conceda pelo menos a permissão de acesso a dados de **leitura + execução** para listar e copiar o conteúdo de uma pasta ou permissão de **leitura** para copiar um único arquivo. Nenhum requisito no controle de acesso no nível da conta.
>- **Para usar data Lake Store como coletor**, conceda pelo menos a permissão de acesso a dados **Write + execute** para criar itens filho na pasta. E se você usar Azure IR para capacitar a cópia (tanto a origem quanto o coletor estão na nuvem), para permitir que Data Factory detecte a região de Data Lake Store, conceda pelo menos a função **leitor** no controle de acesso da conta (iam). Se você quiser evitar essa função IAM, [especifique executionLocation](data-factory-data-movement-activities.md#global) com o local do seu data Lake Store na atividade de cópia.
>- Se você **usar o assistente de cópia para criar pipelines**, conceda pelo menos a função **leitor** no controle de acesso à conta (iam). Além disso, conceda pelo menos permissão de **leitura + execução** ao seu data Lake Store raiz ("/") e seus filhos. Caso contrário, você poderá ver a mensagem "as credenciais fornecidas são inválidas".

Use a autenticação de entidade de serviço especificando as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| **servicePrincipalId** | Especifique o ID de cliente. da aplicação | Sim |
| **servicePrincipalKey** | Especifique a chave da aplicação. | Sim |
| **tenant** | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Pode recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. | Sim |

**Exemplo: autenticação de entidade de serviço**
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

### <a name="user-credential-authentication"></a>Autenticação de credenciais de usuário
Como alternativa, você pode usar a autenticação de credenciais de usuário para copiar de ou para Data Lake Store especificando as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| **nesse** | Clique no botão **autorizar** no Editor de data Factory e insira sua credencial que atribui a URL de autorização gerada automaticamente a essa propriedade. | Sim |
| **sessionId** | ID da sessão OAuth da sessão de autorização do OAuth. Cada ID de sessão é exclusiva e pode ser usada apenas uma vez. Essa configuração é gerada automaticamente quando você usa o editor de Data Factory. | Sim |

> [!IMPORTANT]
> Certifique-se de conceder a permissão adequada ao usuário no Azure Data Lake Store:
>- **Para usar data Lake Store como fonte**, conceda pelo menos a permissão de acesso a dados de **leitura + execução** para listar e copiar o conteúdo de uma pasta ou permissão de **leitura** para copiar um único arquivo. Nenhum requisito no controle de acesso no nível da conta.
>- **Para usar data Lake Store como coletor**, conceda pelo menos a permissão de acesso a dados **Write + execute** para criar itens filho na pasta. E se você usar Azure IR para capacitar a cópia (tanto a origem quanto o coletor estão na nuvem), para permitir que Data Factory detecte a região de Data Lake Store, conceda pelo menos a função **leitor** no controle de acesso da conta (iam). Se você quiser evitar essa função IAM, [especifique executionLocation](data-factory-data-movement-activities.md#global) com o local do seu data Lake Store na atividade de cópia.
>- Se você **usar o assistente de cópia para criar pipelines**, conceda pelo menos a função **leitor** no controle de acesso à conta (iam). Além disso, conceda pelo menos permissão de **leitura + execução** ao seu data Lake Store raiz ("/") e seus filhos. Caso contrário, você poderá ver a mensagem "as credenciais fornecidas são inválidas".

**Exemplo: autenticação de credenciais de usuário**
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
O código de autorização que você gera usando o botão **autorizar** expira após um determinado período de tempo. A seguinte mensagem significa que o token de autenticação expirou:

Erro de operação de credencial: invalid_grant-AADSTS70002: erro ao validar as credenciais. AADSTS70008: a concessão de acesso fornecida expirou ou foi revogada. ID de rastreamento: d18629e8-af88-43c5-88e3-d8419eb1fca1 ID de correlação: fac30a0c-6be6-4e02-8D69-a776d2ffefd7 timestamp: 2015-12-15 21-09-31Z.

A tabela a seguir mostra os tempos de expiração de diferentes tipos de contas de usuário:

| Tipo de utilizador | Expira após |
|:--- |:--- |
| Contas de usuário *não* gerenciadas pelo Azure Active Directory (por exemplo, @hotmail.com ou @live.com) |12 horas |
| Contas de usuários gerenciadas pelo Azure Active Directory |14 dias após a última execução de fatia <br/><br/>90 dias, se uma fatia com base em um serviço vinculado baseado em OAuth for executada pelo menos uma vez a cada 14 dias |

Se você alterar sua senha antes do tempo de expiração do token, o token expirará imediatamente. Você verá a mensagem mencionada anteriormente nesta seção.

Você pode reautorizar a conta usando o botão **autorizar** quando o token expirar para reimplantar o serviço vinculado. Você também pode gerar valores para as propriedades **SessionID** e **Authorization** programaticamente usando o seguinte código:


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
Para obter detalhes sobre as classes de Data Factory usadas no código, consulte os tópicos classe [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [classe AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)e [classe AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) . Adicione uma referência à versão `2.9.10826.1824` do `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` para a classe `WindowsFormsWebAuthenticationDialog` usada no código.

## <a name="troubleshooting-tips"></a>Sugestões para a resolução de problemas

**Sintoma:** Ao copiar dados **para** o Azure data Lake Store, se a atividade de cópia falhar com o seguinte erro:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Causa raiz:** Há dois motivos possíveis:

1. O `resourceGroupName` e/ou `subscriptionId` especificado em Azure Data Lake Store serviço vinculado está incorreto;
2. O usuário ou a entidade de serviço não tem a permissão necessária.

**Resolução:**

1. Verifique se as `subscriptionId` e `resourceGroupName` especificadas no serviço vinculado `typeProperties` são, de fato, aquelas às quais sua conta do data Lake pertence.

2. Certifique-se de conceder pelo menos a função **leitor** ao usuário ou à entidade de serviço na conta do data Lake. Veja como fazer isso:

    1. Acesse o portal do Azure-> sua conta de Data Lake Store
    2. Clique em **controle de acesso (iam)** na folha do data Lake Store
    3. Clique em **Adicionar atribuição de função**
    4. Defina **função** como **leitor**e selecione o usuário ou a entidade de serviço que você usa para copiar para conceder acesso

3. Se você não quiser conceder a função de **leitor** para o usuário ou entidade de serviço, a alternativa é [especificar explicitamente um local de execução](data-factory-data-movement-activities.md#global) na atividade de cópia com o local do seu data Lake Store. Exemplo:

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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para especificar um DataSet para representar dados de entrada em um Data Lake Store, você define a propriedade **Type** do conjunto como **AzureDataLakeStore**. Defina a propriedade **linkedServiceName** do conjunto de um como o nome do data Lake Store serviço vinculado. Para obter uma lista completa de seções e propriedades JSON disponíveis para definir conjuntos de os, consulte o artigo [criando conjuntos](data-factory-create-datasets.md) de os. As seções de um conjunto de dados em JSON, como a **estrutura**, a **disponibilidade**e a **política**, são semelhantes para todos os tipos de conjunto de dados (Azure SQL Database, BLOB do Azure e tabela do Azure, por exemplo). A seção **typeproperties** é diferente para cada tipo de conjunto de dados e fornece informações como o local e o formato dos dados no armazenamento de dados.

A seção **typeproperties** para um DataSet do tipo **AzureDataLakeStore** contém as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| **folderPath** |Caminho para o contêiner e a pasta em Data Lake Store. |Sim |
| **fileName** |Nome do arquivo em Azure Data Lake Store. A propriedade **filename** é opcional e diferencia maiúsculas de minúsculas. <br/><br/>Se você especificar **filename**, a atividade (incluindo Copy) funcionará no arquivo específico.<br/><br/>Quando **filename** não for especificado, Copy inclui todos os arquivos em **FolderPath** no conjunto de dados de entrada.<br/><br/>Quando **filename** não for especificado para um conjunto de resultados de saída e **preserveHierarchy** não for especificado no coletor de atividade, o nome do arquivo gerado estará no formato `Data._Guid_.txt`. Por exemplo: Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt. |Não |
| **partitionedBy** |A propriedade **partitionedBy** é opcional. Você pode usá-lo para especificar um caminho dinâmico e um nome de arquivo para dados de série temporal. Por exemplo, **FolderPath** pode ser parametrizado para cada hora dos dados. Para obter detalhes e exemplos, consulte a propriedade partitionedBy. |Não |
| **format** | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**e **ParquetFormat**. Definir o **tipo** propriedade sob **formato** para um dos seguintes valores. Para obter mais informações, consulte as seções [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), formato [Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e [formato parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) nos [formatos de arquivo e compactação com suporte](data-factory-supported-file-and-compression-formats.md) no artigo Azure data Factory. <br><br> Se você quiser copiar arquivos "no estado em que se encontram" entre repositórios baseados em arquivo (cópia binária), ignore a seção `format` nas definições de conjunto de dados de entrada e saída. |Não |
| **çã** | Especifica o tipo e o nível de compressão dos dados. Tipos suportados são **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Os níveis de suporte são **Optimal** e **Fastest**. Para obter mais informações, consulte [formatos de arquivo e compactação com suporte pelo Azure data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

### <a name="the-partitionedby-property"></a>A propriedade partitionedBy
Você pode especificar propriedades **FolderPath** e **filename** dinâmicas para dados de série temporal com a propriedade **partitionedBy** , funções de data Factory e variáveis de sistema. Para obter detalhes, consulte o artigo [funções de Azure data Factory e variáveis do sistema](data-factory-functions-variables.md) .


No exemplo a seguir, `{Slice}` é substituído pelo valor da variável de sistema Data Factory `SliceStart` no formato especificado (`yyyyMMddHH`). O nome `SliceStart` se refere à hora de início da fatia. A propriedade `folderPath` é diferente para cada fatia, como em `wikidatagateway/wikisampledataout/2014100103` ou `wikidatagateway/wikisampledataout/2014100104`.

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

No exemplo a seguir, o ano, o mês, o dia e a hora de `SliceStart` são extraídos em variáveis separadas que são usadas pelas propriedades `folderPath` e `fileName`:
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
Para obter mais detalhes sobre conjuntos de data de série temporal, agendamento e fatias, consulte os conjuntos de os [DataSets nos artigos Azure data Factory](data-factory-create-datasets.md) e [Data Factory agendamento e execução](data-factory-scheduling-and-execution.md) .


## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . Propriedades como nome, descrição, tabelas de entrada e saída e política estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção **typeproperties** de uma atividade variam de acordo com cada tipo de atividade. Para uma atividade de cópia, elas variam de acordo com os tipos de fontes e coletores.

**AzureDataLakeStoreSource** dá suporte à seguinte propriedade na seção **typeproperties** :

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| **recursive** |Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. |True (valor padrão), false |Não |

**AzureDataLakeStoreSink** dá suporte às seguintes propriedades na seção **typeproperties** :

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| **copyBehavior** |Especifica o comportamento de cópia. |<b>PreserveHierarchy</b>: preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico para o caminho relativo do ficheiro de destino para a pasta de destino.<br/><br/><b>FlattenHierarchy</b>: todos os arquivos da pasta de origem são criados no primeiro nível da pasta de destino. Os arquivos de destino são criados com nomes gerados automaticamente.<br/><br/><b>MergeFiles</b>: mescla todos os arquivos da pasta de origem em um arquivo. Se o nome de ficheiro ou blob for especificado, o nome de ficheiro intercalada é o nome especificado. Caso contrário, o nome do arquivo será gerado automaticamente. |Não |

### <a name="recursive-and-copybehavior-examples"></a>Exemplos de recursiva e copyBehavior
Esta secção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursiva e copyBehavior.

| recursive | copyBehavior | Comportamento resultante |
| --- | --- | --- |
| true |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a mesma estrutura da origem<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>o Pasta1 de destino é criado com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| true |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>o Pasta1 de destino é criado com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + 5 ficheiro de conteúdo é mesclado num ficheiro com o nome de ficheiro gerado automaticamente |
| false |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Subfolder1 com File3, File4 e File5 não são aplicadas. |
| false |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/><br/>Subfolder1 com File3, File4 e File5 não são aplicadas. |
| false |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 conteúdo é mesclado num arquivo com o nome de ficheiro gerado automaticamente. Nome gerado automaticamente para File1<br/><br/>Subfolder1 com File3, File4 e File5 não são aplicadas. |

## <a name="supported-file-and-compression-formats"></a>Formatos de arquivo e compactação com suporte
Para obter detalhes, consulte os [formatos de arquivo e compactação no Azure data Factory](data-factory-supported-file-and-compression-formats.md) artigo.

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>Exemplos de JSON para copiar dados de e para Data Lake Store
Os exemplos a seguir fornecem definições de exemplo de JSON. Você pode usar essas definições de exemplo para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Os exemplos mostram como copiar dados de e para Data Lake Store e o armazenamento de BLOBs do Azure. No entanto, os dados podem ser copiados _diretamente_ de qualquer uma das fontes para qualquer um dos coletores com suporte. Para obter mais informações, consulte a seção "armazenamentos e formatos de dados com suporte" no artigo [mover dados usando a atividade de cópia](data-factory-data-movement-activities.md) .

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Exemplo: copiar dados do armazenamento de BLOBs do Azure para Azure Data Lake Store
O código de exemplo nesta seção mostra:

* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um serviço vinculado do tipo [AzureDataLakeStore](#linked-service-properties).
* Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureDataLakeStore](#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que usa [blobname](data-factory-azure-blob-connector.md#copy-activity-properties) e [AzureDataLakeStoreSink](#copy-activity-properties).

Os exemplos mostram como os dados de série temporal do armazenamento de BLOBs do Azure são copiados para Data Lake Store a cada hora.

**Serviço ligado do Armazenamento do Azure**

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

**Azure Data Lake Store serviço vinculado**

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
> Para obter detalhes de configuração, consulte a seção [Propriedades do serviço vinculado](#linked-service-properties) .
>

**Conjunto de dados de entrada do blobs do Azure**

No exemplo a seguir, os dados são coletados de um novo BLOB a cada hora (`"frequency": "Hour", "interval": 1`). O caminho da pasta e o nome do arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa a parte de ano, mês e dia da hora de início. O nome do arquivo usa a parte de hora da hora de início. A configuração de `"external": true` informa ao serviço de Data Factory que a tabela é externa à data factory e não é produzida por uma atividade no data factory.

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

**Conjunto de Azure Data Lake Store de saída**

O exemplo a seguir copia dados para Data Lake Store. Novos dados são copiados para Data Lake Store a cada hora.

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

**Atividade de cópia em um pipeline com uma origem de BLOB e um coletor de Data Lake Store**

No exemplo a seguir, o pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída. A atividade de cópia está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de `source` é definido como `BlobSource`e o tipo de `sink` é definido como `AzureDataLakeStoreSink`.

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

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Exemplo: copiar dados de Azure Data Lake Store para um blob do Azure
O código de exemplo nesta seção mostra:

* Um serviço vinculado do tipo [AzureDataLakeStore](#linked-service-properties).
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [AzureDataLakeStore](#dataset-properties).
* Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que usa [AzureDataLakeStoreSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O código copia dados de série temporal de Data Lake Store para um blob do Azure a cada hora.

**Azure Data Lake Store serviço vinculado**

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
> Para obter detalhes de configuração, consulte a seção [Propriedades do serviço vinculado](#linked-service-properties) .
>

**Serviço ligado do Armazenamento do Azure**

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
**Azure Data Lake conjunto de dados de entrada**

Neste exemplo, definir `"external"` como `true` informa ao serviço de Data Factory que a tabela é externa à data factory e não é produzida por uma atividade na data factory.

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
**Conjunto de dados de saída do blob do Azure**

No exemplo a seguir, os dados são gravados em um novo BLOB a cada hora (`"frequency": "Hour", "interval": 1`). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa a parte de ano, mês, dia e horas da hora de início.

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

**Uma atividade de cópia em um pipeline com uma fonte de Azure Data Lake Store e um coletor de BLOB**

No exemplo a seguir, o pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída. A atividade de cópia está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de `source` é definido como `AzureDataLakeStoreSource`e o tipo de `sink` é definido como `BlobSink`.

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

Na definição da atividade de cópia, você também pode mapear colunas do conjunto de fonte de origem para colunas no conjunto de coleta. Para obter detalhes, consulte [mapeando colunas do conjunto de informações em Azure data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e otimização
Para saber mais sobre os fatores que afetam o desempenho da atividade de cópia e como otimizá-la, consulte o artigo [Guia de desempenho e ajuste da atividade de cópia](data-factory-copy-activity-performance.md) .
