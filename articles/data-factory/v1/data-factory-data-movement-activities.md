---
title: Mover dados com a atividade de cópia | Documentos da Microsoft
description: 'Saiba mais sobre o movimento de dados no Data Factory pipelines: migração de dados entre arquivos na cloud e entre um arquivo no local e um arquivo na cloud. Utilize a atividade de cópia.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 67543a20-b7d5-4d19-8b5e-af4c1fd7bc75
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c188c23f87715b6ba5b90f6015b59f2a347ec0cf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60486966"
---
# <a name="move-data-by-using-copy-activity"></a>Mover dados com a atividade de cópia
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-data-movement-activities.md)
> * [Versão 2 (versão atual)](../copy-activity-overview.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [atividade de cópia no V2](../copy-activity-overview.md).

## <a name="overview"></a>Descrição geral
No Azure Data Factory, pode utilizar a atividade de cópia para copiar dados entre aplicações no local e na cloud arquivos de dados. Depois dos dados são copiados, ele pode ser ainda mais transformado e analisado. Também pode utilizar a atividade de cópia para publicar os resultados da análise de business intelligence (BI) e consumo de aplicativo e de transformação.

![Função de atividade de cópia](media/data-factory-data-movement-activities/copy-activity.png)

Atividade de cópia utiliza a tecnologia de seguro, confiável, escalonável, e [serviço globalmente disponível](#global). Este artigo fornece detalhes sobre movimento de dados no Data Factory e a atividade de cópia.

Em primeiro lugar, vamos ver como ocorre a migração de dados entre dois arquivos de dados em nuvem e entre um arquivo de dados no local e um arquivo de dados na cloud.

> [!NOTE]
> Para saber mais sobre as atividades em geral, veja [Noções básicas sobre pipelines e atividades](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Copiar dados entre dois arquivos de dados em nuvem
Quando os arquivos de dados de origem e sink na cloud, atividade de cópia aborda as seguintes fases para copiar dados da origem para o sink. O serviço que alimenta atividade de cópia:

1. Lê os dados do arquivo de dados de origem.
2. Executa a serialização/desserialização, compactação/descompactação, mapeamento de coluna e conversão de tipos. Ele faz essas operações com base nas configurações do conjunto de dados de entrada, o conjunto de dados de saída e a atividade de cópia.
3. Escreve dados para o arquivo de dados de destino.

O serviço escolhe automaticamente a região ideal para efetuar o movimento de dados. Esta região é, normalmente, o mais próximo para o arquivo de dados de sink um.

![Cópia na cloud para a cloud](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copiar dados entre um arquivo de dados no local e um arquivo de dados na cloud
De forma segura mover dados entre um arquivo de dados no local e um arquivo de dados na cloud, instale o Data Management Gateway no seu computador no local. O Data Management Gateway é um agente que permite o movimento de dados híbrido e o processamento. Pode instalá-lo na mesma máquina à medida que o arquivo de dados em si, ou num computador separado que tem acesso ao arquivo de dados.

Neste cenário, o Data Management Gateway efetua a serialização/desserialização, a compactação/descompactação, a coluna de mapeamento e a conversão do tipo. Não fluxo de dados através do serviço do Azure Data Factory. Em vez disso, o Data Management Gateway diretamente escreve os dados para o arquivo de destino.

![Cópia de no local-para-cloud](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Ver [mover dados entre locais e na cloud arquivos de dados](data-factory-move-data-between-onprem-and-cloud.md) para uma introdução e passo a passo. Ver [Data Management Gateway](data-factory-data-management-gateway.md) para obter informações detalhadas sobre este agente.

Também pode mover os arquivos de dados de/para suporte de dados que estiverem alojados em máquinas virtuais (VMs) IaaS do Azure com o Data Management Gateway. Neste caso, pode instalar o Gateway de gestão de dados na mesma VM à medida que o arquivo de dados em si, ou numa VM separada que tem acesso ao arquivo de dados.

## <a name="supported-data-stores-and-formats"></a>Formatos e arquivos de dados suportados
A Atividade de Cópia no Data Factory copia os dados de um arquivo de dados de origem para um arquivo de dados sink. O Data Factory suporta os arquivos de dados seguintes. Os dados de qualquer origem podem ser escritos em qualquer sink. Clique num arquivo de dados para saber como copiar dados de e para esse arquivo.

> [!NOTE] 
> Se precisar de mover dados de/para um arquivo de dados que a Atividade de Cópia não suporte ou de utilizar uma **atividade personalizada** na Fábrica de Dados com a sua própria lógica para copiar/mover dados. Para obter detalhes sobre criar e utilizar uma atividade personalizada, veja [Use custom activities in an Azure Data Factory pipeline (Utilizar atividades personalizadas num pipeline do Azure Data Factory)](data-factory-use-custom-activities.md).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Os arquivos de dados com * podem estar no local ou no Azure IaaS e requerem a instalação do [Data Management Gateway](data-factory-data-management-gateway.md) num computador no local/Azure IaaS.

### <a name="supported-file-formats"></a>Formatos de ficheiros suportados
Pode usar a atividade de cópia para **copiar ficheiros como-é** entre arquivos de dois dados baseados em ficheiros, pode ignorar o [Formatar secção](data-factory-create-datasets.md) em ambas as definições do conjunto de dados de entrada e saída. Os dados são copiados com eficiência sem qualquer serialização/desserialização.

Atividade de cópia também lê a partir do e grava arquivos em formatos especificados: **Texto, JSON, Avro, ORC e no Parquet**e o codec de compressão **GZip, Deflate, BZip2 e ZipDeflate** são suportados. Ver [formatos de ficheiro e de compressão suportados](data-factory-supported-file-and-compression-formats.md) com detalhes.

Por exemplo, pode efetuar as seguintes atividades de cópia:

* Copiar dados no SQL Server no local e escrever para o Azure Data Lake Store em formato ORC.
* Copiar arquivos no formato de texto (CSV) de sistema de ficheiros no local e escrita em BLOBs do Azure no formato Avro.
* Copiar arquivos compactados de sistema de ficheiros no local e, em seguida, descomprimir terra para o Azure Data Lake Store.
* Copiar dados em formato compactado texto (CSV) de GZip do Blob do Azure e escrever para a base de dados do Azure SQL.

## <a name="global"></a>Movimento de dados globalmente disponível
O Azure Data Factory está disponível apenas nas regiões E.U.A. oeste, E.U.A. leste e Europa do Norte. No entanto, o serviço que alimenta atividade de cópia está disponível globalmente nas seguintes regiões e localizações geográficas. A topologia globalmente disponível garante que o movimento de dados eficiente que normalmente evita saltos entre regiões. Ver [serviços por região](https://azure.microsoft.com/regions/#services) para disponibilidade do Data Factory e o movimento de dados numa região.

### <a name="copy-data-between-cloud-data-stores"></a>Copiar dados entre arquivos de dados de cloud
Quando os arquivos de dados de origem e sink na cloud, o Data Factory utiliza uma implementação de serviço na região mais próxima do sink na mesma região para mover os dados. Veja os mapeamentos na tabela seguinte:

| Geografia dos arquivos de dados de destino | Região do arquivo de dados de destino | Região utilizada para o movimento de dados |
|:--- |:--- |:--- |
| Estados Unidos | EUA Leste | EUA Leste |
| &nbsp; | EUA Leste 2 | EUA Leste 2 |
| &nbsp; | EUA Central | EUA Central |
| &nbsp; | EUA Centro-Norte | EUA Centro-Norte |
| &nbsp; | EUA Centro-Sul | EUA Centro-Sul |
| &nbsp; | EUA Centro-Oeste | EUA Centro-Oeste |
| &nbsp; | EUA Oeste | EUA Oeste |
| &nbsp; | EUA Oeste 2 | EUA Oeste 2 |
| Canadá | Leste do Canadá | Canadá Central |
| &nbsp; | Canadá Central | Canadá Central |
| Brasil | Sul do Brasil | Sul do Brasil |
| Europa | Europa do Norte | Europa do Norte |
| &nbsp; | Europa Ocidental | Europa Ocidental |
| Reino Unido | Reino Unido Oeste | Reino Unido Sul |
| &nbsp; | Reino Unido Sul | Reino Unido Sul |
| Ásia-Pacífico | Sudeste Asiático | Sudeste Asiático |
| &nbsp; | Ásia Oriental | Sudeste Asiático |
| Austrália | Leste da Austrália | Leste da Austrália |
| &nbsp; | Sudeste da Austrália | Sudeste da Austrália |
| Índia | Índia Central | Índia Central |
| &nbsp; | Oeste da Índia | Índia Central |
| &nbsp; | Sul da Índia | Índia Central |
| Japão | Leste do Japão | Leste do Japão |
| &nbsp; | Oeste do Japão | Leste do Japão |
| Coreia | Coreia do Sul Central | Coreia do Sul Central |
| &nbsp; | Coreia do Sul | Coreia do Sul Central |

Em alternativa, pode indicar explicitamente a região do serviço Data Factory para ser utilizado para efetuar a cópia especificando `executionLocation` propriedade na atividade de cópia `typeProperties`. Os valores suportados para esta propriedade estão listados na acima **região utilizada para movimento de dados** coluna. Tenha em atenção de que os seus dados são, por meio dessa região, transmitidos durante a cópia. Por exemplo, a cópia entre o Azure armazena na Coreia, pode especificar `"executionLocation": "Japan East"` para encaminhar através de região do Japão (consulte [JSON de exemplo](#by-using-json-scripts) como referência).

> [!NOTE]
> Se a região do arquivo de dados de destino não está na lista anterior ou, indetetáveis, por predefinição atividade de cópia falhar em vez de utilizar uma região alternativa, a menos que `executionLocation` está especificado. A lista de região suportada será expandida ao longo do tempo.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copiar dados entre um arquivo de dados no local e um arquivo de dados na cloud
Quando os dados estão a ser copiados no local (ou máquinas virtuais do Azure/IaaS) arquivos e na cloud [Data Management Gateway](data-factory-data-management-gateway.md) executa o movimento de dados numa máquina no local ou numa máquina virtual. Os dados não fluem através do serviço na cloud, a menos que utilize o [cópia faseada](data-factory-copy-activity-performance.md#staged-copy) capacidade. Neste caso, os dados percorrem o armazenamento de Blobs do Azure de teste antes de serem gravado no arquivo de dados de sink.

## <a name="create-a-pipeline-with-copy-activity"></a>Criar um pipeline com atividade de cópia
Pode criar um pipeline com atividade de cópia de duas formas:

### <a name="by-using-the-copy-wizard"></a>Utilizando o Assistente de cópia
O Assistente de cópia do Data Factory ajuda-o a criar um pipeline com atividade de cópia. Este pipeline permite-lhe copiar dados de fontes suportadas para destinos *sem ter de escrever JSON* definições para os serviços ligados, conjuntos de dados e pipelines. Ver [Assistente de cópia do Data Factory](data-factory-copy-wizard.md) para obter detalhes sobre o assistente.  

### <a name="by-using-json-scripts"></a>Ao utilizar scripts JSON
Pode utilizar o Editor do Data Factory no portal do Azure, Visual Studio ou do Azure PowerShell para criar uma definição de JSON para um pipeline (ao utilizar a atividade de cópia). Em seguida, pode implementá-la para criar o pipeline na fábrica de dados. Consulte [Tutorial: Utilizar a atividade de cópia num pipeline do Azure Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter um tutorial com instruções passo a passo.    

As propriedades JSON (por exemplo, nome, descrição, entrada e saída de tabelas e políticas) estão disponíveis para todos os tipos de atividades. Propriedades que estão disponíveis no `typeProperties` secção da atividade varia com cada tipo de atividade.

Para a atividade de cópia, o `typeProperties` secção varia de acordo com os tipos de origens e sinks. Clique em origem/sink na [suportado origens e sinks](#supported-data-stores-and-formats) secção para saber mais sobre as propriedades de tipo que oferece suporte a atividade de cópia para esse arquivo de dados.

Eis um exemplo de definição de JSON:

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
A agenda que é definida no conjunto de dados de saída determina quando a atividade é executada (por exemplo: **diária**, a frequência como **dia**e o intervalo como **1**). A atividade copia dados de um conjunto de dados de entrada (**origem**) para um conjunto de dados de saída (**sink**).

É possível especificar mais do que um conjunto de dados de entrada para a atividade de cópia. Elas são usadas para verificar as dependências antes da atividade é executada. No entanto, apenas os dados do primeiro conjunto de dados são copiados para o conjunto de dados de destino. Para obter mais informações, consulte [agendamento e execução](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Desempenho e otimização
Consulte a [guia de sintonização de desempenho de atividade de cópia e](data-factory-copy-activity-performance.md), que descreve os principais fatores que afetam o desempenho de movimento de dados (atividade de cópia) no Azure Data Factory. Também o desempenho observado durante o teste interno de lista e descreve várias formas de otimizar o desempenho de atividade de cópia.

## <a name="fault-tolerance"></a>Tolerância a falhas
Por predefinição, a atividade de cópia irá parar copiar dados e retorno falha quando encontrar dados incompatíveis entre a origem e sink; Embora possa configurar explicitamente para ignorar e registar linhas incompatíveis e apenas uma cópia desses dados compatíveis para fazer a cópia foi concluída com êxito. Consulte a [tolerância a falhas de atividade de cópia](data-factory-copy-activity-fault-tolerance.md) em mais detalhes.

## <a name="security-considerations"></a>Considerações de segurança
Consulte a [considerações de segurança](data-factory-data-movement-security-considerations.md), que descreve a infraestrutura de segurança que utilizam o serviços de movimento de dados no Azure Data Factory para proteger os dados.

## <a name="scheduling-and-sequential-copy"></a>Cópia de agendamento e sequencial
Ver [agendamento e execução](data-factory-scheduling-and-execution.md) para obter informações detalhadas sobre como funciona o agendamento e execução no Data Factory. É possível executar várias operações de cópia de um após o outro de forma seqüencial/ordenada. Consulte a [copiar sequencialmente](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) secção.

## <a name="type-conversions"></a>Conversões de tipo
Os arquivos de dados diferentes têm sistemas de diferentes tipos nativos. Atividade de cópia executa conversões de tipos automáticas de tipos de origem para o sink de tipos com a abordagem de dois passos seguintes:

1. Converta entre tipos de origem nativas para um tipo .NET.
2. Converta a partir de um tipo de .NET para um tipo de sink nativo.

O mapeamento de um sistema de tipo nativo para um tipo de .NET para um arquivo de dados é o artigo de arquivo de dados correspondentes. (Clique na ligação específica na tabela de arquivos de dados suportados). Pode utilizar estes mapeamentos para determinar os tipos de adequada ao criar as suas tabelas, para que a atividade de cópia executa as conversões de certas.

## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre a atividade de cópia, veja [copiar dados de armazenamento de Blobs do Azure para a base de dados do Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Para saber mais sobre mover dados de um arquivo de dados no local para um arquivo de dados na cloud, veja [mover dados do local para a cloud de arquivos de dados](data-factory-move-data-between-onprem-and-cloud.md).
