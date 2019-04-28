---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 80503ad154a9fc4d01614ffd2816f9d5fd497fdb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309533"
---
<a name="paths"></a>
## <a name="paths"></a>Caminhos

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>Detetar pontos de anomalias para o tempo de pontos de dados de série pedido
```
POST /anomalydetection
```


#### <a name="parameters"></a>Parâmetros

|Type|Name|Descrição|Esquema|
|---|---|---|---|
|**Corpo**|**body**  <br>*required*|Série de tempo os pontos de dados e o período, se for necessário.|[request](#request)|


#### <a name="responses"></a>Respostas

|Código de HTTP|Descrição|Esquema|
|---|---|---|
|**200**|Operação concluída com êxito.|< [resposta](#response) > matriz|
|**400**|Não é possível analisar o pedido do JSON.|Sem Conteúdo|
|**403**|O certificado que forneceu não é aceite pelo servidor.|Sem Conteúdo|
|**405**|Método não permitido.|Sem Conteúdo|
|**500**|Erro Interno do Servidor.|Sem Conteúdo|


#### <a name="consumes"></a>Consome

* `application/json`


#### <a name="produces"></a>Produz

* `application/json`


#### <a name="tags"></a>Etiquetas

* anomalydetection



