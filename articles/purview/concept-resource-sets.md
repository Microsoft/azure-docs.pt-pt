---
title: Compreender os conjuntos de recursos
description: Este artigo explica quais são os conjuntos de recursos e como o Azure Purview os cria.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 330a6e54ee88781f71c4a861051aab94f8eef81f
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587906"
---
# <a name="understanding-resource-sets"></a>Compreender os conjuntos de recursos

Este artigo ajuda-o a entender como o Azure Purview utiliza conjuntos de recursos para mapear os ativos de dados para recursos lógicos.
## <a name="background-info"></a>Informação de fundo

Os sistemas de processamento de dados à escala normalmente armazenam uma única tabela num disco como vários ficheiros. Este conceito é representado em Azure Purview utilizando conjuntos de recursos. Um conjunto de recursos é um único objeto no catálogo que representa um grande número de ativos em armazenamento.

Por exemplo, suponha que o seu cluster Spark tenha persistido um DataFrame numa fonte de dados da Azure Data Lake Storage (ADLS) Gen2. Embora em Spark a tabela pareça um único recurso lógico, no disco existem provavelmente milhares de ficheiros Parquet, cada um dos quais representa uma divisão do conteúdo total do DataFrame. Os dados de IoT e os dados de registo web têm o mesmo desafio. Imagine que tem um sensor que faz registo de ficheiros várias vezes por segundo. Não vai demorar muito até teres centenas de milhares de ficheiros de registo daquele único sensor.

Para enfrentar o desafio de mapear um grande número de ativos de dados para um único recurso lógico, o Azure Purview utiliza conjuntos de recursos.

## <a name="how-azure-purview-detects-resource-sets"></a>Como a Azure Purview deteta conjuntos de recursos

O Azure Purview suporta a deteção de conjuntos de recursos no Azure Blob Storage, ADLS Gen1 e ADLS Gen2.

A Azure Purview deteta automaticamente conjuntos de recursos durante a digitalização. Esta funcionalidade analisa todos os dados que são ingeridos através da digitalização e compara-os a um conjunto de padrões definidos.

Por exemplo, suponha que você digitaliza uma fonte de dados cuja URL é `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` . A Azure Purview olha para os segmentos do caminho e determina se correspondem a quaisquer padrões incorporados. Tem padrões incorporados para GUIDs, números, formatos de data, códigos de localização (por exemplo, en-us), e assim por diante. Neste caso, o padrão de número corresponde *a 23*. A Azure Purview assume que este ficheiro faz parte de um conjunto de recursos chamado `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` .

Ou, para um URL como `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` , Azure Purview corresponde tanto ao padrão de localização como ao padrão de número, produzindo um conjunto de recursos chamado `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` .

Utilizando esta estratégia, o Azure Purview mapearia os seguintes recursos para o mesmo conjunto de recursos, `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` :

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Tipos de ficheiros que o Azure Purview não detetará como conjuntos de recursos

A visão intencional não tenta classificar a maioria dos tipos de ficheiros de documentos como Word, Excel ou PDF como Conjuntos de Recursos. A exceção é o formato CSV, uma vez que é um formato comum de ficheiro dividido.

## <a name="how-azure-purview-scans-resource-sets"></a>Como a Azure Purview digitaliza conjuntos de recursos

Quando a Azure Purview deteta recursos que pensa fazerem parte de um conjunto de recursos, muda de uma varredura completa para uma amostra. Numa amostra, abre apenas um subconjunto dos ficheiros que pensa estarem no conjunto de recursos. Para cada ficheiro que abre, utiliza o seu esquema e executa os seus classificadores. A Azure Purview encontra então o mais recente recurso entre os recursos abertos e utiliza o esquema e classificações desse recurso na entrada para todo o conjunto de recursos definidos no catálogo.

## <a name="what-azure-purview-stores-about-resource-sets"></a>O que a Azure Purview armazena sobre conjuntos de recursos

Além de esquema único e classificações, a Azure Purview armazena as seguintes informações sobre conjuntos de recursos:

