---
title: Formato TSV de teste de lote - QnA Maker
titleSuffix: Azure Cognitive Services
description: Compreender o formato TSV para testes de lote
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: dccf034b6497651db70b6cc09fff3f1b1d645a1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73507827"
---
# <a name="batch-testing-tsv-format"></a>Formato TSV de teste de lote

Os testes de lote estão disponíveis a partir do [código fonte](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) ou como um [fecho executável descartável.](https://aka.ms/qna_btzip) O formato do comando para executar o teste do lote é:

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|Valor Esperado|
|--|--|
|1|nome do ficheiro TSV formatado com campos de [entrada TSV](#tsv-input-fields)|
|2|URI para endpoint, com YOUR-HOST a partir da página de publicação do portal QnA Maker.|
|3|ENDPOINT-KEY, encontrado na página de publicação do portal QnA Maker.|
|4|nome do ficheiro TSV criado por teste de lote para resultados.|

Utilize as seguintes informações para compreender e implementar o formato TSV para testes de lote. 

## <a name="tsv-input-fields"></a>Campos de entrada TSV

|Campos de ficheiros de entrada TSV|Notas|
|--|--|
|KBID|O seu ID KB encontrado na página Editorial.|
|Pergunta|A pergunta que um utilizador entraria.|
|Etiquetas de metadados|opcional|
|Parâmetro superior|opcional| 
|ID de resposta esperada|opcional|

![Formato de entrada para ficheiro TSV para teste de lote.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>Campos de saída TSV 

|Parâmetros de ficheiro de saída TSV|Notas|
|--|--|
|KBID|O seu ID KB encontrado na página Editorial.|
|Pergunta|A questão tal como inserida no ficheiro de entrada.|
|Resposta|Resposta máxima da sua base de conhecimento.|
|ID de resposta|ID de resposta|
|Classificação|Pontuação de previsão para resposta. |
|Etiquetas de metadados|associado com resposta devolvida|
|ID de resposta esperada|opcional (apenas quando é dada identificação de resposta esperada)|
|Rótulo de julgamento|opcionais, os valores podem ser: correctoou ou incorreto (apenas quando for dada a resposta esperada)|
