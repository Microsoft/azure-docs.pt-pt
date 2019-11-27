---
title: Mover dados do HDFS local
description: Saiba mais sobre como mover dados do HDFS local usando Azure Data Factory.
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
ms.openlocfilehash: ad5695f1bde1013b6a4c010f4a80256eac09fe63
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682566"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Mover dados do HDFS local usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-hdfs-connector.md)
> * [Versão 2 (versão atual)](../connector-hdfs.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [conector do HDFS na v2](../connector-hdfs.md).

Este artigo explica como usar a atividade de cópia no Azure Data Factory para mover dados de um HDFS local. Ele se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados do HDFS para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Atualmente, o data Factory dá suporte apenas à movimentação de dados de um HDFS local para outros armazenamentos de dados, mas não para a movimentação de dados de outros armazenamentos de dados para um HDFS local.

> [!NOTE]
> A atividade de cópia não exclui o arquivo de origem depois que ele é copiado com êxito para o destino. Se você precisar excluir o arquivo de origem após uma cópia bem-sucedida, crie uma atividade personalizada para excluir o arquivo e use a atividade no pipeline. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Habilitando a conectividade
Data Factory serviço dá suporte à conexão com o HDFS local usando o gateway de Gerenciamento de Dados. Confira o artigo [movendo dados entre locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre gerenciamento de dados gateway e instruções passo a passo sobre como configurar o gateway. Use o gateway para se conectar ao HDFS, mesmo se ele estiver hospedado em uma VM IaaS do Azure.

> [!NOTE]
> Verifique se o gateway de Gerenciamento de Dados pode acessar **todos** os [servidor de nó de nome]: [porta do nó de nome] e [servidores de nó de dados]: [porta do nó de dados] do cluster Hadoop. O padrão [porta do nó de nome] é 50070 e o padrão [porta do nó de dados] é 50075.

Embora você possa instalar o gateway no mesmo computador local ou na VM do Azure como o HDFS, recomendamos que você instale o gateway em um computador separado/VM IaaS do Azure. Ter o gateway em um computador separado reduz a contenção de recursos e melhora o desempenho. Quando você instala o gateway em um computador separado, o computador deve ser capaz de acessar o computador com o HDFS.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que move dados de uma fonte HDFS usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de cópia**. Consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter uma explicação rápida sobre como criar um pipeline usando o assistente para copiar dados.

Você também pode usar as seguintes ferramentas para criar um pipeline: **portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net**e **API REST**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory.
2. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída.

Quando você usa o assistente, as definições de JSON para essas entidades de Data Factory (serviços vinculados, conjuntos de valores e o Pipeline) são criadas automaticamente para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades de Data Factory usando o formato JSON.  Para obter um exemplo com definições de JSON para Data Factory entidades usadas para copiar dados de um armazenamento de dados HDFS, confira a seção [exemplo de JSON: copiar dados do HDFS local para o blob do Azure](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) deste artigo.

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas ao HDFS:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
Um serviço vinculado vincula um armazenamento de dados a um data factory. Você cria um serviço vinculado do tipo **HDFS** para vincular um HDFS local ao seu data Factory. A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do HDFS.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade Type deve ser definida como: **HDFS** |Sim |
| url |URL para o HDFS |Sim |
| authenticationType |Anônimo ou Windows. <br><br> Para usar a **autenticação Kerberos** para o conector HDFS, consulte [esta seção](#use-kerberos-authentication-for-hdfs-connector) para configurar seu ambiente local de acordo. |Sim |
| userName |Nome de usuário para autenticação do Windows. Para a autenticação Kerberos, especifique `<username>@<domain>.com`. |Sim (para autenticação do Windows) |
| palavra-passe |Senha para autenticação do Windows. |Sim (para autenticação do Windows) |
| gatewayName |Nome do gateway que o serviço de Data Factory deve usar para se conectar ao HDFS. |Sim |
| encryptedCredential |Saída [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) da credencial de acesso. |Não |

### <a name="using-anonymous-authentication"></a>Usando a autenticação anônima

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

### <a name="using-windows-authentication"></a>Usando a autenticação do Windows

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
Para obter uma lista completa das seções & propriedades disponíveis para definir os conjuntos de valores, consulte o artigo [criando conjuntos](data-factory-create-datasets.md) de itens. As seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, BLOB do Azure, tabela do Azure, etc.).

A seção **typeproperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção typeproperties do conjunto de um do tipo **FileShare** (que inclui o conjunto de conjuntos de o HDFS) tem as seguintes propriedades

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho para a pasta. Exemplo: `myfolder`<br/><br/>Use o caractere de escape ' \ ' para caracteres especiais na cadeia de caracteres. Por exemplo: para pasta\subpasta, especifique a pasta\\\\subpasta e para d:\samplefolder, especifique d:\\\\pastadeexemplo.<br/><br/>Você pode combinar essa propriedade com **partitionBy** para ter caminhos de pasta com base em data/hora de início/término da fatia. |Sim |
| fileName |Especifique o nome do arquivo no **FolderPath** se você quiser que a tabela se refira a um arquivo específico na pasta. Se você não especificar nenhum valor para essa propriedade, a tabela apontará para todos os arquivos na pasta.<br/><br/>Quando fileName não for especificado para um conjunto de resultados de saída, o nome do arquivo gerado estaria no seguinte formato: <br/><br/>`Data.<Guid>.txt` (por exemplo: Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt |Não |
| partitionedBy |partitionedBy pode ser usado para especificar um nome de arquivo folderPath dinâmico para dados de série temporal. Exemplo: folderPath parametrizado para cada hora dos dados. |Não |
| format | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se quiser **copiar ficheiros como-é** entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída. |Não |
| compression | Especifica o tipo e o nível de compressão dos dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Optimal** e **Fastest**. Para obter mais informações, consulte [formatos de arquivo e compactação em Azure data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> filename e FileFilter não podem ser usados simultaneamente.

### <a name="using-partionedby-property"></a>Usando a propriedade partionedBy
Conforme mencionado na seção anterior, você pode especificar um folderPath e um nome de arquivo dinâmico para dados de série temporal com a propriedade **partitionedBy** , [funções de data Factory e as variáveis de sistema](data-factory-functions-variables.md).

Para saber mais sobre conjuntos de data de série temporal, agendamento e fatias, confira criando artigos sobre [criação de conjuntos](data-factory-create-datasets.md)de os, [agendando & execução](data-factory-scheduling-and-execution.md)e [criando pipelines](data-factory-create-pipelines.md) .

#### <a name="sample-1"></a>Exemplo 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Neste exemplo, {Slice} é substituído pelo valor de Data Factory variável de sistema SliceStart no formato (AAAAMMDDHH) especificado. O SliceStart refere-se à hora de início da fatia. FolderPath é diferente para cada fatia. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

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
Neste exemplo, ano, mês, dia e hora de SliceStart são extraídos em variáveis separadas que são usadas pelas propriedades folderPath e fileName.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das seções & propriedades disponíveis para definir as atividades, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . Propriedades como nome, descrição, tabelas de entrada e saída e políticas estão disponíveis para todos os tipos de atividades.

Enquanto que as propriedades disponíveis na seção typeproperties da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fontes e coletores.

Para a atividade de cópia, quando a fonte é do tipo **FileSystemName** , as seguintes propriedades estão disponíveis na seção typeproperties:

O **FileSystemName** dá suporte às seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursive |Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. |True, false (padrão) |Não |

## <a name="supported-file-and-compression-formats"></a>Formatos de arquivo e compactação com suporte
Consulte [formatos de arquivo e compactação no artigo Azure data Factory](data-factory-supported-file-and-compression-formats.md) sobre detalhes.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Exemplo de JSON: copiar dados do HDFS local para o blob do Azure
Este exemplo mostra como copiar dados de um HDFS local para o armazenamento de BLOBs do Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure data Factory.  

O exemplo fornece definições de JSON para as entidades de Data Factory a seguir. Você pode usar essas definições para criar um pipeline para copiar dados do HDFS para o armazenamento de BLOBs do Azure usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. Um serviço vinculado do tipo [OnPremisesHdfs](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [FileShare](#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que usa o [FileSystemName](#copy-activity-properties) e o [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de um HDFS local para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados. As instruções no artigo [movendo dados entre os locais e a nuvem do local](data-factory-move-data-between-onprem-and-cloud.md) .

**Serviço vinculado do HDFS:** Este exemplo usa a autenticação do Windows. Consulte a seção [serviço vinculado do HDFS](#linked-service-properties) para diferentes tipos de autenticação que você pode usar.

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

**Serviço vinculado do armazenamento do Azure:**

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

**Conjunto de dados de entrada do HDFS:** Este conjunto de DataSet refere-se à pasta HDFS DataTransfer/UnitTest/. O pipeline copia todos os arquivos desta pasta para o destino.

A configuração "external": "true" informa ao serviço de Data Factory que o conjunto de os é externo ao data factory e não é produzido por uma atividade no data factory.

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

**Conjunto de resultados de saída de blob do Azure:**

Os dados são gravados em um novo BLOB a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

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

**Uma atividade de cópia em um pipeline com origem do sistema de arquivos e coletor de blob:**

O pipeline contém uma atividade de cópia que é configurada para usar esses conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** está definido como **FileSystemName** e o tipo de **coletor** está definido como **BlobSink**. A consulta SQL especificada para a propriedade de **consulta** seleciona os dados na última hora a serem copiados.

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

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Usar a autenticação Kerberos para o conector HDFS
Há duas opções para configurar o ambiente local para usar a autenticação Kerberos no conector do HDFS. Você pode escolher o que melhor se adapta ao seu caso.
* Opção 1: [ingressar o computador de gateway no realm Kerberos](#kerberos-join-realm)
* Opção 2: [habilitar a confiança mútua entre o domínio do Windows e o realm do Kerberos](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Opção 1: ingressar o computador de gateway no realm Kerberos

#### <a name="requirement"></a>Obrigatoriedade

* O computador do gateway precisa ingressar no realm do Kerberos e não pode ingressar em nenhum domínio do Windows.

#### <a name="how-to-configure"></a>Como configurar:

**No computador do gateway:**

1.  Execute o utilitário **Ksetup** para configurar o servidor e o realm KDC Kerberos.

    O computador deve ser configurado como um membro de um grupo de trabalho, pois um realm Kerberos é diferente de um domínio do Windows. Isso pode ser feito definindo o realm do Kerberos e adicionando um servidor KDC da seguinte maneira. Substitua *realm.com* pelo seu respectivo Realm, conforme necessário.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Reinicie** o computador depois de executar estes dois comandos.

2.  Verifique a configuração com o comando **Ksetup** . A saída deve ser semelhante a:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Em Azure Data Factory:**

* Configure o conector HDFS usando a **autenticação do Windows** junto com seu nome de entidade de segurança e senha Kerberos para se conectar à fonte de dados HDFS. Verifique a seção [Propriedades do serviço vinculado do HDFS](#linked-service-properties) em detalhes da configuração.

### <a name="kerberos-mutual-trust"></a>Opção 2: habilitar a confiança mútua entre o domínio do Windows e o realm do Kerberos

#### <a name="requirement"></a>Obrigatoriedade
*   O computador do gateway deve ingressar em um domínio do Windows.
*   Você precisa de permissão para atualizar as configurações do controlador de domínio.

#### <a name="how-to-configure"></a>Como configurar:

> [!NOTE]
> Substitua REALM.COM e AD.COM no tutorial a seguir com seu próprio território e controlador de domínio respectivos, conforme necessário.

**No servidor KDC:**

1. Edite a configuração do KDC no arquivo **krb5. conf** para permitir que o KDC confie no domínio do Windows referente ao seguinte modelo de configuração. Por padrão, a configuração está localizada em **/etc/krb5.conf**.

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

   **Reinicie** o serviço KDC após a configuração.

2. Prepare uma entidade de segurança chamada **krbtgt/realm. com\@AD.com** no servidor KDC com o seguinte comando:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. Em **Hadoop. Security. auth_to_local** arquivo de configuração do serviço HDFS, adicione `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**No controlador de domínio:**

1.  Execute os seguintes comandos **Ksetup** para adicionar uma entrada de realm:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Estabeleça confiança do domínio do Windows para o realm do Kerberos. [password] é a senha para a entidade de segurança **krbtgt/realm. COM\@AD.com**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Selecione o algoritmo de criptografia usado no Kerberos.

    1. Vá para Gerenciador do Servidor > gerenciamento de Política de Grupo > domínio > objetos política de grupo > a política de domínio padrão ou ativa e edite.

    2. Na janela pop-up **Editor de gerenciamento de política de grupo** , vá para configuração do computador > políticas > configurações do Windows > configurações de segurança > políticas locais > opções de segurança e configure a **segurança de rede: configurar tipos de criptografia permitido para Kerberos**.

    3. Selecione o algoritmo de criptografia que você deseja usar ao se conectar ao KDC. Normalmente, você pode simplesmente selecionar todas as opções.

        ![Configurar tipos de criptografia para Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Use o comando **Ksetup** para especificar o algoritmo de criptografia a ser usado no realm específico.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Crie o mapeamento entre a conta de domínio e a entidade de segurança Kerberos para usar a entidade de segurança Kerberos no domínio do Windows.

    1. Inicie as ferramentas administrativas > **Active Directory usuários e computadores**.

    2. Configure recursos avançados clicando em **exibir** > **recursos avançados**.

    3. Localize a conta para a qual você deseja criar mapeamentos e clique com o botão direito do mouse para exibir **mapeamentos de nome** > clique na guia **nomes Kerberos** .

    4. Adicione uma entidade de segurança do realm.

        ![Mapear identidade de segurança](media/data-factory-hdfs-connector/map-security-identity.png)

**No computador do gateway:**

* Execute os comandos **Ksetup** a seguir para adicionar uma entrada de realm.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Em Azure Data Factory:**

* Configure o conector HDFS usando a **autenticação do Windows** junto com sua conta de domínio ou entidade de segurança Kerberos para se conectar à fonte de dados HDFS. Verifique a seção [Propriedades do serviço vinculado do HDFS](#linked-service-properties) em detalhes da configuração.

> [!NOTE]
> Para mapear colunas do conjunto de fonte de origem para colunas do conjunto de coleta, consulte [mapeando colunas do conjunto de linhas no Azure data Factory](data-factory-map-columns.md).


## <a name="performance-and-tuning"></a>Desempenho e ajuste
Consulte [Guia de ajuste do desempenho de atividade de cópia &](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Azure data Factory e várias maneiras de otimizá-lo.
