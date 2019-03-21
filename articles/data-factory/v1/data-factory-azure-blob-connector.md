---
title: Copiar dados de/para o armazenamento de Blobs do Azure | Documentos da Microsoft
description: 'Saiba como copiar dados de BLOBs no Azure Data Factory. Utilize a nossa amostra: Como copiar dados de e para armazenamento de Blobs do Azure e base de dados do Azure SQL.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ea4cf03b368cebbfc7d1229be28014b54f2c11d0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58004311"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Copiar dados de ou para armazenamento de Blobs do Azure com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-azure-blob-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-blob-storage.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conector de armazenamento de Blobs do Azure no V2](../connector-azure-blob-storage.md).


Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para copiar dados de e para armazenamento de Blobs do Azure. Ele se baseia no [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma visão geral do movimento de dados com a atividade de cópia.

## <a name="overview"></a>Descrição geral
Pode copiar dados de qualquer arquivo de dados de origem suportada para o armazenamento de Blobs do Azure ou do armazenamento de Blobs do Azure para qualquer arquivo de dados de sink suportados. A tabela seguinte fornece uma lista dos arquivos de dados suportados como origens ou coletores da atividade de cópia. Por exemplo, pode mover dados **partir** uma base de dados do SQL Server ou uma base de dados SQL do Azure **para** um armazenamento de Blobs do Azure. E, pode copiar dados **partir** armazenamento de Blobs do Azure **para** um Azure SQL Data Warehouse ou de uma coleção do Azure Cosmos DB.

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **do armazenamento de Blobs do Azure** para os seguintes dados armazena:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados de arquivos de dados seguintes **para o armazenamento de Blobs do Azure**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> Atividade de cópia suporta a cópia de dados de/para contas de armazenamento do Azure para fins gerais e armazenamento de BLOBs de acesso frequente/esporádico. A atividade suporta **ler de bloco, acrescentar ou blobs de páginas**, mas suporta **escrita em blobs de blocos apenas**. Armazenamento Premium do Azure não é suportado como um sink porque ele é apoiado por blobs de página.
>
> Atividade de cópia não elimina os dados da origem depois dos dados são copiados com êxito para o destino. Se tiver de eliminar dados de origem depois de uma cópia com êxito, crie uma [atividade personalizada](data-factory-use-custom-activities.md) para eliminar os dados e utilize a atividade no pipeline. Por exemplo, veja a [exemplo de blob ou uma pasta de eliminação no GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity).

## <a name="get-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move os dados de/para um armazenamento de Blobs do Azure com ferramentas/APIs diferentes.

A maneira mais fácil para criar um pipeline é utilizar o **Assistente para copiar**. Este artigo tem um [passo a passo](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) para criar um pipeline para copiar dados a partir de uma localização de armazenamento de Blobs do Azure para outra localização de armazenamento de Blobs do Azure. Para obter um tutorial sobre como criar um pipeline para copiar dados de um armazenamento de Blobs do Azure para a base de dados do Azure SQL, consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md).

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**e  **REST API**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se usar as ferramentas ou APIs, que execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink:

