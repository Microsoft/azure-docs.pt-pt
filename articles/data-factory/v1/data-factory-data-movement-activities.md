---
title: Mover dados utilizando a Atividade de Cópia
description: 'Saiba mais sobre o movimento de dados nos oleodutos data factory: migração de dados entre lojas de nuvem, e entre uma loja no local e uma loja na nuvem. Use a atividade de cópia.'
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8a3fe99896ff244d2e35737919e6797141095acb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364023"
---
# <a name="move-data-by-using-copy-activity"></a>Mover dados utilizando a Atividade de Cópia
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-data-movement-activities.md)
> * [Versão 2 (versão atual)](../copy-activity-overview.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte a [Copy Activity em V2](../copy-activity-overview.md).

## <a name="overview"></a>Descrição Geral
Na Azure Data Factory, pode utilizar a Copy Activity para copiar dados entre as lojas de dados no local e as lojas de dados em nuvem. Depois de os dados serem copiados, pode ser ainda mais transformado e analisado. Também pode utilizar a Copy Activity para publicar resultados de transformação e análise para inteligência empresarial (BI) e consumo de aplicações.

![Função da Atividade de Cópia](media/data-factory-data-movement-activities/copy-activity.png)

Copy Activity é alimentado por um serviço seguro, fiável, escalável e [globalmente disponível](#global). Este artigo fornece detalhes sobre o movimento de dados na Data Factory e Na Atividade de Cópia.

Primeiro, vamos ver como a migração de dados ocorre entre duas lojas de dados em nuvem, e entre uma loja de dados no local e uma loja de dados em nuvem.

> [!NOTE]
> Para conhecer as atividades em geral, consulte [a Compreensão de oleodutos e atividades.](data-factory-create-pipelines.md)
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Copiar dados entre duas lojas de dados em nuvem
Quando tanto as lojas de dados de origem como de sumidouro estão na nuvem, a Copy Activity passa pelas fases seguintes para copiar dados da fonte para a pia. O serviço que dá poder à Atividade de Cópia:

1. Lê dados da loja de dados de origem.
2. Executa serialização/deserialização, compressão/descompressão, mapeamento de colunas e conversão de tipo. Faz estas operações com base nas configurações do conjunto de dados de entrada, conjunto de dados de saída e Atividade de Cópia.
3. Escreve dados para a loja de dados de destino.

O serviço escolhe automaticamente a região ideal para realizar o movimento de dados. Esta região é geralmente a mais próxima da loja de dados da pia.

![Cópia nuvem-a-nuvem](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copiar dados entre uma loja de dados no local e uma loja de dados em nuvem
Para mover de forma segura dados entre uma loja de dados no local e uma loja de dados em nuvem, instale o Data Management Gateway na sua máquina no local. Data Management Gateway é um agente que permite o movimento e processamento de dados híbridos. Pode instalá-lo na mesma máquina que a própria loja de dados, ou numa máquina separada que tenha acesso à loja de dados.

Neste cenário, a Data Management Gateway realiza a serialização/deserialização, compressão/descompressão, mapeamento de colunas e conversão de tipo. Os dados não fluem através do serviço Azure Data Factory. Em vez disso, o Data Management Gateway escreve diretamente os dados para a loja de destino.

![Cópia no local para nuvem](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Consulte [os dados de movimento entre as lojas de dados no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para uma introdução e passagem. Consulte [o Data Management Gateway](data-factory-data-management-gateway.md) para obter informações detalhadas sobre este agente.

Também pode mover dados de/para lojas de dados suportadas que estão hospedadas em máquinas virtuais Azure IaaS (VMs) utilizando o Data Management Gateway. Neste caso, pode instalar o Data Management Gateway no mesmo VM que a própria loja de dados, ou num VM separado que tenha acesso à loja de dados.

## <a name="supported-data-stores-and-formats"></a>Lojas e formatos de dados suportados
A Atividade de Cópia no Data Factory copia os dados de um arquivo de dados de origem para um arquivo de dados sink. O Data Factory suporta os arquivos de dados seguintes. Os dados de qualquer origem podem ser escritos em qualquer sink. Clique num arquivo de dados para saber como copiar dados de e para esse arquivo.

> [!NOTE] 
> Se precisar de mover dados de/para um arquivo de dados que a Atividade de Cópia não suporte ou de utilizar uma **atividade personalizada** na Fábrica de Dados com a sua própria lógica para copiar/mover dados. Para obter detalhes sobre criar e utilizar uma atividade personalizada, veja [Use custom activities in an Azure Data Factory pipeline (Utilizar atividades personalizadas num pipeline do Azure Data Factory)](data-factory-use-custom-activities.md).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Os arquivos de dados com * podem estar no local ou no Azure IaaS e requerem a instalação do [Data Management Gateway](data-factory-data-management-gateway.md) num computador no local/Azure IaaS.

### <a name="supported-file-formats"></a>Formatos de ficheiro suportados
Pode utilizar a Copy Activity para **copiar ficheiros** em duas lojas de dados baseadas em ficheiros, pode saltar a secção de [formato](data-factory-create-datasets.md) nas definições de conjunto de dados de entrada e saída. Os dados são copiados de forma eficiente sem qualquer serialização/deserialização.

Copy Activity também lê e escreve para ficheiros em formatos especificados: **Texto, JSON, Avro, ORC e Parquet**, e o codec **GZip, Deflate, BZip2 e ZipDeflate são** suportados. Consulte [os formatos de ficheiro e compressão suportados](data-factory-supported-file-and-compression-formats.md) com detalhes.

Por exemplo, pode fazer as seguintes atividades de cópia:

* Copie os dados numa base de dados do SQL Server e escreva para a Azure Data Lake Store em formato ORC.
* Copie ficheiros em formato de texto (CSV) a partir do Sistema de Ficheiros no local e escreva para Azure Blob no formato Avro.
* Copie ficheiros zipped do Sistema de Ficheiros no local e descomprima e depois aterre na Azure Data Lake Store.
* Copie os dados no formato GZip comprimido (CSV) a partir de Azure Blob e escreva para a Base de Dados Azure SQL.

## <a name="globally-available-data-movement"></a><a name="global"></a>Movimento de dados globalmente disponível
A Azure Data Factory está disponível apenas nas regiões oeste dos EUA, Leste dos EUA e Norte da Europa. No entanto, o serviço que alimenta a Copy Activity está disponível globalmente nas seguintes regiões e geografias. A topologia globalmente disponível garante um movimento de dados eficiente que geralmente evita o lúpulo entre regiões. Consulte [serviços por região](https://azure.microsoft.com/regions/#services) para a disponibilidade de Data Factory e Data Movement numa região.

### <a name="copy-data-between-cloud-data-stores"></a>Copiar dados entre lojas de dados em nuvem
Quando tanto as lojas de dados de origem como as de sumidouros estão na nuvem, a Data Factory utiliza uma implementação de serviço na região mais próxima da pia na mesma geografia para mover os dados. Veja os mapeamentos na tabela seguinte:

| Geografia das lojas de dados de destino | Região da loja de dados de destino | Região utilizada para o movimento de dados |
|:--- |:--- |:--- |
| Estados Unidos da América | E.U.A. Leste | E.U.A. Leste |
| &nbsp; | E.U.A. Leste 2 | E.U.A. Leste 2 |
| &nbsp; | E.U.A. Central | E.U.A. Central |
| &nbsp; | E.U.A. Centro-Norte | E.U.A. Centro-Norte |
| &nbsp; | E.U.A. Centro-Sul | E.U.A. Centro-Sul |
| &nbsp; | E.U.A. Centro-Oeste | E.U.A. Centro-Oeste |
| &nbsp; | E.U.A. Oeste | E.U.A. Oeste |
| &nbsp; | E.U.A. Oeste 2 | E.U.A. Oeste 2 |
| Canadá | Leste do Canadá | Canadá Central |
| &nbsp; | Canadá Central | Canadá Central |
| Brasil | Sul do Brasil | Sul do Brasil |
| Europa | Europa do Norte | Europa do Norte |
| &nbsp; | Europa Ocidental | Europa Ocidental |
| Reino Unido | Oeste do Reino Unido | Sul do Reino Unido |
| &nbsp; | Sul do Reino Unido | Sul do Reino Unido |
| Ásia-Pacífico | Sudeste Asiático | Sudeste Asiático |
| &nbsp; | Ásia Leste | Sudeste Asiático |
| Austrália | Leste da Austrália | Leste da Austrália |
| &nbsp; | Austrália Sudeste | Austrália Sudeste |
| Índia | Índia Central | Índia Central |
| &nbsp; | Oeste da Índia | Índia Central |
| &nbsp; | Sul da Índia | Índia Central |
| Japão | Leste do Japão | Leste do Japão |
| &nbsp; | Oeste do Japão | Leste do Japão |
| Coreia | Coreia do Sul Central | Coreia do Sul Central |
| &nbsp; | Sul da Coreia do Sul | Coreia do Sul Central |

Em alternativa, pode indicar explicitamente a região do serviço data factory a ser utilizado para executar a cópia especificando `executionLocation` a propriedade em atividade de cópia `typeProperties` . Os valores suportados para esta propriedade estão listados na região acima **utilizada para coluna de movimento de dados.** Note que os seus dados passam por aquela região através do fio durante a cópia. Por exemplo, para copiar entre lojas Azure na Coreia, pode especificar `"executionLocation": "Japan East"` a rota através da região japonesa (ver amostra [JSON](#by-using-json-scripts) como referência).

> [!NOTE]
> Se a região da loja de dados de destino não estiver na lista anterior ou indetetável, por defeito a Atividade de Cópia falha em vez de passar por uma região alternativa, a menos que `executionLocation` seja especificado. A lista de regiões apoiadas será alargada ao longo do tempo.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copiar dados entre uma loja de dados no local e uma loja de dados em nuvem
Quando os dados estão a ser copiados entre as lojas no local (ou Azure virtual machines/IaaS) e as lojas cloud, [o Data Management Gateway](data-factory-data-management-gateway.md) realiza movimentos de dados numa máquina no local ou numa máquina virtual. Os dados não fluem através do serviço na nuvem, a menos que utilize a capacidade [de cópia encenada.](data-factory-copy-activity-performance.md#staged-copy) Neste caso, os dados fluem através do armazenamento Azure Blob antes de ser escrito na loja de dados da pia.

## <a name="create-a-pipeline-with-copy-activity"></a>Criar um oleoduto com Atividade de Cópia
Pode criar um oleoduto com a Copy Activity de várias formas:

### <a name="by-using-the-copy-wizard"></a>Utilizando o Assistente de Cópia
O Assistente de Cópia de Fábrica de Dados ajuda-o a criar um oleoduto com a Atividade de Cópia. Este oleoduto permite-lhe copiar dados de fontes suportadas para destinos sem escrever definições *JSON* para serviços ligados, conjuntos de dados e oleodutos. Consulte [o Assistente de Cópia da Fábrica de Dados](data-factory-copy-wizard.md) para obter mais detalhes sobre o assistente.  

### <a name="by-using-json-scripts"></a>Usando scripts JSON
Pode utilizar o Data Factory Editor em Visual Studio ou Azure PowerShell para criar uma definição JSON para um pipeline (utilizando a Copy Activity). Em seguida, pode implantá-lo para criar o oleoduto na Data Factory. Consulte [tutorial: Utilize a atividade de cópia num oleoduto Azure Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para um tutorial com instruções passo a passo.    

As propriedades JSON (como nome, descrição, tabelas de entrada e saída e políticas) estão disponíveis para todos os tipos de atividades. As propriedades disponíveis na `typeProperties` secção da atividade variam com cada tipo de atividade.

Para a Atividade de Cópia, a `typeProperties` secção varia consoante os tipos de fontes e pias. Clique numa fonte/pia na secção [de fontes e pias suportadas](#supported-data-stores-and-formats) para saber mais sobre as propriedades tipo que a Copy Activity suporta para essa loja de dados.

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
O calendário definido no conjunto de dados de saída determina quando a atividade é executado (por exemplo: **diária,** frequência como **dia,** e intervalo como **1**). A atividade copia os dados de um conjunto de dados de entrada **(fonte)** para um conjunto de dados de saída **(pia).**

Pode especificar mais de um conjunto de dados de entrada para a Atividade de Cópia. São usados para verificar as dependências antes da atividade ser executada. No entanto, apenas os dados do primeiro conjunto de dados são copiados para o conjunto de dados de destino. Para mais informações, consulte [Agendamento e execução.](data-factory-scheduling-and-execution.md)  

## <a name="performance-and-tuning"></a>Desempenho e otimização
Consulte o guia de [desempenho e afinação da Atividade de Cópia,](data-factory-copy-activity-performance.md)que descreve factores-chave que afetam o desempenho do movimento de dados (Copy Activity) na Azure Data Factory. Também enumera o desempenho observado durante os testes internos e discute várias formas de otimizar o desempenho da Copy Activity.

## <a name="fault-tolerance"></a>Tolerância a falhas
Por predefinição, a atividade de cópias deixará de copiar dados e devolveu a falha quando encontrar dados incompatíveis entre a fonte e o lavatório; enquanto pode configurar explicitamente para saltar e registar as linhas incompatíveis e apenas copiar esses dados compatíveis para fazer a cópia ter sucesso. Consulte a tolerância à [falha da atividade](data-factory-copy-activity-fault-tolerance.md) da cópia em mais detalhes.

## <a name="security-considerations"></a>Considerações de segurança
Consulte as [considerações de Segurança](data-factory-data-movement-security-considerations.md), que descreve a infraestrutura de segurança que os serviços de movimento de dados na Azure Data Factory utilizam para proteger os seus dados.

## <a name="scheduling-and-sequential-copy"></a>Agendamento e cópia sequencial
Consulte [agendamento e execução](data-factory-scheduling-and-execution.md) para obter informações detalhadas sobre como o agendamento e execução funciona na Fábrica de Dados. É possível executar várias operações de cópia uma após a outra de forma sequencial/ordenada. Consulte a secção [Copiar sequencialmente.](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)

## <a name="type-conversions"></a>Conversões de tipos
Diferentes lojas de dados têm diferentes sistemas de tipo nativo. Copy Activity executa conversões automáticas de tipo de origem para tipos de pia com a seguinte abordagem em duas etapas:

1. Converter de tipos de origem nativo para um tipo .NET.
2. Converter de um tipo .NET para um tipo de pia nativa.

O mapeamento de um sistema de tipo nativo para um tipo .NET para uma loja de dados está no respetivo artigo de loja de dados. (Clique no link específico na tabela de lojas de dados suportadas). Pode utilizar estes mapeamentos para determinar os tipos apropriados durante a criação das suas tabelas, de modo a que a Copy Activity realize as conversões certas.

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre a Atividade de Cópia, consulte [os dados da Cópia do armazenamento da Azure Blob para a Base de Dados Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Para obter informações sobre a deslocação de dados de uma loja de dados no local para uma loja de dados em nuvem, consulte [mover dados do local para lojas de dados na nuvem](data-factory-move-data-between-onprem-and-cloud.md).
