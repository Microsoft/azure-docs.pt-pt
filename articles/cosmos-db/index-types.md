---
title: Tipos de índice no Azure Cosmos DB
description: Visão geral dos tipos de índice no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/5/2018
ms.author: rimman
ms.openlocfilehash: f45663fd0f63537f87ee4466ad5f17cce0bed6a3
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961725"
---
# <a name="index-types-in-azure-cosmos-db"></a>Tipos de índice no Azure Cosmos DB

Existem várias opções de onde configura a política de indexação para um caminho. Pode especificar uma ou mais definições de indexação para cada caminho:

- **Tipo de dados:** Cadeia de caracteres, número, ponto, polígono ou LineString (pode conter apenas uma entrada por tipo de dados por caminho).

- **Tipo de índice:** O hash (consultas de igualdade), intervalo (igualdade, intervalo ou ORDER BY consultas) ou Geográ (consultas espaciais).

- **Precisão:** Para um índice de Hash, isso varia de 1 a 8 para cadeias de caracteres e números e o valor predefinido é 3. Para um índice do intervalo, o valor de precisão máxima é de -1. Ele pode variar entre 1 e 100 (precisão máxima) para a cadeia de caracteres ou valores numéricos.

## <a name="index-kind"></a>Tipo de índice

O Azure Cosmos DB suporta o índice de Hash e o índice do intervalo para cada caminho que pode ser configurado para tipos de dados de cadeia de caracteres ou um número, ou ambos.

- **Índice de hash** oferece suporte a igualdade eficiente e consultas de JUNÇÃO. Para a maioria dos casos de utilização, os índices de Hash não tem uma precisão maior que o valor predefinido de 3 bytes. O tipo de dados pode ser a cadeia de caracteres ou número.

  > [!NOTE]
  > Um novo esquema de índice que já não utiliza o tipo de índice de Hash de suporte de contentores do Azure Cosmos. Se especificar um tipo de índice de Hash na política de indexação, os pedidos CRUD no contentor silenciosamente irão ignorar o tipo de índice e a resposta do contêiner contém apenas o tipo de índice do intervalo. Por predefinição, todos os novos contentores de Cosmos utilizam o novo esquema de índice. 
  
- **Índice do intervalo** suporta consultas de igualdade eficiente, consultas de intervalo (usando >, <>, =, < =,! =) e consultas de ORDER BY. ORDER By consultas por predefinição também exigem a precisão de índice máximo (-1). O tipo de dados pode ser a cadeia de caracteres ou número.

- **Índice espacial** suporta eficiente espaciais (dentro e a distância) consultas. O tipo de dados pode ser LineString, Polygon ou ponto. O Azure Cosmos DB também suporta o tipo de índice espacial para cada caminho que pode ser especificado para os tipos de dados LineString, Polygon ou ponto. O valor no caminho especificado tem de ser um fragmento de GeoJSON válido, como {"type": "Point", "coordenadas": [0,0, 10.0]}. Azure Cosmos DB suporta a indexação automática de LineString, Polygon e ponto de tipos de dados.

Seguem-se exemplos de consultas de Hash, intervalo, e os índices espaciais podem ser utilizados para servir:

| **Tipo de índice** | **Caso de utilização/descrição** |
| ---------- | ---------------- |
| Hash  | Hash sobre/prop /? (ou /) pode ser utilizado para servir as seguintes consultas de forma eficiente:<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>Hash sobre/propriedades / [] /? (ou / ou/propriedades /) pode ser utilizado para servir as seguintes consultas de forma eficiente:<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5  |
| Intervalo  | Intervalo em/prop /? (ou /) pode ser utilizado para servir as seguintes consultas de forma eficiente:<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop   |
| Espacial     | Intervalo em/prop /? (ou /) pode ser utilizado para servir as seguintes consultas de forma eficiente:<br><br>SELECT FROM c de coleção<br><br>ONDE ST_DISTANCE (c.prop, {"type": "Point", "coordenadas": [0,0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Point",...}) – indexação nos pontos ativados<br><br>SELECIONAR a partir de coleção c onde ST_WITHIN({"type": "Polígono",...}, c.prop) – com indexação de polígonos ativados.     |

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

