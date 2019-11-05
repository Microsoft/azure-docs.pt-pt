---
title: Formato TSV de teste em lote-QnA Maker
titleSuffix: Azure Cognitive Services
description: Entender o formato TSV para testes em lotes
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: dccf034b6497651db70b6cc09fff3f1b1d645a1b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507827"
---
# <a name="batch-testing-tsv-format"></a>Formato TSV de teste do lote

O teste em lotes está disponível no [código-fonte](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) ou como um [executável que pode ser baixado](https://aka.ms/qna_btzip). O formato do comando para executar o teste em lotes é:

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|Valor esperado|
|--|--|
|1|nome do arquivo TSV formatado com os [campos de entrada TSV](#tsv-input-fields)|
|2|URI para ponto de extremidade, com o-HOST da página de publicação do portal de QnA Maker.|
|3|CHAVE de ponto de extremidade, encontrada na página publicar do portal de QnA Maker.|
|4|nome do arquivo TSV criado pelo teste do lote para resultados.|

Use as informações a seguir para entender e implementar o formato TSV para testes em lotes. 

## <a name="tsv-input-fields"></a>Campos de entrada TSV

|Campos do arquivo de entrada TSV|Notas|
|--|--|
|KBID|Sua ID da KB encontrada na página de publicação.|
|Pergunta|A pergunta que um usuário deve inserir.|
|Etiquetas de metadados|Adicional|
|Parâmetro superior|Adicional| 
|ID de resposta esperada|Adicional|

![Formato de entrada para o arquivo TSV para teste em lotes.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>Campos de saída TSV 

|Parâmetros do arquivo de saída TSV|Notas|
|--|--|
|KBID|Sua ID da KB encontrada na página de publicação.|
|Pergunta|A pergunta como inserida no arquivo de entrada.|
|Resposta|Resposta principal da sua base de dados de conhecimento.|
|ID da resposta|ID da resposta|
|Classificação|Pontuação de previsão para resposta. |
|Etiquetas de metadados|associado à resposta retornada|
|ID de resposta esperada|opcional (somente quando a ID de resposta esperada é fornecida)|
|Etiqueta de julgamento|opcional, os valores podem ser: correto ou incorreto (somente quando a resposta esperada é fornecida)|