1. Criar uma **fábrica de dados**. Uma fábrica de dados pode conter um ou mais pipelines.
2. Crie **serviços ligados** para ligar a dados de entrada e saídos armazena à fábrica de dados. Por exemplo, se estiver a copiar dados de um armazenamento de Blobs do Azure para uma base de dados SQL do Azure, criar dois serviços ligados para ligar a sua conta de armazenamento do Azure e a base de dados SQL do Azure à fábrica de dados. Para propriedades de serviço ligado que são específicas para o armazenamento de Blobs do Azure, consulte [propriedades do serviço ligado](#linked-service-properties) secção.
2. Crie **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. O exemplo mencionado no último passo, vai criar um conjunto de dados para especificar o contentor de BLOBs e a pasta que contém os dados de entrada. Além disso, crie outro conjunto de dados para especificar a tabela SQL na base de dados SQL do Azure que contém os dados copiados do armazenamento de Blobs. Para as propriedades do conjunto de dados que são específicas para o armazenamento de Blobs do Azure, consulte [propriedades do conjunto de dados](#dataset-properties) secção.
3. Criar uma **pipeline** com uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, vai utilizar BlobSource como uma origem e SqlSink como sink para a atividade de cópia. Da mesma forma, se estiver a copiar na SQL Database do Azure para armazenamento de Blobs do Azure, utilize SqlSource e BlobSink a atividade de cópia. Para propriedades de atividade de cópia que são específicas para o armazenamento de Blobs do Azure, consulte [propriedades da atividade copy](#copy-activity-properties) secção. Para obter detalhes sobre como usar um arquivo de dados como uma origem ou sink, clique na ligação na secção anterior para seu armazenamento de dados.

Quando utiliza o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) são criadas automaticamente para. Ao utilizar ferramentas/APIs (exceto a .NET API), define essas entidades do Data Factory com o formato JSON.  Para exemplos com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de/para um armazenamento de Blobs do Azure, consulte [exemplos JSON](#json-examples-for-copying-data-to-and-from-blob-storage  ) seção deste artigo.

As secções seguintes fornecem detalhes sobre as propriedades JSON utilizadas para definir entidades do Data Factory específicas para o armazenamento de Blobs do Azure.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
Existem dois tipos de serviços ligados, que pode utilizar para ligar um armazenamento do Azure a uma fábrica de dados do Azure. São: **AzureStorage** serviço ligado e **AzureStorageSas** serviço ligado. O serviço ligado do armazenamento do Azure fornece a fábrica de dados com o acesso global para o armazenamento do Azure. Ao passo que o Azure Storage SAS (assinatura de acesso partilhado) ligado o serviço fornece a fábrica de dados com acesso restrito/tempo-limite para o armazenamento do Azure. Não existem outras diferenças entre estes dois serviços ligados. Escolha o serviço ligado que atenda às suas necessidades. As secções seguintes fornecem mais detalhes sobre estes dois serviços ligados.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para especificar um conjunto de dados para representar os dados de entrada ou de saída num armazenamento de Blobs do Azure, defina a propriedade de tipo de conjunto de dados para: **AzureBlob**. Definir o **linkedServiceName** serviço ligado de propriedade de conjunto de dados para o nome do armazenamento do Azure ou SAS de armazenamento do Azure.  Especificam as propriedades do tipo do conjunto de dados do **contentor de BLOBs** e o **pasta** no armazenamento de Blobs.

Para obter uma lista completa de secções JSON e propriedades disponíveis para definir conjuntos de dados, consulte a [criar conjuntos de dados](data-factory-create-datasets.md) artigo. Seções, como a estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).

Data factory suporta os seguintes valores de tipo de .NET em conformidade com CLS, com base para fornecer informações sobre o tipo "estrutura" para origens de dados de esquema na leitura, como de Blobs do Azure: Int16, Int32, Int64, Single, Double, Decimal, Byte [], booleano, cadeia, Guid, Datetime, Datetimeoffset, período de tempo. Fábrica de dados executa automaticamente as conversões de tipo ao mover dados de um arquivo de dados de origem para um arquivo de dados de sink.

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização, etc., formatar os dados no arquivo de dados. Os typeProperties secção para o conjunto de dados do tipo **AzureBlob** conjunto de dados tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho para o contentor e a pasta no armazenamento de Blobs. Exemplo: myblobcontainer\myblobfolder\ |Sim |
| fileName |Nome do blob. nome de ficheiro é opcional e diferencia maiúsculas de minúsculas.<br/><br/>Se especificar um nome de ficheiro, a atividade (incluindo cópia) funciona no Blob específico.<br/><br/>Quando o nome de ficheiro não for especificada, a cópia inclui todos os Blobs no folderPath para conjunto de dados de entrada.<br/><br/>Quando **fileName** não está especificado para um conjunto de dados de saída e **preserveHierarchy** não está especificado no sink de atividade, o nome do ficheiro gerado seria a seguir este formato: Dados. <Guid>. txt (por exemplo:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| partitionedBy |partitionedBy propriedade é opcional. Pode usá-lo para especificar um folderPath dinâmico e o nome do ficheiro de dados de séries de tempo. Por exemplo, pode ser parametrizado folderPath por cada hora de dados. Consulte a [usando a seção de propriedade partitionedBy](#using-partitionedBy-property) para obter detalhes e exemplos. |Não |
| Formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se quiser **copiar ficheiros como-é** entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída. |Não |
| Compressão | Especifica o tipo e o nível de compressão dos dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Ideal** e **mais rápida**. Para obter mais informações, consulte [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

### <a name="using-partitionedby-property"></a>Usando a propriedade de partitionedBy
Conforme mencionado na secção anterior, pode especificar um folderPath dinâmico e o nome do ficheiro de dados de séries de tempo com o **partitionedBy** propriedade, [funções de fábrica de dados e as variáveis do sistema](data-factory-functions-variables.md).

Para obter mais informações sobre conjuntos de dados de séries de tempo, agendamento e setores, consulte [criar conjuntos de dados](data-factory-create-datasets.md) e [agendamento e execução](data-factory-scheduling-and-execution.md) artigos.

#### <a name="sample-1"></a>Exemplo 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Neste exemplo, {setor} é substituído pelo valor da variável do sistema SliceStart fábrica de dados no formato (YYYYMMDDHH) especificado. SliceStart refere-se para iniciar a hora do setor. FolderPath é diferente para cada setor. Por exemplo: deverá/wikisampledataout/2014100103 ou deverá/wikisampledataout/2014100104

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

Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas, que são utilizadas pelas propriedades folderPath e nome de ficheiro.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades como nome, descrição, entrada e saída de conjuntos de dados e as políticas estão disponíveis para todos os tipos de atividades. Ao passo que, propriedades disponíveis no **typeProperties** secção da atividade varia com cada tipo de atividade. Para a atividade de cópia, elas variam consoante os tipos de origens e sinks. Se estiver a mover dados de um armazenamento de Blobs do Azure, definir o tipo de origem na atividade de cópia para **BlobSource**. Da mesma forma, se estiver a mover dados para um armazenamento de Blobs do Azure, definir o tipo de sink na atividade de cópia para **BlobSink**. Esta secção fornece uma lista das propriedades compatíveis com BlobSource e BlobSink.

**BlobSource** suporta as seguintes propriedades na **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursiva |Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. |False a TRUE (valor predefinido), |Não |

**BlobSink** suporta as seguintes propriedades **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| copyBehavior |Define o comportamento de cópia quando a origem é BlobSource ou sistema de ficheiros. |<b>PreserveHierarchy</b>: preserva a hierarquia de ficheiros na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico para o caminho relativo do ficheiro de destino para a pasta de destino.<br/><br/><b>FlattenHierarchy</b>: todos os ficheiros da pasta de origem estão no primeiro nível de pasta de destino. Os ficheiros de destino têm o nome gerado automaticamente. <br/><br/><b>MergeFiles</b>: une todos os ficheiros da pasta de origem para um ficheiro. Se o nome de ficheiro/Blob for especificado, o nome de ficheiro intercalada seria o nome especificado; caso contrário, seria o nome de ficheiro gerado automaticamente. |Não |

**BlobSource** também oferece suporte a essas duas propriedades para compatibilidade com versões anteriores.

* **treatEmptyAsNull**: Especifica se tratar de uma cadeia nula ou vazia como valor nulo.
* **skipHeaderLineCount** -Especifica quantas linhas tem de ser ignoradas. É aplicável apenas ao conjunto de dados de entrada está a utilizar TextFormat.

Da mesma forma, **BlobSink** suporta a seguinte propriedade para compatibilidade com versões anteriores.

* **blobWriterAddHeader**: Especifica se pretende adicionar um cabeçalho de definições da coluna ao escrever para um conjunto de dados de saída.

Conjuntos de dados agora suportam as seguintes propriedades que implementam a mesma funcionalidade: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

A tabela seguinte fornece orientações sobre como utilizar as novas propriedades do conjunto de dados em vez destas propriedades de origem/sink do blob.

| Propriedade de atividade de cópia | Propriedade de conjunto de dados |
|:--- |:--- |
| skipHeaderLineCount no BlobSource |Propriedades skipLineCount e firstRowAsHeader. Linhas são ignoradas primeiro e, em seguida, é de leitura a primeira linha como cabeçalho. |
| treatEmptyAsNull no BlobSource |treatEmptyAsNull num conjunto de dados de entrada |
| blobWriterAddHeader no BlobSink |firstRowAsHeader num conjunto de dados de saída |

Ver [especificar TextFormat](data-factory-supported-file-and-compression-formats.md#text-format) secção para obter informações detalhadas sobre estas propriedades.

### <a name="recursive-and-copybehavior-examples"></a>Exemplos de recursiva e copyBehavior
Esta secção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursiva e copyBehavior.

| recursiva | copyBehavior | Comportamento resultante |
| --- | --- | --- |
| true |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a mesma estrutura de origem<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>O destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| true |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>O destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + 5 ficheiro de conteúdo é mesclado num ficheiro com o nome de ficheiro gerado automaticamente |
| false |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>A pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Subfolder1 com File3, File4 e File5 não são aplicadas. |
| false |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>A pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/><br/>Subfolder1 com File3, File4 e File5 não são aplicadas. |
| false |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>A pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 conteúdo é mesclado num arquivo com o nome de ficheiro gerado automaticamente. Nome gerado automaticamente para File1<br/><br/>Subfolder1 com File3, File4 e File5 não são aplicadas. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Descrição Passo a Passo: Utilizar o Assistente de cópia para copiar dados de/para o armazenamento de BLOBs
Vamos examinar como copiar rapidamente dados de/para um armazenamento de Blobs do Azure. Nestas instruções, os dados de origem e destino armazena do tipo: Armazenamento de Blobs do Azure. O pipeline nestas instruções copia dados de uma pasta para outra pasta no mesmo contentor de Blobs. Este passo a passo é intencionalmente simples para mostrar as definições ou propriedades quando utilizar o armazenamento de BLOBs como uma origem ou sink.

### <a name="prerequisites"></a>Pré-requisitos
1. Criar um para fins gerais **conta de armazenamento do Azure** se ainda não tiver um. Utilize o armazenamento de BLOBs como ambas **origem** e **destino** do arquivo de dados nestas instruções. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md) para obter os passos para criar uma.
2. Criar um contentor de BLOBs denominado **adfblobconnector** na conta de armazenamento.
4. Crie uma pasta denominada **entrada** no **adfblobconnector** contentor.
5. Crie um ficheiro denominado **Emp. txt** com o seguinte conteúdo e carregue-o para o **entrada** pasta usando ferramentas como [Explorador de armazenamento do Azure](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>Criar a fábrica de dados
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **criar um recurso** no canto superior esquerdo, clique em **inteligência + análise**e clique em **Data Factory**.
3. Na **nova fábrica de dados** painel:  
    1. Introduza **ADFBlobConnectorDF** para o **nome**. O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro: `*Data factory name “ADFBlobConnectorDF” is not available`, altere o nome da fábrica de dados (por exemplo, yournameADFBlobConnectorDF) e tente criar novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.
    2. Selecione a sua **subscrição** do Azure.
    3. Grupo de recursos, selecione **utilizar existente** para selecionar um grupo de recursos existente (ou) selecione **criar nova** para introduzir um nome para um grupo de recursos.
    4. Selecione uma **localização** para a fábrica de dados.
    5. Selecione a caixa de verificação **Afixar ao dashboard**, na parte inferior do painel.
    6. Clique em **Criar**.
3. Após concluir a criação, verá o painel **Data Factory**, conforme apresentado na imagem seguinte:  ![Home page do Data factory](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Assistente de Cópia
1. Na home page do Data Factory, clique nas **copiar dados** mosaico para iniciar **Assistente de cópia de dados** num separador à parte.  
    
    > [!NOTE]
    > Se vir que o navegador da web é "Autorizar...", desative/desmarque **bloquear cookies de terceiros e dados do site** definição (ou) mantenha-a ativada e crie uma exceção para **login.microsoftonline.com**e, em seguida, tente iniciar novamente o assistente.
2. Na página **Propriedades**:
    1. Introduza **CopyPipeline** para **nome da tarefa**. O nome da tarefa é o nome do pipeline na fábrica de dados.
    2. Introduza um **Descrição** para a tarefa (opcional).
    3. Para **cadência de tarefas ou agenda de tarefas**, mantenha a **regularmente executada numa agenda** opção. Se quiser executar esta tarefa apenas uma vez, em vez de executar repetidamente com base numa agenda, selecione **executar uma vez agora**. Se selecionar, **executar agora uma vez** opção, uma [pipeline única](data-factory-create-pipelines.md#onetime-pipeline) é criado.
    4. Manter as definições para **periódico padrão**. Esta tarefa é executada diariamente entre as horas de início e de fim que especifique no próximo passo.
    5. Alteração da **hora da data de início** ao **04/21/2017**.
    6. Alteração da **data e hora de fim** ao **04/25/2017**. Pode querer digite a data em vez de navegar por meio de calendário.
    8. Clique em **Seguinte**.
        ![Ferramenta copiar – página de propriedades](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. Na página **Arquivo de dados de origem**, clique no mosaico **Blob Storage do Azure**. Utilize esta página para especificar o arquivo de dados de origem para a tarefa de cópia. Pode utilizar um serviço ligado do arquivo de dados existente (ou) especificar um novo arquivo de dados. Para utilizar um serviço ligado existente, selecionaria **de serviços ligados existentes** e selecione o serviço ligado correto.
    ![Ferramenta copiar – página de arquivo de dados de origem](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. Na página **Especificar a conta de armazenamento de blobs do Azure**:
    1. Mantenha o nome gerado automaticamente para **nome da ligação**. O nome da ligação é o nome do serviço ligado do tipo: Armazenamento do Azure.
    2. Confirme que a opção **A partir de subscrições do Azure** está selecionada para o **Método de seleção de contas**.
    3. Selecione a sua subscrição do Azure ou manter **Selecionar tudo** para **subscrição do Azure**.
    4. Selecione uma **Conta de armazenamento do Azure** na lista das contas de armazenamento do Azure disponíveis na subscrição selecionada. Também pode optar por introduzir as definições de conta de armazenamento manualmente, selecionando **introduzir manualmente** opção para o **método de seleção de contas**.
    5. Clique em **Seguinte**.  
        ![Ferramenta copiar – especificar a conta de armazenamento de Blobs do Azure](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. Na página **Escolher o ficheiro ou pasta de entrada**:
    1. Faça duplo clique em **adfblobcontainer**.
    2. Selecione **entrada**e clique em **escolha**. Nestas instruções, selecione a pasta de entrada. Pode também selecionar o ficheiro Emp. txt na pasta em vez disso.
        ![Ferramenta copiar - escolher o ficheiro de entrada ou a pasta](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. Sobre o **escolher o ficheiro de entrada ou a pasta** página:
    1. Confirme que o **ficheiro ou pasta** está definida como **adfblobconnector/input**. Se os ficheiros estiverem em subpastas, por exemplo, 2017/04/01, 2017/04/02 e assim por diante, introduza adfblobconnector/input / {ano} / {month} / {day} para ficheiro ou pasta. Quando pressiona TAB para fora da caixa de texto, verá três listas pendentes para selecionar formatos por ano (AAAA), o mês (MM) e o dia (dd).
    2. Não defina **copiar ficheiro recursivamente**. Selecione esta opção para recursivamente completa através de pastas para os ficheiros sejam copiados para o destino.
    3. Isso não é possível a **cópia binária** opção. Selecione esta opção para efetuar uma cópia binária do ficheiro de origem para o destino. Não selecione para este passo a passo para que pode ver mais opções nas páginas seguintes.
    4. Confirme que o **tipo de compressão** está definida como **nenhum**. Selecione um valor para esta opção se os arquivos de origem são compactados em um dos formatos suportados.
    5. Clique em **Seguinte**.
    ![Ferramenta copiar - escolher o ficheiro de entrada ou a pasta](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. Na página **Definições do formato de ficheiro**, pode ver os delimitadores e o esquema que é detetado automaticamente pelo assistente ao analisar o ficheiro.
    1. Confirme as seguintes opções:  
        a. O **formato de ficheiro** está definida como **formato de texto**. Pode ver todos os formatos suportados na lista pendente. Por exemplo: JSON, Avro, ORC, Parquet.
       b. O **delimitador de colunas** está definida como `Comma (,)`. Pode ver os delimitadores de coluna suportados pelo Data Factory na lista pendente. Também pode especificar um delimitador personalizado.
       c. O **delimitador de linhas** está definida como `Carriage Return + Line feed (\r\n)`. Pode ver os delimitadores de linhas suportados pelo Data Factory na lista pendente. Também pode especificar um delimitador personalizado.
       d. O **Ignorar contagem de linhas** está definida como **0**. Se quiser algumas linhas de ser ignorada na parte superior do ficheiro, introduza o número aqui.
       e. O **primeira linha de dados contém os nomes das colunas** não está definido. Se os ficheiros de origem contêm nomes de coluna na primeira linha, selecione esta opção.
       f. O **tratar o valor da coluna vazia como null** opção está definida.
    2. Expanda **definições avançadas** para ver a opção avançada disponível.
    3. Na parte inferior da página, consulte a **pré-visualização** de dados do ficheiro Emp. txt.
    4. Clique em **esquema** separador na parte inferior para ver o esquema que o Assistente para copiar inferido examinando os dados no arquivo de origem.
    5. Clique em **Seguinte** depois de rever os delimitadores e pré-visualizar os dados.
    ![Ferramenta copiar – definições do formato de ficheiro](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. Sobre o **página de arquivo de dados de destino**, selecione **armazenamento de Blobs do Azure**e clique em **seguinte**. Estiver a utilizar o armazenamento de Blobs do Azure como ambos os arquivos de dados de origem e destino nestas instruções.  
    ![Ferramenta copiar – arquivo de dados de destino selecione](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. No **especifique a conta de armazenamento de Blobs do Azure** página:  
    1. Introduza **AzureStorageLinkedService** para o **nome da ligação** campo.
    2. Confirme que a opção **A partir de subscrições do Azure** está selecionada para o **Método de seleção de contas**.
    3. Selecione a sua **subscrição** do Azure.
    4. Selecione a sua conta de armazenamento do Azure.
    5. Clique em **Seguinte**.
10. Sobre o **escolher o ficheiro de saída ou a pasta** página:  
    1. Especifique **caminho da pasta** como **adfblobconnector/saída / {ano} / {month} / {day}**. Introduza **SEPARADOR**.
    1. Para o **ano**, selecione **aaaa**.
    1. Para o **mês**, confirme que está definida como **MM**.
    1. Para o **dia**, confirme que está definida como **dd**.
    1. Confirme que o **tipo de compressão** está definida como **nenhum**.
    1. Confirme que o **copiar comportamento** está definida como **intercalar ficheiros**. Se o ficheiro de saída com o mesmo nome já existir, o novo conteúdo é adicionado ao mesmo ficheiro no final.
    1. Clique em **Seguinte**.
       ![Ferramenta copiar – escolha a pasta ou ficheiro de saída](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. Sobre o **definições do formato de ficheiro** página, reveja as definições e clique em **próxima**. Uma das opções adicionais aqui é adicionar um cabeçalho de ficheiro de saída. Se selecionar essa opção, é adicionada uma linha de cabeçalho com nomes das colunas do esquema da origem. Pode renomear os nomes de coluna predefinido ao visualizar o esquema para a origem. Por exemplo, pode alterar a primeira coluna Nome próprio e a segunda coluna Last Name. Em seguida, o ficheiro de saída é gerado com um cabeçalho com esses nomes, como nomes de colunas.
    ![Ferramenta copiar – definições do formato do ficheiro de destino](media/data-factory-azure-blob-connector/file-format-destination.png)
12. Na **as definições de desempenho** página, confirme se **unidades de cloud** e **cópias em paralelo** são definidas para **automática**e clique em seguinte. Para obter detalhes sobre estas definições, consulte [copie o guia de sintonização de desempenho de atividade e](data-factory-copy-activity-performance.md#parallel-copy).
    ![Ferramenta copiar – definições de desempenho](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. Sobre o **resumo** página, reveja todas as definições (propriedades da tarefa, as definições de origem e de destino e as definições de cópia) e clique em **próxima**.
    ![Ferramenta copiar – página de resumo](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Reveja as informações na página **Resumo** e clique em **Concluir**. O assistente cria dois serviços ligados, dois conjuntos de dados (entrada e saída) e um pipeline na fábrica de dados (a partir da qual iniciou o Assistente de Cópia).
    ![Ferramenta copiar – página de implementação](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>Monitorizar o pipeline (tarefa de cópia)

1. Clique no link `Click here to monitor copy pipeline` sobre o **implementação** página.
2. Deverá ver o **monitorizar e gerir a aplicação** num separador à parte.  ![Monitorizar e gerir aplicações](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Alterar o **começar** tempo na parte superior para `04/19/2017` e **end** altura de `04/27/2017`e, em seguida, clique em **aplicar**.
4. Deverá ver cinco janelas de atividade no **janelas de ATIVIDADE** lista. O **WindowStart** vezes devem abranger todos os dias do início do pipeline para horas de fim do pipeline.
5. Clique em **Atualize** botão para o **janelas de ATIVIDADE** lista algumas vezes até ver o estado de todas as janelas de atividade é definida como Ready.
6. Agora, certifique-se de que os ficheiros de saída são gerados na pasta de saída do contentor de adfblobconnector. Deverá ver a seguinte estrutura de pastas na pasta de saída:
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
   Para obter informações detalhadas sobre como monitorizar e gerir fábricas de dados, consulte [monitorizar e gerir o pipeline do Data Factory](data-factory-monitor-manage-app.md) artigo.

### <a name="data-factory-entities"></a>Entidades do Data Factory
Agora, mude para o separador com a home page da fábrica de dados. Tenha em atenção que existem dois serviços ligados, dois conjuntos de dados e um pipeline na fábrica de dados agora.

![Home page do Data Factory com entidades](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Clique em **autor e implementar** para iniciar o Editor do Data Factory.

![Editor do Data Factory](media/data-factory-azure-blob-connector/data-factory-editor.png)

Deverá ver as seguintes entidades do Data Factory na sua fábrica de dados:

- Dois serviços ligados. Uma para a origem e outro para o destino. Consulte os serviços ligados para a mesma conta de armazenamento do Azure nestas instruções.
- Dois conjuntos de dados. Um conjunto de dados de entrada e um conjunto de dados de saída. Nestas instruções, ambos utilizam o mesmo contentor de BLOBs, mas Consulte para pastas diferentes (entrada e saída).
- Um pipeline. O pipeline contém uma atividade de cópia que utiliza uma origem de blob e um sink de BLOBs para copiar dados a partir de uma localização de Blobs do Azure para outra localização de Blobs do Azure.

As secções seguintes fornecem mais informações sobre estas entidades.

#### <a name="linked-services"></a>Serviços ligados
Deverá ver dois serviços ligados. Uma para a origem e outro para o destino. Nestas instruções, ambas as definições parecem iguais, exceto para os nomes. O **tipo** do serviço ligado está definido como **AzureStorage**. A propriedade mais importante da definição do serviço ligado está a **connectionString**, que é utilizado pelo Data Factory para ligar à sua conta de armazenamento do Azure em tempo de execução. Ignore a propriedade hubName na definição.

##### <a name="source-blob-storage-linked-service"></a>Serviço de ligado de armazenamento de BLOBs de origem
```json
{
    "name": "Source-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

##### <a name="destination-blob-storage-linked-service"></a>Serviço de ligado de armazenamento de blob de destino

```json
{
    "name": "Destination-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

Para obter mais informações sobre o serviço ligado do armazenamento do Azure, consulte [propriedades do serviço ligado](#linked-service-properties) secção.

#### <a name="datasets"></a>Conjuntos de dados
Existem dois conjuntos de dados: um conjunto de dados de entrada e um conjunto de dados de saída. O tipo do conjunto de dados está definido como **AzureBlob** para ambos.

O conjunto de dados de entrada aponta para o **entrada** pasta da **adfblobconnector** contentor de Blobs. O **externo** estiver definida como **verdadeiro** para este conjunto de dados que os dados não são produzidos pelo pipeline com a atividade de cópia que usa este conjunto de dados como entrada.

O conjunto de dados de saída aponta para o **saída** pasta do mesmo contentor de Blobs. O conjunto de dados de saída também utiliza o ano, mês e dia dos **SliceStart** variável do sistema para avaliar dinamicamente o caminho para o ficheiro de saída. Para obter uma lista de funções e variáveis de sistema do Data Factory suportadas, consulte [funções de Data Factory e variáveis de sistema](data-factory-functions-variables.md). O **externo** estiver definida como **falso** (valor predefinido) porque este conjunto de dados é produzido pelo pipeline.

Para obter mais informações sobre as propriedades suportadas pelo conjunto de dados de Blobs do Azure, consulte [propriedades do conjunto de dados](#dataset-properties) secção.

##### <a name="input-dataset"></a>Conjunto de dados de entrada

```json
{
    "name": "InputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Source-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/input/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

##### <a name="output-dataset"></a>Conjunto de dados de saída

```json
{
    "name": "OutputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Destination-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/output/{year}/{month}/{day}",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
            "partitionedBy": [
                { "name": "year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }
            ]
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

#### <a name="pipeline"></a>Pipeline
O pipeline tem apenas uma atividade. O **tipo** da atividade está definida como **cópia**. Nas propriedades do tipo para a atividade, há duas seções, uma para a origem e a outra para o sink. O tipo de origem está definido como **BlobSource** como a atividade está a copiar dados de um armazenamento de Blobs. O tipo de sink é definido como **BlobSink** como a atividade copiar dados para um armazenamento de Blobs. A atividade de cópia obtém InputDataset-z4y como entrada e OutputDataset-z4y como o resultado.

Para obter mais informações sobre as propriedades suportadas pelo BlobSource e BlobSink, consulte [propriedades da atividade Copy](#copy-activity-properties) secção.

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "MergeFiles",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-z4y"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-z4y"
                    }
                ],
                "policy": {
                    "timeout": "1.00:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 3,
                    "longRetry": 0,
                    "longRetryInterval": "00:00:00"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "Activity-0-Blob path_ adfblobconnector_input_->OutputDataset-z4y"
            }
        ],
        "start": "2017-04-21T22:34:00Z",
        "end": "2017-04-25T05:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>Exemplos JSON para copiar dados de e para armazenamento de BLOBs
Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de e para armazenamento de Blobs do Azure e base de dados do Azure SQL. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das origens qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure Data Factory.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>Exemplo JSON: Copiar dados de armazenamento de BLOBs para base de dados SQL
O exemplo a seguir mostra:

1. Um serviço ligado do tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](#linked-service-properties).
3. Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](#dataset-properties).
4. Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. R [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que utiliza [BlobSource](#copy-activity-properties) e [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

As cópias de exemplo blob de dados a partir do Azure de série de tempo para um SQL do Azure de tabela por hora. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

**Serviço ligado do SQL do Azure:**

```json
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
**Serviço ligado do armazenamento do Azure:**

```json
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
O Azure Data Factory suporta dois tipos de serviços de armazenamento do Azure ligada: **AzureStorage** e **AzureStorageSas**. Para o primeiro, especifica a cadeia de ligação que inclui a chave da conta e para aquele posterior, especifique o Uri de assinatura de acesso partilhado (SAS). Ver [serviços ligados](#linked-service-properties) secção para obter detalhes.

**Conjunto de dados de entrada BLOBs do Azure:**

Dados são captados um blob novo a cada hora (frequência: hora, intervalo: 1). O nome de ficheiro e caminho de pasta para o blob dinamicamente são avaliados com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês e parte do dia da hora de início e nome de ficheiro utiliza a parte de hora a hora de início. "externo": "true" definição informa o Data Factory, que a tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
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
**Conjunto de dados SQL do Azure:**

Os dados de cópias de exemplo para uma tabela com o nome "MyTable" numa base de dados SQL do Azure. Crie a tabela na base de dados SQL do Azure com o mesmo número de colunas, como esperar que o ficheiro CSV de BLOBs para conter. Novas linhas são adicionadas à tabela de cada hora.

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Uma atividade de cópia num pipeline com a origem do Blob e de sink do SQL:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **BlobSource** e **sink** tipo está definido como **SqlSink**.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
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
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>Exemplo JSON: Copiar dados do SQL do Azure para BLOBs do Azure
O exemplo a seguir mostra:

1. Um serviço ligado do tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](#linked-service-properties).
3. Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](#dataset-properties).
5. R [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) e [BlobSink](#copy-activity-properties).

O exemplo copia dados de séries de tempo de uma tabela do SQL do Azure para um blob do Azure à hora. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

**Serviço ligado do SQL do Azure:**

```json
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
**Serviço ligado do armazenamento do Azure:**

```json
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
O Azure Data Factory suporta dois tipos de serviços de armazenamento do Azure ligada: **AzureStorage** e **AzureStorageSas**. Para o primeiro, especifica a cadeia de ligação que inclui a chave da conta e para aquele posterior, especifique o Uri de assinatura de acesso partilhado (SAS). Ver [serviços ligados](#linked-service-properties) secção para obter detalhes.

**Conjunto de dados de entrada SQL do Azure:**

O exemplo assume que criou uma tabela "MyTable" SQL do Azure e contém uma coluna chamada "timestampcolumn" para dados de séries de tempo.

A definição "externo": "true" informa serviço Data Factory que a tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

```json
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

**Conjunto de dados de Blobs do Azure:**

Os dados são escritos para um blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e partes de horas da hora de início.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
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

**Uma atividade de cópia num pipeline com a origem SQL e de sink do Blob:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **SqlSource** e **sink** tipo está definido como **BlobSink**. A consulta SQL especificada para o **SqlReaderQuery** propriedade seleciona os dados na hora anterior para copiar.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
> Para mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de sink, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e Otimização
Ver [desempenho de atividade de cópia e guia de ajuste](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chave a que um impacto no desempenho de movimento de dados (atividade de cópia) no Azure Data Factory e diversas maneiras para otimizá-lo.
