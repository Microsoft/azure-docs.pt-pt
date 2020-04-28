---
title: ST_ISVALID na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL ST_ISVALID em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fbddbe82ae13585b8259a66dffaeef8024baf5d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71349359"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
 Devolve um valor booleano indicando se a expressão especificada de GeoJSON Point, Polygon ou LineString é válida.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*spatial_expr*  
   É uma expressão GeoJSON Point, Polygon ou LineString.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão booleana.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo que se segue mostra como verificar se um ponto é válido usando ST_VALID.  
  
  Por exemplo, este ponto tem um valor de latitude que não está na gama válida de valores [-90, 90], pelo que a consulta devolve falso.  
  
  Para os polígonos, a especificação GeoJSON requer que o último par de coordenadas fornecido deve ser o mesmo que o primeiro, para criar uma forma fechada. Os pontos dentro de um polígono devem ser especificados por ordem no sentido contrário ao dos ponteiros do relógio. Um polígono especificado no sentido horário representa o inverso da região no seu interior.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções espaciais Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
