---
title: Suporte ao idioma-API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: 'Uma lista de idiomas naturais com suporte pelo API de Análise de Texto. Este artigo explica quais idiomas têm suporte para cada operação: análise de sentimentos, extração de frases-chave, detecção de idioma e reconhecimento de entidade.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: aahi
ms.openlocfilehash: 96539a59bd22644eaedb88886bc5b9facb4ca1c1
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302102"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Suporte a idiomas e regiões para o API de Análise de Texto

Este artigo explica quais idiomas têm suporte para cada operação: análise de sentimentos, extração de frases-chave, detecção de idioma e reconhecimento de entidade nomeada.

## <a name="language-detection"></a>Deteção de Idioma

O API de Análise de Texto pode detectar uma ampla gama de linguagens, variantes, dialetos e alguns idiomas regionais/culturais.  Detecção de Idioma retorna o "script" de um idioma. Por exemplo, para a frase "Eu tenho um cachorro", ela retornará `en` em vez `en-US`de. O único caso especial é chinês, em que o recurso de detecção de `zh_CHS` idioma `zh_CHT` retornará ou se puder determinar o script dado ao texto fornecido. Em situações em que um script específico não pode ser identificado para um documento chinês, ele retornará `zh`simplesmente.

Não publicamos a lista exata de idiomas para esse recurso, mas ele pode detectar uma ampla gama de linguagens, variantes, dialetos e alguns idiomas regionais/culturais. 

Se você tiver conteúdo expresso em uma linguagem usada com menos frequência, poderá tentar Detecção de Idioma para ver se ele retorna um código. A resposta para idiomas que não podem ser detectados é `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Reconhecimento de entidade Análise de Sentimento, Extração de Frases-chave e nomeada

Para análise de sentimentos, extração de frases-chave e reconhecimento de entidade, a lista de idiomas com suporte é mais seletiva, pois os analisadores são refinados para acomodar as regras linguísticas de idiomas adicionais. No momento, o suporte para o conjunto completo de [tipos de entidade](how-tos/text-analytics-how-to-entity-linking.md#supported-types-for-named-entity-recognition) está limitado aos seguintes idiomas: 
* Português
* Chinês-simplificado
* Francês
* Alemão
* Espanhol

Somente as `Person` `Location` entidades e`Organization` nomeadas são retornadas para os outros idiomas.

## <a name="language-list-and-status"></a>Lista de idiomas e status

O suporte a idiomas é inicialmente distribuído na visualização, graduando para o status de GA (disponibilidade geral), independentemente uns dos outros e do serviço de Análise de Texto geral. É possível que as linguagens permaneçam em visualização, mesmo que API de Análise de Texto transições para disponibilidade geral.

| Idioma    | Código do idioma | Sentimento | Frases-chave | Reconhecimento de Entidades Nomeadas |   Notas  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| Árabe      | `ar`          |           |             | ✔ \*                     | |
| Checo       | `cs`          |           |             | ✔ \*                     | |
| Chinês-simplificado | `zh-CN`|           |             | ✔ \        |    |
| Dinamarquês      | `da`          | ✔ \*     | ✔           | ✔ \*            |     |
| Neerlandês       | `nl`          | ✔ \*     | ✔          |  ✔ \*           |     |
| Português     | `en`          | ✔        | ✔           |  ✔ \*\*     |      |
| Finlandês     | `fi`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Francês      | `fr`          | ✔        | ✔           |  ✔ \           |     |
| Alemão      | `de`          | ✔ \*     | ✔           |  ✔ \          |     |
| Grego       | `el`          | ✔ \*     |             |            |     |
| Húngaro   | `hu`          |           |             |  ✔ \*          |     | 
| Italiano     | `it`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Japonês    | `ja`          |          | ✔           |  ✔ \*          |     |
| Coreano      | `ko`          |          | ✔           |  ✔ \*          |     |
| Norueguês (bokmål) | `no`  | ✔ \*     |  ✔          | ✔ \*            |     |
| Polaco      | `pl`          | ✔ \*     |  ✔          |  ✔ \*           |     |
| Português (Portugal) | `pt-PT`| ✔        |  ✔          | ✔ \*      |`pt`também aceito|
| Português (Brasil)   | `pt-BR`|          |  ✔   |  ✔ \*       |     |
| Russo     | `ru`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Espanhol     | `es`          | ✔        | ✔           |   ✔ \*\*      |     | 
| Sueco     | `sv`          | ✔ \*     | ✔           |   ✔ \*          |     |
| Turco     | `tr`          | ✔ \*     |             |   ✔ \*          |  |

\*O suporte ao idioma está em versão prévia

\*\*O [reconhecimento de entidade nomeada](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-ner) e a [vinculação de entidade](how-tos/text-analytics-how-to-entity-linking.md#entity-linking) estão disponíveis para esse idioma.    

## <a name="see-also"></a>Consulte também

[Página de documentação de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/)   
[Página de Produto dos Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/)
