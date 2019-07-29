---
title: Emparelhamento e alinhamento de sentença-Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Durante a execução do treinamento, as frases presentes em documentos paralelos são emparelhadas ou alinhadas. O tradutor personalizado aprende uma frase por vez, lendo uma frase, a tradução desta sentença. Em seguida, ele alinha palavras e frases nessas duas frases entre si.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: e9bc5c876da6bd2be1b22b389b819e51330b2e50
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595471"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Emparelhamento e alinhamento de sentença em documentos paralelos

Durante o treinamento, as frases presentes em documentos paralelos são emparelhadas ou alinhadas. O tradutor personalizado relata o número de frases que foi capaz de emparelhar como as sentenças alinhadas em cada um dos conjuntos de dados.

## <a name="pairing-and-alignment-process"></a>Processo de emparelhamento e alinhamento

O tradutor personalizado aprende traduções de sentenças uma frase por vez. Ele leituras uma frase da origem e, em seguida, a tradução desta frase do destino. Em seguida, ele alinha palavras e frases nessas duas frases entre si. Esse processo permite que ele crie um mapa das palavras e frases em uma frase para as palavras e frases equivalentes na tradução desta frase. O alinhamento tenta garantir que o sistema se treina em frases que são traduções umas das outras.

## <a name="pre-aligned-documents"></a>Documentos alinhados previamente

Se você souber que tem documentos paralelos, poderá substituir o alinhamento da frase fornecendo arquivos de texto alinhados. Você pode extrair todas as frases de ambos os documentos para o arquivo de texto, organizar uma frase por linha e `.align` carregar com uma extensão. A `.align` extensão sinaliza o tradutor personalizado de que ele deve ignorar o alinhamento da frase.

Para obter melhores resultados, tente certificar-se de que você tem uma frase por linha em seus arquivos. Não têm caracteres de nova linha dentro de uma frase, pois isso causará alinhamentos inadequados.

## <a name="suggested-minimum-number-of-extracted-and-aligned-sentences"></a>Número mínimo sugerido de sentenças extraídas e alinhadas

Para que um treinamento seja bem sucedido, a tabela a seguir mostra o número mínimo de frases extraídas e frases alinhadas necessárias em cada conjunto de dados. O número mínimo sugerido de sentenças extraídas é muito maior do que o número mínimo sugerido de frases alinhadas para levar em conta o fato de que o alinhamento de frase pode não ser capaz de alinhar todas as frases extraídas com êxito.

| Conjunto de dados   | Contagem de frases mínimas extraídas sugeridas | Contagem de sentenças com alinhamento mínimo sugerida | Contagem máxima de frases alinhadas |
|------------|--------------------------------------------|------------------------------------------|--------------------------------|
| Formação   | 10,000                                     | 2\.000                                    | Sem limite superior                 |
| Orienta     | 2\.000                                      | 500                                      | 2,500                          |
| Testes    | 2\.000                                      | 500                                      | 2,500                          |
| Dicionário | 0                                          | 0                                        | Sem limite superior                 |

## <a name="next-steps"></a>Passos Seguintes

- Saiba como usar um [dicionário](what-is-dictionary.md) no Tradutor personalizado.
