---
title: Mover dados usando a atividade de cópia
description: 'Saiba mais sobre a movimentação de dados em pipelines Data Factory: migração de dados entre armazenamentos de nuvem e entre um repositório local e um armazenamento em nuvem. Use a atividade de cópia.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 67543a20-b7d5-4d19-8b5e-af4c1fd7bc75
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fbaa8c3544b35978786404619879f59ab91a6979
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931925"
---
# <a name="move-data-by-using-copy-activity"></a>Mover dados usando a atividade de cópia
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-data-movement-activities.md)
> * [Versão 2 (versão atual)](../copy-activity-overview.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [atividade de cópia na v2](../copy-activity-overview.md).

## <a name="overview"></a>Visão geral
No Azure Data Factory, você pode usar a atividade de cópia para copiar dados entre armazenamentos de dados locais e na nuvem. Depois dos dados são copiados, ele pode ser ainda mais transformado e analisado. Também pode utilizar a atividade de cópia para publicar os resultados da análise de business intelligence (BI) e consumo de aplicativo e de transformação.

![Função de atividade de cópia](media/data-factory-data-movement-activities/copy-activity.png)

A atividade de cópia é alimentada por um serviço seguro, confiável, escalonável e [disponível globalmente](#global). Este artigo fornece detalhes sobre a movimentação de dados no Data Factory e na atividade de cópia.

Primeiro, vamos ver como a migração de dados ocorre entre dois armazenamentos de dados de nuvem e entre um armazenamento de dados local e um armazenamento de dados na nuvem.

> [!NOTE]
> Para saber mais sobre as atividades em geral, consulte [noções básicas sobre pipelines e atividades](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Copiar dados entre dois armazenamentos de dados em nuvem
Quando os armazenamentos de dados de origem e de coletor estiverem na nuvem, a atividade de cópia passará pelos seguintes estágios para copiar dados da origem para o coletor. O serviço que alimenta atividade de cópia:

1. Lê dados do armazenamento de dados de origem.
2. Executa serialização/desserialização, compactação/descompactação, mapeamento de coluna e conversão de tipo. Ele faz essas operações com base nas configurações do conjunto de dados de entrada, o conjunto de dados de saída e a atividade de cópia.
3. Grava dados no armazenamento de dados de destino.

O serviço escolhe automaticamente a região ideal para executar a movimentação de dados. Essa região geralmente é a mais próxima do armazenamento de dados do coletor.

![Cópia da nuvem para a nuvem](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copiar dados entre um armazenamento de dados local e um armazenamento de dados na nuvem
Para mover dados com segurança entre um armazenamento de dados local e um armazenamento de dados de nuvem, instale Gerenciamento de Dados gateway no computador local. Gerenciamento de Dados gateway é um agente que permite a movimentação e o processamento de dados híbridos. Você pode instalá-lo no mesmo computador que o armazenamento de dados em si ou em um computador separado que tenha acesso ao armazenamento de dados.

Nesse cenário, Gerenciamento de Dados gateway executa a serialização/desserialização, a compactação/descompactação, o mapeamento de coluna e a conversão de tipo. Os dados não fluem pelo serviço de Azure Data Factory. Em vez disso, Gerenciamento de Dados gateway grava diretamente os dados no armazenamento de destino.

![Cópia local para nuvem](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Confira [mover dados entre armazenamentos de dados locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter uma introdução e instruções. Consulte [Gerenciamento de dados gateway](data-factory-data-management-gateway.md) para obter informações detalhadas sobre este agente.

Você também pode mover dados de/para armazenamentos de dados com suporte hospedados em VMs (máquinas virtuais) IaaS do Azure usando Gerenciamento de Dados gateway. Nesse caso, você pode instalar Gerenciamento de Dados gateway na mesma VM que o armazenamento de dados em si ou em uma VM separada que tenha acesso ao armazenamento de dados.

## <a name="supported-data-stores-and-formats"></a>Formatos e arquivos de dados suportados
A Atividade de Cópia no Data Factory copia os dados de um arquivo de dados de origem para um arquivo de dados sink. O Data Factory suporta os arquivos de dados seguintes. Os dados de qualquer origem podem ser escritos em qualquer sink. Clique num arquivo de dados para saber como copiar dados de e para esse arquivo.

> [!NOTE] 
> Se precisar de mover dados de/para um arquivo de dados que a Atividade de Cópia não suporte ou de utilizar uma **atividade personalizada** na Fábrica de Dados com a sua própria lógica para copiar/mover dados. Para obter detalhes sobre criar e utilizar uma atividade personalizada, veja [Use custom activities in an Azure Data Factory pipeline (Utilizar atividades personalizadas num pipeline do Azure Data Factory)](data-factory-use-custom-activities.md).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Os arquivos de dados com * podem estar no local ou no Azure IaaS e requerem a instalação do [Data Management Gateway](data-factory-data-management-gateway.md) num computador no local/Azure IaaS.

### <a name="supported-file-formats"></a>Formatos de ficheiros suportados
Você pode usar a atividade de cópia para **copiar arquivos no estado em que se encontram** entre dois armazenamentos de dados baseados em arquivo, você pode ignorar a [seção de formato](data-factory-create-datasets.md) nas definições de conjunto de dado de entrada e saída. Os dados são copiados com eficiência sem nenhuma serialização/desserialização.

A atividade de cópia também lê e grava em arquivos em formatos especificados: **texto, JSON, Avro, Orc e parquet**, e o codec de compactação **gzip, deflate, BZIP2 e ZipDeflate** têm suporte. Ver [formatos de ficheiro e de compressão suportados](data-factory-supported-file-and-compression-formats.md) com detalhes.

Por exemplo, pode efetuar as seguintes atividades de cópia:

* Copiar dados no SQL Server no local e escrever para o Azure Data Lake Store em formato ORC.
* Copiar arquivos no formato de texto (CSV) de sistema de ficheiros no local e escrita em BLOBs do Azure no formato Avro.
* Copiar arquivos compactados de sistema de ficheiros no local e, em seguida, descomprimir terra para o Azure Data Lake Store.
* Copiar dados em formato compactado texto (CSV) de GZip do Blob do Azure e escrever para a base de dados do Azure SQL.

## <a name="global"></a>Movimentação de dados disponível globalmente
Azure Data Factory está disponível apenas nas regiões oeste dos EUA, leste dos EUA e Europa Setentrional. No entanto, o serviço que alimenta a atividade de cópia está disponível globalmente nas seguintes regiões e regiões geográficas. A topologia globalmente disponível garante que o movimento de dados eficiente que normalmente evita saltos entre regiões. Ver [serviços por região](https://azure.microsoft.com/regions/#services) para disponibilidade do Data Factory e o movimento de dados numa região.

### <a name="copy-data-between-cloud-data-stores"></a>Copiar dados entre armazenamentos de dados de nuvem
Quando os armazenamentos de dados de origem e de coletor estiverem na nuvem, Data Factory usará uma implantação de serviço na região mais próxima do coletor na mesma geografia para mover os dados. Veja os mapeamentos na tabela seguinte:

| Geografia dos armazenamentos de dados de destino | Região do armazenamento de dados de destino | Região usada para movimentação de dados |
|:--- |:--- |:--- |
| Estados Unidos | Este dos E.U.A. | Este dos E.U.A. |
| &nbsp; | Este dos E.U.A. 2 | Este dos E.U.A. 2 |
| &nbsp; | Centro dos E.U.A. | Centro dos E.U.A. |
| &nbsp; | E.U.A. Centro-Norte | E.U.A. Centro-Norte |
| &nbsp; | E.U.A. Centro-Sul | E.U.A. Centro-Sul |
| &nbsp; | E.U.A. Centro-Oeste | E.U.A. Centro-Oeste |
| &nbsp; | Oeste dos E.U.A. | Oeste dos E.U.A. |
| &nbsp; | E.U.A. Oeste 2 | E.U.A. Oeste 2 |
| Canadá | Leste do Canadá | Canadá Central |
| &nbsp; | Canadá Central | Canadá Central |
| Brasil | Sul do Brasil | Sul do Brasil |
| Europa | Europa do Norte | Europa do Norte |
| &nbsp; | Europa Ocidental | Europa Ocidental |
| Reino Unido | Oeste do Reino Unido | Sul do Reino Unido |
| &nbsp; | Sul do Reino Unido | Sul do Reino Unido |
| Ásia-Pacífico | Sudeste Asiático | Sudeste Asiático |
| &nbsp; | Este Asiático | Sudeste Asiático |
| Austrália | Leste da Austrália | Leste da Austrália |
| &nbsp; | Sudeste da Austrália | Sudeste da Austrália |
| Índia | Centro da Índia | Centro da Índia |
| &nbsp; | Oeste da Índia | Centro da Índia |
| &nbsp; | Sul da Índia | Centro da Índia |
| Japão | Este do Japão | Este do Japão |
| &nbsp; | Oeste do Japão | Este do Japão |
| Coreia | Coreia do Sul Central | Coreia do Sul Central |
| &nbsp; | Sul da Coreia do Sul | Coreia do Sul Central |

Como alternativa, você pode indicar explicitamente a região do serviço de Data Factory a ser usada para executar a cópia especificando `executionLocation` Propriedade em `typeProperties`atividade de cópia. Os valores com suporte para essa propriedade são listados na **região acima usada para a coluna de movimentação de dados** . Observe que seus dados passam por essa região durante a cópia. Por exemplo, para copiar entre os repositórios do Azure na Coreia, você pode especificar `"executionLocation": "Japan East"` para rotear pela região do Japão (consulte [exemplo de JSON](#by-using-json-scripts) como referência).

> [!NOTE]
> Se a região do armazenamento de dados de destino não estiver na lista anterior ou não for detectável, por padrão, a atividade de cópia falhará em vez de passar por uma região alternativa, a menos que `executionLocation` seja especificado. A lista de regiões com suporte será expandida ao longo do tempo.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copiar dados entre um armazenamento de dados local e um armazenamento de dados na nuvem
Quando os dados estão sendo copiados entre o local (ou as máquinas virtuais/IaaS do Azure) e os repositórios de nuvem, [Gerenciamento de dados gateway](data-factory-data-management-gateway.md) executa a movimentação de dados em um computador local ou em uma máquina virtual. Os dados não fluem pelo serviço na nuvem, a menos que você use o recurso de [cópia em etapas](data-factory-copy-activity-performance.md#staged-copy) . Nesse caso, os dados fluem pelo armazenamento de BLOBs do Azure de preparo antes de serem gravados no armazenamento de dados do coletor.

## <a name="create-a-pipeline-with-copy-activity"></a>Criar um pipeline com a atividade de cópia
Você pode criar um pipeline com a atividade de cópia de duas maneiras:

### <a name="by-using-the-copy-wizard"></a>Usando o assistente de cópia
O assistente de cópia Data Factory ajuda a criar um pipeline com a atividade de cópia. Esse pipeline permite copiar dados de fontes com suporte para destinos *sem gravar* definições de JSON para serviços vinculados, conjuntos de dados e pipelines. Consulte [Data Factory assistente de cópia](data-factory-copy-wizard.md) para obter detalhes sobre o assistente.  

### <a name="by-using-json-scripts"></a>Usando scripts JSON
Você pode usar o editor de Data Factory no Visual Studio ou Azure PowerShell para criar uma definição de JSON para um pipeline (usando a atividade de cópia). Em seguida, você pode implantá-lo para criar o pipeline no Data Factory. Confira [tutorial: usar a atividade de cópia em um pipeline de Azure data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter um tutorial com instruções detalhadas.    

As propriedades JSON (como nome, descrição, tabelas de entrada e saída e políticas) estão disponíveis para todos os tipos de atividades. As propriedades que estão disponíveis na seção `typeProperties` da atividade variam de acordo com cada tipo de atividade.

Para a atividade de cópia, a seção `typeProperties` varia dependendo dos tipos de fontes e coletores. Clique em uma fonte/coletor na seção [fontes e coletores com suporte](#supported-data-stores-and-formats) para saber mais sobre as propriedades de tipo às quais a atividade de cópia dá suporte para esse armazenamento de dados.

Aqui está uma definição de JSON de exemplo:

```json
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from Azure blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputBlobTable"
          }
        ],
        "outputs": [
          {
            "name": "OutputSQLTable"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          },
          "executionLocation": "Japan East"          
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```
O agendamento definido no conjunto de resultados de saída determina quando a atividade é executada (por exemplo: **diária**, frequência como **dia**e intervalo como **1**). A atividade copia dados de um DataSet de entrada (**origem**) para um conjunto de dados de saída (**coletor**).

Você pode especificar mais de um conjunto de dados de entrada para a atividade de cópia. Eles são usados para verificar as dependências antes da execução da atividade. No entanto, somente os dados do primeiro DataSet são copiados para o conjunto do dados de destino. Para obter mais informações, consulte [agendamento e execução](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Desempenho e otimização
Consulte a [guia de sintonização de desempenho de atividade de cópia e](data-factory-copy-activity-performance.md), que descreve os principais fatores que afetam o desempenho de movimento de dados (atividade de cópia) no Azure Data Factory. Também o desempenho observado durante o teste interno de lista e descreve várias formas de otimizar o desempenho de atividade de cópia.

## <a name="fault-tolerance"></a>Tolerância a falhas
Por padrão, a atividade de cópia interromperá a cópia de dados e retornará uma falha quando encontrar dados incompatíveis entre a origem e o coletor; Embora você possa configurar explicitamente para ignorar e registrar as linhas incompatíveis e copiar apenas os dados compatíveis para que a cópia seja bem-sucedida. Consulte a [tolerância a falhas de atividade de cópia](data-factory-copy-activity-fault-tolerance.md) em mais detalhes.

## <a name="security-considerations"></a>Considerações de segurança
Veja as [considerações de segurança](data-factory-data-movement-security-considerations.md), que descrevem a infraestrutura de segurança que os serviços de movimentação de dados em Azure data Factory usados para proteger seus dados.

## <a name="scheduling-and-sequential-copy"></a>Agendamento e cópia sequencial
Consulte [agendamento e execução](data-factory-scheduling-and-execution.md) para obter informações detalhadas sobre como funciona o agendamento e a execução no data Factory. É possível executar várias operações de cópia uma após a outra, de maneira sequencial/ordenada. Consulte a seção [copiar sequencialmente](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) .

## <a name="type-conversions"></a>Conversões de tipos
Armazenamentos de dados diferentes têm sistemas de tipos nativos diferentes. A atividade de cópia executa conversões automáticas de tipos de origem em tipos de coletor com a seguinte abordagem de duas etapas:

1. Converter de tipos de origem nativos em um tipo .NET.
2. Converter de um tipo .NET em um tipo de coletor nativo.

O mapeamento de um sistema de tipos nativo para um tipo .NET para um armazenamento de dados está no respectivo artigo de armazenamento de dados. (Clique no link específico na tabela armazenamentos de dados com suporte). Você pode usar esses mapeamentos para determinar os tipos apropriados ao criar suas tabelas, de modo que a atividade de cópia execute as conversões corretas.

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre a atividade de cópia, consulte [copiar dados do armazenamento de BLOBs do Azure para o banco de dados SQL do Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Para saber mais sobre como mover dados de um armazenamento de dados local para um armazenamento de dados de nuvem, consulte [mover dados do local para armazenamentos de dados na nuvem](data-factory-move-data-between-onprem-and-cloud.md).
