---
title: Mover dados de uma fonte HTTP - Azure
description: Saiba como mover dados de uma fonte HTTP no local ou na nuvem utilizando a Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/22/2018
robots: noindex
ms.openlocfilehash: ce29b5a112d70575a721b0b527947fd95868da80
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382927"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Mover dados de uma fonte HTTP utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-http-connector.md)
> * [Versão 2 (versão atual)](../connector-http.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Azure Data Factory, consulte o [conector HTTP em V2](../connector-http.md).

Este artigo descreve como utilizar a Copy Activity na Azure Data Factory para mover dados de um ponto final no local ou na nuvem HTTP para uma loja de dados de lavatórios suportados. Este artigo baseia-se em [dados movendo-se utilizando a Copy Activity,](data-factory-data-movement-activities.md)que apresenta uma visão geral do movimento de dados utilizando a Copy Activity. O artigo também lista as lojas de dados que a Copy Activity suporta como fontes e sumidouros.

A Data Factory suporta atualmente apenas a transferência de dados de uma fonte HTTP para outras lojas de dados. Não suporta a transferência de dados de outras lojas de dados para um destino HTTP.

## <a name="supported-scenarios-and-authentication-types"></a>Cenários apoiados e tipos de autenticação

Pode utilizar este conector HTTP para obter dados tanto de *uma nuvem como de um ponto final HTTP/S no local,* utilizando os métodos HTTP **GET** ou **POST.** São suportados os seguintes tipos de autenticação: **Anónimo,** **Básico,** **Digest,** **Windows** e **ClientCertificate**. Note a diferença entre este conector e o [conector](data-factory-web-table-connector.md)de mesa Web . O conector de mesa Web extrai o conteúdo da tabela a partir de uma página web HTML.

Ao copiar dados de um ponto final HTTP no local, tem de instalar o Data Management Gateway no ambiente no local ou num VM Azure. Para saber mais sobre o Gateway de Gestão de Dados e para obter instruções passo a passo sobre como configurar o gateway, consulte [dados de deslocação entre os locais do local e a nuvem.](data-factory-move-data-between-onprem-and-cloud.md)

## <a name="get-started"></a>Introdução

Pode criar um pipeline que tenha uma atividade de cópia para mover dados de uma fonte HTTP utilizando diferentes ferramentas ou APIs:

- A forma mais fácil de criar um oleoduto é utilizar o assistente de dados de cópia. Para uma rápida passagem pela criação de um oleoduto utilizando o assistente de dados de cópia, consulte [Tutorial: Crie um pipeline utilizando o assistente copy](data-factory-copy-data-wizard-tutorial.md).

- Também pode utilizar as seguintes ferramentas para criar um pipeline: o **Visual Studio**, **Azure PowerShell**, um **modelo Azure Resource Manager**, a **API .NET** ou a **API REST**. Para obter instruções passo a passo sobre como criar um oleoduto que tenha uma atividade de cópia, consulte o tutorial de [Copy Activity](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Para amostras JSON que copiam dados de uma fonte HTTP para o armazenamento de Azure Blob, consulte [exemplos JSON](#json-examples).

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

A tabela a seguir descreve elementos JSON específicos do serviço ligado HTTP:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo | A propriedade **tipo** deve ser definida para **Http**. | Yes |
| url | O URL base para o servidor web. | Yes |
| authenticationType | Especifica o tipo de autenticação. Os valores permitidos são **Anónimos,** **Básicos,** **Digest,** **Windows** e **ClientCertificate**. <br><br> Consulte secções posteriores neste artigo para obter mais propriedades e amostras JSON para estes tipos de autenticação. | Yes |
| enableServerCertificateValidation | Especifica se deve ativar a validação do certificado TLS/SSL do servidor se a fonte for um servidor web HTTPS. Quando o seu servidor HTTPS utilizar um certificado auto-assinado, descreva-o **em falso**. | No<br /> (o padrão é **verdadeiro)** |
| gatewayName | O nome da instância Data Management Gateway para usar para ligar a uma fonte HTTP no local. | Sim, se estiver a copiar dados a partir de uma fonte HTTP no local |
| criptografadoCredential | A credencial encriptada para aceder ao ponto final HTTP. O valor é autogerido quando configura as informações de autenticação no assistente copy ou utilizando a caixa de diálogo **ClickOnce.** | No<br /> (aplicar apenas quando copiar dados de um servidor HTTP no local) |

Para obter mais informações sobre a definição de credenciais para uma fonte de dados do conector HTTP no local, consulte [os dados de movimento entre as fontes no local e a nuvem utilizando o Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-digest-or-windows-authentication"></a>Utilização da autenticação Básica, Digest ou Windows

Definir **autenticaçãoType** para **Basic**, **Digest** ou **Windows**. Para além das propriedades genéricas do conector HTTP descritas nas secções anteriores, definir as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| userName | O nome de utilizador a utilizar para aceder ao ponto final HTTP. | Yes |
| palavra-passe | A palavra-passe para o utilizador **(nome de utilizador).** | Yes |

**Exemplo: Utilização da autenticação Básica, Digest ou Windows**

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

### <a name="using-clientcertificate-authentication"></a>Utilização da autenticação Do ClienteCertificado

Para utilizar a autenticação básica, **defie a autenticaçãoType** ao **ClienteCerti.** Para além das propriedades genéricas do conector HTTP descritas nas secções anteriores, definir as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| EmbeddedCertData | O conteúdo codificado da Base64 dos dados binários do ficheiro PFX. | Especifique ou **incorporasseCertData** ou **certThumbprint** |
| certThumbprint | A impressão digital do certificado que foi instalado na loja de certificados da sua máquina de gateway. Aplicar apenas quando copiar dados de uma fonte HTTP no local. | Especifique ou **incorporasseCertData** ou **certThumbprint** |
| palavra-passe | A senha que está associada ao certificado. | No |

Se utilizar **o certThumbprint** para autenticação e o certificado estiver instalado na loja pessoal do computador local, conceda permissões de leitura ao serviço gateway:

1. Abra a Consola de Gestão da Microsoft (MMC). Adicione o encaixe de **certificados** que visa **o Computador Local.**
2. Expandir **Certificados**  >  **Pessoais** e, em seguida, selecionar **Certificados**.
3. Clique com o botão direito no certificado da loja pessoal e, em seguida, selecione **Todas as Tarefas**  > **Gerencie as Teclas Privadas**.
3. No separador **Segurança,** adicione a conta de utilizador sob a qual o Serviço de Anfitriões gateway de gestão de dados está em execução, com acesso lido ao certificado.  

**Exemplo: Utilização de um certificado de cliente**

Este serviço ligado liga a sua fábrica de dados a um servidor web HTTP no local. Utiliza um certificado de cliente que está instalado na máquina que tem o Data Management Gateway instalado.

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

**Exemplo: Usar um certificado de cliente num ficheiro**

Este serviço ligado liga a sua fábrica de dados a um servidor web HTTP no local. Utiliza um ficheiro de certificado de cliente na máquina que tem o Data Management Gateway instalado.

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Algumas secções de um ficheiro JSON do conjunto de dados, tais como estrutura, disponibilidade e política, são semelhantes para todos os tipos de conjunto de dados (Azure SQL Database, armazenamento Azure Blob, armazenamento de mesa Azure).

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [criar conjuntos de dados](data-factory-create-datasets.md).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados. A secção **typeProperties** fornece informações sobre a localização dos dados na loja de dados. A secção **typeProperties** para um conjunto de dados do tipo **Http** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | O **tipo** de conjunto de dados deve ser definido em **Http**. | Yes |
| relativoUrl | Um URL relativo ao recurso que contém os dados. Quando o caminho não é especificado, apenas é utilizado o URL especificado na definição de serviço ligada. <br><br> Para construir um URL dinâmico, pode utilizar [funções de Data Factory e variáveis do sistema.](data-factory-functions-variables.md) Exemplo: **relativoUrl**: **$$Text.Formato('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)**. | No |
| requestMethod | O método HTTP. Os valores permitidos são **GET** e **POST.** | No <br />(predefinição é **GET)** |
| cabeçalhos adicionais | Cabeçalhos de pedido HTTP adicionais. | No |
| requestCorp | O corpo para o pedido HTTP. | No |
| formato | Se pretender *recuperar os dados de um ponto final HTTP como-é* sem analisá-los, salte a definição de **formato.** <br><br> Se pretender analisar o conteúdo de resposta HTTP durante a cópia, suportam-se os seguintes tipos de formato: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat** e **ParquetFormat**. Para mais informações, consulte [o formato Text,](data-factory-supported-file-and-compression-formats.md#text-format) [formato JSON,](data-factory-supported-file-and-compression-formats.md#json-format) [formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [formato Orc,](data-factory-supported-file-and-compression-formats.md#orc-format)e [formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) |No |
| compressão | Especifique o tipo e o nível de compressão para os dados. Tipos suportados: **GZip,** **Deflate,** **BZip2** e **ZipDeflate**. Níveis suportados: **Ideal** e **Mais Rápido**. Para obter mais informações, consulte [os formatos de arquivo e compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

**Exemplo: Utilização do método GET (predefinido)**

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

## <a name="copy-activity-properties"></a>Propriedades de Atividade de Cópia

Propriedades como nome, descrição, tabelas de entrada e saída, e a política está disponível para todos os tipos de atividades.

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [criar oleodutos.](data-factory-create-pipelines.md) 

As propriedades que estão disponíveis na secção **de tipoProperties** da atividade variam com cada tipo de atividade. Para uma atividade de cópia, as propriedades variam dependendo dos tipos de fontes e pias.

Atualmente, quando a fonte em Atividade de Cópia é do tipo **HttpSource,** as seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| httpRequestTimeout | O tempo limite (o valor **TimeSpan)** para o pedido HTTP obter uma resposta. É o tempo limite para obter uma resposta, não o tempo limite para ler os dados de resposta. | No<br />(valor predefinido: **00:01:40)** |

## <a name="supported-file-and-compression-formats"></a>Formatos de ficheiro e de compressão suportados

Consulte [os formatos de arquivo e compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md) para mais informações.

## <a name="json-examples"></a>Exemplos JSON

Os exemplos a seguir fornecem definições JSON de amostra que pode usar para criar um oleoduto utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Os exemplos mostram como copiar dados de uma fonte HTTP para o armazenamento de Azure Blob. No entanto, os dados podem ser copiados *diretamente* de qualquer uma das fontes para qualquer uma das pias [que são suportadas](data-factory-data-movement-activities.md#supported-data-stores-and-formats) através da utilização da Copy Activity na Azure Data Factory.

**Exemplo: Copiar dados de uma fonte HTTP para o armazenamento da Azure Blob**

A solução data factory para esta amostra contém as seguintes entidades da Data Factory:

*   Um serviço ligado do tipo [HTTP](#linked-service-properties).
*   Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
*   Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [Http](#dataset-properties).
*   Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
*   Um [pipeline](data-factory-create-pipelines.md) que tem uma atividade de cópia que utiliza [HttpSource](#copy-activity-properties) e [BlobSink.](data-factory-azure-blob-connector.md#copy-activity-properties)

A amostra copia dados de uma fonte HTTP para uma bolha Azure a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

### <a name="http-linked-service"></a>Serviço ligado HTTP

Este exemplo utiliza o serviço linked HTTP com autenticação anónima. Consulte [o serviço http linked](#linked-service-properties) para diferentes tipos de autenticação que pode utilizar.

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

A definição **externa** à **verdade** informa o serviço Data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

Os dados são escritos para uma nova bolha a cada hora **(frequência:** **hora,** **intervalo**: **1**).

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

O pipeline contém uma atividade de cópia configurada para utilizar os conjuntos de dados de entrada e saída. A atividade de cópia está programada para ser executada a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **HttpSource** e o tipo **de pia** está definido para **BlobSink**.

Para obter a lista de propriedades que **o HttpSource** suporta, consulte [HttpSource](#copy-activity-properties).

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
> Para mapear colunas de um conjunto de dados de origem para colunas de um conjunto de dados de sumidouro, consulte [as colunas de conjunto de dados de mapeamento na Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e otimização

Para conhecer os factores-chave que afetam o desempenho do movimento de dados (Copy Activity) na Azure Data Factory e várias formas de otimizá-lo, consulte o desempenho da Atividade de [Cópia e o guia de afinação](data-factory-copy-activity-performance.md).
