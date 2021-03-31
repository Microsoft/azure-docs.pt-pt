---
title: Emparelhamento de frases e alinhamento - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Durante a execução do treino, as frases presentes em documentos paralelos são emparelhadas ou alinhadas. O Tradutor Personalizado aprende a traduzir uma frase de cada vez, lendo uma frase, a tradução desta frase. Depois alinha palavras e frases nestas duas frases uma à outra.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 0c33d766bfd3dff47ddb151e8ce4ea7b25c37548
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98897956"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Emparelhamento de frases e alinhamento em documentos paralelos

Após o upload dos documentos, as frases presentes em documentos paralelos são emparelhadas ou alinhadas. O Tradutor Personalizado relata o número de frases que foi capaz de emparelhar como as Frases Alinhadas em cada um dos conjuntos de dados.

## <a name="pairing-and-alignment-process"></a>Processo de emparelhamento e alinhamento

O Tradutor Personalizado aprende traduções de frases uma frase de cada vez. Lê uma frase do texto de origem e, em seguida, a tradução desta frase a partir do texto-alvo. Depois alinha palavras e frases nestas duas frases uma à outra. Este processo permite-lhe criar um mapa das palavras e frases numa frase às palavras e frases equivalentes na tradução da sua frase. O alinhamento tenta garantir que o sistema treina em frases que são traduções uns dos outros.

## <a name="pre-aligned-documents"></a>Documentos pré-alinhados

Se souber que tem documentos paralelos, pode anular o alinhamento da frase fornecendo ficheiros de texto pré-alinhados. Pode extrair todas as frases de ambos os documentos em ficheiro de texto, organizar uma frase por linha e fazer upload com uma `.align` extensão. A `.align` extensão indica que o Tradutor Personalizado deve saltar o alinhamento da frase.

Para obter os melhores resultados, tente certificar-se de que tem uma frase por linha nos seus ficheiros. Não tenha caracteres novos dentro de uma frase, pois isso causará maus alinhamentos.

## <a name="suggested-minimum-number-of-sentences"></a>Número mínimo de frases sugerido

Para que uma formação tenha sucesso, a tabela abaixo mostra o número mínimo de frases exigidas em cada tipo de documento.Esta limitação é uma rede de segurança para garantir que as suas frases paralelas contenham vocabulário único suficiente para treinar com sucesso um modelo de tradução. A orientação geral é que as frases paralelas mais em domínio da qualidade da tradução humana devem produzir modelos de maior qualidade.

| Tipo de documento   | Contagem mínima sugerida | Contagem máxima da sentença |
|------------|--------------------------------------------|--------------------------------|
| Formação   | 10,000                                     | Sem limite superior                 |
| Ajuste     | 500                                      | 2.500       |
| Testar    | 500                                      | 2.500  |
| Dicionário | 0                                          | Sem limite superior                 |

> [!NOTE]
> - A formação não começará e falhará se a contagem mínima de 10.000 para a formação não for cumprida. 
> - Sintonização e Testes são opcionais. Se não os fornecer, o sistema removerá uma percentagem adequada do Treino para utilizar para validação e teste. 
> - Pode treinar um modelo usando apenas dados de dicionário. Consulte o [Que é Dicionário.](./what-is-dictionary.md)

## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar um [dicionário](what-is-dictionary.md) em Tradutor Personalizado.
