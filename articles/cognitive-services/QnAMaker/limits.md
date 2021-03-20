---
title: Limites e limites - QnA Maker
description: A QnA Maker tem meta-limites para partes da base de conhecimento e serviço. É importante manter a sua base de conhecimento dentro desses limites para testar e publicar.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 1e57ae537c271e61f0b2d37f5320cb177b04802b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98164877"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker limites e limites de base de conhecimento

Os limites do Fabricante QnA fornecidos abaixo são uma combinação dos limites do nível de preços de [pesquisa cognitiva Azure](../../search/search-limits-quotas-capacity.md) e os [limites de nível de preços do Fabricante QnA](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). Você precisa saber ambos os conjuntos de limites para entender quantas bases de conhecimento você pode criar por recurso e quão grande cada base de conhecimento pode crescer.

## <a name="knowledge-bases"></a>Bases de conhecimento

O número máximo de bases de conhecimento baseia-se nos [limites do nível de pesquisa cognitiva do Azure.](../../search/search-limits-quotas-capacity.md)

|**Nível de pesquisa cognitiva Azure** | **Gratuito** | **Básica** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Número máximo de bases de conhecimento publicadas permitidas|2|14|49|199|199|2,999|

 Por exemplo, se o seu nível tiver 15 índices permitidos, pode publicar 14 bases de conhecimento (1 índice por base de conhecimentos publicados). O décimo quinto `testkb` índice, é usado para todas as bases de conhecimento para autoria e teste.

## <a name="extraction-limits"></a>Limites de extração

### <a name="file-naming-constraints"></a>Restrições de nomeação de ficheiros

Os nomes dos ficheiros não podem incluir os seguintes caracteres:

|Não use o caráter|
|--|
|Cotação única `'`|
|Cotação dupla `"`|

### <a name="maximum-file-size"></a>Tamanho máximo do ficheiro

|Formato|Tamanho do ficheiro máximo (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Número máximo de ficheiros

O número máximo de ficheiros que podem ser extraídos e o tamanho máximo do ficheiro baseia-se nos **[limites do nível de preços do QnA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)**.

> [!NOTE]
> QnA Maker gerido (Preview) é um serviço gratuito sem limites no número de fontes que podem ser adicionadas. Atualmente, a produção está limitada a 10 transações por segundo tanto para as APIs de gestão como para as APIs de previsão.

### <a name="maximum-number-of-deep-links-from-url"></a>Número máximo de ligações profundas a partir de URL

O número máximo de ligações profundas que podem ser rastejadas para a extração de QnAs a partir de uma página de URL é **de 20**.

## <a name="metadata-limits"></a>Limites de metadados

Os metadados são apresentados como uma chave baseada em texto:par de valor, tais como `product:windows 10` . É armazenado e comparado em minúsculas.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Por Azure Cognitive Search priceing tier

O número máximo de campos de metadados por base de conhecimento baseia-se nos **[limites do nível de pesquisa cognitiva do Azure.](../../search/search-limits-quotas-capacity.md)**

|**Nível de pesquisa cognitiva Azure** | **Gratuito** | **Básica** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Campos máximos de metadados por serviço QnA Maker (em todos os KBs)|1,000|100*|1,000|1,000|1,000|1,000|

### <a name="by-name-and-value"></a>Pelo nome e valor

Os caracteres de comprimento e aceitáveis para nome e valor dos metadados estão listados na tabela seguinte.

|Item|Chars permitidos|Combinação de padrão regex|Max chars|
|--|--|--|--|
|Nome (chave)|Permite<br>alfanumérico (letras e dígitos)<br>`_` (sublinhar)<br> Não deve conter espaços.|`^[a-zA-Z0-9_]+$`|100|
|Valor|Permite tudo, exceto<br>`:` (cólon)<br>`|` (tubo vertical)<br>Só é permitido um valor.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Limites de conteúdo da Base de Conhecimento
Limites globais sobre o conteúdo na base de conhecimento:
* Duração do texto de resposta: 25.000 caracteres
* Duração do texto de pergunta: 1.000 caracteres
* Comprimento do texto chave dos metadados: 100 caracteres
* Comprimento do texto do valor dos metadados: 500 caracteres
* Caracteres suportados para nome de metadados: Alfabetos, dígitos e `_`
* Caracteres suportados para valor de metadados: Todos exceto `:` e `|`
* Duração do ficheiro: 200
* Formatos de ficheiros suportados: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Número máximo de perguntas alternativas: 300
* Número máximo de pares de perguntas-respostas: Depende do **[nível de Pesquisa Cognitiva Azure](../../search/search-limits-quotas-capacity.md#document-limits)** escolhido. Um par de perguntas e respostas mapeia para um documento no índice de Pesquisa Cognitiva Azure.
* Página URL/HTML: 1 milhão de caracteres

## <a name="create-knowledge-base-call-limits"></a>Criar limites de chamada base de conhecimento:
Estes representam os limites para cada um criar ação de base de conhecimento; isto é, clicar em *Criar KB* ou chamar a API createKnowledgeBase.
* Número máximo recomendado de perguntas alternativas por resposta: 300
* Número máximo de URLs: 10
* Número máximo de ficheiros: 10
* Número máximo de QnAs permitido por chamada: 1000

## <a name="update-knowledge-base-call-limits"></a>Atualizar limites de chamada de base de conhecimento
Estes representam os limites para cada ação de atualização; isto é, clicar em *Guardar e treinar* ou ligar para a API updateKnowledgeBase.
* Comprimento de cada nome de origem: 300
* Número máximo recomendado de perguntas alternativas adicionadas ou eliminadas: 300
* Número máximo de campos de metadados adicionados ou eliminados: 10
* Número máximo de URLs que podem ser atualizados: 5
* Número máximo de QnAs permitido por chamada: 1000

## <a name="next-steps"></a>Passos seguintes

Saiba quando e como alterar os [níveis de preços do serviço](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku).
