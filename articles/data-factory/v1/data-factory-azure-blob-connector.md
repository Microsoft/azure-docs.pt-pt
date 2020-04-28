---
title: Copiar dados de/para Armazenamento De Blob Azure
description: 'Saiba como copiar dados blob na Azure Data Factory. Utilize a nossa amostra: Como copiar dados de e para a Base de Dados Azure Blob e Azure SQL.'
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79282136"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Copiar dados de ou para o Armazenamento de Blob Azure utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-azure-blob-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-blob-storage.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector de armazenamento De Armazenamento Azure Blob em V2](../connector-azure-blob-storage.md).


Este artigo explica como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de e para o Armazenamento de Blob Azure. Baseia-se no artigo Atividades do Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade de cópia.

## <a name="overview"></a>Descrição geral
Pode copiar dados de qualquer loja de dados de origem suportada para o Armazenamento De Blob Azure ou do Armazenamento Azure Blob para qualquer loja de dados de sink suportado. A tabela seguinte fornece uma lista de lojas de dados suportadas como fontes ou pias pela atividade de cópia. Por exemplo, pode mover dados **de** uma base de dados do SQL Server ou de uma base de dados Azure SQL **para** um armazenamento de blob Azure. E, pode copiar dados **do** armazenamento de blob Azure **para** um Armazém de Dados Azure SQL ou uma coleção Azure Cosmos DB.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **do Armazenamento Azure Blob** para as seguintes lojas de dados:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados das seguintes lojas de dados **para o Armazenamento De Blob Azure:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> A Copy Activity suporta a cópia de dados de/para ambas as contas de armazenamento de azure de uso geral e armazenamento hot/cool Blob. A atividade suporta a **leitura de blocos, apêndices ou bolhas de página,** mas suporta a **escrita apenas para bloquear bolhas**. O Armazenamento Azure Premium não é suportado como um lavatório porque é apoiado por bolhas de página.
>
> A Copy Activity não elimina dados da fonte depois de os dados terem sido copiados com sucesso para o destino. Se necessitar de eliminar os dados de origem após uma cópia bem sucedida, crie uma [atividade personalizada](data-factory-use-custom-activities.md) para eliminar os dados e utilizar a atividade no pipeline. Por exemplo, consulte a amostra de [apagar a bolha ou a pasta no GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity).

## <a name="get-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de/para um Armazenamento De Blob Azure utilizando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é utilizar o **Assistente de Cópia**. Este artigo tem um [walkthrough](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) para criar um pipeline para copiar dados de um local de armazenamento Azure Blob para outro local de armazenamento Azure Blob. Para um tutorial sobre a criação de um pipeline para copiar dados de um Armazenamento Azure Blob para base de dados Azure SQL, consulte [Tutorial: Crie um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md).

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Estúdio Visual,** **Azure PowerShell,** **Modelo de Gestor de Recursos Azure,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de sumidouro:

