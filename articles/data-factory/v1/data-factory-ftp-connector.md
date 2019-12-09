---
title: Mover dados de um servidor FTP usando Azure Data Factory
description: Saiba mais sobre como mover dados de um servidor FTP usando Azure Data Factory.
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
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927164"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Mover dados de um servidor FTP usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-ftp-connector.md)
> * [Versão 2 (versão atual)](../connector-ftp.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [conector de FTP na v2](../connector-ftp.md).

Este artigo explica como usar a atividade de cópia no Azure Data Factory para mover dados de um servidor FTP. Ele se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados de um servidor FTP para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . O Data Factory atualmente dá suporte apenas à movimentação de dados de um servidor FTP para outros armazenamentos de dados, mas não à movimentação de dados de outros armazenamentos de dados para um servidor FTP. Ele dá suporte a servidores FTP locais e na nuvem.

> [!NOTE]
> A atividade de cópia não exclui o arquivo de origem depois que ele é copiado com êxito para o destino. Se você precisar excluir o arquivo de origem após uma cópia bem-sucedida, crie uma atividade personalizada para excluir o arquivo e use a atividade no pipeline.

## <a name="enable-connectivity"></a>Habilitar conectividade
Se você estiver movendo dados de um servidor FTP **local** para um armazenamento de dados de nuvem (por exemplo, para o armazenamento de BLOBs do Azure), instale e use gerenciamento de dados gateway. O gateway de Gerenciamento de Dados é um agente cliente que está instalado em seu computador local e permite que os serviços de nuvem se conectem a um recurso local. Para obter detalhes, consulte [Gerenciamento de dados gateway](data-factory-data-management-gateway.md). Para obter instruções detalhadas sobre como configurar o gateway e usá-lo, consulte [movendo dados entre os locais e a nuvem local](data-factory-move-data-between-onprem-and-cloud.md). Você usa o gateway para se conectar a um servidor FTP, mesmo se o servidor estiver em uma VM (máquina virtual) de IaaS (infraestrutura como serviço) do Azure.

É possível instalar o gateway no mesmo computador local ou na VM IaaS como o servidor FTP. No entanto, recomendamos que você instale o gateway em um computador separado ou em uma VM IaaS para evitar a contenção de recursos e para um melhor desempenho. Quando você instala o gateway em um computador separado, o computador deve ser capaz de acessar o servidor FTP.

## <a name="get-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que move dados de uma origem de FTP usando diferentes ferramentas ou APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de data Factory Copy**. Consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter uma explicação rápida.

Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **PowerShell**, **Azure Resource Manager template**, **API .net**e **API REST**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory.
2. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída.

Quando você usa o assistente, as definições de JSON para essas entidades de Data Factory (serviços vinculados, conjuntos de valores e o Pipeline) são criadas automaticamente para você. Ao usar ferramentas ou APIs (exceto a API .NET), você define essas entidades de Data Factory usando o formato JSON. Para obter um exemplo com definições de JSON para Data Factory entidades usadas para copiar dados de um armazenamento de dados FTP, consulte a seção [exemplo de JSON: copiar dados do servidor FTP para o blob do Azure](#json-example-copy-data-from-ftp-server-to-azure-blob) deste artigo.

> [!NOTE]
> Para obter detalhes sobre os formatos de arquivo e compactação com suporte a serem usados, consulte [formatos de arquivo e compactação em Azure data Factory](data-factory-supported-file-and-compression-formats.md).

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas ao FTP.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela a seguir descreve os elementos JSON específicos para um serviço vinculado de FTP.

| Propriedade | Descrição | Obrigatório | Predefinição |
| --- | --- | --- | --- |
| tipo |Defina isso como FtpServer. |Sim |&nbsp; |
| anfitrião |Especifique o nome ou endereço IP do servidor FTP. |Sim |&nbsp; |
| authenticationType |Especifique o tipo de autenticação. |Sim |Básico, anônimo |
| o nome de utilizador |Especifique o usuário que tem acesso ao servidor FTP. |Não |&nbsp; |
| palavra-passe |Especifique a senha para o usuário (username). |Não |&nbsp; |
| encryptedCredential |Especifique a credencial criptografada para acessar o servidor FTP. |Não |&nbsp; |
| gatewayName |Especifique o nome do gateway no gateway Gerenciamento de Dados para se conectar a um servidor FTP local. |Não |&nbsp; |
| porta |Especifique a porta na qual o servidor FTP está escutando. |Não |21 |
| enableSsl |Especifique se deseja usar o FTP em um canal SSL/TLS. |Não |true |
| enableServerCertificateValidation |Especifique se a validação de certificado SSL do servidor deve ser habilitada quando você estiver usando o canal FTP sobre SSL/TLS. |Não |true |

>[!NOTE]
>O conector de FTP dá suporte ao acesso ao servidor FTP sem criptografia ou criptografia SSL/TLS explícita; Ele não dá suporte à criptografia SSL/TLS implícita.

### <a name="use-anonymous-authentication"></a>Usar autenticação anônima

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

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Usar nome de usuário e senha em texto sem formatação para autenticação básica

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

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Usar porta, enableSsl, enableServerCertificateValidation

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

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>Usar o encryptedCredential para autenticação e gateway

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
Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte [criando conjuntos](data-factory-create-datasets.md)de os. As seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados.

A seção **typeproperties** é diferente para cada tipo de conjunto de texto. Ele fornece informações específicas para o tipo de conjunto de dados. A seção **typeproperties** para um conjunto de um DataSet do tipo **FileShare** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| folderPath |Subcaminho da pasta. Use o caractere de escape ' \ ' para caracteres especiais na cadeia de caracteres. Consulte amostra de serviço vinculado e definições de conjunto de exemplos.<br/><br/>Você pode combinar essa propriedade com **partitionBy** para ter caminhos de pasta com base nas horas de início e de término da fatia. |Sim |
| fileName |Especifique o nome do arquivo no **FolderPath** se você quiser que a tabela se refira a um arquivo específico na pasta. Se você não especificar nenhum valor para essa propriedade, a tabela apontará para todos os arquivos na pasta.<br/><br/>Quando **filename** não for especificado para um conjunto de resultados de saída, o nome do arquivo gerado estará no seguinte formato: <br/><br/>`Data.<Guid>.txt` (exemplo: Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt) |Não |
| fileFilter |Especifique um filtro a ser usado para selecionar um subconjunto de arquivos no **FolderPath**, em vez de todos os arquivos.<br/><br/>Os valores permitidos são: `*` (vários caracteres) e `?` (caractere único).<br/><br/>Exemplo 1: `"fileFilter": "*.log"`<br/>Exemplo 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> **FileFilter** é aplicável a um conjunto de dados de FileShare de entrada. Essa propriedade não tem suporte com o Sistema de Arquivos Distribuído do Hadoop (HDFS). |Não |
| partitionedBy |Usado para especificar um **FolderPath** e um **nome de arquivo** dinâmico para dados de série temporal. Por exemplo, você pode especificar um **FolderPath** que é parametrizado para cada hora dos dados. |Não |
| format | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte as seções [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), formato [Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e [formato parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Se você quiser copiar arquivos como estão entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas definições de conjunto de dados de entrada e saída. |Não |
| compression | Especifica o tipo e o nível de compressão dos dados. Os tipos com suporte são **gzip**, **deflate**, **bzip2**e **ZipDeflate**, e os níveis com suporte são **ideais** e **mais rápidos**. Para obter mais informações, consulte [formatos de arquivo e compactação em Azure data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |
| useBinaryTransfer |Especifique se deseja usar o modo de transferência binária. Os valores são verdadeiros para o modo binário (esse é o valor padrão) e false para ASCII. Essa propriedade só pode ser usada quando o tipo de serviço vinculado associado for do tipo: FtpServer. |Não |

> [!NOTE]
> **filename** e **FileFilter** não podem ser usados simultaneamente.

### <a name="use-the-partionedby-property"></a>Usar a propriedade partionedBy
Conforme mencionado na seção anterior, você pode especificar um **FolderPath** e um **nome de arquivo** dinâmico para dados de série temporal com a propriedade **partitionedBy** .

Para saber mais sobre conjuntos de data, agendamento e fatias de série temporal, confira [criando conjuntos](data-factory-create-datasets.md)de, [agendamento e execução](data-factory-scheduling-and-execution.md)e [criando pipelines](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Exemplo 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Neste exemplo, {Slice} é substituído pelo valor de Data Factory variável de sistema SliceStart, no formato especificado (AAAAMMDDHH). O SliceStart refere-se à hora de início da fatia. O caminho da pasta é diferente para cada fatia. (Por exemplo, wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.)

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
Neste exemplo, o ano, o mês, o dia e a hora de SliceStart são extraídos em variáveis separadas que são usadas pelas propriedades **FolderPath** e **filename** .

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte [criando pipelines](data-factory-create-pipelines.md). Propriedades como nome, descrição, tabelas de entrada e saída e políticas estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção **typeproperties** da atividade, por outro lado, variam de acordo com cada tipo de atividade. Para a atividade de cópia, as propriedades de tipo variam de acordo com os tipos de fontes e coletores.

Na atividade de cópia, quando a origem é do tipo **FileSystemName**, a seguinte propriedade está disponível na seção **typeproperties** :

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursive |Indica se os dados são lidos recursivamente a partir das subpastas ou apenas da pasta especificada. |True, false (padrão) |Não |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>Exemplo de JSON: copiar dados do servidor FTP para o blob do Azure
Este exemplo mostra como copiar dados de um servidor FTP para o armazenamento de BLOBs do Azure. No entanto, os dados podem ser copiados diretamente para qualquer um dos coletores declarados nos [armazenamentos de dados e formatos com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats), usando a atividade de cópia no data Factory.

Os exemplos a seguir fornecem exemplos de definições de JSON que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)ou o [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md):

* Um serviço vinculado do tipo [ftpserver](#linked-service-properties)
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [FileShare](#dataset-properties)
* Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Um [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que usa o [sqlsystemname](#copy-activity-properties) e o [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

O exemplo copia dados de um servidor FTP para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

### <a name="ftp-linked-service"></a>Serviço vinculado de FTP

Este exemplo usa a autenticação básica, com o nome de usuário e a senha em texto sem formatação. Você também pode usar uma das seguintes maneiras:

* Autenticação anônima
* Autenticação básica com credenciais criptografadas
* FTP sobre SSL/TLS (FTPS)

Consulte a seção [serviço vinculado de FTP](#linked-service-properties) para diferentes tipos de autenticação que você pode usar.

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

Esse conjunto de um refere-se à pasta FTP `mysharedfolder` e `test.csv`de arquivo. O pipeline copia o arquivo para o destino.

A configuração de **external** como **true** informa ao serviço de data Factory que o dataset é externo ao data Factory e não é produzido por uma atividade no data Factory.

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

Os dados são gravados em um novo BLOB a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente, com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Uma atividade de cópia em um pipeline com origem do sistema de arquivos e coletor de BLOB

O pipeline contém uma atividade de cópia que é configurada para usar os conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** é definido como **FileSystemName**e o tipo de **coletor** é definido como **BlobSink**.

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
> Para mapear colunas do conjunto de fonte de origem para colunas do conjunto de coleta, consulte [mapeando colunas do conjunto de linhas no Azure data Factory](data-factory-map-columns.md).

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

* Para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Data Factory e várias maneiras de otimizá-lo, consulte o [Guia de desempenho e ajuste da atividade de cópia](data-factory-copy-activity-performance.md).

* Para obter as instruções passo a passo para criar um pipeline com uma atividade de cópia, consulte o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
