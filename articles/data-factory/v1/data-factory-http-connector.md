---
title: Mover dados de uma fonte http - Azure
description: Saiba como mover dados de uma fonte de HTTP no local ou na nuvem utilizando a Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e668f44bbc3d2e381edeb80c568a41355584a4ee
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78387567"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Mova dados de uma fonte HTTP utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-http-connector.md)
> * [Versão 2 (versão atual)](../connector-http.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Azure Data Factory, consulte o [conector HTTP em V2](../connector-http.md).


Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de um ponto final em http no local ou em nuvem HTTP para uma loja de dados de sink suportado. Este artigo baseia-se em dados de Movimento utilizando a [Copy Activity,](data-factory-data-movement-activities.md)que apresenta uma visão geral do movimento de dados utilizando a Atividade de Cópia. O artigo também lista as lojas de dados que a Copy Activity suporta como fontes e afunda.

A Data Factory suporta atualmente apenas a transferência de dados de uma fonte http para outras lojas de dados. Não suporta a mudança de dados de outras lojas de dados para um destino HTTP.

## <a name="supported-scenarios-and-authentication-types"></a>Cenários suportados e tipos de autenticação

Pode utilizar este conector HTTP para recuperar dados tanto de uma nuvem como de *um ponto final http/S no local* utilizando os métodos HTTP **GET** ou **POST.** Os seguintes tipos de autenticação são suportados: **Anónimo,** **Básico,** **Digest,** **Digest, Windows,** e **ClientCertificate**. Note a diferença entre este conector e o [conector](data-factory-web-table-connector.md)da tabela Web . O conector de tabela Web extrai o conteúdo da tabela a partir de uma página web HTML.

Quando copia dados de um ponto final http no local, deve instalar gateway de gestão de dados no ambiente no local ou num VM Azure. Para saber mais sobre o Gateway de Gestão de Dados e para obter instruções passo a passo sobre como configurar o gateway, consulte os dados móveis entre as [localizações no local e a nuvem](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="get-started"></a>Introdução

Pode criar um pipeline que tenha uma atividade de cópia para mover dados de uma fonte HTTP utilizando diferentes ferramentas ou APIs:

- A maneira mais fácil de criar um pipeline é utilizar o assistente de dados de cópia. Para uma rápida passagem pela criação de um pipeline utilizando o assistente de dados de cópia, consulte [Tutorial: Crie um pipeline utilizando o assistente Copy](data-factory-copy-data-wizard-tutorial.md).

- Também pode utilizar as seguintes ferramentas para criar um pipeline: o **Estúdio Visual,** **O Azure PowerShell,** um **modelo de Gestor de Recursos Azure,** o **.NET API,** ou o **REST API**. Para obter instruções passo a passo sobre como criar um pipeline que tenha uma atividade de cópia, consulte o [tutorial copy activity](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Para amostras JSON que copiam dados de uma fonte http para o armazenamento de Blob Azure, consulte [os exemplos jSON](#json-examples).

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

A tabela seguinte descreve elementos JSON específicos do serviço ligado http:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo | A propriedade **tipo** deve ser definida para **Http**. | Sim |
| url | O URL base para o servidor web. | Sim |
| authenticationType | Especifica o tipo de autenticação. Os valores permitidos são **Anónimos,** **Básicos,** **Digest,** **Windows**e **ClientCertificate.** <br><br> Consulte secções posteriores neste artigo para obter mais propriedades e amostras JSON para estes tipos de autenticação. | Sim |
| enableServerCertificateValidation | Especifica se permite a validação do certificado SSL do servidor se a fonte for um servidor web HTTPS. Quando o seu servidor HTTPS utilizar um certificado auto-assinado, detetete isto como **falso**. | Não<br /> (o padrão é **verdadeiro)** |
| gatewayName | O nome da instância Gateway de Gestão de Dados para utilizar para ligar a uma fonte http no local. | Sim, se estiver a copiar dados de uma fonte http no local |
| encryptedCredential | A credencial encriptada para aceder ao ponto final http. O valor é autogerado quando configura as informações de autenticação no assistente copiar ou utilizando a caixa de diálogo **ClickOnce.** | Não<br /> (aplicar apenas quando copiar dados de um servidor HTTP no local) |

Para mais detalhes sobre a definição de credenciais para uma fonte de dados do conector HTTP no local, consulte [mover dados entre fontes no local e a nuvem utilizando](data-factory-move-data-between-onprem-and-cloud.md)o Portal de Gestão de Dados .

### <a name="using-basic-digest-or-windows-authentication"></a>Utilização de autenticação básica, digestiva ou windows

Definir **autenticaçãoTipo** para **básico,** **digesto,** ou **Windows**. Para além das propriedades genéricas do conector HTTP descritas nas secções anteriores, deteteas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| userName | O nome do utilizador a utilizar para aceder ao ponto final http. | Sim |
| palavra-passe | A palavra-passe para o utilizador (nome de**utilizador).** | Sim |

**Exemplo: Utilização de autenticação básica, digestiva ou windows**

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

### <a name="using-clientcertificate-authentication"></a>Utilização da autenticação do ClientCertificate

Para utilizar a autenticação básica, detete **teaautenticaçãoType** para **O ClienteCertificate**. Para além das propriedades genéricas do conector HTTP descritas nas secções anteriores, deteteas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| embeddedCertData | O conteúdo codificado base64 de dados binários do ficheiro PFX. | Especificar **incorporadaCertData** ou **certThumbprint** |
| certThumbprint | A impressão digital do certificado que foi instalado na loja cert da sua máquina de gateway. Aplicar apenas quando copiar dados de uma fonte http no local. | Especificar **incorporadaCertData** ou **certThumbprint** |
| palavra-passe | A senha que está associada ao certificado. | Não |

Se utilizar **certThumbprint** para autenticação e o certificado estiver instalado na loja pessoal do computador local, conceda permissões de leitura para o serviço gateway:

1. Abra a Consola de Gestão da Microsoft (MMC). Adicione o snap-in dos **Certificados** que visa o **Computador Local**.
2. Expandir **Certificados** > **Pessoais**e, em seguida, selecionar **Certificados**.
3. Clique no certificado da loja pessoal e, em seguida, selecione **Todas as Tarefas** >**Gerir Chaves Privadas**.
3. No separador **Segurança,** adicione a conta de utilizador sob a qual está a funcionar o Serviço de Hospedaria gateway de gestão de dados, com acesso lido ao certificado.  

**Exemplo: Utilização de um certificado de cliente**

Este serviço ligado liga a sua fábrica de dados a um servidor web HTTP no local. Utiliza um certificado de cliente que está instalado na máquina que tem o Portal de Gestão de Dados instalado.

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

**Exemplo: Utilização de um certificado de cliente num ficheiro**

Este serviço ligado liga a sua fábrica de dados a um servidor web HTTP no local. Utiliza um ficheiro de certificado de cliente na máquina que tem gateway de gestão de dados instalado.

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

Algumas secções de um conjunto de dados Ficheiro JSON, tais como estrutura, disponibilidade e política, são semelhantes para todos os tipos de conjuntos de dados (Base de Dados Azure SQL, armazenamento Azure Blob, armazenamento de mesa Azure).

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Criar conjuntos](data-factory-create-datasets.md)de dados .

A secção **typeProperties** é diferente para cada tipo de conjunto de dados. A secção **TypeProperties** fornece informações sobre a localização dos dados na loja de dados. A secção **typeProperties** para um conjunto de dados do tipo **Http** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** do conjunto de dados deve ser definido para **Http**. | Sim |
| relativeUrl | Um URL relativo ao recurso que contém os dados. Quando o caminho não é especificado, apenas é utilizado o URL especificado na definição de serviço ligado. <br><br> Para construir um URL dinâmico, pode utilizar funções de Fábrica de [Dados e variáveis do sistema](data-factory-functions-variables.md). Exemplo: **relativaUrl**: **$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)** . | Não |
| solicitarMétodo | O método HTTP. Os valores permitidos são **GET** e **POST**. | Não <br />(predefinido é **GET)** |
| additionalHeaders | Cabeçalhos adicionais de pedido HTTP. | Não |
| requestBody | O corpo para o pedido http. | Não |
| format | Se pretender *recuperar os dados de um ponto final http como está* sem os analisar, ignore a definição do **formato.** <br><br> Se pretender analisar o conteúdo de resposta HTTP durante a cópia, são suportados os seguintes tipos de **formato: TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat**e **ParquetFormat**. Para mais informações, consulte [o formato Texto,](data-factory-supported-file-and-compression-formats.md#text-format) [formato JSON,](data-factory-supported-file-and-compression-formats.md#json-format) [formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e [formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) |Não |
| compression | Especifica o tipo e o nível de compressão dos dados. Tipos suportados: **GZip,** **Deflate,** **BZip2,** e **ZipDeflate**. Níveis suportados: **Ideal** e **Mais Rápido**. Para mais informações, consulte [formatos de ficheiroe de compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

**Exemplo: Utilizando o método GET (padrão)**

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

**Exemplo: Utilização do método POST**

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

Propriedades como nome, descrição, tabelas de entrada e saída, e a política estão disponíveis para todos os tipos de atividades.

Para obter uma lista completa de secções e propriedades disponíveis para definir [atividades, consulte Criar oleodutos.](data-factory-create-pipelines.md) 

As propriedades disponíveis na secção **tipoPropriedades** da atividade variam com cada tipo de atividade. Para uma atividade de cópia, as propriedades variam dependendo dos tipos de fontes e pias.

Atualmente, quando a fonte na Atividade de Cópia é do tipo **HttpSource,** são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| httpRequestTimeout | O prazo (o valor **TimeSpan)** para o pedido http para obter uma resposta. É o tempo livre para obter uma resposta, não o tempo para ler dados de resposta. | Não<br />(valor predefinido: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Formatos de ficheiros e compressão suportados

Consulte [os formatos de arquivo e compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md) para mais informações.

## <a name="json-examples"></a>Exemplos jSON

Os exemplos seguintes fornecem definições JSON de amostra que pode usar para criar um pipeline utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Os exemplos mostram como copiar dados de uma fonte http para o armazenamento De Blob. No entanto, os dados podem ser copiados *diretamente* de qualquer uma das fontes para qualquer uma das pias [que são suportadas](data-factory-data-movement-activities.md#supported-data-stores-and-formats) através da utilização da Copy Activity na Azure Data Factory.

**Exemplo: Copiar dados de uma fonte http para armazenamento De Blob Azure**

A solução Data Factory para esta amostra contém as seguintes entidades data Factory:

*   Um serviço ligado do tipo [HTTP.](#linked-service-properties)
*   Um serviço ligado do tipo [AzureStorage.](data-factory-azure-blob-connector.md#linked-service-properties)
*   Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [Http](#dataset-properties).
*   Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
*   Um [pipeline](data-factory-create-pipelines.md) que tem uma atividade de cópia que utiliza [httpSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados de uma fonte http para uma bolha Azure a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

### <a name="http-linked-service"></a>Serviço ligado http

Este exemplo utiliza o serviço ligado http com autenticação anónima. Consulte o [serviço ligado http](#linked-service-properties) para diferentes tipos de autenticação que pode utilizar.

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

### <a name="azure-storage-linked-service"></a>Serviço ligado ao armazenamento Azure

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

A definição **externa** para **verdadeira** informa o serviço Data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

Os dados são escritos para uma nova bolha a cada hora **(frequência:** **hora,** **intervalo:** **1**).

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

O pipeline contém uma atividade de cópia configurada para utilizar os conjuntos de dados de entrada e saída. A atividade de cópia está programada para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **HttpSource** e o tipo de **pia** é definido para **BlobSink**.

Para a lista de propriedades que **o HttpSource** suporta, consulte [httpSource](#copy-activity-properties).

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
> Para mapear colunas de um conjunto de dados de origem para colunas a partir de um conjunto de dados de sumidouro, consulte [colunas de conjunto de dados de mapeamento na Fábrica](data-factory-map-columns.md)de Dados Azure .

## <a name="performance-and-tuning"></a>Desempenho e otimização

Para conhecer os factores-chave que afetam o desempenho do movimento de dados (Copy Activity) na Fábrica de Dados Azure e várias formas de o otimizar, consulte o desempenho da Atividade de Cópia e o guia de [afinação.](data-factory-copy-activity-performance.md)
