---
title: Mover dados utilizando a Atividade de Cópia
description: 'Conheça o movimento de dados nos oleodutos data Factory: migração de dados entre lojas de nuvem, e entre uma loja no local e uma loja de nuvem. Utilize a Atividade da Cópia.'
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281889"
---
# <a name="move-data-by-using-copy-activity"></a>Mover dados utilizando a Atividade de Cópia
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-data-movement-activities.md)
> * [Versão 2 (versão atual)](../copy-activity-overview.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [a Copy Activity em V2](../copy-activity-overview.md).

## <a name="overview"></a>Descrição geral
Na Azure Data Factory, pode utilizar a Copy Activity para copiar dados entre as lojas de dados no local e na nuvem. Depois de copiados os dados, pode ser transformado e analisado. Também pode usar a Copy Activity para publicar resultados de transformação e análise para inteligência empresarial (BI) e consumo de aplicação.

![Papel da Atividade de Cópia](media/data-factory-data-movement-activities/copy-activity.png)

A Copy Activity é alimentada por um serviço seguro, fiável, escalável e [globalmente disponível.](#global) Este artigo fornece detalhes sobre o movimento de dados na Data Factory e copy activity.

Primeiro, vamos ver como a migração de dados ocorre entre duas lojas de dados em nuvem, e entre uma loja de dados no local e uma loja de dados em nuvem.

> [!NOTE]
> Para conhecer as atividades em geral, consulte [a Compreensão de oleodutos e atividades.](data-factory-create-pipelines.md)
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Copiar dados entre duas lojas de dados em nuvem
Quando as lojas de dados de origem e pia estão na nuvem, a Copy Activity passa pelas seguintes fases para copiar dados da fonte para o lavatório. O serviço que alimenta a Atividade de Cópia:

1. Lê os dados da loja de dados de origem.
2. Realiza a serialização/desserialização, compressão/descompressão, mapeamento de colunas e conversão de tipo. Faz estas operações com base nas configurações do conjunto de dados de entrada, conjunto de dados de saída e Atividade de Cópia.
3. Escreve dados para a loja de dados de destino.

O serviço escolhe automaticamente a região ideal para realizar o movimento de dados. Esta região é geralmente a mais próxima da loja de dados do lavatório.

![Cópia cloud-to-cloud](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copiar dados entre uma loja de dados no local e uma loja de dados em nuvem
Para mover de forma segura os dados entre uma loja de dados no local e uma loja de dados em nuvem, instale o Portal de Gestão de Dados na sua máquina no local. Data Management Gateway é um agente que permite o movimento e processamento de dados híbridos. Pode instalá-lo na mesma máquina que a própria loja de dados, ou numa máquina separada que tenha acesso ao armazenamento de dados.

Neste cenário, o Portal de Gestão de Dados realiza a serialização/desserialização, compressão/descompressão, mapeamento de colunas e conversão de tipo. Os dados não fluem através do serviço Azure Data Factory. Em vez disso, o Portal de Gestão de Dados escreve diretamente os dados para a loja de destino.

![Cópia no local-para-nuvem](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Consulte [os dados entre as instalações e as lojas de dados em nuvem](data-factory-move-data-between-onprem-and-cloud.md) para uma introdução e passagem. Consulte o Portal de [Gestão](data-factory-data-management-gateway.md) de Dados para obter informações detalhadas sobre este agente.

Também pode mover dados de/para lojas de dados suportadas que estão hospedadas em máquinas virtuais Azure IaaS (VMs) utilizando gateway de gestão de dados. Neste caso, pode instalar o Portal de Gestão de Dados no mesmo VM que a própria loja de dados, ou num VM separado que tenha acesso à loja de dados.

## <a name="supported-data-stores-and-formats"></a>Lojas e formatos de dados suportados
A Atividade de Cópia no Data Factory copia os dados de um arquivo de dados de origem para um arquivo de dados sink. O Data Factory suporta os arquivos de dados seguintes. Os dados de qualquer origem podem ser escritos em qualquer sink. Clique num arquivo de dados para saber como copiar dados de e para esse arquivo.

> [!NOTE] 
> Se precisar de mover dados de/para um arquivo de dados que a Atividade de Cópia não suporte ou de utilizar uma **atividade personalizada** na Fábrica de Dados com a sua própria lógica para copiar/mover dados. Para obter detalhes sobre criar e utilizar uma atividade personalizada, veja [Use custom activities in an Azure Data Factory pipeline (Utilizar atividades personalizadas num pipeline do Azure Data Factory)](data-factory-use-custom-activities.md).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Os arquivos de dados com * podem estar no local ou no Azure IaaS e requerem a instalação do [Data Management Gateway](data-factory-data-management-gateway.md) num computador no local/Azure IaaS.

### <a name="supported-file-formats"></a>Formatos de ficheiro suportados
Pode utilizar a Copy Activity para **copiar ficheiros como está** entre duas lojas de dados baseadas em ficheiros, pode ignorar a secção de [formato](data-factory-create-datasets.md) nas definições de conjunto de dados de entrada e saída. Os dados são copiados de forma eficiente sem qualquer serialização/desserialização.

A Atividade de Cópia também lê e escreve para ficheiros em formatos especificados: **Texto, JSON, Avro, ORC e Parquet,** e o código de compressão **GZip, Deflate, BZip2 e ZipDeflate** são suportados. Consulte [formatos de ficheiros suportados e compressão](data-factory-supported-file-and-compression-formats.md) com detalhes.

Por exemplo, pode fazer as seguintes atividades de cópia:

* Copie os dados no sql server no local e escreva para a Azure Data Lake Store em formato ORC.
* Copiar ficheiros em formato de texto (CSV) a partir do Sistema de Ficheiros no local e escrever para Azure Blob em formato Avro.
* Copie ficheiros com fecho do sistema de ficheiros no local e descomprima e aterre na Azure Data Lake Store.
* Copie os dados no formato gZip comprimido (CSV) do Azure Blob e escreva para a Base de Dados Azure SQL.

## <a name="globally-available-data-movement"></a><a name="global"></a>Movimento de dados globalmente disponível
A Azure Data Factory está disponível apenas nas regiões oeste dos EUA, Leste dos EUA e Norte da Europa. No entanto, o serviço que alimenta a Copy Activity está disponível globalmente nas seguintes regiões e geografias. A topologia globalmente disponível garante um movimento eficiente de dados que geralmente evita o lúpulo transversal. Consulte [serviços por região](https://azure.microsoft.com/regions/#services) para a disponibilidade de Data Factory e Data Movement numa região.

### <a name="copy-data-between-cloud-data-stores"></a>Copiar dados entre lojas de dados em nuvem
Quando as lojas de dados de origem e pia estão na nuvem, a Data Factory utiliza uma implantação de serviços na região mais próxima da pia na mesma geografia para mover os dados. Veja os mapeamentos na tabela seguinte:

| Geografia das lojas de dados de destino | Região da loja de dados de destino | Região utilizada para o movimento de dados |
|:--- |:--- |:--- |
| Estados Unidos | E.U.A. Leste | E.U.A. Leste |
| &nbsp; | E.U.A. Leste 2 | E.U.A. Leste 2 |
| &nbsp; | E.U.A. Central | E.U.A. Central |
| &nbsp; | E.U.A. Centro-Norte | E.U.A. Centro-Norte |
| &nbsp; | E.U.A. Centro-Sul | E.U.A. Centro-Sul |
| &nbsp; | E.U.A. Centro-Oeste | E.U.A. Centro-Oeste |
| &nbsp; | E.U.A. Oeste | E.U.A. Oeste |
| &nbsp; | E.U.A.Oeste 2 | E.U.A.Oeste 2 |
| Canadá | Leste do Canadá | Canadá Central |
| &nbsp; | Canadá Central | Canadá Central |
| Brasil | Sul do Brasil | Sul do Brasil |
| Europa | Europa do Norte | Europa do Norte |
| &nbsp; | Europa ocidental | Europa ocidental |
| Reino Unido | Oeste do Reino Unido | Sul do Reino Unido |
| &nbsp; | Sul do Reino Unido | Sul do Reino Unido |
| Ásia-Pacífico | Ásia Sudeste | Ásia Sudeste |
| &nbsp; | Ásia Leste | Ásia Sudeste |
| Austrália | Leste da Austrália | Leste da Austrália |
| &nbsp; | Austrália Sudeste | Austrália Sudeste |
| Índia | Índia Central | Índia Central |
| &nbsp; | Oeste da Índia | Índia Central |
| &nbsp; | Sul da Índia | Índia Central |
| Japão | Leste do Japão | Leste do Japão |
| &nbsp; | Oeste do Japão | Leste do Japão |
| Coreia | Coreia do Sul Central | Coreia do Sul Central |
| &nbsp; | Sul da Coreia do Sul | Coreia do Sul Central |

Em alternativa, pode indicar explicitamente a região do serviço Data `executionLocation` Factory a `typeProperties`ser utilizado para executar a cópia especificando a propriedade no âmbito da Copy Activity . Os valores suportados para esta propriedade estão listados na região acima utilizada para coluna de movimento de **dados.** Note que os seus dados passam por aquela região sobre o fio durante a cópia. Por exemplo, para copiar entre lojas Azure `"executionLocation": "Japan East"` na Coreia, pode especificar para percorrer a região do Japão (ver [a amostra JSON](#by-using-json-scripts) como referência).

> [!NOTE]
> Se a região da loja de dados de destino não estiver na lista anterior ou indetetável, por padrão a Atividade de Cópia falha em vez de passar por uma região alternativa, a menos que `executionLocation` seja especificado. A lista da região apoiada será alargada ao longo do tempo.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copiar dados entre uma loja de dados no local e uma loja de dados em nuvem
Quando os dados estão a ser copiados entre as instalações (ou máquinas virtuais Azure/IaaS) e as lojas cloud, o [Data Management Gateway](data-factory-data-management-gateway.md) realiza o movimento de dados numa máquina ou máquina virtual no local. Os dados não fluem através do serviço na nuvem, a menos que utilize a capacidade de [cópia encenada.](data-factory-copy-activity-performance.md#staged-copy) Neste caso, os dados fluem através da encenação do armazenamento Da Blob antes de ser escrito na loja de dados do lavatório.

## <a name="create-a-pipeline-with-copy-activity"></a>Criar um pipeline com Atividade de Cópia
Pode criar um pipeline com Copy Activity de várias formas:

### <a name="by-using-the-copy-wizard"></a>Ao usar o Assistente de Cópia
O Assistente de Cópia da Fábrica de Dados ajuda-o a criar um pipeline com a Atividade de Cópia. Este pipeline permite-lhe copiar dados de fontes suportadas para destinos sem escrever definições *jSON* para serviços, conjuntos de dados e oleodutos ligados. Consulte o [Assistente de Cópia](data-factory-copy-wizard.md) da Fábrica de Dados para obter detalhes sobre o assistente.  

### <a name="by-using-json-scripts"></a>Usando scripts JSON
Pode utilizar o Data Factory Editor no Visual Studio, ou azure PowerShell para criar uma definição JSON para um pipeline (utilizando a Copy Activity). Em seguida, pode implantá-lo para criar o oleoduto na Fábrica de Dados. Consulte [Tutorial: Utilize a Atividade de Cópia num oleoduto Azure Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para um tutorial com instruções passo a passo.    

As propriedades jSON (tais como nome, descrição, tabelas de entrada e saída, e políticas) estão disponíveis para todos os tipos de atividades. As propriedades disponíveis `typeProperties` na secção da atividade variam com cada tipo de atividade.

No que diz `typeProperties` matéria de cópia, a secção varia consoante os tipos de fontes e pias. Clique numa secção de fonte/pia na secção de [fontes suportadas e pias](#supported-data-stores-and-formats) para saber sobre propriedades de tipo que a Copy Activity suporta para essa loja de dados.

Aqui está uma definição JSON de amostra:

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
O calendário definido no conjunto de dados de saída determina quando a atividade funciona (por exemplo: **diariamente,** frequência como **dia**, e intervalo como **1**). A atividade copia dados de um conjunto de dados de entrada **(fonte)** para um conjunto de dados de saída **(pia).**

Pode especificar mais de um conjunto de dados de entrada para copy activity. São utilizados para verificar as dependências antes da atividade ser executada. No entanto, apenas os dados do primeiro conjunto de dados são copiados para o conjunto de dados de destino. Para mais informações, consulte [Agendamento e execução](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Desempenho e otimização
Consulte o guia de [desempenho e afinação](data-factory-copy-activity-performance.md)da Atividade de Cópia, que descreve factores-chave que afetam o desempenho do movimento de dados (Copy Activity) na Fábrica de Dados Azure. Também enumera o desempenho observado durante os testes internos e discute várias formas de otimizar o desempenho da Atividade de Cópia.

## <a name="fault-tolerance"></a>Tolerância a falhas
Por predefinição, a atividade de cópia deixará de copiar dados e devolverá a falha quando encontrar dados incompatíveis entre fonte e sumidouro; enquanto pode configurar explicitamente para saltar e registar as linhas incompatíveis e apenas copiar esses dados compatíveis para que a cópia tenha sido bem sucedida. Consulte a tolerância à falha da [Atividade de Cópia](data-factory-copy-activity-fault-tolerance.md) em mais detalhes.

## <a name="security-considerations"></a>Considerações de segurança
Consulte as [considerações de Segurança](data-factory-data-movement-security-considerations.md), que descreve a infraestrutura de segurança que os serviços de movimento de dados na Azure Data Factory utilizam para proteger os seus dados.

## <a name="scheduling-and-sequential-copy"></a>Agendamento e cópia sequencial
Consulte [o Agendamento e execução](data-factory-scheduling-and-execution.md) para obter informações detalhadas sobre como funciona o agendamento e execução na Data Factory. É possível executar várias operações de cópia uma após a outra de forma sequencial/ordenada. Consulte a secção [Copy sequencialmente.](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)

## <a name="type-conversions"></a>Conversões de tipos
Diferentes lojas de dados têm diferentes sistemas de tipo nativo. A Atividade de Cópia executa conversões automáticas de tipos de tipo de origem para tipos de sink com a seguinte abordagem de dois passos:

1. Converter de tipos de origem nativa para um tipo .NET.
2. Converta de um tipo .NET para um tipo de pia nativa.

O mapeamento de um sistema de tipo nativo para um tipo .NET para uma loja de dados está no respetivo artigo da loja de dados. (Clique no link específico na tabela de lojas de dados suportados). Pode utilizar estes mapeamentos para determinar os tipos apropriados enquanto cria as suas tabelas, de modo a que a Copy Activity realize as conversões certas.

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre a Atividade de Cópia, consulte [os dados da Cópia do armazenamento do Azure Blob para a Base de Dados Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Para saber sobre a mudança de dados de uma loja de dados no local para uma loja de dados em nuvem, consulte [mover dados do local para lojas de dados em nuvem](data-factory-move-data-between-onprem-and-cloud.md).
