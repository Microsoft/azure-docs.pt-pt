---
title: Mova dados de um servidor FTP utilizando a Azure Data Factory
description: Saiba como mover dados de um servidor FTP utilizando a Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 55c8bf2210eb0990a91aeff1f90e4af4db2c22ab
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78387687"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Mova dados de um servidor FTP utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-ftp-connector.md)
> * [Versão 2 (versão atual)](../connector-ftp.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector FTP em V2](../connector-ftp.md).

Este artigo explica como usar a atividade de cópia na Azure Data Factory para mover dados de um servidor FTP. Baseia-se no artigo de atividades do [movimento Data,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade da cópia.

Pode copiar dados de um servidor FTP para qualquer loja de dados de sink suportado. Para obter uma lista de lojas de dados suportadas como pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) A Data Factory suporta atualmente apenas a transferência de dados de um servidor FTP para outras lojas de dados, mas não transfere dados de outras lojas de dados para um servidor FTP. Suporta tanto no local como nos servidores FTP em nuvem.

> [!NOTE]
> A atividade da cópia não elimina o ficheiro fonte depois de ser copiado com sucesso para o destino. Se necessitar de eliminar o ficheiro fonte após uma cópia bem sucedida, crie uma atividade personalizada para apagar o ficheiro e utilize a atividade no pipeline.

## <a name="enable-connectivity"></a>Ativar conectividade
Se estiver a mover dados de um servidor FTP **no local** para uma loja de dados em nuvem (por exemplo, para armazenamento Azure Blob), instale e utilize o Portal de Gestão de Dados. O Portal de Gestão de Dados é um agente cliente que está instalado na sua máquina no local, e permite que os serviços de nuvem se conectem a um recurso no local. Para mais detalhes, consulte [Data Management Gateway](data-factory-data-management-gateway.md). Para obter instruções passo a passo sobre a configuração do portal e a sua utilização, consulte os dados móveis entre as [localizações no local e a nuvem](data-factory-move-data-between-onprem-and-cloud.md). Utiliza a porta de entrada para se ligar a um servidor FTP, mesmo que o servidor esteja numa infraestrutura Azure como máquina virtual de serviço (IaaS).

É possível instalar o portal na mesma máquina no local ou IaaS VM como o servidor FTP. No entanto, recomendamos que instale o gateway numa máquina separada ou Em VM IaaS para evitar a contenção de recursos e para um melhor desempenho. Quando instalar o portal numa máquina separada, a máquina deverá poder aceder ao servidor FTP.

## <a name="get-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de uma fonte FTP utilizando diferentes ferramentas ou APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**da Fábrica de Dados . Consulte [Tutorial: Crie um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Visual Studio,** **PowerShell,** **Modelo De Gestor de Recursos Azure,** **.NET API**, e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que transe os dados de uma loja de dados de origem para uma loja de dados de sumidouro:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos** de dados para representar dados de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que tome um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, as definições jSON para estas entidades da Fábrica de Dados (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas ou APIs (exceto .NET API), define estas entidades da Fábrica de Dados utilizando o formato JSON. Para obter uma amostra com definições JSON para entidades da Data Factory que são usadas para copiar dados de uma loja de dados FTP, consulte o [exemplo JSON: Copiar dados do servidor FTP para](#json-example-copy-data-from-ftp-server-to-azure-blob) a secção de blob Azure deste artigo.

