---
title: Mover dados do servidor SFTP com o Azure Data Factory | Documentos da Microsoft
description: Saiba mais sobre como mover dados de um local ou no servidor SFTP na cloud com o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 55a829f2ed32152d14ef637c8079f7af8c487bea
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58097649"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Mover dados de um servidor SFTP com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-sftp-connector.md)
> * [Versão 2 (versão atual)](../connector-sftp.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [SFTPconnector no V2](../connector-sftp.md).

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para mover dados de um servidor SFTP no local/nuvem para um arquivo de dados sink suportado. Este artigo baseia-se a [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo apresenta uma visão geral de movimento de dados com a atividade de cópia e a lista dos arquivos de dados suportados como origens/sinks.

O Data factory suporta, atualmente, apenas mover dados de um servidor SFTP para outros arquivos de dados, mas não para mover dados de outros arquivos de dados para um servidor SFTP. Ele oferece suporte tanto no local e servidores SFTP na cloud.

> [!NOTE]
> Atividade de cópia não elimina o ficheiro de origem após ser copiada com êxito para o destino. Se tiver de eliminar o ficheiro de origem depois de uma cópia com êxito, crie uma atividade personalizada para excluir o arquivo e usar a atividade no pipeline.

## <a name="supported-scenarios-and-authentication-types"></a>Cenários suportados e tipos de autenticação
Pode utilizar este conector SFTP para copiar dados a partir **ambos os servidores SFTP e servidores SFTP no local da cloud**. **Básica** e **SshPublicKey** tipos de autenticação são suportados quando ligar ao servidor SFTP.

Quando se copiam dados a partir de um servidor SFTP no local, tem de instalar um Gateway de gestão de dados no local ambiente/Azure VM. Ver [Data Management Gateway](data-factory-data-management-gateway.md) para obter detalhes sobre o gateway. Ver [mover dados entre localizações no local e na cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter instruções passo a passo sobre como instalar o gateway e usá-lo.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move os dados de uma origem SFTP utilizando ferramentas/APIs diferentes.

- A maneira mais fácil para criar um pipeline é utilizar o **Assistente para copiar**. Consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para um rápido passo a passo sobre como criar um pipeline com o Assistente para copiar dados.

- Também pode utilizar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**e  **REST API**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. Para obter exemplos de JSON copiar dados do servidor SFTP para armazenamento de Blobs do Azure, consulte [exemplo de JSON: Copiar dados do servidor SFTP para BLOBs do Azure](#json-example-copy-data-from-sftp-server-to-azure-blob) seção deste artigo.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela seguinte fornece uma descrição para elementos JSON específicos ao serviço ligado de FTP.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo | A propriedade de tipo deve ser definida como `Sftp`. |Sim |
| anfitrião | Nome ou endereço IP do servidor SFTP. |Sim |
| porta |Porta em que o servidor SFTP está a escutar. O valor predefinido é: 21 |Não |
| authenticationType |Especifique o tipo de autenticação. Valores permitidos: **Basic**, **SshPublicKey**. <br><br> Consulte a [usando a autenticação básica](#using-basic-authentication) e [a utilizar o SSH autenticação de chave pública](#using-ssh-public-key-authentication) secções em mais propriedades e exemplos de JSON, respetivamente. |Sim |
| skipHostKeyValidation | Especifique se pretende ignorar a validação da chave de anfitrião. | Não. O valor predefinido: Falso |
| hostKeyFingerprint | Especifique a impressão digital da chave de anfitrião. | Sim se o `skipHostKeyValidation` é definido como false.  |
| gatewayName |Nome do Gateway de gestão de dados para ligar a um servidor SFTP no local. | Sim se copiam dados a partir de um servidor SFTP no local. |
| encryptedCredential | Credencial encriptada para aceder ao servidor SFTP. Gerado automaticamente quando especifica a autenticação básica (nome de utilizador + palavra-passe) ou SshPublicKey autenticação (nome de utilizador + caminho da chave privado ou conteúdo) no Assistente de cópia ou a caixa de diálogo de pop-up do ClickOnce. | Não. Aplicam-se apenas quando se copiam dados a partir de um servidor SFTP no local. |

### <a name="using-basic-authentication"></a>Usando a autenticação básica

Para utilizar a autenticação básica, defina `authenticationType` como `Basic`e especifique as seguintes propriedades além do conector do SFTP genéricas introduzidas na última secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| o nome de utilizador | Utilizador que tenha acesso ao servidor SFTP. |Sim |
| palavra-passe | Palavra-passe para o utilizador (nome de utilizador). | Sim |

#### <a name="example-basic-authentication"></a>Exemplo: Autenticação básica
```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Exemplo: Autenticação básica com a credencial encriptada

```JSON
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
      }
}
```

### <a name="using-ssh-public-key-authentication"></a>Utilizar a autenticação de chave pública SSH

Para utilizar a autenticação de chave pública SSH, defina `authenticationType` como `SshPublicKey`e especifique as seguintes propriedades além do conector do SFTP genéricas introduzidas na última secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| o nome de utilizador |Utilizador que tenha acesso ao servidor SFTP |Sim |
| privateKeyPath | Especifique um caminho absoluto para o ficheiro de chave privada pode aceder a esse gateway. | Especifique a `privateKeyPath` ou `privateKeyContent`. <br><br> Aplicam-se apenas quando se copiam dados a partir de um servidor SFTP no local. |
| privateKeyContent | Uma cadeia de caracteres serializada do conteúdo de chave privada. O Assistente de cópia pode ler o ficheiro de chave privada e extrair o conteúdo da chave privado automaticamente. Se estiver a utilizar qualquer outra ferramenta/SDK, use a propriedade privateKeyPath. | Especifique a `privateKeyPath` ou `privateKeyContent`. |
| passPhrase | Especifique a pass frase/palavra-passe para desencriptar a chave privada, se o ficheiro de chave estiver protegido por uma frase de acesso. | Sim, se o ficheiro de chave privada está protegido por uma frase de acesso. |

> [!NOTE]
> Conector do SFTP oferece suporte a chave RSA/DSA OpenSSH. Certifique-se de que o conteúdo do ficheiro de chave começa com "---BEGIN [RSA/DSA] PRIVATE KEY---". Se o ficheiro de chave privada é um ficheiro de formato ppk, utilize a ferramenta Putty para converter de .ppk OpenSSH formato.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Exemplo: Autenticação de SshPublicKey com o caminho do ficheiro chave privado

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Exemplo: Autenticação de SshPublicKey com o conteúdo da chave privado

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [criar conjuntos de dados](data-factory-create-datasets.md) artigo. Seções, como a estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados.

O **typeProperties** secção é diferente para cada tipo de conjunto de dados. Ele fornece informações que são específicas para o tipo de conjunto de dados. Os typeProperties secção para um conjunto de dados do tipo **partilha de ficheiros** conjunto de dados tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho de sub-rotina para a pasta. Utilizar o caráter de escape "\" para carateres especiais na cadeia de caracteres. Ver definições de serviço e o conjunto de dados de exemplo ligada para obter exemplos.<br/><br/>Pode combinar essa propriedade com o **partitionBy** ter pasta caminhos baseados no setor de início/fim datas-horas. |Sim |
| fileName |Especifique o nome do arquivo na **folderPath** se pretender que a tabela para fazer referência a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros na pasta.<br/><br/>Quando o nome de ficheiro não está especificado para um conjunto de dados de saída, o nome do ficheiro gerado seria a seguir este formato: <br/><br/>Dados. <Guid>. txt (exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| fileFilter |Especifique um filtro para ser usado para selecionar um subconjunto de ficheiros em folderPath em vez de todos os ficheiros.<br/><br/>Valores permitidos são: `*` (vários carateres) e `?` (caráter individual).<br/><br/>Exemplos 1: `"fileFilter": "*.log"`<br/>Exemplo 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter se aplica a um conjunto de dados de partilha de ficheiros de entrada. Esta propriedade não é suportada com HDFS. |Não |
| partitionedBy |partitionedBy pode ser utilizado para especificar um folderPath dinâmica, o nome de ficheiro para dados de séries de tempo. Por exemplo, folderPath parametrizado por cada hora de dados. |Não |
| Formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se quiser **copiar ficheiros como-é** entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída. |Não |
| Compressão | Especifica o tipo e o nível de compressão dos dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Ideal** e **mais rápida**. Para obter mais informações, consulte [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |
| useBinaryTransfer |Especifique se utilizar o modo de transferência do binário. Verdadeiro para o modo de binário e ASCII FALSO. Valor predefinido: VERDADEIRO. Esta propriedade só pode ser utilizada quando o tipo de serviço ligado associado é do tipo: FtpServer. |Não |

> [!NOTE]
> nome de ficheiro e fileFilter não podem ser utilizadas em simultâneo.

### <a name="using-partionedby-property"></a>Usando a propriedade de partionedBy
Conforme mencionado na secção anterior, pode especificar um folderPath dinâmica, o nome de ficheiro para dados de séries de tempo com partitionedBy. Pode fazê-lo com as macros do Data Factory e a variável de sistema SliceStart, SliceEnd que indicam o período de tempo de lógica de um setor de dados fornecido.

Para saber mais sobre conjuntos de dados de séries de tempo, agendamento e setores, veja [criar conjuntos de dados](data-factory-create-datasets.md), [agendamento e execução](data-factory-scheduling-and-execution.md), e [criar Pipelines](data-factory-create-pipelines.md) artigos.

#### <a name="sample-1"></a>Exemplo 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Neste exemplo {setor} é substituído pelo valor da variável do sistema SliceStart fábrica de dados no formato (YYYYMMDDHH) especificado. SliceStart refere-se para iniciar a hora do setor. FolderPath é diferente para cada setor. Exemplo: deverá/wikisampledataout/2014100103 ou deverá/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Exemplo 2:

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
Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas, que são utilizadas pelas propriedades folderPath e nome de ficheiro.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição, entrada e saída de tabelas e as políticas estão disponíveis para todos os tipos de atividades.

Ao passo que as propriedades disponíveis na secção typeProperties da atividade variar de acordo com cada tipo de atividade. Para a atividade de cópia, as propriedades do tipo variam consoante os tipos de origens e sinks.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Formatos de ficheiro e de compressão suportados
Ver [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artigo em detalhes.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>Exemplo JSON: Copiar dados do servidor SFTP para BLOBs do Azure
O exemplo seguinte fornece definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de origem SFTP para armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das origens qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure Data Factory.

> [!IMPORTANT]
> Este exemplo fornece trechos JSON. Não inclui instruções passo a passo para criar a fábrica de dados. Ver [mover dados entre localizações no local e na cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter instruções passo a passo.

O exemplo possui as seguintes entidades do data factory:

* Um serviço ligado do tipo [sftp](#linked-service-properties).
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [partilha de ficheiros](#dataset-properties).
* Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* R [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de um servidor SFTP para um blob do Azure a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

**Serviço ligado de SFTP**

Este exemplo utiliza a autenticação básica com o nome de utilizador e palavra-passe em texto simples. Também pode utilizar uma das seguintes formas:

* Autenticação básica com as credenciais encriptadas
* Autenticação de chave pública SSH

Ver [serviço ligado de FTP](#linked-service-properties) secção para diferentes tipos de autenticação, pode utilizar.

```JSON

{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "myuser",
            "password": "mypassword",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```
**Serviço ligado do Armazenamento do Azure**

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
**Conjunto de dados de entrada de SFTP**

Este conjunto de dados refere-se para a pasta SFTP `mysharedfolder` e o ficheiro `test.csv`. O pipeline copia o ficheiro para o destino.

A definição "externo": "true" informa o serviço Data Factory que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

```JSON
{
  "name": "SFTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "SftpLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Conjunto de dados dos Blobs do Azure**

Os dados são escritos para um blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e partes de horas da hora de início.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pipeline com atividade de cópia**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **FileSystemSource** e **sink** tipo está definido como **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
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
        "start": "2017-02-20T18:00:00Z",
        "end": "2017-02-20T19:00:00Z"
    }
}
```

## <a name="performance-and-tuning"></a>Desempenho e Otimização
Ver [desempenho de atividade de cópia e guia de ajuste](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chave a que um impacto no desempenho de movimento de dados (atividade de cópia) no Azure Data Factory e diversas maneiras para otimizá-lo.

## <a name="next-steps"></a>Próximos Passos
Consulte os seguintes artigos:

* [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia.
