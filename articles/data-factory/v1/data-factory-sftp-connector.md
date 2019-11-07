---
title: Mover dados do servidor SFTP usando o Azure Data Factory
description: Saiba mais sobre como mover dados de um servidor SFTP local ou em nuvem usando o Azure Data Factory.
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
ms.openlocfilehash: 1a75b3af46d79cc7a028fa5d36ef1653b1619e8d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682334"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Mover dados de um servidor SFTP usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-sftp-connector.md)
> * [Versão 2 (versão atual)](../connector-sftp.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [SFTPconnector em v2](../connector-sftp.md).

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para mover dados de um servidor SFTP local/na nuvem para um armazenamento de dados de coletor com suporte. Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) que apresenta uma visão geral da movimentação de dados com a atividade de cópia e a lista de armazenamentos de dados com suporte como fontes/coletores.

Atualmente, o data Factory dá suporte apenas à movimentação de dados de um servidor SFTP para outros armazenamentos de dados, mas não para a movimentação de dados de outros armazenamentos de dados para um servidor SFTP. Ele dá suporte a servidores SFTP locais e na nuvem.

> [!NOTE]
> A atividade de cópia não exclui o arquivo de origem depois que ele é copiado com êxito para o destino. Se você precisar excluir o arquivo de origem após uma cópia bem-sucedida, crie uma atividade personalizada para excluir o arquivo e use a atividade no pipeline.

## <a name="supported-scenarios-and-authentication-types"></a>Tipos de autenticação e cenários com suporte
Você pode usar esse conector SFTP para copiar dados de **servidores SFTP de nuvem e de servidores SFTP locais**. Os tipos de autenticação **básica** e **SshPublicKey** têm suporte ao se conectar ao servidor SFTP.