- Dados do último recurso de partição que digitalizou profundamente.
- Informação agregada sobre os recursos de partição que compõem o conjunto de recursos.
- Uma contagem de divisórias que mostra quantos recursos de partição encontrou.
- Uma contagem de esquemas que mostra quantos esquemas únicos encontrou no conjunto de amostras em que fez exames profundos. Este valor é um número entre 1 e 5, ou para valores superiores a 5, 5+.
- Uma lista de tipos de partição quando mais de um único tipo de partição está incluída no conjunto de recursos. Por exemplo, um sensor IoT pode obter tanto ficheiros XML como JSON, embora ambos sejam logicamente parte do mesmo conjunto de recursos.

## <a name="built-in-resource-set-patterns"></a>Padrões de conjunto de recursos incorporados

O Azure Purview suporta os seguintes padrões de definição de recursos. Estes padrões podem aparecer como um nome num diretório ou como parte de um nome de ficheiro.
### <a name="regex-based-patterns"></a>Padrões baseados em Regex

| Nome do padrão | Nome a Apresentar | Descrição |
|--------------|--------------|-------------|
| GUID         | {GUID}       | Um identificador globalmente único, definido no [RFC 4122](https://tools.ietf.org/html/rfc4122) |
| Número       | {N}          | Um ou mais dígitos |
| Formatos de data/hora | {Ano} {Mês} {Dia} {N}     | Apoiamos vários formatos de data/hora, mas todos estão representados com {Year}[delimiter]{Month}[delimiter]{Day} ou série de {N}s. |
| 4ByteHex     | {HEX}        | Um número HEX de 4 dígitos. |
| Localização | {LOC}        | Uma etiqueta linguística definida no [BCP 47](https://tools.ietf.org/html/bcp47), ambos - e _ nomes são suportados (por exemplo, en_ca e en-ca) |

### <a name="complex-patterns"></a>Padrões complexos

| Nome do padrão | Nome a Apresentar | Descrição |
|--------------|--------------|-------------|
| Caminho da Faísca    | {SparkPartitions} | Identificador de ficheiro de partição de faíscas |
| Data (yyyy/mm/dd)InPath  | {Ano}/{Mês}/{Dia} | Padrão ano/mês/dia abrangendo várias pastas |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-catalog"></a>Como os conjuntos de recursos são apresentados no Catálogo Azure Purview

Quando o Azure Purview combina um grupo de ativos num conjunto de recursos, tenta extrair as informações mais úteis para usar como nome de exibição no catálogo. Alguns exemplos da convenção de nomeação padrão aplicados: 

### <a name="example-1"></a>Exemplo 1

Nome qualificado: `https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

Nome do visor: "nome da saída de faísca"

### <a name="example-2"></a>Exemplo 2

Nome qualificado: `https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

Nome do visor: "os meus dados divididos"

### <a name="example-3"></a>Exemplo 3

Nome qualificado: `https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

Nome do visor: "dados"

## <a name="customizing-resource-set-grouping-using-pattern-rules"></a>Personalizar o agrupamento de conjuntos de recursos usando regras de padrão

a azure Purview usa um conjunto de padrões definidos para determinar se um grupo de ativos é um conjunto de recursos. Em alguns casos, o agrupamento de conjuntos de recursos da Azure Purview pode não refletir com precisão a sua propriedade de dados. Estas questões podem incluir:

- Marcando incorretamente um ativo como um conjunto de recursos
- Colocar um ativo no conjunto de recursos errados
- Marcando incorretamente um ativo como não sendo um conjunto de recursos

Para personalizar ou sobrepor como a Azure Purview deteta quais os ativos agrupados como conjuntos de recursos e como são exibidos dentro do catálogo, pode definir regras de padrão no centro de gestão. Para obter instruções passo a passo e sintaxe, consulte [as regras de padrão definidas por recursos](how-to-resource-set-pattern-rules.md).
## <a name="next-steps"></a>Passos seguintes

Para começar com o Azure Purview, consulte [Quickstart: Criar uma conta Azure Purview](create-catalog-portal.md).
