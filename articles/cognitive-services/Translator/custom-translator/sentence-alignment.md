---
title: Emparelhamento e alinhamento de frases - Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Durante a execução do treino, as frases presentes em documentos paralelos são emparelhadas ou alinhadas. Tradutor personalizado aprende traduções uma frase de cada vez, lendo uma frase, a tradução desta frase. Depois alinha palavras e frases nestas duas frases uma sem a outra.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: c8c4bbcfdd8f06d4c2b4759b84a72c5b3cff5a5d
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996351"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Emparelhamento de frases e alinhamento em documentos paralelos

Durante o treino, as frases presentes em documentos paralelos são emparelhadas ou alinhadas. O Tradutor Personalizado relata o número de frases que foi capaz de emparelhar como as Frases Alinhadas em cada um dos conjuntos de dados.

## <a name="pairing-and-alignment-process"></a>Processo de emparelhamento e alinhamento

Tradutor personalizado aprende traduções de frases uma frase de cada vez. Lê uma frase da fonte, e depois a tradução desta frase do alvo. Depois alinha palavras e frases nestas duas frases uma sem a outra. Este processo permite-lhe criar um mapa das palavras e frases numa frase às palavras e frases equivalentes na tradução desta frase. O alinhamento tenta garantir que o sistema treina em frases que são traduções umas das outras.

## <a name="pre-aligned-documents"></a>Documentos pré-alinhados

Se souber que tem documentos paralelos, pode anular o alinhamento da frase fornecendo ficheiros de texto pré-alinhados. Pode extrair todas as frases de ambos os documentos em ficheiros de texto, organizar uma frase por linha e fazer upload com uma `.align` extensão. A extensão indica tradutor `.align` personalizado que deve ignorar o alinhamento da frase.

Para obter os melhores resultados, tente certificar-se de que tem uma frase por linha nos seus ficheiros.Não tenho caracteres de linha nova dentro de uma frase, pois isto causará maus alinhamentos.

## <a name="suggested-minimum-number-of-sentences"></a>Número mínimo sugerido de frases

Para que um treino tenha sucesso, a tabela abaixo mostra o número mínimo de frases exigidas em cada tipo de documento.Esta limitação é uma rede de segurança para garantir que as suas frases paralelas contenham vocabulário único suficiente para treinar com sucesso um modelo de tradução. A orientação geral é ter mais frases paralelas em domínio da qualidade da tradução humana devem produzir modelos de maior qualidade.

| Tipo de documento   | Contagem de penas mínimas sugerida | Contagem máxima de penas |
|------------|--------------------------------------------|--------------------------------|
| Formação   | 10,000                                     | Sem limite superior                 |
| Afinação     | 500                                      | 2.500       |
| Testar    | 500                                      | 2.500  |
| Dicionário | 0                                          | Sem limite superior                 |

> [!NOTE]
> - A formação não começará e falhará se a contagem mínima de 10.000 penas para a formação não for cumprida. 
> - A sintonização e os testes são opcionais. Se não os fornecer, o sistema removerá uma percentagem adequada do Treino para utilizar para validação e teste. 
> - Pode treinar um modelo usando apenas dados do dicionário. Por favor, consulte [o What is Dictionary](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/what-is-dictionary).

## <a name="next-steps"></a>Próximos passos

- Aprenda a usar um [dicionário](what-is-dictionary.md) em Tradutor Personalizado.
