---
title: Design para linguagem - QnA Maker
description: O recurso QnA Maker e todas as bases de conhecimento dentro desse recurso suportam uma única língua. A única língua é necessária para fornecer os melhores resultados de resposta para uma consulta.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: e09e15f71b0574a5612e7f9bacd8aaa4739a441c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777008"
---
# <a name="design-knowledge-base-for-content-language"></a>Desenhar base de conhecimento para linguagem de conteúdo

O recurso QnA Maker, e todas as bases de conhecimento dentro desse recurso, suportam uma única língua. A única língua é necessária para fornecer os melhores resultados de resposta para uma consulta.

## <a name="single-language-per-resource"></a>Língua única por recurso

Considerações do Fabricante QNA para o suporte linguístico:

* Um serviço QnA Maker, e todas as suas bases de conhecimento, suportam apenas uma língua.
* A linguagem é explicitamente definida quando a primeira base de conhecimento do serviço é criada
* A linguagem é determinada a partir dos ficheiros e URLs adicionados quando a base de conhecimento é criada
* A linguagem não pode ser alterada para qualquer outra base de conhecimento no serviço
* O idioma é usado pelo serviço de Pesquisa Cognitiva (ranker #1) e pelo serviço QnA Maker (ranker #2) para gerar a melhor resposta a uma consulta

## <a name="supporting-multiple-languages"></a>Apoio a várias línguas

Se precisar de suportar um sistema de base de conhecimento, que inclui várias línguas, pode escolher um dos seguintes métodos:

* Utilize o [serviço de Texto de Tradução](../../translator/translator-info-overview.md) para traduzir uma pergunta numa única língua antes de enviar a pergunta para a sua base de conhecimentos. Isto permite-lhe focar-se na qualidade de uma única língua e na qualidade das perguntas e respostas alternativas.
* Crie um recurso QnA Maker, e uma base de conhecimento dentro desse recurso, para cada idioma. Isto permite-lhe gerir perguntas alternativas separadas e responder a textos mais matizados para cada idioma. Isto dá-lhe muito mais flexibilidade, mas requer um custo de manutenção muito mais elevado quando as perguntas ou respostas mudam em todas as línguas.

Rever [línguas suportadas](../overview/language-support.md) para o QnA Maker.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Apoie cada idioma com um recurso QnA Maker

* Crie um recurso QnA Maker para cada idioma
* Adicione apenas ficheiros e URLs para esse idioma
* Use uma convenção de nomeação para o recurso para identificar o idioma. Um exemplo é `qna-maker-fr` para todas as bases de conhecimento para documentos franceses

## <a name="next-steps"></a>Passos seguintes

Aprenda [conceitos](query-knowledge-base.md) sobre como consultar a base de conhecimento para obter uma resposta.