---
title: Mover dados de uma origem HTTP - Azure | Documentos da Microsoft
description: Aprenda a mover dados de uma local ou na cloud origem HTTP através do Azure Data Factory.
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
ms.openlocfilehash: 22d6999b2a69aceb4421cea070d784f693bdf9c4
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839286"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Mover dados de uma origem HTTP utilizando o Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory, que está a utilizar:"]
> * [Versão 1](data-factory-http-connector.md)
> * [Versão 2 (versão atual)](../connector-http.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço do Azure Data Factory, veja [conector HTTP no V2](../connector-http.md).


Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para mover dados de uma local ou na cloud ponto final de HTTP para um arquivo de dados de sink suportados. Este artigo baseia-se nas [mover dados com a atividade de cópia](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimento de dados através da atividade de cópia. O artigo também apresenta uma lista de arquivos de dados que a atividade de cópia suporta como origens e sinks.

O Data Factory suporta, atualmente, apenas mover dados de uma origem HTTP para outros arquivos de dados. Ele não dá suporte a mover dados de outros arquivos de dados para um destino de HTTP.

## <a name="supported-scenarios-and-authentication-types"></a>Cenários suportados e tipos de autenticação

Pode utilizar este conector HTTP para obter dados a partir *uma cloud e um ponto de final HTTP/S no local* utilizando o HTTP **obter** ou **POST** métodos. São suportados os seguintes tipos de autenticação: **Anónimo**, **básica**, **Digest**, **Windows**, e **ClientCertificate**. Tenha em atenção a diferença entre este conector e a [conector de tabela de Web](data-factory-web-table-connector.md). O conector de tabela de Web extrai o conteúdo da tabela de uma página da Web HTML.

Quando copia dados a partir de um ponto de final HTTP no local, tem de instalar o Data Management Gateway no ambiente no local ou numa VM do Azure. Para saber mais sobre o Gateway de gestão de dados e para obter instruções passo a passo sobre como configurar o gateway, veja [mover dados entre localizações no local e na cloud](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="get-started"></a>Introdução

Pode criar um pipeline com uma atividade de cópia para mover dados de uma origem HTTP através de APIs ou ferramentas diferentes:

- A maneira mais fácil para criar um pipeline é usar o Assistente para copiar dados. Para uma passo a passo rápido de criação de um pipeline com o Assistente de cópia de dados, consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md).

- Também pode utilizar as seguintes ferramentas para criar um pipeline: o **Visual Studio**, **Azure PowerShell**, uma **modelo Azure Resource Manager**, o **.NET API** , ou o **API de REST**. Para obter instruções passo a passo sobre como criar um pipeline com uma atividade de cópia, consulte a [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Para o JSON de exemplo que dados de cópia de uma origem HTTP para o armazenamento de Blobs do Azure, veja [exemplos JSON](#json-examples).

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

A tabela seguinte descreve os elementos JSON que são específicos para o serviço ligado de HTTP:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| type | O **tipo** propriedade tem de ser definida como **Http**. | Sim |
| url | O URL de base para o servidor web. | Sim |
| authenticationType | Especifica o tipo de autenticação. Valores permitidos são **anónimo**, **básica**, **Digest**, **Windows**, e **ClientCertificate**. <br><br> Consulte as seções posteriores deste artigo para obter mais propriedades e exemplos JSON para esses tipos de autenticação. | Sim |
| enableServerCertificateValidation | Especifica se pretende ativar a validação de certificado SSL do servidor, se a origem for um servidor de web HTTPS. Quando o servidor HTTPS utiliza um certificado autoassinado, defina esta opção como **false**. | Não<br /> (a predefinição é **true**) |
| gatewayName | O nome da instância do Gateway de gestão de dados a utilizar para ligar a uma origem HTTP no local. | Sim, se estiver a copiar dados de uma origem HTTP no local |
| encryptedCredential | A credencial encriptada para aceder ao ponto final de HTTP. O valor é gerado automaticamente, quando configurar as informações de autenticação no Assistente de cópia ou utilizando o **ClickOnce** caixa de diálogo. | Não<br /> (aplicável apenas quando copiar dados de um servidor HTTP no local) |

Para obter detalhes sobre como definir credenciais para uma origem de dados do conector HTTP no local, consulte [mover dados entre origens no local e na cloud com o Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-digest-or-windows-authentication"></a>Utilizar a autenticação básica, Digest ou do Windows

Definir **authenticationType** ao **básica**, **Digest**, ou **Windows**. Para além das propriedades de conector HTTP genéricas descritas nas secções anteriores, defina as propriedades seguintes:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| userName | O nome de utilizador a utilizar para aceder ao ponto final HTTP. | Sim |
| password | A palavra-passe para o utilizador (**nome de utilizador**). | Sim |

**Exemplo: Utilizar a autenticação básica, Digest ou do Windows**

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

### <a name="using-clientcertificate-authentication"></a>Utilizar a autenticação de ClientCertificate

Para utilizar a autenticação básica, defina **authenticationType** ao **ClientCertificate**. Para além das propriedades de conector HTTP genéricas descritas nas secções anteriores, defina as propriedades seguintes:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| embeddedCertData | O conteúdo de dados binários do ficheiro PFX com codificação Base64. | Especifique **embeddedCertData** ou **certThumbprint** |
| certThumbprint | O thumbprint do certificado que foi instalado no arquivo de certificados do seu computador de gateway. Aplicam-se apenas quando copiar dados a partir de uma origem HTTP no local. | Especifique **embeddedCertData** ou **certThumbprint** |
| password | A palavra-passe associada ao certificado. | Não |

Se usar **certThumbprint** para autenticação e o certificado está instalado no arquivo pessoal do computador local, conceda permissões de leitura para o serviço de gateway:

1. Abra o Console de gerenciamento Microsoft (MMC). Adicionar a **certificados** snap-in direcionada **computador Local**.
2. Expanda **certificados** > **pessoal**e, em seguida, selecione **certificados**.
3. O certificado do arquivo pessoal com o botão direito e, em seguida, selecione **todas as tarefas** >**gerir chaves privadas**.
3. Sobre o **segurança** separador, adicione a conta de utilizador sob a qual o serviço de anfitrião de Gateway de gestão de dados está em execução, com acesso de leitura para o certificado.  

**Exemplo: Utilizar um certificado de cliente**

Este serviço ligado liga a fábrica de dados para um servidor de web HTTP no local. Ele usa um certificado de cliente que está instalado na máquina que tenha instalado um Gateway de gestão de dados.

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

**Exemplo: Utilizar um certificado de cliente num arquivo**

Este serviço ligado liga a fábrica de dados para um servidor de web HTTP no local. Utiliza um ficheiro de certificado de cliente na máquina que tenha instalado um Gateway de gestão de dados.

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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Algumas secções de um ficheiro JSON do conjunto de dados, tais como a estrutura, disponibilidade e a política, são semelhantes para todos os tipos de conjunto de dados (SQL Database do Azure, armazenamento de Blobs do Azure, armazenamento de tabelas do Azure).

Para obter uma lista completa de seções e as propriedades que estão disponíveis para definir conjuntos de dados, consulte [criar conjuntos de dados](data-factory-create-datasets.md).

O **typeProperties** secção é diferente para cada tipo de conjunto de dados. O **typeProperties** secção fornece informações sobre a localização dos dados no arquivo de dados. O **typeProperties** secção para um conjunto de dados da **Http** tipo tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | O **tipo** do conjunto de dados deve ser definida como **Http**. | Sim |
| relativeUrl | Um URL relativo ao recurso que contém os dados. Quando o caminho não for especificado, é utilizado apenas o URL especificado na definição do serviço ligado. <br><br> Para construir um URL em dinâmico, pode utilizar [funções de Data Factory e variáveis de sistema](data-factory-functions-variables.md). Example: **relativeUrl**: **$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)** . | Não |
| requestMethod | O método HTTP. Valores permitidos são **Obtenha** e **POST**. | Não <br />(a predefinição é **obter**) |
| additionalHeaders | Cabeçalhos de pedido HTTP adicionais. | Não |
| requestBody | O corpo do pedido HTTP. | Não |
| format | Se quiser *recuperar os dados de um ponto de final HTTP como-é* sem analisá-lo, ignore o **formato** definição. <br><br> Se pretender analisar o conteúdo de resposta HTTP durante a cópia, são suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, e **ParquetFormat**. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). |Não |
| compression | Especifica o tipo e o nível de compressão dos dados. Tipos suportados: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis de suporte: **Ideal** e **mais rápida**. Para obter mais informações, consulte [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

**Exemplo: Usando o método GET (predefinição)**

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

**Exemplo: Usando o método POST**

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

## <a name="copy-activity-properties"></a>Propriedades da atividade copy

As propriedades, como o nome, descrição, entrada e saída de tabelas e a política estão disponíveis para todos os tipos de atividades.

Para obter uma lista completa de seções e as propriedades que estão disponíveis para a definição de atividades, consulte [Criar pipelines](data-factory-create-pipelines.md). 

Propriedades que estão disponíveis no **typeProperties** secção da atividade varia com cada tipo de atividade. Para uma atividade de cópia, propriedades variam consoante os tipos de origens e sinks.

Atualmente, quando a origem na atividade de cópia é do **HttpSource** escreve, as seguintes propriedades são suportadas:

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| httpRequestTimeout | O tempo limite (o **TimeSpan** valor) para o pedido HTTP para obter uma resposta. É o tempo limite para obter uma resposta, não o tempo limite para ler os dados de resposta. | Não<br />(valor predefinido: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Formatos de ficheiro e de compressão suportados

Ver [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md) para obter mais informações.

## <a name="json-examples"></a>Exemplos JSON

Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar um pipeline com [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Os exemplos mostram como copiar dados a partir de uma origem HTTP para o armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados *diretamente* de qualquer uma das origens qualquer um dos sinks [que são suportadas](data-factory-data-movement-activities.md#supported-data-stores-and-formats) através da atividade de cópia no Azure Data Factory.

**Exemplo: Copiar dados a partir de uma origem HTTP para o armazenamento de Blobs do Azure**

A solução de fábrica de dados para este exemplo contém as seguintes entidades do Data Factory:

*   Um serviço ligado do tipo [HTTP](#linked-service-properties).
*   Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
*   Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [Http](#dataset-properties).
*   Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
*   R [pipeline](data-factory-create-pipelines.md) que tem uma atividade de cópia que utiliza [HttpSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de uma origem HTTP para um blob do Azure a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

### <a name="http-linked-service"></a>Serviço ligado de HTTP

Este exemplo utiliza o serviço ligado de HTTP com a autenticação anónima. Ver [HTTP de serviço ligado](#linked-service-properties) para diferentes tipos de autenticação, pode utilizar.

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

### <a name="azure-storage-linked-service"></a>Serviço ligado do armazenamento do Azure

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

### <a name="http-input-dataset"></a>Conjunto de dados de entrada de HTTP

A definição **externo** ao **verdadeiro** informa o serviço Data Factory, que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

Os dados são escritos para um blob novo a cada hora (**frequência**: **hora**, **intervalo**: **1**).

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

### <a name="pipeline-that-uses-a-copy-activity"></a>Pipeline que utiliza uma atividade de cópia

O pipeline contém uma atividade de cópia que está configurada para utilizar os conjuntos de dados de entrada e saídos. A atividade de cópia está agendada para ser executado a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **HttpSource** e o **sink** tipo está definido como **BlobSink**.

Para obter a lista de propriedades que **HttpSource** suporta, consulte [HttpSource](#copy-activity-properties).

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
> Para mapear as colunas a partir de um conjunto de dados de origem para colunas a partir de um conjunto de dados de sink, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e otimização

Para saber mais sobre os principais fatores que afetam o desempenho de movimento de dados (atividade de cópia) no Azure Data Factory e diversas maneiras para otimizá-lo, consulte a [guia de sintonização de desempenho de atividade de cópia e](data-factory-copy-activity-performance.md).
