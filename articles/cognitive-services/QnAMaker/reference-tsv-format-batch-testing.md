---
title: Formato TSV de teste de lote - QnA Maker
titleSuffix: Azure Cognitive Services
description: Compreender o formato TSV para testes de lote
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.openlocfilehash: 0ed948b4bf69fc672a59a7825279a12868d10521
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87132131"
---
# <a name="batch-testing-tsv-format"></a>Formato TSV de teste de lote

Os testes de lote estão disponíveis a partir do [código fonte](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) ou como um [fecho executável transferível](https://aka.ms/qna_btzip). O formato do comando para executar o teste de lote é:

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|Valor Esperado|
|--|--|
|1|nome do ficheiro tsv formatado com [campos de entrada TSV](#tsv-input-fields)|
|2|URI para ponto final, com o SEU ANFITRIÃO a partir da página de Publicação do portal QnA Maker.|
|3|ENDPOINT-KEY, encontrado na página de publicação do portal QnA Maker.|
|4|nome do ficheiro tsv criado pelo teste de lote para resultados.|

Utilize as seguintes informações para compreender e implementar o formato TSV para testes de lote. 

## <a name="tsv-input-fields"></a>Campos de entrada de TSV

|Campos de ficheiros de entrada de TSV|Notas|
|--|--|
|KBID|O seu KB ID encontrado na página publicar.|
|Pergunta|A pergunta que um utilizador entraria.|
|Etiquetas de metadados|opcional|
|Parâmetro superior|opcional| 
|ID de resposta esperada|opcional|

![Formato de entrada para ficheiro TSV para testes de lote.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>Campos de saída de TSV 

|Parâmetros de ficheiro de saída de TSV|Notas|
|--|--|
|KBID|O seu KB ID encontrado na página publicar.|
|Pergunta|A pergunta inserida no ficheiro de entrada.|
|Resposta|Resposta máxima da sua base de conhecimento.|
|ID de resposta|ID de resposta|
|Resultado|Previsão de resposta. |
|Etiquetas de metadados|associado com resposta devolvida|
|ID de resposta esperada|opcional (apenas quando o ID de resposta esperada é dado)|
|Rótulo de julgamento|opcional, os valores podem ser: corretos ou incorretos (apenas quando a resposta esperada é dada)|
