---
title: Mover dados de uma origem HTTP-Azure
description: Saiba como mover dados de uma fonte HTTP local ou na nuvem usando Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4133393b7b21394ea397598a5e1651ee370f92f0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682518"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Mover dados de uma origem HTTP usando Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-http-connector.md)
> * [Versão 2 (versão atual)](../connector-http.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Azure Data Factory, consulte [conector http na v2](../connector-http.md).


Este artigo descreve como usar a atividade de cópia em Azure Data Factory para mover dados de um ponto de extremidade de HTTP local ou de nuvem para um armazenamento de dados de coletor com suporte. Este artigo se baseia em [mover dados usando a atividade de cópia](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados usando a atividade de cópia. O artigo também lista os armazenamentos de dados com suporte da atividade de cópia como fontes e coletores.

O Data Factory atualmente dá suporte apenas à movimentação de dados de uma origem HTTP para outros armazenamentos de dados. Ele não dá suporte à movimentação de dados de outros armazenamentos de dados para um destino HTTP.

## <a name="supported-scenarios-and-authentication-types"></a>Tipos de autenticação e cenários com suporte

Você pode usar esse conector HTTP para recuperar dados de *uma nuvem e um ponto de extremidade http/S local* usando os métodos **Get** ou **post** do http. Há suporte para os seguintes tipos de autenticação: **Anonymous**, **Basic**, **Digest**, **Windows**e **ClientCertificate**. Observe a diferença entre esse conector e o [conector de tabela da Web](data-factory-web-table-connector.md). O conector de tabela da Web extrai o conteúdo da tabela de uma página HTML.

Ao copiar dados de um ponto de extremidade HTTP local, você deve instalar Gerenciamento de Dados gateway no ambiente local ou em uma VM do Azure. Para saber mais sobre o Gerenciamento de Dados gateway e instruções passo a passo sobre como configurar o gateway, consulte [movendo dados entre os locais e a nuvem local](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="get-started"></a>Introdução

Você pode criar um pipeline que tenha uma atividade de cópia para mover dados de uma origem HTTP usando diferentes ferramentas ou APIs:

- A maneira mais fácil de criar um pipeline é usar o assistente de Copiar Dados. Para obter uma explicação rápida da criação de um pipeline usando o assistente de Copiar Dados, consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md).

- Você também pode usar as seguintes ferramentas para criar um pipeline: o **Visual Studio**, **Azure PowerShell**, um **modelo de Azure Resource Manager**, a **API do .net**ou a **API REST**. Para obter instruções detalhadas sobre como criar um pipeline com uma atividade de cópia, consulte o tutorial de atividade de [cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Para exemplos de JSON que copiam dados de uma origem HTTP para o armazenamento de BLOBs do Azure, consulte [exemplos de JSON](#json-examples).

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

A tabela a seguir descreve os elementos JSON que são específicos para o serviço vinculado de HTTP:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo | A propriedade **Type** deve ser definida como **http**. | Sim |
| url | A URL base para o servidor Web. | Sim |
| authenticationType | Especifica o tipo de autenticação. Os valores permitidos são **Anonymous**, **Basic**, **Digest**, **Windows**e **ClientCertificate**. <br><br> Consulte as seções posteriores neste artigo para obter mais propriedades e exemplos de JSON para esses tipos de autenticação. | Sim |
| enableServerCertificateValidation | Especifica se a validação do certificado SSL do servidor deve ser habilitada se a origem for um servidor Web HTTPS. Quando o servidor HTTPS usar um certificado autoassinado, defina-o como **false**. | Não<br /> (o padrão é **true**) |
| gatewayName | O nome da instância de gateway de Gerenciamento de Dados a ser usada para se conectar a uma origem HTTP local. | Sim, se você estiver copiando dados de uma origem HTTP local |
| encryptedCredential | A credencial criptografada para acessar o ponto de extremidade HTTP. O valor é gerado automaticamente quando você configura as informações de autenticação no assistente de cópia ou usando a caixa de diálogo **ClickOnce** . | Não<br /> (aplicar somente quando você copiar dados de um servidor HTTP local) |

Para obter detalhes sobre como configurar as credenciais para uma fonte de dados do conector HTTP local, consulte [mover dados entre fontes locais e a nuvem usando gerenciamento de dados gateway](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-digest-or-windows-authentication"></a>Usando a autenticação básica, Digest ou Windows

Defina **authenticationType** como **Basic**, **Digest**ou **Windows**. Além das propriedades do conector HTTP genérico descritas nas seções anteriores, defina as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Usu | O nome de usuário a ser usado para acessar o ponto de extremidade HTTP. | Sim |
| palavra-passe | A senha do usuário (**username**). | Sim |

**Exemplo: usando a autenticação básica, Digest ou Windows**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>Usando a autenticação ClientCertificate

Para usar a autenticação básica, defina **authenticationType** como **ClientCertificate**. Além das propriedades do conector HTTP genérico descritas nas seções anteriores, defina as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| embeddedCertData | O conteúdo codificado na Base64 de dados binários do arquivo PFX. | Especifique **embeddedCertData** ou **certThumbprint** |
| certThumbprint | A impressão digital do certificado que foi instalado no repositório de certificados do computador do gateway. Aplica-se somente quando você copia dados de uma origem HTTP local. | Especifique **embeddedCertData** ou **certThumbprint** |
| palavra-passe | A senha que está associada ao certificado. | Não |

Se você usar o **certThumbprint** para autenticação e o certificado estiver instalado no repositório pessoal do computador local, conceda permissões de leitura para o serviço do gateway:

1. Abra o MMC (console de gerenciamento Microsoft). Adicione o snap-in de **certificados** que se destina ao **computador local**.
2. Expanda **certificados** > **pessoal**e, em seguida, selecione **certificados**.
3. Clique com o botão direito do mouse no certificado do repositório pessoal e selecione **todas as tarefas** >**gerenciar chaves privadas**.
3. Na guia **segurança** , adicione a conta de usuário na qual o serviço host do Gateway de gerenciamento de dados está em execução, com acesso de leitura ao certificado.  

**Exemplo: usando um certificado de cliente**

Esse serviço vinculado vincula seu data factory a um servidor Web HTTP local. Ele usa um certificado de cliente que está instalado no computador que tem o gateway Gerenciamento de Dados instalado.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"

        }
    }
}
```

**Exemplo: usando um certificado de cliente em um arquivo**

Esse serviço vinculado vincula seu data factory a um servidor Web HTTP local. Ele usa um arquivo de certificado do cliente no computador que tem o gateway Gerenciamento de Dados instalado.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "Base64-encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades de DataSet

Algumas seções de um arquivo JSON de conjunto de dados, como a estrutura, a disponibilidade e a política, são semelhantes para todos os tipos de conjunto de dados (Azure SQL Database, armazenamento de BLOBs do Azure, armazenamento de tabelas do Azure).

Para obter uma lista completa das seções e propriedades que estão disponíveis para definir conjuntos de os, consulte [criando conjuntos](data-factory-create-datasets.md)de os.

A seção **typeproperties** é diferente para cada tipo de conjunto de texto. A seção **typeproperties** fornece informações sobre o local dos dados no armazenamento de dados. A seção **typeproperties** para um DataSet do tipo **http** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** do conjunto de conjuntos deve ser definido como **http**. | Sim |
| relativeUrl | Uma URL relativa para o recurso que contém os dados. Quando o caminho não é especificado, somente a URL especificada na definição de serviço vinculado é usada. <br><br> Para construir uma URL dinâmica, você pode usar [funções data Factory e variáveis do sistema](data-factory-functions-variables.md). Exemplo: **RelativeURL**: **$ $Text. Format ('/My/Report? month = {0: YYYY}-{0: mm} & fmt = csv ', SliceStart)** . | Não |
| requestMethod | O método HTTP. Os valores permitidos são **Get** e **post**. | Não <br />(o padrão é **Get**) |
| additionalHeaders | Cabeçalhos de solicitação HTTP adicionais. | Não |
| requestBody | O corpo da solicitação HTTP. | Não |
| ao | Se você quiser *recuperar os dados de um ponto de extremidade http como estão* sem analisá-los, ignore a configuração de **formato** . <br><br> Se você deseja analisar o conteúdo da resposta HTTP durante a cópia, há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**e **ParquetFormat**. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e [formato parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). |Não |
| çã | Especifique o tipo e o nível de compactação para os dados. Tipos com suporte: **gzip**, **deflate**, **bzip2**e **ZipDeflate**. Níveis com suporte: **ideal** e **mais rápido**. Para obter mais informações, consulte [formatos de arquivo e compactação em Azure data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

**Exemplo: usando o método GET (padrão)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Exemplo: usando o método POST**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
           "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Propriedades como nome, descrição, tabelas de entrada e saída e política estão disponíveis para todos os tipos de atividades.

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte [criando pipelines](data-factory-create-pipelines.md). 

As propriedades que estão disponíveis na seção **typeproperties** da atividade variam de acordo com cada tipo de atividade. Para uma atividade de cópia, as propriedades variam de acordo com os tipos de fontes e coletores.

Atualmente, quando a origem na atividade de cópia é do tipo **httpname** , há suporte para as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| httpRequestTimeout | O tempo limite (o valor de **TimeSpan** ) para a solicitação HTTP obter uma resposta. É o tempo limite para obter uma resposta, não o tempo limite para ler dados de resposta. | Não<br />(valor padrão: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Formatos de arquivo e compactação com suporte

Consulte [formatos de arquivo e compactação em Azure data Factory](data-factory-supported-file-and-compression-formats.md) para obter mais informações.

## <a name="json-examples"></a>Exemplos de JSON

Os exemplos a seguir fornecem exemplos de definições de JSON que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Os exemplos mostram como copiar dados de uma origem HTTP para o armazenamento de BLOBs do Azure. No entanto, os dados podem ser copiados *diretamente* de qualquer uma das fontes para qualquer um dos coletores [com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure data Factory.

**Exemplo: copiar dados de uma origem HTTP para o armazenamento de BLOBs do Azure**

A solução Data Factory para este exemplo contém as seguintes entidades de Data Factory:

*   Um serviço vinculado do tipo [http](#linked-service-properties).
*   Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
*   Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [http](#dataset-properties).
*   Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
*   Um [pipeline](data-factory-create-pipelines.md) que tem uma atividade de cópia que usa [httpname](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de uma origem HTTP para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas em seções que seguem os exemplos.

### <a name="http-linked-service"></a>Serviço vinculado de HTTP

Este exemplo usa o serviço vinculado HTTP com autenticação anônima. Consulte [serviço vinculado http](#linked-service-properties) para diferentes tipos de autenticação que você pode usar.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Serviço vinculado do armazenamento do Azure

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
    }
  }
}
```

### <a name="http-input-dataset"></a>Conjunto de dados de entrada HTTP

A configuração de **external** como **true** informa ao serviço de data Factory que o dataset é externo ao data Factory e não é produzido por uma atividade no data Factory.

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída dos blobs do Azure

Os dados são gravados em um novo BLOB a cada hora (**frequência**: **hora**, **intervalo**: **1**).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

### <a name="pipeline-that-uses-a-copy-activity"></a>Pipeline que usa uma atividade de cópia

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída. A atividade de cópia está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** é definido como **httpname** e o tipo de **coletor** é definido como **BlobSink**.

Para obter a lista de propriedades que o **httpname** dá suporte, consulte [httpname](#copy-activity-properties).

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with a copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
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

> [!NOTE]
> Para mapear colunas de um conjunto de código de origem para colunas de um conjunto de um, consulte [mapeando colunas de DataSet em Azure data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e otimização

Para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Azure Data Factory e várias maneiras de otimizá-lo, consulte o [Guia de desempenho e ajuste da atividade de cópia](data-factory-copy-activity-performance.md).
