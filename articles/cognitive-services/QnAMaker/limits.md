---
title: Limites e limites - QnA Maker
titleSuffix: Azure Cognitive Services
description: A ferramenta QnA Maker tem meta-limites para partes na base de dados de conhecimento e o serviço. É importante manter a sua base de dados de conhecimento dentro desses limites para testar e publicar.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/26/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: fe15cafceea8128735f7241fa5e4187d4d9c47a9
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960059"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Limites e limites de base de dados de conhecimento do QnA Maker
Lista completa dos limites entre a ferramenta QnA Maker.

## <a name="knowledge-bases"></a>Bases de dados de conhecimento

* Número máximo de bases de dados de conhecimento com base em [limites do escalão de Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Escalão de pesquisa do Azure** | **Gratuito** | **Básica** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Número máximo de bases de dados de conhecimento publicados permitido|2|14|49|199|199|2,999|

 Por exemplo, se o seu escalão tem 15 índices permitidos, pode publicar 14 bases de dados de conhecimento (1 índice por base de dados de conhecimento publicado). O índice décimo quinto, `testkb`, é utilizado para todas as bases de dados de conhecimento para criação e teste. 

## <a name="extraction-limits"></a>Limites de extração
* Número máximo de ficheiros que podem ser extraídos e o tamanho máximo do ficheiro: Consulte [preços QnAMaker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)
* Número máximo de ligações profunda que podem ser pesquisados para extração de QnAs de páginas HTML de perguntas frequentes: 20

## <a name="metadata-limits"></a>Limites de metadados
* Número máximo de campos de metadados por base de dados de conhecimento, com base em [limites do escalão de Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Escalão de pesquisa do Azure** | **Gratuito** | **Básica** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Campos de metadados máximo por serviço QnA Maker (em todos os KBs)|1,000|100 *|1,000|1,000|1,000|1,000|

## <a name="knowledge-base-content-limits"></a>Limites de conteúdo de Base de dados de conhecimento
Limites geral para o conteúdo na base de dados de conhecimento:
* Comprimento do texto de resposta: 25,000
* Comprimento do texto da pergunta: 1,000
* Comprimento do texto de chave/valor de metadados: 100
* Carateres suportados para o nome de metadados: Letras do alfabeto, dígitos e _  
* Carateres suportados para o valor de metadados: Tudo, exceto: e | 
* Comprimento do nome de ficheiro: 200
* Formatos de ficheiro suportados: ". tsv", ". pdf", ". txt", ". docx", ". xlsx".
* Número máximo de perguntas alternativos: 100
* Número máximo de pares de perguntas respostas: Depende da [escalão do Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits) escolhido. Um par de perguntas e respostas é mapeado para um documento no índice da Azure Search. 

## <a name="create-knowledge-base-call-limits"></a>Crie limites de chamada de base de dados de conhecimento:
Estes representam os limites para cada criar ação de base de dados; ou seja, clicar *KB criar* ou chamar a API de CreateKnowledgeBase.
* Número máximo de perguntas alternativos por resposta: 100
* Número máximo de URLs: 10
* Número máximo de ficheiros: 10

## <a name="update-knowledge-base-call-limits"></a>Atualizar limites de chamada de base de dados de conhecimento
Estes representam os limites para cada ação de atualização; ou seja, clicar *guardar e treinar* ou chamar a API de UpdateKnowledgeBase.
* Comprimento de cada nome de origem: 300
* Número máximo de perguntas alternativos adicionados ou excluídos: 100
* Número máximo de campos de metadados adicionados ou excluídos: 10
* Número máximo de URLs que podem ser atualizados: 5

## <a name="next-steps"></a>Passos Seguintes

Saiba quando e como alterar os escalões de serviço:

* [A ferramenta QnA Maker](how-to/upgrade-qnamaker-service.md#upgrade-qna-maker-management-sku): Quando tem de ter mais arquivos de origem ou documentos maiores em sua base de dados de conhecimento, para além do escalão atual, atualize o serviço QnA Maker escalão de preço.
* [Serviço de aplicações](how-to/upgrade-qnamaker-service.md#upgrade-app-service): Quando a sua base de dados de conhecimento precisa ser usado mais pedidos a partir da sua aplicação de cliente, atualize o escalão de preço do serviço de aplicações.
* [O Azure Search](how-to/upgrade-qnamaker-service.md#upgrade-azure-search-service): Quando planeia ter muitas bases de dados de conhecimento, atualize o serviço de Azure Search escalão de preço.
