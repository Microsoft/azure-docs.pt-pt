---
title: Tipos de índice no Azure Cosmos DB
description: Visão geral dos tipos de índice no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/13/2019
ms.author: mjbrown
ms.openlocfilehash: 56c0fcb24ac5d255c6a36bcffd327df76f459963
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57990559"
---
# <a name="index-types-in-azure-cosmos-db"></a>Tipos de índice no Azure Cosmos DB

Existem várias opções de onde configura a política de indexação para um caminho. Pode especificar uma ou mais definições de indexação para cada caminho:

- **Tipo de dados:** Cadeia de caracteres, número, ponto, polígono ou LineString (pode conter apenas uma entrada por tipo de dados por caminho).

- **Tipo de índice:** Intervalo (igualdade, intervalo ou ORDER BY consultas) ou Geográ (consultas espaciais).

- **Precisão:** Para um índice do intervalo, o valor de precisão máxima é -1, que também é a predefinição.

## <a name="index-kind"></a>Tipo de índice

O Azure Cosmos DB suporta o índice do intervalo para cada caminho que pode ser configurado para tipos de dados de cadeia de caracteres ou um número, ou ambos.

- **Índice do intervalo** suporta consultas de igualdade eficiente, consultas de JUNÇÃO, consultas de intervalo (usando >, <>, =, < =,! =) e consultas de ORDER BY. ORDER By consultas por predefinição também exigem a precisão de índice máximo (-1). O tipo de dados pode ser a cadeia de caracteres ou número.

- **Índice espacial** suporta eficiente espaciais (dentro e a distância) consultas. O tipo de dados pode ser LineString, Polygon ou ponto. O Azure Cosmos DB também suporta o tipo de índice espacial para cada caminho que pode ser especificado para os tipos de dados LineString, Polygon ou ponto. O valor no caminho especificado tem de ser um fragmento de GeoJSON válido, como {"type": "Point", "coordenadas": [0,0, 10.0]}. Azure Cosmos DB suporta a indexação automática de LineString, Polygon e ponto de tipos de dados.

Seguem-se exemplos de consultas de intervalo e os índices espaciais podem ser utilizados para servir:

| **Tipo de índice** | **Caso de utilização/descrição** |
| ---------- | ---------------- |
| Intervalo      | Intervalo em/prop /? (ou /) pode ser utilizado para servir as seguintes consultas de forma eficiente:<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop<br><br>Intervalo em/Propriedades / [] /? (ou / ou/propriedades /) pode ser utilizado para servir as seguintes consultas de forma eficiente:<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5  |
| Espacial    | Intervalo em/prop /? (ou /) pode ser utilizado para servir as seguintes consultas de forma eficiente:<br><br>SELECIONAR a partir de coleção c onde ST_DISTANCE(c.prop, {"type": "Point", "coordenadas": [0,0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Point",...}) – indexação nos pontos ativados<br><br>SELECIONAR a partir de coleção c onde ST_WITHIN({"type": "Polígono",...}, c.prop) – com indexação de polígonos ativados. |

## <a name="default-behavior-of-index-kinds"></a>Comportamento predefinido de tipos de índice

- Se existe um índice do intervalo de qualquer precisão para sinalizar que uma análise poderá ser necessária para servir a consulta, nesse caso, por predefinição, é devolvido um erro para consultas com operadores de intervalo como > =.

- Consultas de intervalo podem ser executadas sem um índice do intervalo, utilizando o cabeçalho "x-ms-documentdb-enable-análise" na REST API ou a opção de pedido "EnableScanInQuery" com o SDK de .NET. Se existirem quaisquer outros filtros na consulta que o Azure Cosmos DB pode usar o índice para filtrar contra, nenhum erro é retornado.

- Por predefinição, é devolvido um erro para consultas espaciais se não existir um índice geográfico ou outros filtros que podem ser fornecidos de índice. Estas consultas podem ser executadas como uma análise com x-ms-documentdb-enable-análise ou EnableScanInQuery.

## <a name="index-precision"></a>Precisão de índice

> [!NOTE]
> Um novo esquema de índice que já não necessita de uma precisão de índice personalizado que não seja o value(-1) precisão máxima de suporte de contentores do Azure Cosmos. Com esse método, os caminhos são sempre indexados com a precisão máxima. Se especificar um valor de precisão na política de indexação, os pedidos CRUD num contentores silenciosamente irão ignorar o valor de precisão e a resposta do contêiner contém apenas o value(-1) precisão máxima.  Por predefinição, todos os novos contentores de Cosmos utilizam o novo esquema de índice.

- Pode usar a precisão de índice para tornar uma compensação entre o armazenamento de índice sobrecarga e o desempenho da consulta. Para números, recomendamos que utilize a configuração de precisão de padrão de -1 (máximo). Como os números são 8 bytes em JSON, isto é equivalente a uma configuração de 8 bytes. Escolher um valor inferior para precisão, por exemplo, 1 a 7, significa que mapeia os valores dentro de alguns intervalos para o mesmo índice de entrada. Por conseguinte, é possível reduzir o espaço de armazenamento de índice, mas a execução de consultas poderá ter de processar mais itens. Por conseqüência, ele consome mais débito/RUs.

- Precisão de índice tem mais prático do aplicativo com intervalos de cadeia de caracteres. Como cadeias de caracteres podem ser qualquer comprimento arbitrário, a escolha de precisão o índice poderá afetar o desempenho das consultas de intervalo de cadeia de caracteres. Também poderá afetar a quantidade de espaço de armazenamento de índice que é necessário. Índices de intervalo de cadeia de caracteres podem ser configurados com uma precisão de índice entre 1 e 100 ou -1 (máximo). Se quiser executar consultas de ORDER BY em Propriedades de cadeia de caracteres, tem de especificar uma precisão de -1 para os caminhos correspondentes.

- Os índices espaciais utilizam sempre a precisão de índice predefinido para todos os tipos (ponto, LineString e polígonos). A precisão de índice predefinido para os índices espaciais não pode ser substituída.

O Azure Cosmos DB devolve um erro quando uma consulta utiliza ORDER BY, mas não tem um índice do intervalo contra o caminho de consultados com a precisão máxima.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a indexação no Azure Cosmos DB, veja os artigos seguintes:

- [Descrição geral de indexação](index-overview.md)
- [Política de indexação](indexing-policies.md)
- [Caminhos de índice](index-paths.md)

