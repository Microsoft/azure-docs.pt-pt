---
title: Copiar dados de/para o armazenamento de BLOBs do Azure
description: 'Saiba como copiar dados de BLOB em Azure Data Factory. Use nosso exemplo: como copiar dados de e para o armazenamento de BLOBs do Azure e o banco de dados SQL do Azure.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: eab332f102b9e39981e2d8ed6e84f73fada87a1a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981673"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Copiar dados de ou para o armazenamento de BLOBs do Azure usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-azure-blob-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-blob-storage.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [conector do armazenamento de BLOBs do Azure na v2](../connector-azure-blob-storage.md).


Este artigo explica como usar a atividade de cópia no Azure Data Factory para copiar dados de e para o armazenamento de BLOBs do Azure. Ele se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

## <a name="overview"></a>Visão geral
Você pode copiar dados de qualquer armazenamento de dados de origem com suporte para o armazenamento de blob do Azure ou do armazenamento de BLOBs do Azure para qualquer armazenamento de dados de coletor com suporte. A tabela a seguir fornece uma lista de armazenamentos de dados com suporte como fontes ou coletores pela atividade de cópia. Por exemplo, você pode mover dados **de** um SQL Server banco de dado ou de um Azure SQL Database **para** um armazenamento de BLOBs do Azure. E você pode copiar dados **do armazenamento de** BLOBs do Azure **para** um SQL data warehouse do Azure ou uma coleção de Azure Cosmos DB.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Cenários suportados
Você pode copiar dados **do armazenamento de BLOBs do Azure** para os seguintes armazenamentos de dados:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Você pode copiar dados dos seguintes repositórios de dados **para o armazenamento de BLOBs do Azure**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> A atividade de cópia dá suporte à cópia de dados de/para as contas de armazenamento do Azure de uso geral e para o armazenamento de BLOBs quente/frio. A atividade dá suporte à **leitura de blobs de bloco, acréscimo ou página**, mas dá suporte **à gravação somente em blobs de blocos**. Não há suporte para o armazenamento Premium do Azure como um coletor porque ele é apoiado por blobs de página.
>
> A atividade de cópia não exclui dados da origem depois que os dados são copiados com êxito para o destino. Se você precisar excluir dados de origem após uma cópia bem-sucedida, crie uma [atividade personalizada](data-factory-use-custom-activities.md) para excluir os dados e use a atividade no pipeline. Para obter um exemplo, consulte a [amostra excluir BLOB ou pasta no GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity).