> [!NOTE]
> Para mais detalhes sobre formatos de ficheiros e compressão suportados para usar, consulte [formatos de ficheiroe de compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades data Factory específicas ao FTP.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela seguinte descreve elementos JSON específicos de um serviço ligado ao FTP.

| Propriedade | Descrição | Necessário | Predefinição |
| --- | --- | --- | --- |
| tipo |Derete isto para ftpServer. |Sim |&nbsp; |
| host |Especifique o nome ou endereço IP do servidor FTP. |Sim |&nbsp; |
| authenticationType |Especifique o tipo de autenticação. |Sim |Básico, Anónimo |
| o nome de utilizador |Especifique o utilizador que tem acesso ao servidor FTP. |Não |&nbsp; |
| palavra-passe |Especifique a palavra-passe para o utilizador (nome de utilizador). |Não |&nbsp; |
| encryptedCredential |Especifique a credencial encriptada para aceder ao servidor FTP. |Não |&nbsp; |
| gatewayName |Especifique o nome da porta de entrada no Portal de Gestão de Dados para ligar a um servidor FTP no local. |Não |&nbsp; |
| porta |Especifique a porta em que o servidor FTP está a ouvir. |Não |21 |
| enableSsl |Especifique se utilizar ftp sobre um canal SSL/TLS. |Não |true |
| enableServerCertificateValidation |Especifique se ativa a validação do certificado SSL do servidor quando estiver a utilizar ftp através do canal SSL/TLS. |Não |true |

>[!NOTE]
>O conector FTP suporta aceder ao servidor FTP sem encriptação ou encriptação explícita SSL/TLS; não suporta encriptação implícita sSL/TLS.

### <a name="use-anonymous-authentication"></a>Utilizar a autenticação anónima

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

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Utilize o nome de utilizador e a palavra-passe em texto simples para autenticação básica

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

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Utilize a porta, o enableSsl, o enableServerCertificateValidação

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

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>Utilize Credential encriptado para autenticação e gateway

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
Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Criar conjuntos](data-factory-create-datasets.md)de dados . Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjuntode dados.

