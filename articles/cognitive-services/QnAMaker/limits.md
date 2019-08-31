---
title: Limites e limites - QnA Maker
titleSuffix: Azure Cognitive Services
description: A ferramenta QnA Maker tem meta-limites para partes na base de dados de conhecimento e o serviço. É importante manter a sua base de dados de conhecimento dentro desses limites para testar e publicar.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: c7b0dc39d2da403383f245b9ff3227734c58cbbe
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193476"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Limites e limites de base de dados de conhecimento do QnA Maker

Os limites de QnA Maker fornecidos abaixo são uma combinação dos [limites do tipo de preço Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) e os [limites do tipo de preço do QnA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). Você precisa saber os dois conjuntos de limites para entender quantas bases de dados de conhecimento você pode criar por recurso e o tamanho de cada base de dados de conhecimento.

## <a name="knowledge-bases"></a>Bases de dados de conhecimento

O número máximo de bases de dados de conhecimento baseia-se nos [limites da camada de Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity).

|**Escalão de pesquisa do Azure** | **Gratuito** | **Básica** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Número máximo de bases de dados de conhecimento publicadas permitido|2|14|49|199|199|2,999|

 Por exemplo, se sua camada tiver 15 índices permitidos, você poderá publicar 14 bases de dados de conhecimento (1 índice por base de dados de conhecimento publicado). O índice décimo-quinto, `testkb`, é usado para todas as bases de dados de conhecimento para criação e teste. 

## <a name="extraction-limits"></a>Limites de extração

### <a name="maximum-number-of-files"></a>Número máximo de arquivos

O número máximo de arquivos que podem ser extraídos e o tamanho máximo do arquivo baseia-se nos limites do seu **[QnA Maker tipo de preço](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** .

### <a name="maximum-number-of-deep-links-from-url"></a>Número máximo de links profundos da URL

O número máximo de links profundos que podem ser rastreados para extração de QnAs de uma página de URL é **20**.

## <a name="metadata-limits"></a>Limites de metadados

### <a name="by-azure-search-pricing-tier"></a>Por Azure Search tipo de preço

O número máximo de campos de metadados por base de dados de conhecimento baseia-se em seus **[limites de camada de Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** .

|**Escalão de pesquisa do Azure** | **Gratuito** | **Básica** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Campos de metadados máximo por serviço QnA Maker (em todos os KBs)|1,000|100 *|1,000|1,000|1,000|1,000|

### <a name="by-name-and-value"></a>Por nome e valor

O comprimento e os caracteres aceitáveis para o nome e o valor dos metadados são listados na tabela a seguir.

|Item|Caracteres permitidos|Correspondência de padrão de Regex|Máximo de caracteres|
|--|--|--|--|
|Name|Permitem<br>alfanumérico (letras e dígitos)<br>`_`sublinhado|`^[a-zA-Z0-9_]+$`|100|
|Value|Permite tudo, exceto<br>`:`pontos<br>`|`(canal vertical)|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Limites de conteúdo de Base de dados de conhecimento
Limites geral para o conteúdo na base de dados de conhecimento:
* Tamanho do texto de resposta: 25,000
* Tamanho do texto da pergunta: 1,000
* Comprimento do texto de chave/valor de metadados: 100
* Caracteres com suporte para o nome de metadados: Alfabetos, dígitos e`_`  
* Caracteres com suporte para o valor de metadados: Todos, `:` exceto e`|` 
* Comprimento do nome do arquivo: 200
* Formatos de ficheiro suportados: ". tsv", ". pdf", ". txt", ". docx", ". xlsx".
* Número máximo de perguntas alternativas: 300
* Número máximo de pares de respostas de perguntas: Depende da **[camada de Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** escolhida. Um par de perguntas e respostas é mapeado para um documento no índice Azure Search. 
* URL/página HTML: 1 milhão caracteres

## <a name="create-knowledge-base-call-limits"></a>Crie limites de chamada de base de dados de conhecimento:
Estes representam os limites para cada criar ação de base de dados; ou seja, clicar *KB criar* ou chamar a API de CreateKnowledgeBase.
* Número máximo de perguntas alternativas por resposta: 300
* Número máximo de URLs: 10
* Número máximo de arquivos: 10

## <a name="update-knowledge-base-call-limits"></a>Atualizar limites de chamada de base de dados de conhecimento
Estes representam os limites para cada ação de atualização; ou seja, clicar *guardar e treinar* ou chamar a API de UpdateKnowledgeBase.
* Comprimento de cada nome de origem: 300
* Número máximo de perguntas alternativas adicionadas ou excluídas: 300
* Número máximo de campos de metadados adicionados ou excluídos: 10
* Número máximo de URLs que podem ser atualizadas: 5

## <a name="next-steps"></a>Passos Seguintes

Saiba quando e como alterar os [tipos de preço de serviço](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker).