## <a name="get-started"></a>Começar
Você pode criar um pipeline com uma atividade de cópia que move dados de/para um armazenamento de BLOBs do Azure usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de cópia**. Este artigo tem um [passo a passos](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) para criar um pipeline para copiar dados de um local de armazenamento de BLOBs do Azure para outro local de armazenamento de BLOBs do Azure. Para obter um tutorial sobre a criação de um pipeline para copiar dados de um armazenamento de BLOBs do Azure para o Azure SQL Database, consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md).

Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net**e **API REST**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie um **Data Factory**. Um data factory pode conter um ou mais pipelines.
2. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory. Por exemplo, se estiver copiando dados de um armazenamento de blob do Azure para um banco de dados SQL do Azure, você criará dois serviços vinculados para vincular sua conta de armazenamento do Azure e o banco de dados SQL do Azure ao seu data factory. Para propriedades do serviço vinculado que são específicas para o armazenamento de BLOBs do Azure, consulte a seção [Propriedades do serviço vinculado](#linked-service-properties) .
2. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia. No exemplo mencionado na última etapa, você cria um conjunto de dados para especificar o contêiner de BLOBs e a pasta que contém o dado de entrada. Além disso, você cria outro conjunto de dados para especificar a tabela SQL no banco de dado SQL do Azure que contém a data copiada do armazenamento de BLOBs. Para propriedades de conjunto de informações específicas do armazenamento de BLOBs do Azure, consulte a seção [Propriedades do conjunto](#dataset-properties) de informações.
3. Crie um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída. No exemplo mencionado anteriormente, você usa o Blobname como uma origem e sqlsink como um coletor para a atividade de cópia. Da mesma forma, se você estiver copiando do banco de dados SQL do Azure para o armazenamento de BLOBs do Azure, use sqlsource e BlobSink na atividade de cópia. Para propriedades da atividade de cópia que são específicas do armazenamento de BLOBs do Azure, consulte a seção [Propriedades da atividade de cópia](#copy-activity-properties) . Para obter detalhes sobre como usar um armazenamento de dados como uma origem ou um coletor, clique no link na seção anterior para seu armazenamento de dados.

Quando você usa o assistente, as definições de JSON para essas entidades de Data Factory (serviços vinculados, conjuntos de valores e o Pipeline) são criadas automaticamente para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades de Data Factory usando o formato JSON.  Para obter exemplos com definições de JSON para Data Factory entidades usadas para copiar dados de/para um armazenamento de BLOBs do Azure, consulte a seção [exemplos de JSON](#json-examples-for-copying-data-to-and-from-blob-storage  ) deste artigo.

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas para o armazenamento de BLOBs do Azure.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
Há dois tipos de serviços vinculados que você pode usar para vincular um armazenamento do Azure a uma data factory do Azure. Eles são: serviço vinculado **AzureStorage** e serviço vinculado **AzureStorageSas** . O serviço vinculado do armazenamento do Azure fornece o data factory com acesso global ao armazenamento do Azure. Enquanto que o serviço vinculado do SAS do armazenamento do Azure (assinatura de acesso compartilhado) fornece o data factory com acesso restrito/limitado ao armazenamento do Azure. Não há outras diferenças entre esses dois serviços vinculados. Escolha o serviço vinculado que atenda às suas necessidades. As seções a seguir fornecem mais detalhes sobre esses dois serviços vinculados.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para especificar um DataSet para representar dados de entrada ou de saída em um armazenamento de BLOBs do Azure, defina a propriedade Type do DataSet como: **AzureBlob**. Defina a propriedade **linkedServiceName** do conjunto de um como o nome do armazenamento do Azure ou do serviço vinculado de SAS do armazenamento do Azure.  As propriedades de tipo do conjunto de um especificam o **contêiner de blob** e a **pasta** no armazenamento de BLOBs.

Para obter uma lista completa das seções JSON & propriedades disponíveis para definir os conjuntos de valores, consulte o artigo [criando conjuntos](data-factory-create-datasets.md) de itens. As seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, BLOB do Azure, tabela do Azure, etc.).

O data Factory dá suporte aos seguintes valores de tipo baseado em .NET em conformidade com CLS para fornecer informações de tipo em "estrutura" para fontes de dados de esquema em leitura como blob do Azure: Int16, Int32, Int64, Single, Double, Decimal, Byte [], bool, String, GUID, DateTime, DateTimeOffset, TimeSpan. Data Factory executa automaticamente conversões de tipo ao mover dados de um armazenamento de dados de origem para um armazenamento de dados de coletor.

A seção **typeproperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local, o formato, etc., do dado no armazenamento de dados. A seção typeproperties para o conjunto de **AzureBlob** do tipo DataSet tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| folderPath |Caminho para o contentor e a pasta no armazenamento de Blobs. Exemplo: myblobcontainer\myblobfolder\ |Sim |
| fileName |Nome do blob. o nome do arquivo é opcional e diferencia maiúsculas de minúsculas.<br/><br/>Se você especificar um nome de arquivo, a atividade (incluindo a cópia) funcionará no blob específico.<br/><br/>Quando fileName não for especificado, Copy incluirá todos os BLOBs no folderPath para o conjunto de dados de entrada.<br/><br/>Quando **filename** não for especificado para um conjunto de dados de saída e **preserveHierarchy** não for especificado no coletor de atividade, o nome do arquivo gerado estaria no seguinte formato: `Data.<Guid>.txt` (por exemplo: Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt) |Não |
| partitionedBy |partitionedBy é uma propriedade opcional. Você pode usá-lo para especificar um folderPath e um nome de arquivo dinâmico para dados de série temporal. Por exemplo, folderPath pode ser parametrizado para cada hora dos dados. Consulte a [seção usando a propriedade partitionedBy](#using-partitionedby-property) para obter detalhes e exemplos. |Não |
| format | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se quiser **copiar ficheiros como-é** entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída. |Não |
| compression | Especifica o tipo e o nível de compressão dos dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Optimal** e **Fastest**. Para obter mais informações, consulte [formatos de arquivo e compactação em Azure data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

### <a name="using-partitionedby-property"></a>Usando a propriedade partitionedBy
Conforme mencionado na seção anterior, você pode especificar um folderPath e um nome de arquivo dinâmico para dados de série temporal com a propriedade **partitionedBy** , [funções de data Factory e as variáveis de sistema](data-factory-functions-variables.md).

Para obter mais informações sobre conjuntos de dados de série temporal, agendamento e fatias, consulte [criando conjuntos](data-factory-create-datasets.md) de dados e [agendando](data-factory-scheduling-and-execution.md) artigos de execução de &.

#### <a name="sample-1"></a>Exemplo 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Neste exemplo, {Slice} é substituído pelo valor de Data Factory variável de sistema SliceStart no formato (AAAAMMDDHH) especificado. O SliceStart refere-se à hora de início da fatia. FolderPath é diferente para cada fatia. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104

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

Neste exemplo, ano, mês, dia e hora de SliceStart são extraídos em variáveis separadas que são usadas pelas propriedades folderPath e fileName.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das seções & propriedades disponíveis para definir as atividades, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . Propriedades como nome, descrição, conjuntos de dados de entrada e saída e políticas estão disponíveis para todos os tipos de atividades. Enquanto que as propriedades disponíveis na seção **typeproperties** da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fontes e coletores. Se você estiver movendo dados de um armazenamento de BLOBs do Azure, defina o tipo de origem na atividade de cópia como **blobname**. Da mesma forma, se você estiver movendo dados para um armazenamento de BLOBs do Azure, defina o tipo de coletor na atividade de cópia como **BlobSink**. Esta seção fornece uma lista das propriedades com suporte por Blobname e BlobSink.

O **blobname** dá suporte às seguintes propriedades na seção **typeproperties** :

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursive |Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. |True (valor padrão), false |Não |

**BlobSink** dá suporte às seguintes propriedades **typeproperties** da seção:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| copyBehavior |Define o comportamento de cópia quando a origem é BLOB ou FileSystem. |<b>PreserveHierarchy</b>: preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico para o caminho relativo do ficheiro de destino para a pasta de destino.<br/><br/><b>FlattenHierarchy</b>: todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os ficheiros de destino têm o nome gerado automaticamente. <br/><br/><b>MergeFiles</b>: mescla todos os arquivos da pasta de origem em um arquivo. Se o nome de ficheiro/Blob for especificado, o nome de ficheiro intercalada seria o nome especificado; caso contrário, seria o nome de ficheiro gerado automaticamente. |Não |

O **blobname** também dá suporte a essas duas propriedades para compatibilidade com versões anteriores.

* **treatEmptyAsNull**: especifica se a cadeia de caracteres nula ou vazia deve ser tratada como um valor nulo.
* **skipHeaderLineCount** -especifica quantas linhas precisam ser ignoradas. Ele é aplicável somente quando o conjunto de dados de entrada está usando TextFormat.

Da mesma forma, o **BlobSink** dá suporte à propriedade a seguir para compatibilidade com versões anteriores.

* **blobWriterAddHeader**: especifica se é para adicionar um cabeçalho de definições de coluna durante a gravação em um conjunto de uma saída.

Agora, os conjuntos de linhas dão suporte às seguintes propriedades que implementam a mesma funcionalidade: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

A tabela a seguir fornece orientação sobre como usar as novas propriedades de conjunto de entrada no lugar dessas propriedades de origem/coletor de BLOB.

| Propriedade da atividade de cópia | Propriedade DataSet |
|:--- |:--- |
| skipHeaderLineCount em Blobname |skipLineCount e firstRowAsHeader. As linhas são ignoradas primeiro e, em seguida, a primeira linha é lida como um cabeçalho. |
| treatEmptyAsNull em Blobname |treatEmptyAsNull no conjunto de dados de entrada |
| blobWriterAddHeader em BlobSink |firstRowAsHeader no conjunto de saída |

Consulte [especificando a seção TextFormat](data-factory-supported-file-and-compression-formats.md#text-format) para obter informações detalhadas sobre essas propriedades.

### <a name="recursive-and-copybehavior-examples"></a>Exemplos de recursiva e copyBehavior
Esta secção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursiva e copyBehavior.

| recursive | copyBehavior | Comportamento resultante |
| --- | --- | --- |
| true |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a mesma estrutura da origem<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>o Pasta1 de destino é criado com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| true |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>o Pasta1 de destino é criado com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + 5 ficheiro de conteúdo é mesclado num ficheiro com o nome de ficheiro gerado automaticamente |
| false |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Subfolder1 com File3, File4 e File5 não são aplicadas. |
| false |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/><br/>Subfolder1 com File3, File4 e File5 não são aplicadas. |
| false |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 conteúdo é mesclado num arquivo com o nome de ficheiro gerado automaticamente. Nome gerado automaticamente para File1<br/><br/>Subfolder1 com File3, File4 e File5 não são aplicadas. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Walkthrough: usar o assistente de cópia para copiar dados de/para o armazenamento de BLOBs
Vejamos como copiar dados rapidamente de/para um armazenamento de BLOBs do Azure. Neste tutorial, os armazenamentos de dados de origem e destino do tipo: armazenamento de BLOBs do Azure. O pipeline neste tutorial copia dados de uma pasta para outra pasta no mesmo contêiner de BLOB. Isso é intencionalmente simples para mostrar as configurações ou propriedades ao usar o armazenamento de BLOBs como fonte ou coletor.

### <a name="prerequisites"></a>Pré-requisitos
1. Crie uma **conta de armazenamento do Azure** de uso geral, se você ainda não tiver uma. Você usa o armazenamento de BLOBs como armazenamento de dados de **origem** e de **destino** neste passo a passos. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md) para obter os passos para criar uma.
2. Crie um contêiner de blob chamado **adfblobconnector** na conta de armazenamento.
4. Crie uma pasta chamada **Input** no contêiner **adfblobconnector** .
5. Crie um arquivo chamado **EMP. txt** com o conteúdo a seguir e carregue-o na pasta de **entrada** usando ferramentas como [Gerenciador de armazenamento do Azure](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>Criar o data factory
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **criar um recurso** no canto superior esquerdo, clique em **inteligência + análise**e clique em **Data Factory**.
3. No painel **novo data Factory** :  
    1. Digite **ADFBlobConnectorDF** para o **nome**. O nome do Azure Data Factory deve ser globalmente exclusivo. Se você receber o erro: `*Data factory name “ADFBlobConnectorDF” is not available`, altere o nome do data factory (por exemplo, yournameADFBlobConnectorDF) e tente criar novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.
    2. Selecione a sua **subscrição** do Azure.
    3. Para grupo de recursos, selecione **usar existente** para selecionar um grupo de recursos existente (ou) selecione **criar novo** para inserir um nome para um grupo de recursos.
    4. Selecione uma **localização** para a fábrica de dados.
    5. Selecione a caixa de verificação **Afixar ao dashboard**, na parte inferior do painel.
    6. Clique em **Criar**.
3. Depois que a criação for concluída, você verá a folha **Data Factory** , conforme mostrado na imagem a seguir: ![Data Factory Home Page](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Assistente de Cópia
1. Na home page Data Factory, clique no bloco **copiar dados** para iniciar o **Assistente de copiar dados** em uma guia separada.  

    > [!NOTE]
    > Se você vir que o navegador da Web está preso em "autorizando...", desabilite/desmarque a configuração **bloquear cookies de terceiros e dados do site** (ou) Mantenha-a habilitada e crie uma exceção para **login.microsoftonline.com** e tente iniciar o assistente novamente.
2. Na página **Propriedades**:
    1. Insira **CopyPipeline** para o **nome da tarefa**. O nome da tarefa é o nome do pipeline em seu data factory.
    2. Insira uma **Descrição** para a tarefa (opcional).
    3. Para a **cadência da tarefa ou a agenda da tarefa**, mantenha a opção **executar regularmente na agenda** . Se você quiser executar essa tarefa apenas uma vez, em vez de executar repetidamente em um agendamento, selecione **executar uma vez agora**. Se você selecionar, a opção **executar uma vez agora** , um [pipeline de uso único](data-factory-create-pipelines.md#onetime-pipeline) será criado.
    4. Mantenha as configurações de **padrão recorrente**. Essa tarefa é executada diariamente entre as horas de início e término especificadas na próxima etapa.
    5. Altere a **data e hora de início** para **04/21/2017**.
    6. Altere a **data e hora de término** para **04/25/2017**. Talvez você queira digitar a data em vez de navegar pelo calendário.
    8. Clique em **Seguinte**.
        Ferramenta de cópia de ![-página Propriedades](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. Na página **Arquivo de dados de origem**, clique no mosaico **Blob Storage do Azure**. Utilize esta página para especificar o arquivo de dados de origem para a tarefa de cópia. Pode utilizar um serviço ligado do arquivo de dados existente (ou) especificar um novo arquivo de dados. Para usar um serviço vinculado existente, você deve selecionar **entre os serviços vinculados existentes** e selecionar o serviço vinculado correto.
    Ferramenta de cópia de ![– página de armazenamento de dados de origem](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. Na página **Especificar a conta de armazenamento de blobs do Azure**:
    1. Mantenha o nome gerado automaticamente para o **nome da conexão**. O nome da conexão é o nome do serviço vinculado do tipo: armazenamento do Azure.
    2. Confirme que a opção **A partir de subscrições do Azure** está selecionada para o **Método de seleção de contas**.
    3. Selecione sua assinatura do Azure ou mantenha **selecionar tudo** para a **assinatura do Azure**.
    4. Selecione uma **Conta de armazenamento do Azure** na lista das contas de armazenamento do Azure disponíveis na subscrição selecionada. Você também pode optar por inserir as configurações da conta de armazenamento manualmente selecionando a opção **inserir manualmente** para o **método de seleção de conta**.
    5. Clique em **Seguinte**.  
        Ferramenta de cópia de ![-especifique a conta de armazenamento de BLOBs do Azure](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. Na página **Escolher o ficheiro ou pasta de entrada**:
    1. Clique duas vezes em **adfblobcontainer**.
    2. Selecione **entrada**e clique em **escolher**. Neste tutorial, você seleciona a pasta de entrada. Você também pode selecionar o arquivo EMP. txt na pasta em vez disso.
        ![ferramenta de cópia-escolha o arquivo ou a pasta de entrada](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. Na página **escolha o arquivo ou pasta de entrada** :
    1. Confirme se o **arquivo ou a pasta** está definida como **adfblobconnector/Input**. Se os arquivos estiverem em subpastas, por exemplo, 2017/04/01, 2017/04/02 e assim por diante, insira adfblobconnector/Input/{ano}/{month}/{Day} para arquivo ou pasta. Quando você pressiona TAB na caixa de texto, você vê três listas suspensas para selecionar formatos para ano (aaaa), mês (MM) e dia (DD).
    2. Não defina **Copiar arquivo recursivamente**. Selecione esta opção para percorrer de forma recursiva as pastas para que os arquivos sejam copiados para o destino.
    3. Não a opção de **cópia binária** . Selecione esta opção para executar uma cópia binária do arquivo de origem para o destino. Não selecione para este passo a passos para que você possa ver mais opções nas próximas páginas.
    4. Confirme se o **tipo de compactação** está definido como **nenhum**. Selecione um valor para esta opção se os arquivos de origem forem compactados em um dos formatos com suporte.
    5. Clique em **Seguinte**.
    ![ferramenta de cópia-escolha o arquivo ou a pasta de entrada](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. Na página **Definições do formato de ficheiro**, pode ver os delimitadores e o esquema que é detetado automaticamente pelo assistente ao analisar o ficheiro.
    1. Confirme as seguintes opções:  
        a. O **formato de arquivo** é definido como **formato de texto**. Você pode ver todos os formatos com suporte na lista suspensa. Por exemplo: JSON, Avro, ORC, parquet.
       b. O **delimitador de coluna** é definido como `Comma (,)`. Você pode ver os outros delimitadores de coluna com suporte pelo Data Factory na lista suspensa. Você também pode especificar um delimitador personalizado.
       c. O **delimitador de linha** é definido como `Carriage Return + Line feed (\r\n)`. Você pode ver os outros delimitadores de linha com suporte pelo Data Factory na lista suspensa. Você também pode especificar um delimitador personalizado.
       d. A **contagem de linhas de ignorar** é definida como **0**. Se você quiser que algumas linhas sejam ignoradas na parte superior do arquivo, insira o número aqui.
       e. A **primeira linha de dados contém nomes de coluna** não está definida. Se os arquivos de origem contiverem nomes de coluna na primeira linha, selecione essa opção.
       f. A opção **tratar valor da coluna vazia como NULL** está definida.
    2. Expanda **Configurações avançadas** para ver a opção avançada disponível.
    3. Na parte inferior da página, consulte a **Visualização** de dados do arquivo EMP. txt.
    4. Clique na guia **esquema** na parte inferior para ver o esquema que o assistente de cópia inferiu examinando os dados no arquivo de origem.
    5. Clique em **Seguinte** depois de rever os delimitadores e pré-visualizar os dados.
    Ferramenta de cópia de ![-configurações de formato de arquivo](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. Na **página armazenamento de dados de destino**, selecione armazenamento de **BLOBs do Azure**e clique em **Avançar**. Você está usando o armazenamento de BLOBs do Azure como armazenamentos de dados de origem e de destino neste passo a passos.  
    Ferramenta de cópia de ![-selecione o armazenamento de dados de destino](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. Na página **especificar a conta de armazenamento de BLOBs do Azure** :  
    1. Insira **AzureStorageLinkedService** para o campo **nome da conexão** .
    2. Confirme que a opção **A partir de subscrições do Azure** está selecionada para o **Método de seleção de contas**.
    3. Selecione a sua **subscrição** do Azure.
    4. Selecione sua conta de armazenamento do Azure.
    5. Clique em **Seguinte**.
10. Na página **escolha o arquivo de saída ou a pasta** :  
    1. Especifique o **caminho da pasta** como **adfblobconnector/output/{ano}/{month}/{Day}** . Digite **Tab**.
    1. Para o **ano**, selecione **aaaa**.
    1. Para o **mês**, confirme se ele está definido como **mm**.
    1. Para o **dia**, confirme se ele está definido como **DD**.
    1. Confirme se o **tipo de compactação** está definido como **nenhum**.
    1. Confirme se o **comportamento da cópia** está definido como **mesclar arquivos**. Se o arquivo de saída com o mesmo nome já existir, o novo conteúdo será adicionado ao mesmo arquivo no final.
    1. Clique em **Seguinte**.
       ![ferramenta de cópia-escolha o arquivo ou a pasta de saída](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. Na página **configurações de formato de arquivo** , examine as configurações e clique em **Avançar**. Uma das opções adicionais aqui é adicionar um cabeçalho ao arquivo de saída. Se você selecionar essa opção, uma linha de cabeçalho será adicionada com nomes das colunas do esquema da origem. Você pode renomear os nomes de coluna padrão ao exibir o esquema para a origem. Por exemplo, você pode alterar a primeira coluna para First Name e segunda coluna para Last Name. Em seguida, o arquivo de saída é gerado com um cabeçalho com esses nomes como nomes de coluna.
    ![ferramenta de cópia-configurações de formato de arquivo para o destino](media/data-factory-azure-blob-connector/file-format-destination.png)
12. Na página **configurações de desempenho** , confirme se **unidades de nuvem** e **cópias paralelas** estão definidas como **automático**e clique em Avançar. Para obter detalhes sobre essas configurações, consulte [Guia de desempenho e ajuste da atividade de cópia](data-factory-copy-activity-performance.md#parallel-copy).
    Ferramenta de cópia de ![-configurações de desempenho](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. Na página **Resumo** , examine todas as configurações (Propriedades da tarefa, configurações de origem e destino e configurações de cópia) e clique em **Avançar**.
    Ferramenta de cópia ![-página Resumo](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Reveja as informações na página **Resumo** e clique em **Concluir**. O assistente cria dois serviços ligados, dois conjuntos de dados (entrada e saída) e um pipeline na fábrica de dados (a partir da qual iniciou o Assistente de Cópia).
    Ferramenta de cópia de ![– página de implantação](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>Monitorar o pipeline (Copiar tarefa)

1. Clique no link `Click here to monitor copy pipeline` na página **implantação** .
2. Você deve ver o **aplicativo monitorar e gerenciar** em uma guia separada.  ![monitorar e gerenciar](media/data-factory-azure-blob-connector/monitor-manage-app.png) de aplicativo
3. Altere a hora de **início** na parte superior para `04/19/2017` e hora de **término** para `04/27/2017`e, em seguida, clique em **aplicar**.
4. Você deve ver cinco janelas de atividades na lista **janelas de atividades** . Os tempos de **WindowStart** devem abranger todos os dias desde o início do pipeline até os horários de término do pipeline.
5. Clique no botão **Atualizar** para a lista **janelas de atividades** algumas vezes até ver o status de todas as janelas de atividades definidas como pronto.
6. Agora, verifique se os arquivos de saída são gerados na pasta de saída do contêiner adfblobconnector. Você deverá ver a seguinte estrutura de pastas na pasta de saída:
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
   Para obter informações detalhadas sobre como monitorar e gerenciar Data factories, consulte o artigo [monitorar e gerenciar Data Factory pipeline](data-factory-monitor-manage-app.md) .

### <a name="data-factory-entities"></a>Entidades de Data Factory
Agora, volte para a guia com o Data Factory home page. Observe que há dois serviços vinculados, dois conjuntos de valores e um pipeline no seu data factory agora.

![Data Factory home page com entidades](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Clique em **criar e implantar** para iniciar o Editor de data Factory.

![Editor do Data Factory](media/data-factory-azure-blob-connector/data-factory-editor.png)

Você deve ver as seguintes entidades de Data Factory em seu data factory:

- Dois serviços vinculados. Uma para a origem e a outra para o destino. Ambos os serviços vinculados referem-se à mesma conta de armazenamento do Azure neste guia.
- Dois conjuntos de valores. Um conjunto de dados de entrada e de saída. Neste passo a passos, ambos usam o mesmo contêiner de BLOB, mas se referem a pastas diferentes (entrada e saída).
- Um pipeline. O pipeline contém uma atividade de cópia que usa uma origem de BLOB e um coletor de BLOB para copiar dados de um local de blob do Azure para outro local de blob do Azure.

As seções a seguir fornecem mais informações sobre essas entidades.

#### <a name="linked-services"></a>Serviços ligados
Você deve ver dois serviços vinculados. Uma para a origem e a outra para o destino. Neste tutorial, ambas as definições têm a mesma aparência, exceto os nomes. O **tipo** do serviço vinculado é definido como **AzureStorage**. A propriedade mais importante da definição de serviço vinculado é a **ConnectionString**, que é usada pelo data Factory para se conectar à sua conta de armazenamento do Azure em tempo de execução. Ignore a propriedade hubName na definição.

##### <a name="source-blob-storage-linked-service"></a>Serviço vinculado do armazenamento de blob de origem
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

##### <a name="destination-blob-storage-linked-service"></a>Serviço vinculado do armazenamento de blobs de destino

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

Para obter mais informações sobre o serviço vinculado do armazenamento do Azure, consulte a seção [Propriedades do serviço vinculado](#linked-service-properties) .

#### <a name="datasets"></a>Conjuntos de dados
Há dois conjunto de dados: um conjunto de dados de entrada e de saída. O tipo do conjunto de uma é definido como **AzureBlob** para ambos.

O conjunto de dados de entrada aponta para a pasta de **entrada** do contêiner de blob **adfblobconnector** . A propriedade **external** é definida como **true** para esse conjunto de dados, pois eles não são produzidos pelo pipeline com a atividade de cópia que usa esse DataSet como uma entrada.

O conjunto de resultados de saída aponta para a pasta de **saída** do mesmo contêiner de BLOB. O conjunto de resultados de saída também usa o ano, mês e dia da variável do sistema **SliceStart** para avaliar dinamicamente o caminho para o arquivo de saída. Para obter uma lista de funções e variáveis de sistema com suporte pelo Data Factory, consulte [funções e variáveis de sistema do data Factory](data-factory-functions-variables.md). A propriedade **external** é definida como **false** (valor padrão) porque esse conjunto de valores é produzido pelo pipeline.

Para obter mais informações sobre propriedades com suporte pelo conjunto de dados de blob do Azure, consulte a seção [Propriedades do conjunto](#dataset-properties) de dados.

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

##### <a name="output-dataset"></a>Conjunto de saída

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
O pipeline tem apenas uma atividade. O **tipo** da atividade é definido como **copiar**. Nas propriedades de tipo da atividade, há duas seções, uma para a fonte e a outra para o coletor. O tipo de origem é definido como **blobname** , pois a atividade está copiando dados de um armazenamento de BLOBs. O tipo de coletor é definido como **BlobSink** como a atividade de cópia de dados para um armazenamento de BLOBs. A atividade de cópia usa InputDataset-z4y como entrada e OutputDataset-z4y como a saída.

Para obter mais informações sobre propriedades com suporte por Blobname e BlobSink, consulte a seção [Propriedades da atividade de cópia](#copy-activity-properties) .

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

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>Exemplos de JSON para copiar dados de e para o armazenamento de BLOBs
Os exemplos a seguir fornecem exemplos de definições de JSON que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de e para o armazenamento de BLOBs do Azure e o banco de dados SQL do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure data Factory.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>Exemplo de JSON: copiar dados do armazenamento de BLOBs para o banco de dados SQL
O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](#linked-service-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [AzureBlob](#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que usa [blobname](#copy-activity-properties) e [sqlsink](data-factory-azure-sql-connector.md#copy-activity-properties).

O exemplo copia dados de série temporal de um blob do Azure para uma tabela SQL do Azure por hora. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

**Serviço vinculado do SQL do Azure:**

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
**Serviço vinculado do armazenamento do Azure:**

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
O Azure Data Factory dá suporte a dois tipos de serviços vinculados do armazenamento do Azure: **AzureStorage** e **AzureStorageSas**. Para o primeiro, especifique a cadeia de conexão que inclui a chave de conta e, para o mais recente, especifique o URI de SAS (assinatura de acesso compartilhado). Consulte a seção [Serviços vinculados](#linked-service-properties) para obter detalhes.

**Conjunto de dados de entrada de blob do Azure:**

Os dados são coletados de um novo BLOB a cada hora (frequência: hora, intervalo: 1). O caminho da pasta e o nome do arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa a parte de ano, mês e dia da hora de início e o nome do arquivo usa a parte de hora da hora de início. a configuração "external": "true" informa Data Factory que a tabela é externa à data factory e não é produzida por uma atividade no data factory.

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
**Conjunto de resultados de saída SQL do Azure:**

O exemplo copia dados para uma tabela chamada "MyTable" em um banco de dado SQL do Azure. Crie a tabela no banco de dados SQL do Azure com o mesmo número de colunas que você espera que o arquivo CSV de blob contenha. Novas linhas são adicionadas à tabela a cada hora.

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
**Uma atividade de cópia em um pipeline com origem de BLOB e coletor de SQL:**

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** está definido como **blobname** e o tipo de **coletor** está definido como **sqlsink**.

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
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>Exemplo de JSON: copiar dados do Azure SQL para o blob do Azure
O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](#linked-service-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [sqlsource](data-factory-azure-sql-connector.md#copy-activity-properties) e [BlobSink](#copy-activity-properties).

O exemplo copia dados de série temporal de uma tabela SQL do Azure para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

**Serviço vinculado do SQL do Azure:**

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
**Serviço vinculado do armazenamento do Azure:**

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
O Azure Data Factory dá suporte a dois tipos de serviços vinculados do armazenamento do Azure: **AzureStorage** e **AzureStorageSas**. Para o primeiro, especifique a cadeia de conexão que inclui a chave de conta e, para o mais recente, especifique o URI de SAS (assinatura de acesso compartilhado). Consulte a seção [Serviços vinculados](#linked-service-properties) para obter detalhes.

**Conjunto de dados de entrada do SQL Azure:**

O exemplo supõe que você criou uma tabela "MyTable" no Azure SQL e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal.

A configuração de "external": "true" informa Data Factory serviço de que a tabela é externa à data factory e não é produzida por uma atividade no data factory.

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

**Conjunto de resultados de saída de blob do Azure:**

Os dados são gravados em um novo BLOB a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

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

**Uma atividade de cópia em um pipeline com fonte SQL e coletor de blob:**

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** é definido como **sqlsource** e o tipo de **coletor** é definido como **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora a serem copiados.

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
> Para mapear colunas do conjunto de fonte de origem para colunas do conjunto de coleta, consulte [mapeando colunas do conjunto de linhas no Azure data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Consulte [Guia de ajuste do desempenho de atividade de cópia &](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Azure data Factory e várias maneiras de otimizá-lo.
