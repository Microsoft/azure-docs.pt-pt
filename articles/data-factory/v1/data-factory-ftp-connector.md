---
title: Mover dados de um servidor FTP com o Azure Data Factory | Documentos da Microsoft
description: Saiba mais sobre como mover dados de um servidor FTP com o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4aba7aadbe92b6c4f0ab417785e230bb6a6823df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60486588"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Mover dados de um servidor FTP através do Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-ftp-connector.md)
> * [Versão 2 (versão atual)](../connector-ftp.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conector FTP no V2](../connector-ftp.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados de um servidor FTP. Ele se baseia no [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma visão geral do movimento de dados com a atividade de cópia.

Pode copiar dados de um servidor FTP para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados suportados como sinks a atividade de cópia, consulte a [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. Atualmente, o Data Factory suporta apenas mover dados de um servidor FTP para outros arquivos de dados, mas não mover dados de outros dados de arquivos para um servidor FTP. Ele oferece suporte tanto no local e servidores FTP na cloud.

> [!NOTE]
> A atividade de cópia não elimina o ficheiro de origem após ser copiada com êxito para o destino. Se tiver de eliminar o ficheiro de origem depois de uma cópia com êxito, criar uma atividade personalizada para eliminar o ficheiro e utilize a atividade no pipeline.

## <a name="enable-connectivity"></a>Ativar a conectividade
Se estiver a mover dados a partir de um **locais** servidor FTP para uma nuvem de arquivo (por exemplo, para o armazenamento de Blobs do Azure) de dados, instalarem e utilizam o Data Management Gateway. O Data Management Gateway é um agente de cliente que está instalado no seu computador no local e permite que os serviços de cloud ligar a um recurso no local. Para obter detalhes, consulte [Data Management Gateway](data-factory-data-management-gateway.md). Para instruções passo a passo sobre como configurar o gateway e utilizá-lo, veja [mover dados entre localizações no local e na cloud](data-factory-move-data-between-onprem-and-cloud.md). Utilizar o gateway para ligar a um servidor FTP, mesmo se o servidor estiver numa infraestrutura do Azure como uma máquina virtual (VM) do serviço (IaaS).

É possível instalar o gateway no mesmo computador no local ou VM do IaaS que o servidor FTP. No entanto, recomendamos que instale o gateway num computador separado ou VM do IaaS para evitar a contenção de recursos e para um melhor desempenho. Ao instalar o gateway numa máquina separada, a máquina deve ser capaz de aceder ao servidor FTP.

## <a name="get-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move os dados de uma origem FTP através de APIs ou ferramentas diferentes.

A maneira mais fácil para criar um pipeline é utilizar o **Assistente de cópia do Data Factory**. Consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para uma passo a passo rápido.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **PowerShell**, **modelo Azure Resource Manager**, **.NET API**e **REST API**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se usar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink:

1. Crie **serviços ligados** para ligar a dados de entrada e saídos armazena à fábrica de dados.
2. Crie **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia.
3. Criar uma **pipeline** com uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) são criadas automaticamente para. Ao utilizar ferramentas ou APIs (exceto a .NET API), define essas entidades do Data Factory, utilizando o formato JSON. Para obter um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de um arquivo de dados FTP, consulte o [exemplo de JSON: Copiar dados do servidor de FTP para BLOBs do Azure](#json-example-copy-data-from-ftp-server-to-azure-blob) seção deste artigo.

> [!NOTE]
> Para obter mais informações sobre formatos de ficheiro e de compressão suportados para utilizar, consulte [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

As secções seguintes fornecem detalhes sobre as propriedades JSON, que são utilizadas para definir entidades do Data Factory específicas para FTP.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela seguinte descreve os elementos JSON específicos a um serviço FTP ligado.

| Propriedade | Descrição | Necessário | Predefinição |
| --- | --- | --- | --- |
| tipo |Defina esta opção como FtpServer. |Sim |&nbsp; |
| anfitrião |Especifique o nome ou endereço IP do servidor de FTP. |Sim |&nbsp; |
| authenticationType |Especifique o tipo de autenticação. |Sim |Básica, anônima |
| o nome de utilizador |Especifique o utilizador quem tem acesso ao servidor de FTP. |Não |&nbsp; |
| password |Especifique a palavra-passe para o utilizador (nome de utilizador). |Não |&nbsp; |
| encryptedCredential |Especifique a credencial encriptada para aceder ao servidor FTP. |Não |&nbsp; |
| gatewayName |Especifique o nome do gateway no Gateway de gestão de dados para ligar a um servidor FTP no local. |Não |&nbsp; |
| porta |Especifique a porta em que o servidor de FTP está a escutar. |Não |21 |
| enableSsl |Especifique se pretende utilizar o FTP por um canal SSL/TLS. |Não |true |
| enableServerCertificateValidation |Especifique se pretende ativar a validação de certificado SSL do servidor quando estiver a utilizar o FTP canal SSL/TLS. |Não |true |

>[!NOTE]
>O conector FTP oferece suporte ao aceder ao servidor FTP sem encriptação ou a encriptação de SSL/TLS explícita; ele não dá suporte a encriptação de SSL/TLS implícita.

### <a name="use-anonymous-authentication"></a>Utilize a autenticação anónima

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Utilize o nome de utilizador e palavra-passe em texto simples para a autenticação básica

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Utilizar a porta, enableSsl, enableServerCertificateValidation

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>Utilizar encryptedCredential para autenticação e de gateway

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte [criar conjuntos de dados](data-factory-create-datasets.md). Seções, como a estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados.

O **typeProperties** secção é diferente para cada tipo de conjunto de dados. Ele fornece informações que são específicas para o tipo de conjunto de dados. O **typeProperties** secção para um conjunto de dados do tipo **partilha de ficheiros** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Subcaminho para a pasta. Utilizar o caráter de escape "\" para carateres especiais na cadeia de caracteres. Ver definições de serviço e o conjunto de dados de exemplo ligada para obter exemplos.<br/><br/>Pode combinar essa propriedade com o **partitionBy** ter caminhos de pastas com base no início do setor e tempos de data de fim. |Sim |
| fileName |Especifique o nome do arquivo na **folderPath** se pretender que a tabela para fazer referência a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros na pasta.<br/><br/>Quando **fileName** não está especificado para um conjunto de dados de saída, o nome do ficheiro gerado é no seguinte formato: <br/><br/>`Data.<Guid>.txt` (Exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| fileFilter |Especificar um filtro para ser usado para selecionar um subconjunto de ficheiros nos **folderPath**, em vez de todos os ficheiros.<br/><br/>Valores permitidos são: `*` (vários carateres) e `?` (caráter individual).<br/><br/>Exemplo 1: `"fileFilter": "*.log"`<br/>Exemplo 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** se aplica a um conjunto de dados de partilha de ficheiros de entrada. Esta propriedade não é suportada com o Hadoop Distributed File System (HDFS). |Não |
| partitionedBy |Utilizado para especificar um dynamic **folderPath** e **fileName** para dados de séries de tempo. Por exemplo, pode especificar uma **folderPath** que é parametrizado por cada hora de dados. |Não |
| Formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte a [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [formato Parquet ](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se pretender copiar ficheiros como estão entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída. |Não |
| Compressão | Especifica o tipo e o nível de compressão dos dados. Tipos suportados são **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**, e os níveis de suporte são **Optimal** e **mais rápida**. Para obter mais informações, consulte [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |
| useBinaryTransfer |Especifique se pretende utilizar o modo de transferência do binário. Os valores são verdadeiros para o modo binário (que é o valor predefinido) e false para ASCII. Esta propriedade só pode ser utilizada quando o tipo de serviço ligado associado é do tipo: FtpServer. |Não |

> [!NOTE]
> **nome de ficheiro** e **fileFilter** não pode ser utilizada em simultâneo.

### <a name="use-the-partionedby-property"></a>Utilize a propriedade partionedBy
Conforme mencionado na secção anterior, pode especificar um dynamic **folderPath** e **fileName** para dados de séries de tempo com o **partitionedBy** propriedade.

Para saber mais sobre conjuntos de dados de séries de tempo, agendamento e setores, veja [criar conjuntos de dados](data-factory-create-datasets.md), [agendamento e execução](data-factory-scheduling-and-execution.md), e [Criar pipelines](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Exemplo 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Neste exemplo, {setor} é substituído pelo valor da variável do sistema SliceStart, fábrica de dados no formato especificado (YYYYMMDDHH). SliceStart refere-se para iniciar a hora do setor. O caminho da pasta é diferente para cada setor. (Por exemplo, deverá/wikisampledataout/2014100103 ou deverá/wikisampledataout/2014100104.)

#### <a name="sample-2"></a>Exemplo 2

```json
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
Neste exemplo, o ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas, que são utilizadas pelos **folderPath** e **fileName** propriedades.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte [Criar pipelines](data-factory-create-pipelines.md). Propriedades, tais como o nome, descrição, entrada e saída de tabelas e as políticas estão disponíveis para todos os tipos de atividades.

Propriedades disponíveis no **typeProperties** secção da atividade, por outro lado, variar com cada tipo de atividade. Para a atividade de cópia, as propriedades do tipo variam consoante os tipos de origens e sinks.

Na atividade de cópia, quando a origem é do tipo **FileSystemSource**, a seguinte propriedade está disponível no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursiva |Indica se os dados são lidos recursivamente das subpastas, ou apenas a partir da pasta especificada. |TRUE, False (predefinição) |Não |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>Exemplo JSON: Copiar dados do servidor de FTP para BLOBs do Azure
Este exemplo mostra como copiar dados de um servidor FTP para o armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados diretamente para qualquer um dos sinks indicados na [arquivos de dados e formatos suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats), utilizando a atividade de cópia na fábrica de dados.

Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), ou [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md):

* Um serviço ligado do tipo [FtpServer](#linked-service-properties)
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [partilha de ficheiros](#dataset-properties)
* Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* R [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

O exemplo copia dados de um servidor FTP para um blob do Azure a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

### <a name="ftp-linked-service"></a>Serviço ligado de FTP

Este exemplo utiliza a autenticação básica, com o nome de utilizador e palavra-passe em texto simples. Também pode utilizar uma das seguintes formas:

* Autenticação anónima
* Autenticação básica com as credenciais encriptadas
* FTP sobre SSL/TLS (FTPS)

Consulte a [serviço ligado de FTP](#linked-service-properties) secção para diferentes tipos de autenticação, pode utilizar.

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```
### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
### <a name="ftp-input-dataset"></a>Conjunto de dados de entrada de FTP

Este conjunto de dados refere-se para a pasta FTP `mysharedfolder` e o ficheiro `test.csv`. O pipeline copia o ficheiro para o destino.

A definição **externo** ao **verdadeiro** informa o serviço Data Factory, que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída do Blob do Azure

Os dados são escritos para um blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente, com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza o ano, mês, dia e partes de horas da hora de início.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
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
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Uma atividade de cópia num pipeline com o sink de origem e de Blobs do sistema de ficheiros

O pipeline contém uma atividade de cópia que está configurada para utilizar os conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **FileSystemSource**e o **sink** tipo está definido como **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
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
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```
> [!NOTE]
> Para mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de sink, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

* Para saber mais sobre os principais fatores que afetará o desempenho de movimento de dados (atividade de cópia) no Data Factory e várias formas para otimizá-lo, consulte a [copie o guia de sintonização de desempenho de atividade e](data-factory-copy-activity-performance.md).

* Para obter instruções passo a passo para criar um pipeline com uma atividade de cópia, consulte a [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
