---
title: Suporte linguístico - QnA Maker
titleSuffix: Azure Cognitive Services
description: Uma lista de cultura, línguas naturais apoiadas pela QnA Maker para a sua base de conhecimento. Não misture línguas na mesma base de conhecimento.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2019
ms.openlocfilehash: 1edd5ffc2578a27a53c7e9a46a4a5f1cf61331ff
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605064"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>Suporte linguístico para um recurso e bases de conhecimento do QnA Maker

Este artigo descreve as opções de suporte linguístico para os recursos e bases de conhecimento do QnA Maker. 

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

O idioma para o serviço é selecionado quando cria a primeira base de conhecimento no recurso. Todas as bases de conhecimento adicionais no recurso devem estar na mesma língua. 

O idioma determina a relevância dos resultados que o QnA Maker fornece em resposta às consultas do utilizador. O recurso QnA Maker, e todas as bases de conhecimento dentro desse recurso, suportam uma única língua. A única língua é necessária para fornecer os melhores resultados de resposta para uma consulta.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

Na gestão do QnA Maker, você tem a escolha de fazer configurações de linguagem ao nível de base de conhecimento individual. Esta definição só pode ser ativada com a base de conhecimento do serviço. Uma vez definidos, as definições de idioma não podem ser alteradas para o serviço. 

Se selecionar definições de linguagem para ser específico da base de conhecimento, então é-lhe permitido criar bases de conhecimento de diferentes línguas no próprio serviço. 

---

## <a name="single-language-per-resource"></a>Língua única por recurso

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

Considere o seguinte:

* Um serviço QnA Maker, e todas as suas bases de conhecimento, suportam apenas uma língua.
* A linguagem é explicitamente definida quando a primeira base de conhecimento do serviço é criada.
* A linguagem é determinada a partir dos ficheiros e URLs adicionados quando a base de conhecimento é criada.
* A linguagem não pode ser alterada para qualquer outra base de conhecimento no serviço.
* O idioma é usado pelo serviço de Pesquisa Cognitiva (ranker #1) e pelo serviço QnA Maker (ranker #2) para gerar a melhor resposta a uma consulta.

# <a name="qnamaker-managed-preview"></a>[QnAMaker gerido (Pré-visualização)](#tab/v2)
![Definição de linguagem no Fabricante QnA gerido](../media/language-support/language-setting-managed.png)

Se **não selecionar a caixa de verificação para ativar a definição de idioma por base de conhecimento,** considere o seguinte: 
* Um serviço QnA Maker, e todas as suas bases de conhecimento, suportará apenas uma língua.
* A linguagem é explicitamente definida quando a primeira base de conhecimento do serviço é criada
* A linguagem é determinada a partir dos ficheiros e URLs adicionados quando a base de conhecimento é criada
* A linguagem não pode ser alterada para qualquer outra base de conhecimento no serviço
* O idioma é usado pelo serviço de Pesquisa Cognitiva (ranker #1) e pelo serviço QnA Maker (ranker #2) para gerar a melhor resposta a uma consulta

---

## <a name="supporting-multiple-languages-in-one-qna-maker-resource"></a>Suporte a várias línguas num recurso do QnA Maker

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)
Esta funcionalidade não é suportada na nossa versão estável geralmente disponível (GA). Confira a QnA Maker conseguiu testar esta funcionalidade. 

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)
* Quando está a criar a primeira base de conhecimento no seu serviço, tem a opção de ativar a definição de idioma por base de conhecimento. Selecione a caixa de verificação, para criar bases de conhecimento pertencentes a diferentes idiomas dentro de um serviço.
* A opção de definição de idioma não pode ser modificada para o serviço, uma vez criada a primeira base de conhecimento.
* Se ativar definições de linguagem específicas de cada base de conhecimento, em vez de ter um índice de teste para o serviço, terá um índice de teste por base de conhecimento. 

![Definição de linguagem no Fabricante QnA gerido](../media/language-support/language-setting-managed.png)

---

## <a name="supporting-multiple-languages-in-one-knowledge-base"></a>Apoiar várias línguas numa base de conhecimento

Se precisar de suportar um sistema de base de conhecimento, que inclui vários idiomas, pode:

* Utilize o [serviço de Tradução](../../translator/translator-info-overview.md) para traduzir uma pergunta numa única língua antes de enviar a pergunta para a sua base de conhecimentos. Isto permite-lhe focar-se na qualidade de uma única língua e na qualidade das perguntas e respostas alternativas.
* Crie um recurso QnA Maker, e uma base de conhecimento dentro desse recurso, para cada idioma. Isto permite-lhe gerir perguntas alternativas separadas e responder a textos mais matizados para cada idioma. Isto dá-lhe muito mais flexibilidade, mas requer um custo de manutenção muito mais elevado quando as perguntas ou respostas mudam em todas as línguas.


## <a name="languages-supported"></a>Idiomas suportados

A lista a seguir contém os idiomas suportados para um recurso QnA Maker. 

| Linguagem |
|--|
| Árabe |
| Arménio |
| Bangla |
| Basco |
| Búlgaro |
| Catalão |
| Chinese_Simplified |
| Chinese_Traditional |
| Croata |
| Checo |
| Dinamarquês |
| Neerlandês |
| Inglês |
| Estónio |
| Finlandês |
| Francês |
| Galego |
| Alemão |
| Grego |
| Guzerate |
| Hebraico |
| Hindi |
| Húngaro |
| Islandês |
| Indonésio |
| Irlandês |
| Italiano |
| Japonês |
| Canarês |
| Coreano |
| Letão |
| Lituano |
| Malaiala |
| Malaio |
| Norueguês |
| Polaco |
| Português |
| Punjabi |
| Romeno |
| Russo |
| Serbian_Cyrillic |
| Serbian_Latin |
| Eslovaco |
| Esloveno |
| Espanhol |
| Sueco |
| Tâmil |
| Telugu |
| Tailandês |
| Turco |
| Ucraniano |
| Urdu |
| Vietnamita |

## <a name="query-matching-and-relevance"></a>Combinação de consulta e relevância
O QnA Maker depende dos [analisadores de linguagem Azure Cognitive Search](/rest/api/searchservice/language-support) para fornecer resultados.

Enquanto as capacidades de Pesquisa Cognitiva Azure estão a par para línguas suportadas, o QnA Maker tem um ranker adicional que se situa acima dos resultados de pesquisa do Azure. Neste modelo ranker, usamos algumas características semânticas e baseadas em palavras nas seguintes línguas.

|Línguas com ranker adicional|
|--|
|Chinês|
|Checo|
|Neerlandês|
|Inglês|
|Francês|
|Alemão|
|Húngaro|
|Italiano|
|Japonês|
|Coreano|
|Polaco|
|Português|
|Espanhol|
|Sueco|

Este ranking adicional é um trabalho interno do ranker do QnA Maker.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Seleção de idioma](../index.yml)