---
title: Copiar dados para e de geração 1 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Saiba como copiar dados de e para o Data Lake Store com o Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3bb372c4c3ddb79429df20c24c691c847e927e2a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57975615"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Copiar dados para e de geração 1 de armazenamento do Data Lake com o Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-azure-datalake-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-data-lake-store.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conector de geração 1 de armazenamento do Azure Data Lake no V2](../connector-azure-data-lake-store.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados de e para o Azure Data Lake Storage Gen1 (anteriormente conhecido como o Azure Data Lake Store). Ele se baseia no [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo uma visão geral do movimento de dados com atividade de cópia.

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **do Azure Data Lake Store** para os seguintes dados armazena:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados de arquivos de dados seguintes **para o Azure Data Lake Store**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Crie uma conta do Data Lake Store antes de criar um pipeline com atividade de cópia. Para obter mais informações, consulte [introdução ao Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Tipos de autenticação suportados
O conector do Data Lake Store suporta estes tipos de autenticação:
* Autenticação do principal de serviço
* Autenticação de credencial (OAuth) do utilizador

Recomendamos que utilize autenticação do principal de serviço, especialmente para uma cópia de dados agendada. Comportamento de expiração do token pode ocorrer a autenticação de credenciais de utilizador. Para obter detalhes de configuração, consulte a [propriedades do serviço ligado](#linked-service-properties) secção.

## <a name="get-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move os dados de/para um Store do Azure Data Lake ao utilizar ferramentas/APIs diferentes.

A maneira mais fácil para criar um pipeline para copiar dados está a utilizar o **Assistente para copiar**. Para obter um tutorial sobre como criar um pipeline com o Assistente de cópia, veja [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md).

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**e  **REST API**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se usar as ferramentas ou APIs, que execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink:

1. Criar uma **fábrica de dados**. Uma fábrica de dados pode conter um ou mais pipelines.
2. Crie **serviços ligados** para ligar a dados de entrada e saídos armazena à fábrica de dados. Por exemplo, se estiver a copiar dados de um armazenamento de Blobs do Azure para um Store do Azure Data Lake, criar dois serviços ligados para ligar a sua conta de armazenamento do Azure e o Azure Data Lake store à sua fábrica de dados. Para as propriedades do serviço ligado que são específicas para o Azure Data Lake Store, consulte [propriedades do serviço ligado](#linked-service-properties) secção.
2. Crie **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. O exemplo mencionado no último passo, vai criar um conjunto de dados para especificar o contentor de BLOBs e a pasta que contém os dados de entrada. Além disso, crie outro conjunto de dados para especificar o caminho do ficheiro e pasta no Data Lake store que contém os dados copiados do armazenamento de Blobs. Para as propriedades do conjunto de dados que são específicas para o Azure Data Lake Store, consulte [propriedades do conjunto de dados](#dataset-properties) secção.
3. Criar uma **pipeline** com uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, vai utilizar BlobSource como uma origem e um AzureDataLakeStoreSink como sink para a atividade de cópia. Da mesma forma, se estiver a copiar do Azure Data Lake Store para armazenamento de Blobs do Azure, utilize AzureDataLakeStoreSource e BlobSink a atividade de cópia. Para propriedades de atividade de cópia que são específicas para o Azure Data Lake Store, consulte [propriedades da atividade copy](#copy-activity-properties) secção. Para obter detalhes sobre como usar um arquivo de dados como uma origem ou sink, clique na ligação na secção anterior para seu armazenamento de dados.

Quando utiliza o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) são criadas automaticamente para. Ao utilizar ferramentas/APIs (exceto a .NET API), define essas entidades do Data Factory com o formato JSON. Para exemplos com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de/para um Store do Azure Data Lake, veja [exemplos JSON](#json-examples-for-copying-data-to-and-from-data-lake-store) seção deste artigo.

As secções seguintes fornecem detalhes sobre as propriedades JSON utilizadas para definir entidades do Data Factory específicas para o Data Lake Store.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
Os serviços ligados ligam um arquivo de dados para uma fábrica de dados. Vai criar um serviço ligado do tipo **AzureDataLakeStore** para ligar os seus dados de Data Lake Store à sua fábrica de dados. A tabela seguinte descreve os elementos JSON específicos aos serviços do Data Lake Store ligado. Pode escolher entre o principal de serviço e a autenticação de credenciais de utilizador.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| **tipo** | A propriedade de tipo deve ser definida como **AzureDataLakeStore**. | Sim |
| **dataLakeStoreUri** | Informações sobre a conta do Azure Data Lake Store. Estas informações recebe um dos seguintes formatos: `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/`. | Sim |
| **subscriptionId** | ID de subscrição do Azure à qual pertence a conta do Data Lake Store. | Necessário para o sink |
| **resourceGroupName** | Nome do grupo de recursos do Azure à qual pertence a conta do Data Lake Store. | Necessário para o sink |

### <a name="service-principal-authentication-recommended"></a>Autenticação do principal de serviço (recomendada)
Para utilizar autenticação do principal de serviço, registe-se uma entidade de aplicação no Azure Active Directory (Azure AD) e conceder o acesso ao Data Lake Store. Para obter passos detalhados, consulte [autenticação serviço a serviço](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Tome nota dos seguintes valores, o que utilizar para definir o serviço ligado:
* ID da aplicação
* Chave da aplicação
* ID do inquilino

> [!IMPORTANT]
> Certifique-se de que conceder a service principal permissão adequada no Azure Data Lake Store:
>- **Para utilizar o Data Lake Store como origem**, conceder, pelo menos, **leitura + execução** permissão para listar e copie o conteúdo de uma pasta, de acesso a dados ou **leitura** permissão para copiar um ficheiro individual. Nenhum requisito no controle de acesso de nível de conta.
>- **Para utilizar o Data Lake Store como sink**, pelo menos a conceder **escrita + execução** permissão para criar itens subordinados numa pasta de acesso a dados. E se usar o runtime de integração do Azure para aumentar a produtividade de cópia (origem e sink são na cloud), para permitir que o Data Factory detetar a região do Data Lake Store, conceder, pelo menos, **leitor** função no controle de conta de acesso (IAM). Se quiser evitar esta função IAM [especifique executionLocation](data-factory-data-movement-activities.md#global) com a localização do seu Store de Lake de dados na atividade de cópia.
>- Se **utilizar o Assistente para copiar para criar pipelines**, pelo menos a conceder **leitor** função no controle de conta de acesso (IAM). Além disso, pelo menos a conceder **leitura + execução** permissão para a raiz do Data Lake Store ("/") e seus filhos. Caso contrário, poderá ver a mensagem "as credenciais fornecidas são inválidas."

Utilize autenticação do principal de serviço ao especificar as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| **servicePrincipalId** | Especifique o ID de cliente. da aplicação | Sim |
| **servicePrincipalKey** | Especifique a chave da aplicação. | Sim |
| **tenant** | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Pode recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. | Sim |

**Exemplo: Autenticação do principal de serviço**
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

### <a name="user-credential-authentication"></a>Autenticação de credenciais de utilizador
Em alternativa, pode utilizar a autenticação de credenciais de utilizador para copiar do ou para o Data Lake Store, especificando as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| **authorization** | Clique nas **autorizar** no Editor do Data Factory e digitar suas credenciais que atribui o URL de autorização gerado automaticamente a esta propriedade. | Sim |
| **sessionId** | ID de sessão OAuth a partir da sessão de autorização de OAuth. Cada ID de sessão é exclusivo e pode ser usada apenas uma vez. Esta definição é gerada automaticamente ao utilizar o Editor do Data Factory. | Sim |

> [!IMPORTANT]
> Certifique-se de que conceder a permissão adequada do utilizador no Azure Data Lake Store:
>- **Para utilizar o Data Lake Store como origem**, conceder, pelo menos, **leitura + execução** permissão para listar e copie o conteúdo de uma pasta, de acesso a dados ou **leitura** permissão para copiar um ficheiro individual. Nenhum requisito no controle de acesso de nível de conta.
>- **Para utilizar o Data Lake Store como sink**, pelo menos a conceder **escrita + execução** permissão para criar itens subordinados numa pasta de acesso a dados. E se usar o runtime de integração do Azure para aumentar a produtividade de cópia (origem e sink são na cloud), para permitir que o Data Factory detetar a região do Data Lake Store, conceder, pelo menos, **leitor** função no controle de conta de acesso (IAM). Se quiser evitar esta função IAM [especifique executionLocation](data-factory-data-movement-activities.md#global) com a localização do seu Store de Lake de dados na atividade de cópia.
>- Se **utilizar o Assistente para copiar para criar pipelines**, pelo menos a conceder **leitor** função no controle de conta de acesso (IAM). Além disso, pelo menos a conceder **leitura + execução** permissão para a raiz do Data Lake Store ("/") e seus filhos. Caso contrário, poderá ver a mensagem "as credenciais fornecidas são inválidas."

**Exemplo: Autenticação de credenciais de utilizador**
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
O código de autorização que irá gerar utilizando o **autorizar** botão expira após um determinado período de tempo. A seguinte mensagem de erro significa que o token de autenticação expirou:

Erro de operação de credenciais: invalid_grant - Error":"invalid_grant","error_description":"aadsts70002: Credenciais de validação de erro. AADSTS70008: A concessão de acesso fornecido está expirada ou revogada. ID de rastreio: ID de correlação d18629e8-af88-43c5-88e3-d8419eb1fca1: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21-09-31Z.

A tabela seguinte mostra as horas de expiração de diferentes tipos de contas de utilizador:

| Tipo de utilizador | Expira após |
|:--- |:--- |
| Contas de utilizador *não* gerido pelo Azure Active Directory (por exemplo, @hotmail.com ou @live.com) |12 horas |
| Contas de utilizadores geridas pelo Azure Active Directory |Execute 14 dias após o último setor <br/><br/>90 dias, se um setor com base num serviço ligado com base em OAuth for executado, pelo menos, uma vez a cada 14 dias |

Se alterar a palavra-passe antes da hora de expiração do token, o token expira imediatamente. Verá a mensagem mencionada anteriormente nesta secção.

Pode autorizar a conta ao utilizar o **autorizar** botão quando o token expira para voltar a implementar o serviço ligado. Também pode gerar valores para o **sessionId** e **autorização** propriedades por meio de programação, utilizando o seguinte código:


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
Para obter detalhes sobre as classes de fábrica de dados usadas no código, consulte a [classe AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [classe AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), e [ Classe de AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) tópicos. Adicionar uma referência para a versão `2.9.10826.1824` dos `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` para o `WindowsFormsWebAuthenticationDialog` classe usada no código.

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

**Sintoma:** Quando se copiam dados **em** do Azure Data Lake Store, se a sua atividade de cópia falhar com o erro seguinte:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Causa de raiz:** Existem 2 motivos possíveis:

1. O `resourceGroupName` e/ou `subscriptionId` especificado no Azure Data Lake Store ligado serviço está incorreto;
2. O utilizador ou o principal de serviço não tem a permissão necessária.

**Resolução:**

1. Certifique-se o `subscriptionId` e `resourceGroupName` que especificar no serviço ligado `typeProperties` realmente são aqueles que sua conta do data lake pertence.

2. Certifique-se de que, pelo menos, conceder **leitor** função ao utilizador ou principal de serviço na conta do data lake. Eis como torná-lo:

    1. Aceda ao Portal do Azure -> a sua conta do Data Lake Store
    2. Clique em **controlo de acesso (IAM)** no painel do Store de Lake dados
    3. Clique em **adicionar atribuição de função**
    4. Definir **função** como **leitor**e selecione o utilizador ou o principal de serviço a utilizar para a cópia para conceder acesso

3. Se não pretender conceder **leitor** função ao utilizador ou principal de serviço, alternativo é [especificar explicitamente um local de execução](data-factory-data-movement-activities.md#global) na cópia activitywith a localização do seu Store de Lake de dados. Exemplo:

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
Para especificar um conjunto de dados para representar os dados de entrada num Store de Lake de dados, defina o **tipo** propriedade do conjunto de dados para **AzureDataLakeStore**. Definir o **linkedServiceName** serviço ligado de propriedade de conjunto de dados para o nome do Store de Lake dados. Para obter uma lista completa de secções JSON e propriedades disponíveis para definir conjuntos de dados, consulte a [criar conjuntos de dados](data-factory-create-datasets.md) artigo. Secções de um conjunto de dados em JSON, como **estrutura**, **disponibilidade**, e **política**, são semelhantes para todos os tipos de conjunto de dados (banco de dados SQL do Azure, BLOBs do Azure e tabelas do Azure, para exemplo). O **typeProperties** seção é diferente para cada tipo de conjunto de dados e fornece informações como a localização e o formato dos dados no arquivo de dados.

O **typeProperties** secção para um conjunto de dados do tipo **AzureDataLakeStore** contém as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| **folderPath** |Caminho para o contentor e a pasta no Data Lake Store. |Sim |
| **fileName** |Nome do ficheiro no Azure Data Lake Store. O **fileName** propriedade é opcional e diferencia maiúsculas de minúsculas. <br/><br/>Se especificar **fileName**, a atividade (incluindo cópia) funciona no ficheiro específico.<br/><br/>Quando **fileName** não for especificada, cópia inclui todos os ficheiros na **folderPath** do conjunto de dados de entrada.<br/><br/>Quando **fileName** não está especificado para um conjunto de dados de saída e **preserveHierarchy** não está especificado no sink de atividade, o nome do ficheiro gerado está no formato `Data._Guid_.txt`. Por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |Não |
| **partitionedBy** |O **partitionedBy** propriedade é opcional. Pode usá-lo para especificar um caminho dinâmico e o nome de ficheiro para dados de séries temporais. Por exemplo, **folderPath** podem ser parametrizados por cada hora de dados. Para obter detalhes e exemplos, consulte a propriedade partitionedBy. |Não |
| **format** | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, e **ParquetFormat**. Definir o **tipo** propriedade sob **formato** para um dos seguintes valores. Para obter mais informações, consulte a [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato ORC](data-factory-supported-file-and-compression-formats.md#orc-format), e [formato Parquet ](data-factory-supported-file-and-compression-formats.md#parquet-format) secções o [formatos de ficheiro e de compressão suportados pelo Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artigo. <br><br> Se pretender copiar ficheiros "como-é" entre arquivos baseados em ficheiros (binário cópia), ignore o `format` secção em ambas as definições do conjunto de dados de entrada e saída. |Não |
| **compression** | Especifica o tipo e o nível de compressão dos dados. Tipos suportados são **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Os níveis de suporte são **Optimal** e **Fastest**. Para obter mais informações, consulte [formatos de ficheiro e de compressão suportados pelo Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

### <a name="the-partitionedby-property"></a>A propriedade partitionedBy
Pode especificar dinâmica **folderPath** e **fileName** propriedades de dados de séries temporais com o **partitionedBy** propriedade, funções de Data Factory e variáveis do sistema. Para obter detalhes, consulte a [do Azure Data Factory - funções e variáveis de sistema](data-factory-functions-variables.md) artigo.


No exemplo a seguir `{Slice}` é substituído pelo valor da variável de sistema do Data Factory `SliceStart` no formato especificado (`yyyyMMddHH`). O nome `SliceStart` refere-se para a hora de início do setor. O `folderPath` propriedade é diferente para cada setor, como na `wikidatagateway/wikisampledataout/2014100103` ou `wikidatagateway/wikisampledataout/2014100104`.

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

No exemplo seguinte, o ano, mês, dia e hora do `SliceStart` são extraídos em variáveis separadas, que são utilizadas pela `folderPath` e `fileName` propriedades:
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
Para obter mais detalhes sobre os conjuntos de dados de séries temporais, agendamento e setores, consulte a [conjuntos de dados no Azure Data Factory](data-factory-create-datasets.md) e [fábrica de dados de agendamento e execução](data-factory-scheduling-and-execution.md) artigos.


## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Criar pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição, entrada e saída de tabelas e a política estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis no **typeProperties** secção de uma atividade varia com cada tipo de atividade. Para uma atividade de cópia, elas variam consoante os tipos de origens e sinks.

**AzureDataLakeStoreSource** suporta a seguinte propriedade no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| **recursive** |Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. |False a TRUE (valor predefinido), |Não |

**Um AzureDataLakeStoreSink** suporta as seguintes propriedades na **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| **copyBehavior** |Especifica o comportamento de cópia. |<b>PreserveHierarchy</b>: Preserva a hierarquia de ficheiros na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico para o caminho relativo do ficheiro de destino para a pasta de destino.<br/><br/><b>FlattenHierarchy</b>: Todos os ficheiros da pasta de origem são criados no primeiro nível de pasta de destino. Os ficheiros de destino são criados com nomes de geradas automaticamente.<br/><br/><b>MergeFiles</b>: Une todos os ficheiros da pasta de origem para um ficheiro. Se o nome de ficheiro ou blob for especificado, o nome de ficheiro intercalada é o nome especificado. Caso contrário, o nome de ficheiro é gerado automaticamente. |Não |

### <a name="recursive-and-copybehavior-examples"></a>Exemplos de recursiva e copyBehavior
Esta secção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursiva e copyBehavior.

| recursiva | copyBehavior | Comportamento resultante |
| --- | --- | --- |
| true |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a mesma estrutura de origem<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>O destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| true |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>O destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + 5 ficheiro de conteúdo é mesclado num ficheiro com o nome de ficheiro gerado automaticamente |
| false |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>A pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Subfolder1 com File3, File4 e File5 não são aplicadas. |
| false |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>A pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/><br/>Subfolder1 com File3, File4 e File5 não são aplicadas. |
| false |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>A pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 conteúdo é mesclado num arquivo com o nome de ficheiro gerado automaticamente. Nome gerado automaticamente para File1<br/><br/>Subfolder1 com File3, File4 e File5 não são aplicadas. |

## <a name="supported-file-and-compression-formats"></a>Formatos de ficheiro e de compressão suportados
Para obter detalhes, consulte a [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artigo.

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>Exemplos JSON para copiar dados de e para o Data Lake Store
Os exemplos seguintes fornecem definições de JSON de exemplo. Pode utilizar estas definições de exemplo para criar um pipeline com o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Os exemplos mostram como copiar dados para e do armazenamento do Data Lake Store e BLOBs do Azure. No entanto, os dados podem ser copiados _diretamente_ Coletores de partir de qualquer uma das origens qualquer suportadas. Para obter mais informações, consulte a secção "arquivos de dados suportados e formatos" no [mover dados com a atividade de cópia](data-factory-data-movement-activities.md) artigo.

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Exemplo: Copiar dados de armazenamento de Blobs do Azure para o Azure Data Lake Store
Mostra o código de exemplo nesta secção:

* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um serviço ligado do tipo [AzureDataLakeStore](#linked-service-properties).
* Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureDataLakeStore](#dataset-properties).
* R [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [um AzureDataLakeStoreSink](#copy-activity-properties).

Os exemplos mostram como séries de tempo os dados do armazenamento de Blobs do Azure são copiados para o Data Lake Store, a cada hora.

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

**Serviço ligado do Azure Data Lake Store**

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
> Para obter detalhes de configuração, consulte a [propriedades do serviço ligado](#linked-service-properties) secção.
>

**Conjunto de dados de entrada do blobs do Azure**

No exemplo a seguir, dados são captados um blob novo a cada hora (`"frequency": "Hour", "interval": 1`). O nome de ficheiro e caminho de pasta para o blob dinamicamente são avaliados com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza o ano, mês e parte do dia da hora de início. O nome de ficheiro utiliza a parte de hora a hora de início. O `"external": true` definição informa o serviço Data Factory, que a tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

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

**Conjunto de dados do Azure Data Lake Store**

O exemplo seguinte copia dados para o Data Lake Store. Novos dados são copiados para o Data Lake Store a cada hora.

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

**Atividade de cópia num pipeline com uma origem de blob e um sink de Data Lake Store**

No exemplo a seguir, o pipeline contém uma atividade de cópia que está configurada para usar a entrada e conjuntos de dados de saída. A atividade de cópia está agendada para ser executado a cada hora. No pipeline de definição de JSON, o `source` tipo está definido como `BlobSource`e o `sink` tipo está definido como `AzureDataLakeStoreSink`.

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

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Exemplo: Copiar dados do Azure Data Lake Store para um blob do Azure
Mostra o código de exemplo nesta secção:

* Um serviço ligado do tipo [AzureDataLakeStore](#linked-service-properties).
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureDataLakeStore](#dataset-properties).
* Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* R [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza [AzureDataLakeStoreSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O código copia dados de séries temporais de Data Lake Store para um blob do Azure a cada hora.

**Serviço ligado do Azure Data Lake Store**

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
> Para obter detalhes de configuração, consulte a [propriedades do serviço ligado](#linked-service-properties) secção.
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
**Conjunto de dados de entrada do Azure Data Lake**

Neste exemplo, a definição `"external"` para `true` informa o serviço Data Factory, que a tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

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

No exemplo a seguir, os dados são escritos para um blob novo a cada hora (`"frequency": "Hour", "interval": 1`). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza o ano, mês, dia e a parte horas sobre a hora de início.

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

**Uma atividade de cópia num pipeline com uma origem de Azure Data Lake Store e um sink de blob**

No exemplo a seguir, o pipeline contém uma atividade de cópia que está configurada para usar a entrada e conjuntos de dados de saída. A atividade de cópia está agendada para ser executado a cada hora. No pipeline de definição de JSON, o `source` tipo está definido como `AzureDataLakeStoreSource`e o `sink` tipo está definido como `BlobSink`.

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

Na definição da atividade de cópia, também pode mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de sink. Para obter detalhes, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e otimização
Para saber mais sobre os fatores que afetam o desempenho de atividade de cópia e como otimizá-lo, consulte a [guia de sintonização de desempenho de atividade de cópia e](data-factory-copy-activity-performance.md) artigo.
