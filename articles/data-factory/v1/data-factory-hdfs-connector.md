---
title: Mover dados do HDFS no local | Documentos da Microsoft
description: Saiba mais sobre como mover dados do HDFS no local com o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f83b525a423ccb2e66d75032811a5f921238a06b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57893412"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Mover dados do HDFS no local com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-hdfs-connector.md)
> * [Versão 2 (versão atual)](../connector-hdfs.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conector HDFS no V2](../connector-hdfs.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados de um HDFS no local. Ele se baseia no [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma visão geral do movimento de dados com a atividade de cópia.

Pode copiar dados do HDFS para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados suportados como sinks a atividade de cópia, consulte a [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. O Data factory suporta, atualmente, apenas mover dados de um HDFS no local para outros arquivos de dados, mas não para mover dados de outros arquivos de dados para um HDFS no local.

> [!NOTE]
> Atividade de cópia não elimina o ficheiro de origem após ser copiada com êxito para o destino. Se tiver de eliminar o ficheiro de origem depois de uma cópia com êxito, crie uma atividade personalizada para excluir o arquivo e usar a atividade no pipeline. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Ativar a conectividade
Serviço do Data Factory suporta a ligar ao HDFS no local com o Data Management Gateway. Ver [mover dados entre localizações no local e na cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo para saber mais sobre o Gateway de gestão de dados e instruções passo a passo sobre como configurar o gateway. Utilize o gateway para ligar ao HDFS, mesmo que ele está hospedado numa VM de IaaS do Azure.

> [!NOTE]
> Certifique-se com o Data Management Gateway pode aceder **todos os** [server de nó nome]: [nome de porta de nó] e [servidores de nó de dados]: [porta do nó de dados] do cluster de Hadoop. A predefinição [porta do nó de nome] é 50070 e padrão [porta do nó de dados] é 50075.

Embora possa instalar o gateway no mesmo computador no local ou VM do Azure como o HDFS, recomendamos que instale o gateway num máquina/do Azure IaaS VM. Com o gateway num computador separado reduz a contenção de recursos e melhora o desempenho. Ao instalar o gateway numa máquina separada, a máquina deve ser capaz de acessar a máquina com o HDFS.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move os dados de uma origem HDFS ao utilizar ferramentas/APIs diferentes.

A maneira mais fácil para criar um pipeline é utilizar o **Assistente para copiar**. Consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para um rápido passo a passo sobre como criar um pipeline com o Assistente para copiar dados.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**e  **REST API**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se usar as ferramentas ou APIs, que execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink:

1. Crie **serviços ligados** para ligar a dados de entrada e saídos armazena à fábrica de dados.
2. Crie **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia.
3. Criar uma **pipeline** com uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) são criadas automaticamente para. Ao utilizar ferramentas/APIs (exceto a .NET API), define essas entidades do Data Factory com o formato JSON.  Para obter um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de um arquivo de dados do HDFS, veja [exemplo de JSON: Copiar dados do HDFS no local para BLOBs do Azure](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) seção deste artigo.

As secções seguintes fornecem detalhes sobre as propriedades JSON utilizadas para definir entidades do Data Factory específicas ao HDFS:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
Os serviços ligados ligam um arquivo de dados para uma fábrica de dados. Vai criar um serviço ligado do tipo **Hdfs** para ligar um HDFS no local à fábrica de dados. A tabela seguinte fornece uma descrição para elementos JSON específicos HDFS de serviço ligado.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo tem de ser definida como: **Hdfs** |Sim |
| Url |URL para o HDFS |Sim |
| authenticationType |Anónimo, ou Windows. <br><br> Para utilizar **a autenticação Kerberos** para o conector do HDFS, consulte [nesta secção](#use-kerberos-authentication-for-hdfs-connector) para configurar o seu ambiente no local em conformidade. |Sim |
| userName |Autenticação de nome de utilizador para Windows. Para a autenticação Kerberos, especifique `<username>@<domain>.com`. |Sim (para autenticação do Windows) |
| palavra-passe |Palavra-passe para a autenticação do Windows. |Sim (para autenticação do Windows) |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar para o HDFS. |Sim |
| encryptedCredential |[Novo AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) saída da credencial de acesso. |Não |

### <a name="using-anonymous-authentication"></a>Utilizar a autenticação anónima

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>Utilizar a autenticação do Windows

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```
## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [criar conjuntos de dados](data-factory-create-datasets.md) artigo. Seções, como a estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. Os typeProperties secção para o conjunto de dados do tipo **partilha de ficheiros** (que inclui o conjunto de dados do HDFS) tem as seguintes propriedades

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho para a pasta. Exemplo: `myfolder`<br/><br/>Utilizar o caráter de escape "\" para carateres especiais na cadeia de caracteres. Por exemplo: para folder\subfolder, especifique a pasta\\\\subpasta e para d:\samplefolder, especifique d:\\\\samplefolder.<br/><br/>Pode combinar essa propriedade com o **partitionBy** ter pasta caminhos baseados no setor de início/fim datas-horas. |Sim |
| fileName |Especifique o nome do arquivo na **folderPath** se pretender que a tabela para fazer referência a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros na pasta.<br/><br/>Quando o nome de ficheiro não está especificado para um conjunto de dados de saída, o nome do ficheiro gerado seria a seguir este formato: <br/><br/>Dados. <Guid>. txt (por exemplo:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| partitionedBy |partitionedBy pode ser utilizado para especificar um folderPath dinâmica, o nome de ficheiro para dados de séries de tempo. Exemplo: folderPath parametrizado por cada hora de dados. |Não |
| Formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se quiser **copiar ficheiros como-é** entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída. |Não |
| Compressão | Especifica o tipo e o nível de compressão dos dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Ideal** e **mais rápida**. Para obter mais informações, consulte [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> nome de ficheiro e fileFilter não podem ser utilizadas em simultâneo.

### <a name="using-partionedby-property"></a>Usando a propriedade de partionedBy
Conforme mencionado na secção anterior, pode especificar um folderPath dinâmico e o nome do ficheiro de dados de séries de tempo com o **partitionedBy** propriedade, [funções de fábrica de dados e as variáveis do sistema](data-factory-functions-variables.md).

Para saber mais sobre conjuntos de dados de séries de tempo, agendamento e setores, veja [criar conjuntos de dados](data-factory-create-datasets.md), [agendamento e execução](data-factory-scheduling-and-execution.md), e [criar Pipelines](data-factory-create-pipelines.md) artigos.

#### <a name="sample-1"></a>Exemplo 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Neste exemplo {setor} é substituído pelo valor da variável do sistema SliceStart fábrica de dados no formato (YYYYMMDDHH) especificado. SliceStart refere-se para iniciar a hora do setor. FolderPath é diferente para cada setor. Por exemplo: deverá/wikisampledataout/2014100103 ou deverá/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Exemplo 2:

```JSON
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

Ao passo que, propriedades disponíveis na secção typeProperties da atividade variar de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam consoante os tipos de origens e sinks.

Para a atividade de cópia, quando a origem é do tipo **FileSystemSource** as seguintes propriedades estão disponíveis na secção typeProperties:

**FileSystemSource** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursiva |Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. |TRUE, False (predefinição) |Não |

## <a name="supported-file-and-compression-formats"></a>Formatos de ficheiro e de compressão suportados
Ver [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artigo em detalhes.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Exemplo JSON: Copiar dados do HDFS no local para BLOBs do Azure
Este exemplo mostra como copiar dados de um HDFS no local para o armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** a qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure Data Factory.  

O exemplo fornece definições de JSON para as seguintes entidades do Data Factory. Pode utilizar estas definições para criar um pipeline para copiar dados do HDFS para armazenamento de Blobs do Azure, utilizando [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. Um serviço ligado do tipo [OnPremisesHdfs](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [partilha de ficheiros](#dataset-properties).
4. Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. R [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de um HDFS no local para um blob do Azure a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

Como primeiro passo, configure o gateway de gestão de dados. As instruções a [mover dados entre localizações no local e na cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo.

**Serviço ligado do HDFS:** Este exemplo utiliza a autenticação do Windows. Ver [serviço ligado do HDFS](#linked-service-properties) secção para diferentes tipos de autenticação, pode utilizar.

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

**Serviço ligado do armazenamento do Azure:**

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

**Dataset de entrada do HDFS:** Este conjunto de dados refere-se para a pasta HDFS DataTransfer/UnitTest /. O pipeline copia todos os ficheiros nesta pasta para o destino.

A definição "externo": "true" informa o serviço Data Factory que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Conjunto de dados de Blobs do Azure:**

Os dados são escritos para um blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e partes de horas da hora de início.

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Uma atividade de cópia num pipeline com a origem de sistema de ficheiros e de sink do Blob:**

O pipeline contém uma atividade de cópia que está configurado para utilizar estes conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **FileSystemSource** e **sink** tipo está definido como **BlobSink**. A consulta SQL especificada para o **consulta** propriedade seleciona os dados na hora anterior para copiar.

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Utilizar a autenticação Kerberos para o conector do HDFS
Existem duas opções para configurar o ambiente no local de modo a utilizar a autenticação Kerberos no conector HDFS. Pode escolher que o melhor se adequa a seu caso.
* Opção 1: [Junte-se a máquina de gateway no Kerberos realm](#kerberos-join-realm)
* Opção 2: [Ativar a fidedignidade bidirecional entre o domínio do Windows e o Kerberos realm](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Opção 1: Junte-se a máquina de gateway no Kerberos realm

#### <a name="requirement"></a>Requisito:

* A máquina de gateway tem de associar o realm de Kerberos e não é possível associar a qualquer domínio Windows.

#### <a name="how-to-configure"></a>Como configurar:

**No computador de gateway:**

1.  Executar o **Ksetup** utilitário para configurar o servidor de KDC do Kerberos e realm.

    A máquina tem de ser configurada como membro do grupo de trabalho, uma vez que um realm de Kerberos é diferente de um domínio Windows. Isso pode ser conseguido ao definir o realm de Kerberos e adicionar um servidor KDC da seguinte forma. Substitua *REALM.COM* com seu respectivo realm conforme necessário.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Reiniciar** o computador depois de executar estes 2 comandos.

2.  Certifique-se a configuração com o **Ksetup** comando. O resultado deve ser, como:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Na fábrica de dados do Azure:**

* Configure a através do conector HDFS **autenticação do Windows** juntamente com seu nome principal Kerberos e a palavra-passe para ligar à origem de dados de HDFS. Verifique [propriedades de serviço ligado do HDFS](#linked-service-properties) seção sobre detalhes de configuração.

### <a name="kerberos-mutual-trust"></a>Opção 2: Ativar a fidedignidade bidirecional entre o domínio do Windows e o Kerberos realm

#### <a name="requirement"></a>Requisito:
*   A máquina de gateway tem de associar um domínio Windows.
*   Necessita de permissão para atualizar as definições do controlador de domínio.

#### <a name="how-to-configure"></a>Como configurar:

> [!NOTE]
> Substitua REALM.COM e AD.COM no tutorial seguinte com seu próprio realm respectivo e o controlador de domínio, conforme necessário.

**No servidor do KDC:**

1. Editar a configuração de KDC no **krb5.conf** arquivo para permitir que o KDC confiar no domínio do Windows que faça referência ao modelo de configuração seguintes. Por predefinição, a configuração está localizada em **/etc/krb5.conf**.

           [logging]
            default = FILE:/var/log/krb5libs.log
            kdc = FILE:/var/log/krb5kdc.log
            admin_server = FILE:/var/log/kadmind.log

           [libdefaults]
            default_realm = REALM.COM
            dns_lookup_realm = false
            dns_lookup_kdc = false
            ticket_lifetime = 24h
            renew_lifetime = 7d
            forwardable = true

           [realms]
            REALM.COM = {
             kdc = node.REALM.COM
             admin_server = node.REALM.COM
            }
           AD.COM = {
            kdc = windc.ad.com
            admin_server = windc.ad.com
           }

           [domain_realm]
            .REALM.COM = REALM.COM
            REALM.COM = REALM.COM
            .ad.com = AD.COM
            ad.com = AD.COM

           [capaths]
            AD.COM = {
             REALM.COM = .
            }

   **Reiniciar** o serviço KDC após a configuração.

2. Preparar um principal com o nome **krbtgt/REALM.COM\@AD.COM** no servidor KDC com o seguinte comando:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. Na **hadoop.security.auth_to_local** configuração do serviço HDFS do ficheiro, adicione `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**No controlador de domínio:**

1.  Execute o seguinte **Ksetup** comandos para adicionar uma entrada de realm:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Estabelece confiança de domínio do Windows para o Realm de Kerberos. [senha] é a palavra-passe para o principal **krbtgt/REALM.COM\@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Selecione o algoritmo de encriptação utilizado no Kerberos.

    1. Aceda ao Gestor de servidor > Gestão de política de grupo > domínio > objetos de política de grupo > padrão ou política de domínio Active Directory e editar.

    2. Na **Editor de gerenciamento de diretiva de grupo** janela de pop-up, aceda a configuração do computador > políticas > definições do Windows > definições de segurança > Políticas locais > Opções de segurança e configurar **rede segurança: Configurar tipos de encriptação permitidos para Kerberos**.

    3. Selecione o algoritmo de encriptação que pretende utilizar quando ligar ao KDC. Normalmente, pode simplesmente selecionar todas as opções.

        ![Tipos de encriptação de configuração de Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Uso **Ksetup** comando para especificar o algoritmo de encriptação a utilizar sobre o REALM específico.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Crie o mapeamento entre a conta de domínio e o principal de Kerberos, para poder utilizar o principal de Kerberos no domínio do Windows.

    1. Iniciar as ferramentas administrativas > **Active Directory Users and Computers**.

    2. Configurar funcionalidades avançadas clicando **View** > **funcionalidades avançadas**.

    3. Localize a conta à qual pretende criar mapeamentos e, com o botão direito para ver **mapeamentos de nome** > clique em **Kerberos nomes** separador.

    4. Adicione um principal de território.

        ![Identidade de segurança do mapa](media/data-factory-hdfs-connector/map-security-identity.png)

**No computador de gateway:**

* Execute o seguinte **Ksetup** comandos para adicionar uma entrada de realm.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Na fábrica de dados do Azure:**

* Configure a através do conector HDFS **autenticação do Windows** juntamente com a sua conta de domínio ou Principal de Kerberos para ligar à origem de dados de HDFS. Verifique [propriedades de serviço ligado do HDFS](#linked-service-properties) seção sobre detalhes de configuração.

> [!NOTE]
> Para mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de sink, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).


## <a name="performance-and-tuning"></a>Desempenho e Otimização
Ver [desempenho de atividade de cópia e guia de ajuste](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chave a que um impacto no desempenho de movimento de dados (atividade de cópia) no Azure Data Factory e diversas maneiras para otimizá-lo.