Ao copiar dados de um servidor SFTP local, você precisa instalar um Gerenciamento de Dados gateway no ambiente local/VM do Azure. Consulte [Gerenciamento de dados gateway](data-factory-data-management-gateway.md) para obter detalhes sobre o gateway. Confira o artigo [movendo dados entre locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo sobre como configurar o gateway e usá-lo.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que mova dados de uma fonte SFTP usando diferentes ferramentas/APIs.

- A maneira mais fácil de criar um pipeline é usar o **Assistente de cópia**. Consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter uma explicação rápida sobre como criar um pipeline usando o assistente para copiar dados.

- Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net**e **API REST**. Confira o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções detalhadas para criar um pipeline com uma atividade de cópia. Para obter exemplos de JSON para copiar dados do servidor SFTP para o armazenamento de BLOBs do Azure, confira a seção [exemplo de JSON: copiar dados do servidor SFTP para o blob do Azure](#json-example-copy-data-from-sftp-server-to-azure-blob) deste artigo.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
A tabela a seguir fornece a descrição para elementos JSON específicos do serviço vinculado de FTP.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo | A propriedade Type deve ser definida como `Sftp`. |Sim |
| hospedeira | Nome ou endereço IP do servidor SFTP. |Sim |
| porta |Porta na qual o servidor SFTP está escutando. O valor padrão é: 21 |Não |
| authenticationType |Especifique o tipo de autenticação. Valores permitidos: **Basic**, **SshPublicKey**. <br><br> Consulte [usando a autenticação básica](#using-basic-authentication) e [usando seções de autenticação de chave pública SSH](#using-ssh-public-key-authentication) em mais propriedades e exemplos JSON, respectivamente. |Sim |
| skipHostKeyValidation | Especifique se deseja ignorar a validação da chave do host. | Não. O valor padrão: false |
| hostKeyFingerprint | Especifique a impressão digital da chave do host. | Sim se o `skipHostKeyValidation` for definido como false.  |
| gatewayName |Nome do gateway de Gerenciamento de Dados para se conectar a um servidor SFTP local. | Sim, se estiver copiando dados de um servidor SFTP local. |
| encryptedCredential | Credencial criptografada para acessar o servidor SFTP. Gerado automaticamente quando você especifica autenticação básica (nome de usuário + senha) ou autenticação SshPublicKey (nome de usuário + caminho ou conteúdo da chave privada) no assistente de cópia ou na caixa de diálogo pop-up do ClickOnce. | Não. Aplica-se somente ao copiar dados de um servidor SFTP local. |

### <a name="using-basic-authentication"></a>Usando a autenticação básica

Para usar a autenticação básica, defina `authenticationType` como `Basic` e especifique as propriedades a seguir, além das genéricas do conector SFTP introduzidas na última seção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| o nome de utilizador | Usuário que tem acesso ao servidor SFTP. |Sim |
| palavra-passe | Senha do usuário (username). | Sim |

#### <a name="example-basic-authentication"></a>Exemplo: autenticação básica
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

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Exemplo: autenticação básica com credencial criptografada

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

### <a name="using-ssh-public-key-authentication"></a>Usando a autenticação de chave pública SSH

Para usar a autenticação de chave pública SSH, defina `authenticationType` como `SshPublicKey`e especifique as propriedades a seguir, além das genéricas do conector SFTP introduzidas na última seção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| o nome de utilizador |Usuário que tem acesso ao servidor SFTP |Sim |
| privateKeyPath | Especifique o caminho absoluto para o arquivo de chave privada que o gateway pode acessar. | Especifique o `privateKeyPath` ou o `privateKeyContent`. <br><br> Aplica-se somente ao copiar dados de um servidor SFTP local. |
| privateKeyContent | Uma cadeia de caracteres serializada do conteúdo da chave privada. O assistente de cópia pode ler o arquivo de chave privada e extrair automaticamente o conteúdo da chave privada. Se você estiver usando qualquer outra ferramenta/SDK, use a propriedade privateKeyPath em vez disso. | Especifique o `privateKeyPath` ou o `privateKeyContent`. |
| passPhrase | Especifique a frase secreta/senha para descriptografar a chave privada se o arquivo de chave estiver protegido por uma frase secreta. | Sim se o arquivo de chave privada for protegido por uma frase secreta. |

> [!NOTE]
> O conector SFTP dá suporte à chave RSA/DSA OpenSSH. Verifique se o conteúdo do arquivo de chave começa com "-----BEGIN [RSA/DSA]-----chave privada". Se o arquivo de chave privada for um arquivo de formato PPK, use a ferramenta de saída para converter de. PPK para o formato OpenSSH.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Exemplo: autenticação SshPublicKey usando o filePath da chave privada

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

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Exemplo: autenticação SshPublicKey usando conteúdo de chave privada

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

## <a name="dataset-properties"></a>Propriedades de DataSet
Para obter uma lista completa das seções & propriedades disponíveis para definir os conjuntos de valores, consulte o artigo [criando conjuntos](data-factory-create-datasets.md) de itens. As seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados.

A seção **typeproperties** é diferente para cada tipo de conjunto de texto. Ele fornece informações específicas para o tipo de conjunto de dados. A seção typeproperties para um conjunto de conjunto de DataSet do tipo **FileShare** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Sub-caminho para a pasta. Use o caractere de escape ' \ ' para caracteres especiais na cadeia de caracteres. Consulte amostra de serviço vinculado e definições de conjunto de exemplos.<br/><br/>Você pode combinar essa propriedade com **partitionBy** para ter caminhos de pasta com base em data/hora de início/término da fatia. |Sim |
| fileName |Especifique o nome do arquivo no **FolderPath** se você quiser que a tabela se refira a um arquivo específico na pasta. Se você não especificar nenhum valor para essa propriedade, a tabela apontará para todos os arquivos na pasta.<br/><br/>Quando fileName não for especificado para um conjunto de resultados de saída, o nome do arquivo gerado estaria no seguinte formato: <br/><br/>`Data.<Guid>.txt` (exemplo: Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt |Não |
| fileFilter |Especifique um filtro a ser usado para selecionar um subconjunto de arquivos no folderPath em vez de todos os arquivos.<br/><br/>Os valores permitidos são: `*` (vários caracteres) e `?` (caractere único).<br/><br/>Exemplos 1: `"fileFilter": "*.log"`<br/>Exemplo 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> FileFilter é aplicável a um conjunto de dados de FileShare de entrada. Não há suporte para essa propriedade com HDFS. |Não |
| partitionedBy |partitionedBy pode ser usado para especificar um nome de arquivo folderPath dinâmico para dados de série temporal. Por exemplo, folderPath parametrizado para cada hora dos dados. |Não |
| ao | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **Type** em Format para um desses valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e seções de [formato parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção formato nas definições do conjunto de dados de entrada e saída. |Não |
| çã | Especifique o tipo e o nível de compactação para os dados. Os tipos com suporte são: **gzip**, **deflate**, **bzip2**e **ZipDeflate**. Os níveis com suporte são: **ideal** e **mais rápido**. Para obter mais informações, consulte [formatos de arquivo e compactação em Azure data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |
| useBinaryTransfer |Especifique se o modo de transferência binária deve ser usado. True para modo binário e falso ASCII. Valor padrão: true. Essa propriedade só pode ser usada quando o tipo de serviço vinculado associado for do tipo: FtpServer. |Não |

> [!NOTE]
> filename e FileFilter não podem ser usados simultaneamente.

### <a name="using-partionedby-property"></a>Usando a propriedade partionedBy
Conforme mencionado na seção anterior, você pode especificar um nome de arquivo folderPath dinâmico para dados de série temporal com partitionedBy. Você pode fazer isso com as macros Data Factory e a variável de sistema SliceStart, SliceEnd que indicam o período de tempo lógico para uma determinada fatia de dados.

Para saber mais sobre conjuntos de data, agendamento e fatias de série temporal, confira [criar conjuntos](data-factory-create-datasets.md)de os artigos, [agendando & execução](data-factory-scheduling-and-execution.md)e [criando pipelines](data-factory-create-pipelines.md) .

#### <a name="sample-1"></a>Exemplo 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Neste exemplo, {Slice} é substituído pelo valor de Data Factory variável de sistema SliceStart no formato (AAAAMMDDHH) especificado. O SliceStart refere-se à hora de início da fatia. FolderPath é diferente para cada fatia. Exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

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
Neste exemplo, ano, mês, dia e hora de SliceStart são extraídos em variáveis separadas que são usadas pelas propriedades folderPath e fileName.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das seções & propriedades disponíveis para definir as atividades, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . Propriedades como nome, descrição, tabelas de entrada e saída e políticas estão disponíveis para todos os tipos de atividades.

Enquanto que as propriedades disponíveis na seção typeproperties da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, as propriedades de tipo variam de acordo com os tipos de fontes e coletores.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Formatos de arquivo e compactação com suporte
Consulte [formatos de arquivo e compactação no artigo Azure data Factory](data-factory-supported-file-and-compression-formats.md) sobre detalhes.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>Exemplo de JSON: copiar dados do servidor SFTP para o blob do Azure
O exemplo a seguir fornece exemplos de definições de JSON que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados da fonte SFTP para o armazenamento de BLOBs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure data Factory.

> [!IMPORTANT]
> Este exemplo fornece trechos de código JSON. Ele não inclui instruções passo a passo para criar o data factory. Confira o artigo [movendo dados entre os locais e a nuvem no local](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo.

O exemplo tem as seguintes entidades de data factory:

* Um serviço vinculado do tipo [SFTP](#linked-service-properties).
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [FileShare](#dataset-properties).
* Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que usa o [FileSystemName](#copy-activity-properties) e o [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de um servidor SFTP para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

**Serviço vinculado de SFTP**

Este exemplo usa a autenticação básica com nome de usuário e senha em texto sem formatação. Você também pode usar uma das seguintes maneiras:

* Autenticação básica com credenciais criptografadas
* Autenticação de chave pública SSH

Consulte a seção [serviço vinculado FTP](#linked-service-properties) para diferentes tipos de autenticação que você pode usar.

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
**Conjunto de dados de entrada SFTP**

Esse conjunto de DataSet refere-se à pasta SFTP `mysharedfolder` e `test.csv`de arquivo. O pipeline copia o arquivo para o destino.

A configuração "external": "true" informa ao serviço de Data Factory que o conjunto de os é externo ao data factory e não é produzido por uma atividade no data factory.

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

Os dados são gravados em um novo BLOB a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

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

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** está definido como **FileSystemName** e o tipo de **coletor** está definido como **BlobSink**.

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

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Consulte [Guia de ajuste do desempenho de atividade de cópia &](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Azure data Factory e várias maneiras de otimizá-lo.

## <a name="next-steps"></a>Próximos Passos
Consulte os seguintes artigos:

* [Tutorial de atividade da cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia.
