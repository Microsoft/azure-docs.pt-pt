---
title: Compreender conjuntos de recursos
description: Este artigo explica quais são os conjuntos de recursos e como o Azure Purview os cria.
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/19/2020
ms.openlocfilehash: 55efa9443fd59b66a7677c9c460e473715f201df
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553986"
---
# <a name="understanding-resource-sets"></a>Compreender conjuntos de recursos

Este artigo ajuda-o a entender como o Azure Purview utiliza conjuntos de recursos para mapear os ativos de dados para recursos lógicos.

## <a name="background-info"></a>Informação de fundo

Os sistemas de processamento de dados à escala normalmente armazenam uma única tabela num disco como vários ficheiros. Este conceito é representado em Azure Purview utilizando conjuntos de recursos. Um conjunto de recursos é um único objeto no catálogo que representa um grande número de ativos em armazenamento.

Por exemplo, suponha que o seu cluster Spark tenha persistido um DataFrame numa fonte de dados da ADLS Gen2. Embora em Spark a tabela pareça um único recurso lógico, no disco existem provavelmente milhares de ficheiros Parquet, cada um dos quais representa uma divisão do conteúdo total do DataFrame. Os dados de IoT e os dados de registo web têm o mesmo desafio. Imagine que tem um sensor que faz registo de ficheiros várias vezes por segundo. Não vai demorar muito até teres centenas de milhares de ficheiros de registo daquele único sensor.

Para enfrentar o desafio de mapear um grande número de ativos de dados para um único recurso lógico, o Azure Purview utiliza conjuntos de recursos.

## <a name="how-azure-purview-detects-resource-sets"></a>Como a Azure Purview deteta conjuntos de recursos

O Azure Purview suporta a deteção de conjuntos de recursos apenas em Azure Blobs, ADLS Gen1 e ADLS Gen2.

O Azure Purview deteta automaticamente conjuntos de recursos utilizando uma funcionalidade chamada descoberta automatizada de conjuntos de recursos. Esta funcionalidade analisa todos os dados que são ingeridos através da digitalização e compara-os a um conjunto de padrões definidos.

Por exemplo, suponha que você digitaliza uma fonte de dados cuja URL é `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` . A Azure Purview olha para os segmentos do caminho e determina se correspondem a quaisquer padrões incorporados. Tem padrões incorporados para GUIDs, números, formatos de data, códigos de localização (por exemplo, en-us), e assim por diante. Neste caso, o padrão de número corresponde *a 23*. A Azure Purview assume que este ficheiro faz parte de um conjunto de recursos chamado `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` .

Ou, para um URL como `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` , Azure Purview corresponde tanto ao padrão de localização como ao padrão de número, produzindo um conjunto de recursos chamado `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` .

Utilizando esta estratégia, o Azure Purview mapearia os seguintes recursos para o mesmo conjunto de recursos, `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` :

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

> [!Note]
> O Azure Data Lake Storage Gen2 já está em disponibilidade geral. Recomendamos que comece a utilizar hoje. Para mais informações, consulte a página do [produto.](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/)

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Tipos de ficheiros que o Azure Purview não detetará como conjuntos de recursos

A visão intencional não tenta classificar a maioria dos tipos de ficheiros de documentos como Word, Excel ou PDF como Conjuntos de Recursos. A exceção são CSVs, uma vez que é um formato comum de ficheiro dividido.

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

| Nome do padrão | Nome a apresentar | Descrição |
|--------------|--------------|-------------|
| GUID         | {GUID}       | Um identificador globalmente único, tal como definido no [RFC 4122](https://tools.ietf.org/html/rfc4122). |
| Número       | {N}          | Um ou mais dígitos. |
| Formatos de data/hora | {N}     | O Azure Purview suporta diferentes tipos de formatos de data/hora, mas todos são reduzidos a uma série de {N}s. |
| 4ByteHex     | {HEX}        | Um número hexadecimal de quatro dígitos. |
| Localização | {LOC}        | Uma etiqueta linguística, tal como definida no [BCP 47](https://tools.ietf.org/html/bcp47). A azure Purview suporta tags que contêm um hífen (-) ou um sublinhado (_). Por exemplo, en_ca e en-ca. |

## <a name="issues-with-resource-sets"></a>Problemas com conjuntos de recursos

Embora os conjuntos de recursos funcionem bem na maioria dos casos, poderá encontrar os seguintes problemas, nos quais a Azure Purview:

- Marca incorretamente um ativo como um conjunto de recursos
- Coloca um ativo no conjunto de recursos errados
- Incorretamente marca um ativo como não sendo um conjunto de recursos

## <a name="next-steps"></a>Passos seguintes

Para começar com o Catálogo de Dados, consulte [Quickstart: Criar uma conta Azure Purview](create-catalog-portal.md).
