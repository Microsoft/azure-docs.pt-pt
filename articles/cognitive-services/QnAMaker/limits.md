---
title: Limites e limites - QnA Maker
description: A QnA Maker tem meta-limites para partes da base e serviço de conhecimento. É importante manter a sua base de conhecimento dentro desses limites para testar e publicar.
ms.topic: reference
ms.date: 02/14/2020
ms.openlocfilehash: 6375a6c6efc0c7016d9947e04e9479385aa80af5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273342"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker limites e limites de base de conhecimento

Os limites do Fabricante qnA fornecidos abaixo são uma combinação dos limites de preços de [pesquisa cognitiva Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) e os limites de preços do [Fabricante QnA](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). Precisa de saber ambos os conjuntos de limites para perceber quantas bases de conhecimento pode criar por recurso e quão grande cada base de conhecimento pode crescer.

## <a name="knowledge-bases"></a>Bases de conhecimento

O número máximo de bases de conhecimento baseia-se nos limites do nível de [pesquisa cognitiva Azure.](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Nível de pesquisa cognitiva azure** | **Gratuito** | **Básico** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Número máximo de bases de conhecimento publicadas permitidas|2|14|49|199|199|2,999|

 Por exemplo, se o seu nível tiver 15 índices permitidos, pode publicar 14 bases de conhecimento (1 índice por base de conhecimento publicada). O décimo quinto `testkb`índice, é utilizado para todas as bases de conhecimento para autoria e teste.

## <a name="extraction-limits"></a>Limites de extração

### <a name="file-naming-constraints"></a>Restrições de nomeação de ficheiros

Os nomes dos ficheiros não podem incluir os seguintes caracteres:

|Não use o caráter|
|--|
|Citação única`'`|
|Citação dupla`"`|

### <a name="maximum-file-size"></a>Tamanho máximo do ficheiro

|Formato|Tamanho máximo do ficheiro (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Número máximo de ficheiros

O número máximo de ficheiros que podem ser extraídos e o tamanho máximo do ficheiro baseia-se nos limites dos limites dos seus níveis de **[preços qnA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)**.

### <a name="maximum-number-of-deep-links-from-url"></a>Número máximo de ligações profundas a partir de URL

O número máximo de ligações profundas que podem ser rastejadas para extração de QnAs a partir de uma página de URL é **de 20**.

## <a name="metadata-limits"></a>Limites de metadados

Os metadados são apresentados como uma chave `product:windows 10`baseada em texto:par de valor, como . É armazenado e comparado em maiúsculas.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Por Nível de preços de pesquisa cognitiva Azure

O número máximo de campos de metadados por base de conhecimento baseia-se nos limites do seu nível de **[pesquisa cognitiva Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)**.

|**Nível de pesquisa cognitiva azure** | **Gratuito** | **Básico** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Campos máximos de metadados por serviço QnA Maker (em todos os KBs)|1,000|100*|1,000|1,000|1,000|1,000|

### <a name="by-name-and-value"></a>Pelo nome e valor

O comprimento e caracteres aceitáveis para nome e valor de metadados estão listados na tabela seguinte.

|Item|Chars permitidos|Correspondência de padrão regex|Max chars|
|--|--|--|--|
|Nome (chave)|Permite<br>alfanumérico (letras e dígitos)<br>`_`(sublinhado)<br> Não deve conter espaços.|`^[a-zA-Z0-9_]+$`|100|
|Valor|Permite tudo menos<br>`:`(cólon)<br>`|`(tubo vertical)<br>Só é permitido um valor.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Limites de conteúdo da Base de Conhecimento
Limites globais do conteúdo na base de conhecimentos:
* Duração do texto da resposta: 25.000
* Duração do texto de pergunta: 1.000
* Comprimento da chave/texto de valor dos metadados: 100
* Caracteres suportados para nome de metadados: Alfabetos, dígitos e`_`
* Caracteres suportados para valor de `:` metadados: Tudo exceto e`|`
* Comprimento do nome do ficheiro: 200
* Formatos de ficheiros suportados: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Número máximo de perguntas alternativas: 300
* Número máximo de pares de resposta seleções: Depende do nível de **[Pesquisa Cognitiva Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** escolhido. Um par de perguntas e respostas mapeia um documento no índice de pesquisa cognitiva Azure.
* Página URL/HTML: 1 milhão de caracteres

## <a name="create-knowledge-base-call-limits"></a>Criar limites de chamada base de conhecimento:
Estes representam os limites para cada uma criar uma ação de base de conhecimento; isto é, clicar *em Criar KB* ou chamar a CreateKnowledgeBase API.
* Número máximo de perguntas alternativas por resposta: 300
* Número máximo de URLs: 10
* Número máximo de ficheiros: 10

## <a name="update-knowledge-base-call-limits"></a>Atualizar os limites de chamada base do Conhecimento
Estes representam os limites para cada ação de atualização; isto é, clicar em *Guardar e treinar* ou chamar a API updateKnowledgeBase.
* Comprimento de cada nome de origem: 300
* Número máximo de perguntas alternativas adicionadas ou eliminadas: 300
* Número máximo de campos de metadados adicionados ou eliminados: 10
* Número máximo de URLs que podem ser refrescados: 5

## <a name="next-steps"></a>Passos seguintes

Saiba quando e como alterar [os níveis](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)de preços do serviço .
