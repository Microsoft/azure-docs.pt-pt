---
title: Design para linguagem - QnA Maker
description: O recurso QnA Maker e todas as bases de conhecimento dentro desse recurso suportam uma única língua. A única língua é necessária para fornecer os melhores resultados de resposta para uma consulta.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 5cb1dcd35649debbafd2e234606ad4c9d6906ea6
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843425"
---
# <a name="design-knowledge-base-for-content-language"></a>Base de conhecimento de design para linguagem de conteúdo

O recurso QnA Maker, e todas as bases de conhecimento dentro desse recurso, suportam uma única língua. A única língua é necessária para fornecer os melhores resultados de resposta para uma consulta.

## <a name="single-language-per-resource"></a>Língua única por recurso

QnA Maker considerações para apoio linguístico:

* Um serviço QnA Maker, e todas as suas bases de conhecimento, suportam apenas uma língua.
* A linguagem é explicitamente definida quando a primeira base de conhecimento do serviço é criada
* A linguagem é determinada a partir dos ficheiros e URLs adicionados quando a base de conhecimento é criada
* A linguagem não pode ser alterada para quaisquer outras bases de conhecimento no serviço
* O idioma é usado pelo serviço de Pesquisa Cognitiva (ranker #1) e pelo serviço QnA Maker (ranker #2) para gerar a melhor resposta para uma consulta

## <a name="supporting-multiple-languages"></a>Apoiar várias línguas

Se precisar de apoiar um sistema de base de conhecimento, que inclui várias línguas, pode escolher um dos seguintes métodos:

* Utilize o serviço de texto de [tradução](../../translator/translator-info-overview.md) para traduzir uma questão numa única língua antes de enviar a questão para a sua base de conhecimentos. Isto permite-lhe focar-se na qualidade de uma única língua e na qualidade das perguntas e respostas alternativas.
* Crie um recurso QnA Maker, e uma base de conhecimento dentro desse recurso, para cada idioma. Isto permite-lhe gerir perguntas alternativas separadas e responder a textos mais nuances para cada idioma. Isto dá-lhe muito mais flexibilidade, mas requer um custo de manutenção muito mais elevado quando as perguntas ou respostas mudam em todas as línguas.

Rever [idiomas suportados](../overview/language-support.md) para o QnA Maker.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Apoie cada idioma com um recurso QnA Maker

* Crie um recurso QnA Maker para cada idioma
* Apenas adicione ficheiros e URLs para esse idioma
* Use uma convenção de nomeação para o recurso para identificar a língua. Um exemplo é `qna-maker-fr` para todas as bases de conhecimento para documentos franceses

## <a name="next-steps"></a>Passos seguintes

Aprenda [conceitos](query-knowledge-base.md) sobre como consultar a base de conhecimento para obter uma resposta.