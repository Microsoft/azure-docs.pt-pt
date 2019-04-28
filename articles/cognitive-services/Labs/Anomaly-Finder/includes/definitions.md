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
ms.openlocfilehash: 5ad589c4adb60369f81979e214935f73d9eb0755
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309538"
---
<a name="definitions"></a>
## <a name="definitions"></a>Definições

<a name="point"></a>
### <a name="point"></a>Ponto de

|Name|Descrição|Esquema|
|---|---|---|
|**Timestamp**  <br>*Opcional*|O carimbo de hora para o ponto de dados. Certifique-se de que se alinha com a meia-noite e o uso de um UTC data hora de cadeia, por exemplo, 2017-08-01T00:00:00Z.|a cadeia de caracteres (data-hora)|
|**Valor**  <br>*Opcional*|Um valor de medida de dados.|número (double)|


<a name="request"></a>
### <a name="request"></a>Pedir

|Name|Descrição|Esquema|
|---|---|---|
|**Período**  <br>*Opcional*|O período dos pontos de dados. Se o valor é nulo ou não apresenta, a API determina o período de automaticamente.|número (double)|
|**Pontos**  <br>*Opcional*|Pontos de dados de séries de tempo. Os dados devem ser classificados por timestamp ascendente de acordo com o resultado de anomalias. Se os dados não estão ordenados corretamente ou se estiver timestamp duplicado, a API irá detetar os pontos de anomalias corretamente, mas também não foi possível corresponder os pontos retornados com a entrada. Nesse caso, será adicionada uma mensagem de aviso na resposta.|< [aponte](#point) > matriz|


<a name="response"></a>
### <a name="response"></a>Resposta

|Name|Descrição|Esquema|
|---|---|---|
|**ExpectedValues**  <br>*Opcional*|O valor previsto pelo aprendizado com base em modelo. Se os pontos de dados de entrada são ordenados por timestamp ascendente, o índice da matriz pode ser utilizado para mapear o valor esperado e o valor original.|matriz de < número (double) >|
|**IsAnomaly**  <br>*Opcional*|O resultado no se os pontos de dados são anomalias ou não em ambas as direções (picos ou as quedas anormais). TRUE significa que o ponto é anomalias, false significa que o ponto é não anomalias. Se os pontos de dados de entrada são ordenados por timestamp ascendente, o índice da matriz pode ser utilizado para mapear o valor esperado e o valor original.|matriz de < booleano >|
|**IsAnomaly_Neg**  <br>*Opcional*|O resultado no se os pontos de dados são anomalias na direção negativa (dips). TRUE significa que a direção da anomalia é negativa. Se os pontos de dados de entrada são ordenados por timestamp ascendente, o índice da matriz pode ser utilizado para mapear o valor esperado e o valor original.|matriz de < booleano >|
|**IsAnomaly_Pos**  <br>*Opcional*|O resultado no se os pontos de dados são anomalias na direção positiva (picos). TRUE significa que a direção da anomalia é positiva. Se os pontos de dados de entrada são ordenados por timestamp ascendente, o índice da matriz pode ser utilizado para mapear o valor esperado e original.|matriz de < booleano >|
|**LowerMargin**  <br>*Opcional*|(ExpectedValue - LowerMargin) determina o limite inferior que o ponto de dados ainda é considerado como normal. Se os pontos de dados de entrada são ordenados por timestamp ascendente, o índice da matriz pode ser utilizado para mapear o valor esperado e o valor original.|matriz de < número (double) >|
|**Período**  <br>*Opcional*|O período em que a API utilizada para detetar os pontos de anomalias.|número (número de vírgula flutuante)|
|**UpperMargin**  <br>*Opcional*|A soma de ExpectedValue e UpperMargin determina o limite superior que o ponto de dados ainda é considerado como normal. Se os pontos de dados de entrada são ordenados por timestamp ascendente, o índice da matriz pode ser utilizado para mapear o valor esperado e o valor original.|matriz de < número (double) >|
|**WarningText**  <br>*Opcional*|Se os pontos de dados de entrada fornecidos não estão a seguir a regra que requer que a API e os dados podem ainda ser detectados pela API, a API irá analisar os dados e acrescentar as informações de aviso neste campo.|string|