1. Criar uma fábrica de **dados.** Uma fábrica de dados pode conter um ou mais oleodutos.
2. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados. Por exemplo, se estiver a copiar dados de um armazenamento de blob Azure para uma base de dados Azure SQL, cria dois serviços ligados para ligar a sua conta de armazenamento Azure e a base de dados Azure SQL à sua fábrica de dados. Para propriedades de serviço ligadas específicas ao Armazenamento De Blob Azure, consulte a secção de [propriedades de serviço ligadas.](#linked-service-properties)
2. Crie **conjuntos** de dados para representar dados de entrada e saída para a operação de cópia. No exemplo mencionado no último passo, cria-se um conjunto de dados para especificar o recipiente e a pasta blob que contém os dados de entrada. E cria outro conjunto de dados para especificar a tabela SQL na base de dados Azure SQL que detém os dados copiados do armazenamento de blob. Para propriedades de conjunto de dados específicas do Armazenamento de Blob Azure, consulte a secção de propriedades do conjunto de [dados.](#dataset-properties)
3. Crie um **pipeline** com uma atividade de cópia que tome um conjunto de dados como entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, você usa blobSource como fonte e SqlSink como um lavatório para a atividade de cópia. Da mesma forma, se estiver a copiar da Base de Dados Azure SQL para o Armazenamento De Blob Azure, utiliza o SqlSource e o BlobSink na atividade da cópia. Para propriedades de copy activity específicas para o Armazenamento De Blob Azure, consulte a secção de propriedades de atividade de [cópia.](#copy-activity-properties) Para obter mais informações sobre como utilizar uma loja de dados como fonte ou pia, clique no link na secção anterior para a sua loja de dados.

Quando utiliza o assistente, as definições jSON para estas entidades da Fábrica de Dados (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Fábrica de Dados utilizando o formato JSON.  Para amostras com definições JSON para entidades da Fábrica de Dados que são usadas para copiar dados de/para um Armazenamento De Blob Azure, consulte a secção de [exemplos jSON](#json-examples-for-copying-data-to-and-from-blob-storage  ) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Fábrica de Dados específicas do Armazenamento De Blob Azure.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos
Existem dois tipos de serviços ligados que pode utilizar para ligar um Armazenamento Azure a uma fábrica de dados Azure. São eles: Serviço ligado ao **AzureStorage** e serviço ligado ao **AzureStorageSas.** O serviço ligado ao Armazenamento Azure fornece à fábrica de dados acesso global ao Armazenamento Azure. Considerando que o serviço ligado ao armazenamento Azure SAS (Assinatura de Acesso Partilhado) fornece à fábrica de dados um acesso restrito/limitado ao armazenamento do Azure. Não existem outras diferenças entre estes dois serviços ligados. Escolha o serviço vinculado que se adapte às suas necessidades. As seguintes secções fornecem mais detalhes sobre estes dois serviços ligados.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para especificar um conjunto de dados para representar dados de entrada ou saída num Armazenamento De Blob Azure, você definiu a propriedade tipo do conjunto de dados para: **AzureBlob**. Delineie a propriedade **linkedServiceName** do conjunto de dados com o nome do serviço ligado ao Armazenamento Azure ou Azure Storage SAS.  As propriedades do tipo do conjunto de dados especificam o **recipiente blob** e a **pasta** no armazenamento de bolhas.

Para obter uma lista completa das secções da JSON & propriedades disponíveis para definir conjuntos de dados, consulte o artigo Criação de conjuntos de [dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjuntos de dados (Azure SQL, Azure blob, tabela Azure, etc.).

A fábrica de dados suporta os seguintes valores de tipo baseados em CLS para fornecer informações de tipo em "estrutura" para fontes de dados schema-on-read como Azure blob: Int16, Int32, Int64, Single, Double, Decimal, Byte[], Bool, String, Guid, Datetime, Datetimeoffset, Timespan. Data Factory executa automaticamente conversões de tipo ao mover dados de uma loja de dados de origem para uma loja de dados de sumidouro.

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização, formato, etc., dos dados na loja de dados. A secção TypeProperties para conjunto de dados do conjunto de dados do tipo **AzureBlob** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho para o recipiente e pasta no armazenamento de bolhas. Exemplo: myblobcontainer\myblobfolder\ |Sim |
| fileName |Nome da bolha. fileName é opcional e sensível a casos.<br/><br/>Se especificar um nome de ficheiro, a atividade (incluindo cópia) funciona no Blob específico.<br/><br/>Quando o nome do ficheiro não é especificado, a Cópia inclui todos os Blobs na pastaPath para o conjunto de dados de entrada.<br/><br/>Quando o nome do **ficheiro** não é especificado para um conjunto de dados de saída e preservar A **hierarquia** `Data.<Guid>.txt` não é especificada no sumidouro de atividade, o nome do ficheiro gerado estaria no seguinte formato: (por exemplo: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txtt |Não |
| divididoBy |partitionedBy é uma propriedade opcional. Pode usá-lo para especificar uma pasta dinâmicaPath e nome de ficheiro para dados da série de tempo. Por exemplo, a pastaO caminho pode ser parametrizado para cada hora de dados. Consulte a [secção de propriedade siquetada por utilização](#using-partitionedby-property) para mais detalhes e exemplos. |Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat,** **JsonFormat,** **AvroFormat,** **OrcFormat,** **ParquetFormat**. Desloque a propriedade **tipo** em formato a um destes valores. Para mais informações, consulte as secções de [Formato Texto,](data-factory-supported-file-and-compression-formats.md#text-format) [Formato Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Formato Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format)e [Formato Parquet.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Se pretender **copiar ficheiros como está** entre lojas baseadas em ficheiros (cópia binária), ignore a secção de formato nas definições de conjunto de dados de entrada e de saída. |Não |
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip,** **Deflate,** **BZip2,** e **ZipDeflate**. Os níveis suportados são: **Optimal** e **Fastest**. Para mais informações, consulte [formatos de ficheiroe de compressão na Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

### <a name="using-partitionedby-property"></a>Usando propriedade divididaBy
Conforme mencionado na secção anterior, pode especificar uma pasta dinâmicaPath e nome de ficheiro para dados de séries de tempo com a propriedade **divididaBy,** [funções data Factory e as variáveis do sistema](data-factory-functions-variables.md).

Para obter mais informações sobre conjuntos de dados de séries de tempo, agendamento e fatias, consulte [a Criação](data-factory-create-datasets.md) de Conjuntos de Dados e agendamento & artigos de [execução.](data-factory-scheduling-and-execution.md)

#### <a name="sample-1"></a>Exemplo 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Neste exemplo, {Slice} é substituído pelo valor da variável sliceStart do sistema data Factory no formato (YYYYMMDDHH) especificado. O SliceStart refere-se à hora de início da fatia. A pastaCaminho é diferente para cada fatia. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104

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

Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas que são usadas pelas propriedades da pastaPath e fileName.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de secções & propriedades disponíveis para definir atividades, consulte o artigo [Creating Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, conjuntos de dados de entrada e saída, e políticas estão disponíveis para todos os tipos de atividades. Considerando que as propriedades disponíveis na secção **typeProperties** da atividade variam com cada tipo de atividade. Para a atividade de Cópia, variam dependendo dos tipos de fontes e pias. Se estiver a mover dados de um Armazenamento De Blob Azure, delineie o tipo de origem na atividade de cópia para **blobSource**. Da mesma forma, se estiver a mover dados para um Armazenamento De Blob Azure, coloque o tipo de pia na atividade de cópia para **BlobSink**. Esta secção fornece uma lista de propriedades suportadas por BlobSource e BlobSink.

**O BlobSource** suporta as seguintes propriedades na secção **TypeProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursivo |Indica se os dados são lidos recursivamente a partir das subpastas ou apenas a partir da pasta especificada. |Verdadeiro (valor predefinido), Falso |Não |

**BlobSink** suporta a seguinte secção de propriedades **typeProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| copiarComportamento |Define o comportamento da cópia quando a fonte é BlobSource ou FileSystem. |<b>PreserveHierarchy</b>: preserva a hierarquia dos ficheiros na pasta-alvo. O caminho relativo do ficheiro fonte para a pasta fonte é idêntico ao caminho relativo do ficheiro alvo para a pasta-alvo.<br/><br/><b>Hierarquia Plana</b>: todos os ficheiros da pasta fonte estão no primeiro nível da pasta-alvo. Os ficheiros-alvo têm um nome gerado automaticamente. <br/><br/><b>MergeFiles</b>: funde todos os ficheiros da pasta de origem para um ficheiro. Se o nome Ficheiro/Bolha for especificado, o nome do ficheiro fundido será o nome especificado; caso contrário, seria o nome de ficheiro gerado automaticamente. |Não |

**O BlobSource** também suporta estas duas propriedades para a retrocompatibilidade.

* **tratar EmptyAsNull**: Especifica se deve tratar a cadeia nula ou vazia como valor nulo.
* **skipHeaderLineCount** - Especifica quantas linhas precisam de ser ignoradas. Só é aplicável quando o conjunto de dados de entrada estiver a utilizar o TextFormat.

Da mesma forma, **blobSink** suporta a seguinte propriedade para retrocompatibilidade.

* **blobWriterAddHeader**: Especifica se deve adicionar um cabeçalho de definições de coluna enquanto escreve a um conjunto de dados de saída.

Os conjuntos de dados suportam agora as seguintes propriedades que implementam a mesma funcionalidade: **tratar EmptyAsNull,** **skipLineCount,** **firstRowAsHeader**.

O quadro seguinte fornece orientações sobre a utilização das novas propriedades do conjunto de dados no lugar destas propriedades de fonte/pia blob.

| Propriedade de Atividade de Cópia | Propriedade dataset |
|:--- |:--- |
| skipHeaderLineCount no BlobSource |skipLineCount e firstRowAsHeader. As linhas são ignoradas primeiro e depois a primeira linha é lida como um cabeceamento. |
| tratar EmptyAsNull no BlobSource |tratar EmptyAsNull no conjunto de dados de entrada |
| blobWriterAddHeader em BlobSink |firstRowAsHeader no conjunto de dados de saída |

Consulte a secção [TextFormat](data-factory-supported-file-and-compression-formats.md#text-format) para obter informações detalhadas sobre estas propriedades.

### <a name="recursive-and-copybehavior-examples"></a>exemplos recursivos e copyBehavior
Esta secção descreve o comportamento resultante da operação Copy para diferentes combinações de valores recursivos e copyBehavior.

| recursivo | copiarComportamento | Comportamento resultante |
| --- | --- | --- |
| true |preservar Hierarquia |Para uma pasta de origem Pasta 1 com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5<br/><br/>a pasta-alvo Pasta 1 é criada com a mesma estrutura que a fonte<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5. |
| true |achatar a hierarquia |Para uma pasta de origem Pasta 1 com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5<br/><br/>a pasta-alvo 1 é criada com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File5 |
| true |mergeFiles |Para uma pasta de origem Pasta 1 com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5<br/><br/>a pasta-alvo 1 é criada com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + Ficheiro 5 conteúdos são fundidos num só ficheiro com nome de ficheiro gerado automaticamente |
| false |preservar Hierarquia |Para uma pasta de origem Pasta 1 com a seguinte estrutura: <br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5<br/><br/>a pasta-alvo Pasta 1 é criada com a seguinte estrutura<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/><br/>Subpasta1 com File3, File4 e File5 não são captadas. |
| false |achatar a hierarquia |Para uma pasta de origem Pasta 1 com a seguinte estrutura:<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5<br/><br/>a pasta-alvo Pasta 1 é criada com a seguinte estrutura<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para File2<br/><br/><br/>Subpasta1 com File3, File4 e File5 não são captadas. |
| false |mergeFiles |Para uma pasta de origem Pasta 1 com a seguinte estrutura:<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo 5<br/><br/>a pasta-alvo Pasta 1 é criada com a seguinte estrutura<br/><br/>Pasta 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos File1 + File2 são fundidos num ficheiro com nome de ficheiro gerado automaticamente. nome gerado automaticamente para File1<br/><br/>Subpasta1 com File3, File4 e File5 não são captadas. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Walkthrough: Use Copy Wizard para copiar dados de/para blob Storage
Vamos ver como copiar rapidamente dados de/para um armazenamento de blob Azure. Neste passeio, tanto as lojas de dados de origem como de destino: Armazenamento Azure Blob. O gasoduto neste passadiço copia dados de uma pasta para outra pasta no mesmo recipiente de bolhas. Este walkthrough é intencionalmente simples para mostrar-lhe configurações ou propriedades ao usar o Blob Storage como fonte ou pia.

### <a name="prerequisites"></a>Pré-requisitos
1. Crie uma Conta de **Armazenamento Azure** de uso geral se ainda não tiver uma. Você usa o armazenamento de blob como loja de dados de **origem** e **destino** neste walkthrough. se não tiver uma conta de armazenamento Azure, consulte o artigo de [conta de armazenamento Criar uma conta](../../storage/common/storage-account-create.md) de armazenamento para passos para criar uma.
2. Crie um recipiente de blob chamado **adfblobconnector** na conta de armazenamento.
4. Crie uma pasta chamada **entrada** no recipiente **adfblobconnector.**
5. Crie um ficheiro chamado **emp.txt** com o seguinte conteúdo e carregue-o para a pasta de **entrada** utilizando ferramentas como [o Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>Criar a fábrica de dados
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique **em Criar um recurso** a partir do canto superior esquerdo, clique em Inteligência + **análise,** e clique em **Data Factory**.
3. No painel da nova fábrica de **dados:**  
    1. Introduza **a ADFBlobConnectorDF** para o **nome**. O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o `*Data factory name “ADFBlobConnectorDF” is not available`erro: , mude o nome da fábrica de dados (por exemplo, o seu nomeADFBlobConnectorDF) e tente criar novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.
    2. Selecione a sua **subscrição** do Azure.
    3. Para o Grupo de Recursos, selecione **Use existente** para selecionar um grupo de recursos existente (ou) selecione **Criar novo** para introduzir um nome para um grupo de recursos.
    4. Selecione uma **localização** para a fábrica de dados.
    5. Selecione a caixa de verificação **Afixar ao dashboard**, na parte inferior do painel.
    6. Clique em **Criar**.
3. Após a criação estar concluída, vê a lâmina **data** ![Factory como mostrado na seguinte imagem: Página inicial da fábrica de dados](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Assistente de Cópia
1. Na página inicial da Data Factory, clique no azulejo de **dados da Cópia** para lançar copy data **wizard** num separador.  

    > [!NOTE]
    > Se verificar que o navegador web está preso a "Autorizar...", desative/desverifique os **cookies de terceiros do Bloco e** a definição de dados do site (ou) mantenha-o ativado e crie uma exceção para **login.microsoftonline.com** e tente lançar novamente o assistente.
2. Na página **Propriedades**:
    1. Introduza **copyPipeline** para nome de **tarefa**. O nome da tarefa é o nome do oleoduto na sua fábrica de dados.
    2. Insira uma **descrição** para a tarefa (opcional).
    3. Para **cadência de tarefas ou calendário**de tarefas, mantenha a Corrida regularmente na opção de **agendamento.** Se quiser executar esta tarefa apenas uma vez em vez de ser executado repetidamente num horário, selecione **Run uma vez agora**. Se selecionar, **executar uma vez agora** opção, é criado um gasoduto [único.](data-factory-create-pipelines.md#onetime-pipeline)
    4. Mantenha as definições para o **padrão Recorrente**. Esta tarefa é executado diariamente entre os tempos de início e de fim que especifica no próximo passo.
    5. Alterar a data de **início** para **04/21/2017**.
    6. Alterar a **data limite** para **04/25/2017**. Pode querer escrever a data em vez de navegar pelo calendário.
    8. Clique em **Seguinte**.
        ![Página Ferramenta Copiar – Propriedades](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. Na página **Arquivo de dados de origem**, clique no mosaico **Blob Storage do Azure**. Utilize esta página para especificar o arquivo de dados de origem para a tarefa de cópia. Pode utilizar um serviço ligado do arquivo de dados existente (ou) especificar um novo arquivo de dados. Para utilizar um serviço ligado existente, selecionará **os serviços ligados existentes** e selecionará o serviço ligado certo.
    ![Página Ferramenta Copiar – Arquivo de dados de origem](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. Na página **Especificar a conta de armazenamento de blobs do Azure**:
    1. Mantenha o nome gerado automaticamente para **o nome de ligação**. O nome da ligação é o nome do serviço de tipo ligado: Armazenamento Azure.
    2. Confirme que a opção **A partir de subscrições do Azure** está selecionada para o **Método de seleção de contas**.
    3. Selecione a sua subscrição Azure ou mantenha **Selecione tudo** para **subscrição do Azure**.
    4. Selecione uma **Conta de armazenamento do Azure** na lista das contas de armazenamento do Azure disponíveis na subscrição selecionada. Também pode optar por introduzir manualmente as definições da conta de armazenamento selecionando a opção **De introduzir manualmente** para o método de **seleção da Conta**.
    5. Clique em **Seguinte**.  
        ![Ferramenta Copiar – Especificar a conta de armazenamento de blobs do Azure](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. Na página **Escolher o ficheiro ou pasta de entrada**:
    1. Recipiente **adfblob de dois cliques.**
    2. Selecione **a entrada**e clique **em Escolher**. Neste passe, selecione a pasta de entrada. Também pode selecionar o ficheiro emp.txt na pasta.
        ![Ferramenta Copiar – Escolha o ficheiro ou pasta de entrada](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. Na página **escolha o ficheiro de entrada ou a página de pastas:**
    1. Confirme se o **ficheiro ou pasta** está definido para **adfblobconnector/entrada**. Se os ficheiros estiverem em subpastas, por exemplo, 2017/04/01, 2017/04/02, e assim por diante, introduza adfblobconnector/input/{year}/{month}/{day} para ficheiro ou pasta. Ao premir o TAB para fora da caixa de texto, vê três listas de drop-down para selecionar formatos para o ano (yyyy), mês (MM) e dia (dd).
    2. Não desloque **o ficheiro Copy de forma recursiva**. Selecione esta opção para atravessar novamente através de pastas para que os ficheiros sejam copiados para o destino.
    3. Não opção de **cópia binária.** Selecione esta opção para executar uma cópia binária do ficheiro fonte para o destino. Não selecione para este walkthrough para que possa ver mais opções nas páginas seguintes.
    4. Confirme se o **tipo de compressão** está definido para **Nenhum**. Selecione um valor para esta opção se os seus ficheiros de origem forem comprimidos num dos formatos suportados.
    5. Clique em **Seguinte**.
    ![Ferramenta Copiar – Escolha o ficheiro ou pasta de entrada](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. Na página **Definições do formato de ficheiro**, pode ver os delimitadores e o esquema que é detetado automaticamente pelo assistente ao analisar o ficheiro.
    1. Confirme as seguintes opções:  
        a. O **formato** de ficheiro é definido para **o formato Texto**. Pode ver todos os formatos suportados na lista de drop-down. Por exemplo: JSON, Avro, ORC, Parquet.
       b. O **delimitador** da `Comma (,)`coluna está definido para . Pode ver os outros delimitadores de coluna suportados pela Data Factory na lista de drop-down. Também pode especificar um delimitador personalizado.
       c. O **delimitador da** `Carriage Return + Line feed (\r\n)`linha está definido para . Pode ver os outros delimitadores de linha suportados pela Data Factory na lista de drop-down. Também pode especificar um delimitador personalizado.
       d. A **contagem de linha de salto** está definida para **0**. Se quiser que algumas linhas sejam ignoradas no topo do ficheiro, insira o número aqui.
       e. A primeira linha de **dados contém nomes** de colunas não está definida. Se os ficheiros de origem contiverem nomes de colunas na primeira linha, selecione esta opção.
       f. O valor da coluna vazia como opção **nula** é definido.
    2. Expandir **as definições avançadas** para ver a opção avançada disponível.
    3. Na parte inferior da página, consulte a **pré-visualização** de dados do ficheiro emp.txt.
    4. Clique no separador **SCHEMA** na parte inferior para ver o esquema que o assistente de cópia inferiu olhando para os dados do ficheiro fonte.
    5. Clique em **Seguinte** depois de rever os delimitadores e pré-visualizar os dados.
    ![Ferramenta Copiar – Definições do formato de ficheiro](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. Na página da loja de **dados Destination,** selecione **Armazenamento De Blob Azure**, e clique **em Seguinte**. Está a utilizar o Armazenamento De Blob Azure como fonte e lojas de dados de destino nesta passagem.  
    ![Copy Tool - selecione loja de dados de destino](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. Em Especificar a página da conta de **armazenamento do Blob Azure:**  
    1. Introduza **o AzureStorageLinkedService** para o campo de nome de **ligação.**
    2. Confirme que a opção **A partir de subscrições do Azure** está selecionada para o **Método de seleção de contas**.
    3. Selecione a sua **subscrição** do Azure.
    4. Selecione a sua conta de armazenamento Azure.
    5. Clique em **Seguinte**.
10. Na página escolha do ficheiro de saída ou da **página de pastas:**  
    1. especificar **o caminho da pasta** como **adfblobconnector/output/{year}/{month}/{day}**. Introduza **o TAB**.
    1. Para o **ano,** selecione **yyyy**.
    1. Para o **mês,** confirme que está definido para **MM**.
    1. Para o **dia,** confirme que está programado para **dd**.
    1. Confirme se o tipo de **compressão** está definido para **Nenhum**.
    1. Confirme se o comportamento da **cópia** está definido para **ficheiros De fusão**. Se o ficheiro de saída com o mesmo nome já existir, o novo conteúdo é adicionado ao mesmo ficheiro no final.
    1. Clique em **Seguinte**.
       ![Ferramenta de cópia - Escolha ficheiro de saída ou pasta](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. Na página de definições do **formato 'Ficheiro',** reveja as definições e clique em **Next**. Uma das opções adicionais aqui é adicionar um cabeçalho ao ficheiro de saída. Se selecionar essa opção, é adicionada uma linha de cabeçalho com os nomes das colunas do esquema da fonte. Pode renomear os nomes de colunas predefinidos ao visualizar o esquema para a fonte. Por exemplo, pode alterar a primeira coluna para Primeiro Nome e segunda coluna para Apelido. Em seguida, o ficheiro de saída é gerado com um cabeçalho com estes nomes como nomes de colunas.
    ![Ferramenta de cópia - Definições de formato de ficheiro para destino](media/data-factory-azure-blob-connector/file-format-destination.png)
12. Na página de definições de **Desempenho,** confirme que **as unidades de nuvem** e **cópias paralelas** estão definidas para **Auto**, e clique em Seguinte. Para mais detalhes sobre estas definições, consulte o desempenho da atividade do Copy e o guia de [afinação](data-factory-copy-activity-performance.md#parallel-copy).
    ![Ferramenta de cópia - Definições de desempenho](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. Na página **Resumo,** reveja todas as definições (propriedades de tarefas, configurações para origem e destino e definições de cópia), e clique **em Seguinte**.
    ![Ferramenta de cópia - página sumária](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Reveja as informações na página **Resumo** e clique em **Concluir**. O assistente cria dois serviços ligados, dois conjuntos de dados (entrada e saída) e um pipeline na fábrica de dados (a partir da qual iniciou o Assistente de Cópia).
    ![Ferramenta de cópia - página de implantação](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>Monitorizar o gasoduto (tarefa de cópia)

1. Clique no `Click here to monitor copy pipeline` link na página **de Implantação.**
2. Deve ver a **aplicação Monitor e Gerir** num separador separado.  ![Monitorizar e gerir app](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Mude **start** o tempo de `04/19/2017` início na parte superior para e **terminar** a hora para `04/27/2017`, e, em seguida, clique em **Aplicar**.
4. Deve ver cinco janelas de atividade na lista de JANELAS de **ATIVIDADE.** Os tempos **de Início** do Janela devem abranger todos os dias desde o início do gasoduto até aos tempos finais do gasoduto.
5. Clique no botão **Refresh** para a lista **DE JANELAS** de ATIVIDADE algumas vezes até ver o estado de todas as janelas de atividade está definida para Ready.
6. Agora, verifique se os ficheiros de saída são gerados na pasta de saída do recipiente adfblobconnector. Deve ver a seguinte estrutura de pasta na pasta de saída:
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
   Para obter informações detalhadas sobre monitorização e gestão de fábricas de dados, consulte o Monitor e gere o artigo do [pipeline data Factory.](data-factory-monitor-manage-app.md)

### <a name="data-factory-entities"></a>Entidades da Fábrica de Dados
Agora, volte para o separador com a página inicial da Fábrica de Dados. Note que existem dois serviços ligados, dois conjuntos de dados e um oleoduto na sua fábrica de dados agora.

![Página inicial da Fábrica de Dados com entidades](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Clique em **Autor e implemente** para lançar Data Factory Editor.

![Editor do Data Factory](media/data-factory-azure-blob-connector/data-factory-editor.png)

Deve ver as seguintes entidades data Factory na sua fábrica de dados:

- Dois serviços ligados. Um para a fonte e outro para o destino. Ambos os serviços ligados referem-se à mesma conta de Armazenamento Azure nesta passagem.
- Dois conjuntos de dados. Um conjunto de dados de entrada e um conjunto de dados de saída. Nesta passagem, ambos utilizam o mesmo recipiente de bolha, mas referem-se a diferentes pastas (entrada e saída).
- Um oleoduto. O gasoduto contém uma atividade de cópia que utiliza uma fonte de bolha e um lavatório para copiar dados de uma localização de blob Azure para outra localização de blob Azure.

As seguintes secções fornecem mais informações sobre estas entidades.

#### <a name="linked-services"></a>Serviços ligados
Devia ver dois serviços ligados. Um para a fonte e outro para o destino. Nesta passagem, ambas as definições são as mesmas, exceto os nomes. O **tipo** de serviço ligado está definido para **AzureStorage**. A propriedade mais importante da definição de serviço ligada é a **ligaçãoString**, que é usada pela Data Factory para ligar à sua conta de Armazenamento Azure em tempo de execução. Ignore a propriedade hubName na definição.

##### <a name="source-blob-storage-linked-service"></a>Serviço ligado ao armazenamento de bolhas de origem
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

##### <a name="destination-blob-storage-linked-service"></a>Serviço ligado ao armazenamento de blob de destino

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

Para mais informações sobre o serviço ligado ao Armazenamento Azure, consulte a secção de [propriedades de serviço linked.](#linked-service-properties)

#### <a name="datasets"></a>Conjuntos de dados
Existem dois conjuntos de dados: um conjunto de dados de entrada e um conjunto de dados de saída. O tipo do conjunto de dados é definido para **AzureBlob** para ambos.

O conjunto de dados de entrada aponta para a pasta de **entrada** do recipiente de bolha **adfblobconnector.** A propriedade **externa** está definida como **verdadeira** para este conjunto de dados, uma vez que os dados não são produzidos pelo pipeline com a atividade de cópia que toma este conjunto de dados como uma entrada.

O conjunto de dados de saída aponta para a pasta de **saída** do mesmo recipiente de bolhas. O conjunto de dados de saída também utiliza o ano, mês e dia da variável do sistema **SliceStart** para avaliar dinamicamente o caminho para o ficheiro de saída. Para obter uma lista de funções e variáveis do sistema suportadas pela Data Factory, consulte funções da [Data Factory e variáveis do sistema.](data-factory-functions-variables.md) A propriedade **externa** está definida como **falsa** (valor predefinido) porque este conjunto de dados é produzido pelo gasoduto.

Para obter mais informações sobre propriedades suportadas pelo conjunto de dados Do Blob, consulte a secção de [propriedades dataset.](#dataset-properties)

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
O oleoduto tem apenas uma atividade. O **tipo** de atividade está definido para **Copiar**. Nas propriedades do tipo para a atividade, existem duas secções, uma para fonte e outra para pia. O tipo de origem é definido para **BlobSource,** uma vez que a atividade está a copiar dados de um armazenamento de bolhas. O tipo de pia é definido para **BlobSink** como a atividade copiando dados para um armazenamento de bolhas. A atividade de cópia toma o InputDataset-z4y como a entrada e outputDataset-z4y como a saída.

Para obter mais informações sobre propriedades suportadas pela BlobSource e blobSink, consulte a secção de propriedades de [atividade de Cópia.](#copy-activity-properties)

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

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>Exemplos jSON para copiar dados de e para blob armazenamento
Os exemplos seguintes fornecem definições JSON de amostra que pode usar para criar um pipeline utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostram como copiar dados de e para o Azure Blob Storage e Azure SQL Database. No entanto, os dados podem ser copiados **diretamente** de qualquer fonte para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados utilizando a Atividade de Cópia na Fábrica de Dados Azure.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>Exemplo JSON: Copiar dados do Armazenamento Blob para base de dados SQL
A amostra que se segue mostra:

1. Um serviço ligado do tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage.](#linked-service-properties)
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com uma atividade de Cópia que utiliza [blobSource](#copy-activity-properties) e [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

A amostra copia dados da série temporal de uma bolha Azure para uma tabela Azure SQL de hora a hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço ligado ao Azure SQL:**

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
**Serviço ligado ao Armazenamento Azure:**

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
A Azure Data Factory suporta dois tipos de serviços ligados ao Armazenamento Azure: **AzureStorage** e **AzureStorageSas.** Para a primeira, especifica a cadeia de ligação que inclui a chave da conta e para a posterior, especifica a Assinatura de Acesso Partilhado (SAS) Uri. Consulte a secção [de Serviços Ligados](#linked-service-properties) para obter mais detalhes.

**Conjunto de dados de entrada Azure Blob:**

Os dados são recolhidos a partir de uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta e o nome do ficheiro para a bolha são avaliados dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza ano, mês e parte do dia da hora de início e o nome do ficheiro utiliza a parte da hora da hora de início. "externa": a definição "verdadeira" informa a Data Factory de que o quadro é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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
**Conjunto de dados de saída Azure SQL:**

A amostra copia dados para uma tabela chamada "MyTable" numa base de dados Azure SQL. Crie a tabela na sua base de dados Azure SQL com o mesmo número de colunas que espera que o ficheiro Blob CSV contenha. Novas filas são adicionadas à mesa a cada hora.

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
**Uma atividade de cópia num oleoduto com fonte Blob e pia SQL:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **BlobSource** e o tipo **de pia** é definido para **SqlSink**.

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
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>Exemplo JSON: Copiar dados do Azure SQL para O Blob Azure
A amostra que se segue mostra:

1. Um serviço ligado do tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage.](#linked-service-properties)
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureBlob](#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com atividade copy que utiliza [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) e [BlobSink](#copy-activity-properties).

A amostra copia dados da série temporal de uma tabela Azure SQL para uma bolha Azure de hora a hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço ligado ao Azure SQL:**

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
**Serviço ligado ao Armazenamento Azure:**

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
A Azure Data Factory suporta dois tipos de serviços ligados ao Armazenamento Azure: **AzureStorage** e **AzureStorageSas.** Para a primeira, especifica a cadeia de ligação que inclui a chave da conta e para a posterior, especifica a Assinatura de Acesso Partilhado (SAS) Uri. Consulte a secção [de Serviços Ligados](#linked-service-properties) para obter mais detalhes.

**Conjunto de dados de entrada Azure SQL:**

A amostra pressupõe que criou uma tabela "MyTable" no Azure SQL e contém uma coluna chamada "timestampcolumn" para dados da série time.

Definição "externa": "verdadeira" informa o serviço data Factory de que a tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

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

**Conjunto de dados de saída de Azure Blob:**

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza partes ano, mês, dia e horas da hora de início.

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

**Uma atividade de cópia num oleoduto com fonte SQL e pia Blob:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **SqlSource** e o tipo **de pia** é definido para **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora para copiar.

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
> Para mapear colunas desde o conjunto de dados de origem até colunas a partir de conjunto de dados de sumidouro, consulte [colunas de conjunto de dados de mapeamento na Fábrica](data-factory-map-columns.md)de Dados Azure .

## <a name="performance-and-tuning"></a>Desempenho e Afinação
Consulte o [Copy Activity Performance & Tuning Guide](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Fábrica de Dados Do Azure e várias formas de o otimizar.
