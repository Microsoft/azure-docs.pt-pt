---
title: Copiar dados de/para um sistema de ficheiros com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de e para um sistema de ficheiros no local com o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 40086924731876dc44d9651ca46814149dba52f0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432801"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Copiar dados de e para um sistema de ficheiros no local com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-onprem-file-system-connector.md)
> * [Versão 2 (versão atual)](../connector-file-system.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conector de sistema de ficheiros no V2](../connector-file-system.md).


Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para copiar dados de/para um sistema de ficheiros no local. Ele se baseia no [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma visão geral do movimento de dados com a atividade de cópia.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **de um sistema de ficheiros no local** para os seguintes dados armazena:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados de arquivos de dados seguintes **para um sistema de ficheiros no local**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Atividade de cópia não elimina o ficheiro de origem após ser copiada com êxito para o destino. Se tiver de eliminar o ficheiro de origem depois de uma cópia com êxito, crie uma atividade personalizada para excluir o arquivo e usar a atividade no pipeline.

## <a name="enabling-connectivity"></a>Ativar a conectividade
Fábrica de dados suporta a ligação de e para um sistema de ficheiros no local através de **Data Management Gateway**. Tem de instalar o Data Management Gateway no seu ambiente no local para o serviço fábrica de dados ligar a nenhum repositório de dados local suportada, incluindo o sistema de ficheiros. Para saber mais sobre o Gateway de gestão de dados e para obter instruções passo a passo sobre como configurar o gateway, veja [mover dados entre origens no local e a nuvem com o Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md). Para além dos Data Management Gateway, não existem outros arquivos binários precisam ser instalados para comunicar de e para um sistema de ficheiros no local. Tem de instalar e utilizar o Gateway de gestão de dados, mesmo que o sistema de ficheiros é na VM de IaaS do Azure. Para obter informações detalhadas sobre o gateway, consulte [Data Management Gateway](data-factory-data-management-gateway.md).

Para utilizar uma partilha de ficheiros do Linux, instale [Samba](https://www.samba.org/) no seu servidor Linux e instalar o Data Management Gateway num servidor Windows. Instalar o Data Management Gateway num servidor Linux não é suportada.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move os dados de/para um sistema de ficheiros ao utilizar ferramentas/APIs diferentes.

A maneira mais fácil para criar um pipeline é utilizar o **Assistente para copiar**. Consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para um rápido passo a passo sobre como criar um pipeline com o Assistente para copiar dados.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**e  **REST API**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se usar as ferramentas ou APIs, que execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink:

1. Criar uma **fábrica de dados**. Uma fábrica de dados pode conter um ou mais pipelines.
2. Crie **serviços ligados** para ligar a dados de entrada e saídos armazena à fábrica de dados. Por exemplo, se estiver a copiar dados de um armazenamento de Blobs do Azure para um sistema de ficheiros no local, crie dois serviços ligados para ligar o seu sistema de ficheiros no local e a conta de armazenamento do Azure à fábrica de dados. Para as propriedades do serviço ligado que são específicas para um sistema de ficheiros no local, consulte [propriedades do serviço ligado](#linked-service-properties) secção.
3. Crie **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. O exemplo mencionado no último passo, vai criar um conjunto de dados para especificar o contentor de BLOBs e a pasta que contém os dados de entrada. Além disso, crie outro conjunto de dados para especificar a pasta e o nome de ficheiro (opcional) no seu sistema de ficheiros. Para as propriedades do conjunto de dados que são específicas para o sistema de ficheiros no local, consulte [propriedades do conjunto de dados](#dataset-properties) secção.
4. Criar uma **pipeline** com uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, vai utilizar BlobSource como uma origem e FileSystemSink como sink para a atividade de cópia. Da mesma forma, se estiver a copiar do sistema de arquivos no local para o armazenamento de Blobs do Azure, utilize FileSystemSource e BlobSink a atividade de cópia. Para propriedades de atividade de cópia que são específicas para o sistema de ficheiros no local, consulte [propriedades da atividade copy](#copy-activity-properties) secção. Para obter detalhes sobre como usar um arquivo de dados como uma origem ou sink, clique na ligação na secção anterior para seu armazenamento de dados.

Quando utiliza o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) são criadas automaticamente para. Ao utilizar ferramentas/APIs (exceto a .NET API), define essas entidades do Data Factory com o formato JSON.  Para exemplos com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de/para um sistema de ficheiros, consulte [exemplos JSON](#json-examples-for-copying-data-to-and-from-file-system) seção deste artigo.

As secções seguintes fornecem detalhes sobre as propriedades JSON utilizadas para definir entidades do Data Factory específicas ao sistema de ficheiros:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
Pode ligar um sistema de ficheiros no local a uma fábrica de dados do Azure com o **servidor de ficheiros no local** serviço ligado. A tabela seguinte fornece descrições para os elementos JSON que são específicas para o serviço de servidor de ficheiros no local ligado.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |Certifique-se de que a propriedade de tipo é definida como **OnPremisesFileServer**. |Sim |
| anfitrião |Especifica o caminho de raiz da pasta que pretende copiar. Utilizar o caráter de escape "\" para carateres especiais na cadeia de caracteres. Ver [exemplo ligado as definições de serviço e o conjunto de dados](#sample-linked-service-and-dataset-definitions) para obter exemplos. |Sim |
| ID de utilizador |Especifica o ID de utilizador que tem acesso ao servidor. |Não (se escolher encryptedCredential) |
| palavra-passe |Especifique a palavra-passe para o utilizador (ID de utilizador). |Não (se escolher encryptedCredential |
| encryptedCredential |Especifique as credenciais encriptadas que pode obter ao executar o cmdlet New-AzDataFactoryEncryptValue. |Não (se optar por especificar o ID de utilizador e palavra-passe em texto simples) |
| gatewayName |Especifica o nome do gateway que o Data Factory deve utilizar para ligar ao servidor de ficheiros no local. |Sim |


### <a name="sample-linked-service-and-dataset-definitions"></a>Serviço ligado e as definições do conjunto de dados de exemplo
| Cenário | Alojar na definição de serviço ligado | folderPath na definição do conjunto de dados |
| --- | --- | --- |
| Pasta local no computador Gateway de gestão de dados: <br/><br/>Exemplos: D:\\ \* ou D:\folder\subfolder\\* |D:\\ \\ (para o Data Management Gateway 2.0 e versões posteriores) <br/><br/> host local (para versões anteriores que o Data Management Gateway 2.0) |. \\ \\ ou pasta\\\\subpasta (para o Data Management Gateway 2.0 e versões posteriores) <br/><br/>D:\\ \\ ou d:\\\\pasta\\\\subpasta (para a versão do gateway abaixo 2.0) |
| Pasta remota partilhada: <br/><br/>Exemplos: \\ \\myserver\\partilhar\\ \* ou \\ \\myserver\\partilhar\\pasta\\subpasta\\* |\\\\\\\\myserver\\\\share |. \\ \\ ou pasta\\\\subpasta |

>[!NOTE]
>Durante a criação de através da interface do Usuário, não precisa de barra invertida duplo de entrada (`\\`) para o escape tal como faz via JSON, especifique a barra invertida única.

### <a name="example-using-username-and-password-in-plain-text"></a>Exemplo: Usando o nome de utilizador e palavra-passe em texto simples

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

### <a name="example-using-encryptedcredential"></a>Exemplo: Usando encryptedcredential

```JSON
{
  "Name": " OnPremisesFileServerLinkedService ",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "D:\\",
      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
      "gatewayName": "mygateway"
    }
  }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa de seções e as propriedades que estão disponíveis para definir conjuntos de dados, consulte [criar conjuntos de dados](data-factory-create-datasets.md). Seções, como a estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados.

A secção typeProperties é diferente para cada tipo de conjunto de dados. Ele fornece informações como a localização e o formato dos dados no arquivo de dados. Os typeProperties secção para o conjunto de dados do tipo **partilha de ficheiros** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Especifica o Subcaminho para a pasta. Utilizar o caráter de escape '\' para carateres especiais na cadeia de caracteres. Não é suportado o filtro de carateres universais. Ver [exemplo ligado as definições de serviço e o conjunto de dados](#sample-linked-service-and-dataset-definitions) para obter exemplos.<br/><br/>Pode combinar essa propriedade com o **partitionBy** ter pasta caminhos baseados no setor de início/fim datas-horas. |Sim |
| fileName |Especifique o nome do arquivo na **folderPath** se pretender que a tabela para fazer referência a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros na pasta.<br/><br/>Quando **fileName** não está especificado para um conjunto de dados de saída e **preserveHierarchy** não está especificado no sink de atividade, o nome do ficheiro gerado é no seguinte formato: <br/><br/>`Data.<Guid>.txt` (Exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| fileFilter |Especifique um filtro para ser usado para selecionar um subconjunto de ficheiros em folderPath em vez de todos os ficheiros. <br/><br/>Valores permitidos são: `*` (vários carateres) e `?` (caráter individual).<br/><br/>Exemplo 1: "fileFilter": "*. log"<br/>Exemplo 2: "fileFilter": -1 - de 2014?. txt"<br/><br/>Tenha em atenção que fileFilter se aplica a um conjunto de dados de partilha de ficheiros de entrada. |Não |
| partitionedBy |Pode usar partitionedBy para especificar um folderPath/nome de ficheiro dinâmica para dados de séries temporais. Um exemplo é folderPath parametrizado por cada hora de dados. |Não |
| Formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se quiser **copiar ficheiros como-é** entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída. |Não |
| Compressão | Especifica o tipo e o nível de compressão dos dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Ideal** e **mais rápida**. ver [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> Não é possível utilizar nome de ficheiro e fileFilter em simultâneo.

### <a name="using-partitionedby-property"></a>Usando a propriedade de partitionedBy
Conforme mencionado na secção anterior, pode especificar um folderPath dinâmico e o nome do ficheiro de dados de séries de tempo com o **partitionedBy** propriedade, [funções de fábrica de dados e as variáveis do sistema](data-factory-functions-variables.md).

Para obter mais detalhes sobre os conjuntos de dados de séries temporais, agendamento e setores de compreender, veja [criar conjuntos de dados](data-factory-create-datasets.md), [agendamento e execução](data-factory-scheduling-and-execution.md), e [Criar pipelines](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Exemplo 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Neste exemplo, {setor} é substituído pelo valor da variável de sistema SliceStart de fábrica de dados no formato (YYYYMMDDHH). SliceStart refere-se para iniciar a hora do setor. FolderPath é diferente para cada setor. Por exemplo: deverá/wikisampledataout/2014100103 ou deverá/wikisampledataout/2014100104.

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

Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas que utilizam as propriedades de folderPath e nome de ficheiro.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades como nome, descrição, entrada e saída de conjuntos de dados e as políticas estão disponíveis para todos os tipos de atividades. Ao passo que, propriedades disponíveis no **typeProperties** secção da atividade varia com cada tipo de atividade.

Para a atividade de cópia, elas variam consoante os tipos de origens e sinks. Se estiver a mover dados de um sistema de ficheiros no local, defina o tipo de origem na atividade de cópia para **FileSystemSource**. Da mesma forma, se estiver a mover dados para um sistema de ficheiros no local, é definir o tipo de sink na atividade de cópia para **FileSystemSink**. Esta seção fornece uma lista de propriedades suportadas pelo FileSystemSource e FileSystemSink.

**FileSystemSource** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursiva |Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. |TRUE, False (predefinição) |Não |

**FileSystemSink** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| copyBehavior |Define o comportamento de cópia quando a origem é BlobSource ou sistema de ficheiros. |**PreserveHierarchy:** Preserva a hierarquia de ficheiros na pasta de destino. Ou seja, o caminho relativo do ficheiro de origem para a pasta de origem é o mesmo que o caminho relativo do ficheiro de destino para a pasta de destino.<br/><br/>**FlattenHierarchy:** Todos os ficheiros da pasta de origem são criados no primeiro nível de pasta de destino. Os ficheiros de destino são criados com um nome gerado automaticamente.<br/><br/>**MergeFiles:** Une todos os ficheiros da pasta de origem para um ficheiro. Se não for especificado o nome de blob/nome de ficheiro, o nome de ficheiro mesclada é o nome especificado. Caso contrário, ele é um nome de ficheiro gerado automaticamente. |Não |

### <a name="recursive-and-copybehavior-examples"></a>Exemplos de recursiva e copyBehavior
Esta secção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores para as propriedades de recursiva e copyBehavior.

| valor de recursiva | valor de copyBehavior | Comportamento resultante |
| --- | --- | --- |
| true |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a mesma estrutura de origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>O destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| true |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>O destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + 5 ficheiro de conteúdo é mesclado num ficheiro com um nome de ficheiro gerado automaticamente. |
| false |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Não é capturado Subfolder1 com File3, File4 e File5. |
| false |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/>Não é capturado Subfolder1 com File3, File4 e File5. |
| false |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura,<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 conteúdo é mesclado num arquivo com um nome de ficheiro gerado automaticamente.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para File1<br/><br/>Não é capturado Subfolder1 com File3, File4 e File5. |

## <a name="supported-file-and-compression-formats"></a>Formatos de ficheiro e de compressão suportados
Ver [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artigo em detalhes.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>Exemplos JSON para copiar dados de e para sistema de ficheiros
Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar um pipeline com o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados entre um sistema de ficheiros no local e o armazenamento de Blobs do Azure. No entanto, pode copiar dados *diretamente* de qualquer uma das origens qualquer um dos sinks listados na [suportado origens e sinks](data-factory-data-movement-activities.md#supported-data-stores-and-formats) através da atividade de cópia no Azure Data Factory.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Exemplo: Copiar dados de um sistema de ficheiros no local para o armazenamento de Blobs do Azure
Este exemplo mostra como copiar dados de um sistema de ficheiros no local para o armazenamento de Blobs do Azure. O exemplo possui as seguintes entidades do Data Factory:

* Um serviço ligado do tipo [OnPremisesFileServer](#linked-service-properties).
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [partilha de ficheiros](#dataset-properties).
* Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* R [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo seguinte copia dados de séries de tempo de um sistema de ficheiros no local para o armazenamento de Blobs do Azure a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas secções após os exemplos.

Como primeiro passo, configurar o Gateway de gestão de dados de acordo com as instruções em [mover dados entre origens no local e a nuvem com o Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md).

**Serviço ligado do servidor de ficheiros no local:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Recomendamos que utilize o **encryptedCredential** propriedade em vez disso, o **userid** e **palavra-passe** propriedades. Ver [serviço do servidor de ficheiros ligado](#linked-service-properties) para obter detalhes sobre este serviço ligado.

**Serviço ligado do armazenamento do Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Conjunto de dados de entrada de sistema de ficheiros no local:**

Dados são captados um novo ficheiro a cada hora. As propriedades de folderPath e nome de ficheiro são determinadas com base na hora de início do setor.

Definição `"external": "true"` informa a fábrica de dados que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

```JSON
{
  "name": "OnpremisesFileSystemInput",
  "properties": {
    "type": " FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Conjunto de dados de saída de armazenamento de Blobs do Azure:**

Os dados são escritos para um blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza o ano, mês, dia e partes de hora da hora de início.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Uma atividade de cópia num pipeline com a origem de sistema de ficheiros e de sink do Blob:**

O pipeline contém uma atividade de cópia que está configurada para utilizar os conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **FileSystemSource**, e **sink** tipo está definido como **BlobSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T19:00:00",
    "description":"Pipeline for copy activity",
    "activities":[
      {
        "name": "OnpremisesFileSystemtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "OnpremisesFileSystemInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
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
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Exemplo: Copiar dados de base de dados do Azure SQL para um sistema de ficheiros no local
O exemplo a seguir mostra:

* Um serviço ligado do tipo [AzureSqlDatabase.](data-factory-azure-sql-connector.md#linked-service-properties)
* Um serviço ligado do tipo [OnPremisesFileServer](#linked-service-properties).
* Um conjunto de dados de entrada do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* Um conjunto de dados de saída do tipo [partilha de ficheiros](#dataset-properties).
* Um pipeline com uma atividade de cópia que utiliza [SqlSource](data-factory-azure-sql-connector.md##copy-activity-properties) e [FileSystemSink](#copy-activity-properties).

O exemplo copia dados de séries de tempo de uma tabela do SQL do Azure para um sistema de ficheiros no local a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas secções após os exemplos.

**Serviço de ligado de base de dados SQL do Azure:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```

**Serviço ligado do servidor de ficheiros no local:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Recomendamos que utilize o **encryptedCredential** propriedade em vez de usar o **userid** e **palavra-passe** propriedades. Ver [serviço ligado do sistema de ficheiros](#linked-service-properties) para obter detalhes sobre este serviço ligado.

**Conjunto de dados de entrada SQL do Azure:**

O exemplo assume que criou uma tabela "MyTable" no Azure SQL e contém uma coluna chamada "timestampcolumn" para dados de séries temporais.

Definição ``“external”: ”true”`` informa a fábrica de dados que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Conjunto de dados de saída de sistema de ficheiros no local:**

Dados são copiados para um novo ficheiro a cada hora. O nome de ficheiro para o blob e folderPath são determinados com base na hora de início do setor.

```JSON
{
  "name": "OnpremisesFileSystemOutput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Uma atividade de cópia num pipeline com a origem SQL e de sink de sistema de ficheiros:**

O pipeline contém uma atividade de cópia que está configurada para utilizar os conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **SqlSource**e o **sink** tipo está definido como **FileSystemSink**. A consulta SQL especificado para o **SqlReaderQuery** propriedade seleciona os dados na hora anterior para copiar.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T20:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoOnPremisesFile",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "OnpremisesFileSystemOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "FileSystemSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 3,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

Também pode mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de sink na definição da atividade de cópia. Para obter detalhes, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e otimização
 Para saber mais sobre os principais fatores que afetam o desempenho de movimento de dados (atividade de cópia) no Azure Data Factory e diversas maneiras para otimizá-lo, consulte a [guia de sintonização de desempenho de atividade de cópia e](data-factory-copy-activity-performance.md).
