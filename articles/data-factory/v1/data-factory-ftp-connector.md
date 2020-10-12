---
title: Mover dados de um servidor FTP utilizando a Azure Data Factory
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
ms.openlocfilehash: eeeb122d240d8c3eae4ebe1650f67cf0e4b9dac6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80992050"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Mover dados de um servidor FTP utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-ftp-connector.md)
> * [Versão 2 (versão atual)](../connector-ftp.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector FTP em V2](../connector-ftp.md).

Este artigo explica como utilizar a atividade de cópia na Azure Data Factory para mover dados de um servidor FTP. Baseia-se no artigo de atividades de [movimento de dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade da cópia.

Pode copiar dados de um servidor FTP para qualquer loja de dados de lavatório suportado. Para obter uma lista de lojas de dados suportadas como pias pela atividade de cópia, consulte a tabela [de lojas de dados suportada.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) A Data Factory suporta atualmente apenas mover dados de um servidor FTP para outras lojas de dados, mas não mover dados de outras lojas de dados para um servidor FTP. Suporta servidores FTP no local e em nuvem.

> [!NOTE]
> A atividade de cópia não apaga o ficheiro de origem depois de copiado com sucesso para o destino. Se precisar de eliminar o ficheiro de origem após uma cópia bem sucedida, crie uma atividade personalizada para eliminar o ficheiro e utilize a atividade no pipeline.

## <a name="enable-connectivity"></a>Ativar a conectividade
Se estiver a transferir dados de um servidor FTP no local para uma loja **de dados em** nuvem (por exemplo, para o armazenamento de Azure Blob), instale e utilize o Data Management Gateway. O Data Management Gateway é um agente cliente instalado na sua máquina no local, e permite que os serviços de nuvem se conectem a um recurso no local. Para mais informações, consulte [o Data Management Gateway](data-factory-data-management-gateway.md). Para obter instruções passo a passo sobre a configuração do gateway e a sua utilização, consulte [os dados de deslocação entre as localizações no local e a nuvem](data-factory-move-data-between-onprem-and-cloud.md). Utilize o gateway para ligar a um servidor FTP, mesmo que o servidor esteja numa infraestrutura Azure como uma máquina virtual de serviço (IaaS).

É possível instalar o gateway na mesma máquina no local ou IaaS VM que o servidor FTP. No entanto, recomendamos que instale o gateway numa máquina separada ou iaaS VM para evitar a contenção de recursos e para um melhor desempenho. Quando instalar o gateway numa máquina separada, a máquina deverá ser capaz de aceder ao servidor FTP.

## <a name="get-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de uma fonte FTP utilizando diferentes ferramentas ou APIs.

A forma mais fácil de criar um oleoduto é utilizar o Assistente de **Cópia da Fábrica de Dados.** Ver [Tutorial: Criar um oleoduto utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Visual Studio,** **PowerShell,** **Azure Resource Manager,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, as definições de JSON para estas entidades da Data Factory (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas ou APIs (exceto.NET API), define estas entidades da Data Factory utilizando o formato JSON. Para obter uma amostra com definições JSON para entidades da Data Factory que são usadas para copiar dados de uma loja de dados FTP, consulte o [exemplo JSON: Copiar dados do servidor FTP para a secção blob Azure](#json-example-copy-data-from-ftp-server-to-azure-blob) deste artigo.

> [!NOTE]
> Para obter mais informações sobre formatos de ficheiros e compressão suportados, consulte [os formatos de arquivo e compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Data Factory específicas da FTP.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas
A tabela seguinte descreve elementos JSON específicos de um serviço ligado FTP.

| Propriedade | Descrição | Obrigatório | Predefinição |
| --- | --- | --- | --- |
| tipo |Desempate isto ao FtpServer. |Sim |&nbsp; |
| anfitrião |Especifique o nome ou endereço IP do servidor FTP. |Sim |&nbsp; |
| authenticationType |Especifique o tipo de autenticação. |Sim |Básico, Anónimo |
| nome de utilizador |Especifique o utilizador que tem acesso ao servidor FTP. |Não |&nbsp; |
| palavra-passe |Especifique a palavra-passe para o utilizador (nome de utilizador). |Não |&nbsp; |
| criptografadoCredential |Especifique a credencial encriptada para aceder ao servidor FTP. |Não |&nbsp; |
| gatewayName |Especifique o nome do gateway no Gateway de Gestão de Dados para ligar a um servidor FTP no local. |Não |&nbsp; |
| porta |Especifique a porta na qual o servidor FTP está a ouvir. |Não |21 |
| ativarSl |Especificar se deve utilizar o FTP num canal SSL/TLS. |Não |true |
| enableServerCertificateValidation |Especificar se deve ativar a validação do certificado TLS/SSL do servidor quando estiver a utilizar o ftp sobre o canal SSL/TLS. |Não |true |

>[!NOTE]
>O conector FTP suporta o acesso ao servidor FTP sem encriptação ou encriptação SSL/TLS explícita; não suporta encriptação implícita SSL/TLS.

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

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Use o nome de utilizador e a palavra-passe em texto simples para autenticação básica

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

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Utilizar porta, activarSsl, activarServerCertificateValidation

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

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>Use oCredential encriptado para autenticação e gateway

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [criar conjuntos de dados](data-factory-create-datasets.md). Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados.

A secção **typeProperties** é diferente para cada tipo de conjunto de dados. Fornece informações específicas do tipo de conjunto de dados. A secção **typeProperties** para um conjunto de dados do tipo **FileShare** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| folderPath |Subpata para a pasta. Utilize o personagem de fuga ' \ ' para caracteres especiais na corda. Consulte por exemplo o serviço ligado à amostra e definições de conjunto de dados.<br/><br/>Você pode combinar esta propriedade com **partitionBy** para ter caminhos de pasta baseados em cortes de início e fim de datas. |Sim |
| fileName |Especifique o nome do ficheiro na **pastaPata** se pretender que a tabela se refira a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros da pasta.<br/><br/>Quando **o data de ficheiro** não é especificado para um conjunto de dados de saída, o nome do ficheiro gerado encontra-se no seguinte formato: <br/><br/>`Data.<Guid>.txt` (Exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| ficheiroFiltro |Especifique um filtro a utilizar para selecionar um subconjunto de ficheiros na **pastaPa**, em vez de todos os ficheiros.<br/><br/>Os valores permitidos são: `*` (múltiplos caracteres) e `?` (único carácter).<br/><br/>Exemplo 1: `"fileFilter": "*.log"`<br/>Exemplo 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** é aplicável para um conjunto de dados de FileShare de entrada. Esta propriedade não é suportada com Hadoop Distributed File System (HDFS). |Não |
| partitionedBy |Usado para especificar uma **pasta dinâmicaPath** e **data de arquivoName** para dados da série de tempo. Por exemplo, pode especificar uma **pastaPata** que é parametrizada para cada hora de dados. |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desa um destes valores, o **tipo** de propriedade em formato. Para mais informações, consulte as secções [Text Format](data-factory-supported-file-and-compression-formats.md#text-format), [Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e [Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender copiar ficheiros como estão entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e saída. |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são **GZip,** **Deflate,** **BZip2**e **ZipDeflate,** e os níveis suportados são **ideais** e **mais rápidos.** Para obter mais informações, consulte [os formatos de arquivo e compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |
| useTransfera |Especificar se deve utilizar o modo de transferência binário. Os valores são verdadeiros para o modo binário (este é o valor padrão) e falso para ASCII. Esta propriedade só pode ser utilizada quando o tipo de serviço associado é do tipo de serviço associado: FtpServer. |Não |

> [!NOTE]
> **fileName** e **fileFilter** não podem ser utilizados simultaneamente.

### <a name="use-the-partionedby-property"></a>Use a propriedade partionedBy
Como mencionado na secção anterior, pode especificar uma **pasta dinâmicaPath** e **fileName** para dados da série de tempo com a propriedade **partitionedBy.**

Para conhecer conjuntos de dados, agendamentos e fatias de séries de tempo, consulte [criar conjuntos de dados,](data-factory-create-datasets.md) [agendamento e execução,](data-factory-scheduling-and-execution.md)e [criar oleodutos](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Exemplo 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Neste exemplo, {Slice} é substituído pelo valor da variável do sistema de data factory SliceStart, no formato especificado (YYYYMMDDHH). O SliceStart refere-se à hora de início da fatia. O caminho da pasta é diferente para cada fatia. (Por exemplo, wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.)

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
Neste exemplo, o ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas que são usadas pelas propriedades **de pastaPath** e **fileName.**

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [criar oleodutos.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e políticas estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na secção **de tipoProperties** da atividade, por outro lado, variam com cada tipo de atividade. Para a atividade da cópia, as propriedades do tipo variam consoante os tipos de fontes e pias.

Na atividade de cópia, quando a fonte é do tipo **FileSystemSource,** a seguinte propriedade está disponível na secção **typeProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursivo |Indica se os dados são lidos novamente a partir das sub-dobradinhas, ou apenas a partir da pasta especificada. |Verdadeiro, Falso (padrão) |Não |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>Exemplo JSON: Copiar dados do servidor FTP para Azure Blob
Esta amostra mostra como copiar dados de um servidor FTP para o armazenamento de Azure Blob. No entanto, os dados podem ser copiados diretamente para qualquer um dos lavatórios indicados nas [lojas e formatos de dados suportados,](data-factory-data-movement-activities.md#supported-data-stores-and-formats)utilizando a atividade de cópia na Data Factory.

Os exemplos a seguir fornecem definições JSON de amostra que pode usar para criar um oleoduto utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), ou [PowerShell:](data-factory-copy-activity-tutorial-using-powershell.md)

* Um serviço ligado do tipo [FtpServer](#linked-service-properties)
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [FileShare](#dataset-properties)
* Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Um [oleoduto](data-factory-create-pipelines.md) com atividade de cópia que utiliza [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

A amostra copia dados de um servidor FTP para uma bolha Azure a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

### <a name="ftp-linked-service"></a>Serviço ligado FTP

Este exemplo utiliza a autenticação básica, com o nome de utilizador e a palavra-passe em texto simples. Também pode utilizar uma das seguintes formas:

* Autenticação anónima
* Autenticação básica com credenciais encriptadas
* FTP sobre SSL/TLS (FTPS)

Consulte a secção [de serviços vinculada FTP](#linked-service-properties) para diferentes tipos de autenticação que pode utilizar.

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
### <a name="ftp-input-dataset"></a>Conjunto de dados de entrada FTP

Este conjunto de dados refere-se à pasta `mysharedfolder` FTP e ao ficheiro `test.csv` . O oleoduto copia o ficheiro para o destino.

A definição **externa** à **verdadeira** informa o serviço Data Factory de que o conjunto de dados é externo à fábrica de dados, e não é produzido por uma atividade na fábrica de dados.

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

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente, com base na hora de início da fatia que está a ser processada. O percurso da pasta utiliza o ano, mês, dia e horas partes da hora de início.

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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Uma atividade de cópia em um oleoduto com fonte de sistema de arquivo e pia blob

O pipeline contém uma atividade de cópia que está configurada para utilizar os conjuntos de dados de entrada e saída, e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **FileSystemSource,** e o tipo **de pia** está definido para **BlobSink**.

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
> Para mapear colunas de conjunto de dados de origem para colunas a partir do conjunto de dados da pia, consulte [as colunas de conjunto de dados de mapeamento na Azure Data Factory](data-factory-map-columns.md).

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

* Para conhecer os factores-chave que impactam o desempenho do movimento de dados (atividade de cópia) na Data Factory, e várias formas de otimizá-lo, consulte o guia de [desempenho e afinação](data-factory-copy-activity-performance.md)da atividade copy .

* Para obter instruções passo a passo para a criação de um oleoduto com uma atividade de cópia, consulte o tutorial de atividade da [Cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