A secção **typeProperties** é diferente para cada tipo de conjunto de dados. Fornece informações específicas ao tipo de conjunto de dados. A secção **TypeProperties** para um conjunto de dados do tipo **FileShare** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Subpate para a pasta. Use o personagem de fuga ' \ ' para caracteres especiais na corda. Consulte as definições de serviço ligados à amostra e definições de conjunto de dados, por exemplo.<br/><br/>Pode combinar esta propriedade com **partiçãoBy** para ter caminhos de pastas baseados no início da fatia e data de fim. |Sim |
| fileName |Especifique o nome do ficheiro na **pastaPath** se pretender que a tabela se refira a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros da pasta.<br/><br/>Quando o nome do **ficheiro** não é especificado para um conjunto de dados de saída, o nome do ficheiro gerado está no seguinte formato: <br/><br/>`Data.<Guid>.txt` (Exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| fileFilter |Especifique um filtro a utilizar para selecionar um subconjunto de ficheiros na **pastaPath**, em vez de todos os ficheiros.<br/><br/>Os valores permitidos são: `*` (múltiplos caracteres) e `?` (personagem único).<br/><br/>Exemplo 1: `"fileFilter": "*.log"`<br/>Exemplo 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** é aplicável para um conjunto de dados de FileShare de entrada. Esta propriedade não é suportada com hadoop Sistema de Ficheiros Distribuídos (HDFS). |Não |
| divididoBy |Usado para especificar uma **pasta dinâmicaPath** e **fileName** para dados da série de tempo. Por exemplo, pode especificar uma **pastaPath** que é parametrizada para cada hora de dados. |Não |
| format | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desloque a propriedade **tipo** em formato a um destes valores. Para mais informações, consulte as secções de [Formato Texto,](data-factory-supported-file-and-compression-formats.md#text-format) [Formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e [Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender copiar ficheiros como estão entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e de saída. |Não |
| compression | Especifica o tipo e o nível de compressão dos dados. Os tipos suportados são **GZip,** **Deflate,** **BZip2**e **ZipDeflate,** e os níveis suportados são **Optimal** e **Fastest**. Para mais informações, consulte [formatos de ficheiroe de compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |
| useBinaryTransfer |Especifique se utiliza o modo de transferência binário. Os valores são verdadeiros para o modo binário (este é o valor padrão) e falso para ASCII. Esta propriedade só pode ser utilizada quando o tipo de serviço associado é de tipo: FtpServer. |Não |

> [!NOTE]
> **fileName** and **fileFilter** não pode ser utilizado simultaneamente.

### <a name="use-the-partionedby-property"></a>Use a propriedade partionedBy
Tal como mencionado na secção anterior, pode especificar uma **pasta dinâmicaPath** e **fileName** para dados de séries de tempo com a propriedade **partitionbyBy.**

Para conhecer conjuntos de dados de séries de tempo, agendamento e fatias, consulte [Criar conjuntos](data-factory-create-datasets.md)de dados, [agendamento e execução](data-factory-scheduling-and-execution.md)e [criar oleodutos.](data-factory-create-pipelines.md)

#### <a name="sample-1"></a>Exemplo 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Neste exemplo, {Slice} é substituído pelo valor da variável sliceStart do sistema data Factory, no formato especificado (YYYYMMDDHH). O SliceStart refere-se à hora de início da fatia. O caminho da pasta é diferente para cada fatia. (Por exemplo, wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.)

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
Neste exemplo, o ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas que são usadas pelas **propriedades da pastaPath** e **fileName.**

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de secções e propriedades disponíveis para definir [atividades, consulte Criar oleodutos.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e políticas estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na secção **tipoPropriedades** da atividade, por outro lado, variam com cada tipo de atividade. Para a atividade de cópia, as propriedades do tipo variam consoante os tipos de fontes e pias.

Na atividade de cópia, quando a fonte é do tipo **FileSystemSource,** a seguinte propriedade está disponível na secção **typeProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursive |Indica se os dados são lidos recursivamente a partir das subpastas, ou apenas a partir da pasta especificada. |Verdade, Falso (padrão) |Não |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>Exemplo jSON: Copiar dados do servidor FTP para O Blob Azure
Esta amostra mostra como copiar dados de um servidor FTP para o armazenamento de Azure Blob. No entanto, os dados podem ser copiados diretamente para qualquer um dos lavatórios indicados nas [lojas e formatos de dados suportados,](data-factory-data-movement-activities.md#supported-data-stores-and-formats)utilizando a atividade de cópia na Data Factory.

Os seguintes exemplos fornecem definições JSON de amostra que pode usar para criar um pipeline utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), ou [PowerShell:](data-factory-copy-activity-tutorial-using-powershell.md)

* Um serviço ligado do tipo [FtpServer](#linked-service-properties)
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [FileShare](#dataset-properties)
* Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

A amostra copia dados de um servidor FTP para uma bolha Azure a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

### <a name="ftp-linked-service"></a>Serviço ligado ftp

Este exemplo utiliza a autenticação básica, com o nome de utilizador e a palavra-passe em texto simples. Também pode utilizar uma das seguintes formas:

* Autenticação anónima
* Autenticação básica com credenciais encriptadas
* FTP sobre SSL/TLS (FTPS)

Consulte a secção de [serviço ligada ftp](#linked-service-properties) para obter diferentes tipos de autenticação que possa utilizar.

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
### <a name="ftp-input-dataset"></a>Conjunto de dados de entrada ftp

Este conjunto de dados refere-se à pasta FTP `mysharedfolder` e `test.csv`de ficheiros . O oleoduto copia o ficheiro para o destino.

A definição **externa** a **verdadeira** informa o serviço Data Factory de que o conjunto de dados é externo à fábrica de dados, e não é produzido por uma atividade na fábrica de dados.

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

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente, com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza o ano, mês, dia e horas partes da hora de início.

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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Uma atividade de cópia em um pipeline com fonte de sistema de arquivo e pia blob

O pipeline contém uma atividade de cópia configurada para utilizar os conjuntos de dados de entrada e saída, e está programado para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **FileSystemSource**, e o tipo **de pia** é definido para **BlobSink**.

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
> Para mapear colunas desde o conjunto de dados de origem até colunas a partir de conjunto de dados de sumidouro, consulte [colunas de conjunto de dados de mapeamento na Fábrica](data-factory-map-columns.md)de Dados Azure .

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

* Para conhecer os factores-chave que impactam o desempenho do movimento de dados (atividade de cópia) na Data Factory, e várias formas de o otimizar, consulte o desempenho da atividade da Cópia e o guia de [afinação.](data-factory-copy-activity-performance.md)

* Para obter instruções passo a passo para criar um pipeline com uma atividade de cópia, consulte o tutorial de [atividade da Cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
