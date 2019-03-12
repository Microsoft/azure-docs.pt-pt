---
title: Frase de emparelhamento e alinhamento - Translator personalizado
titleSuffix: Azure Cognitive Services
description: Durante a execução de treinamento, frases presentes nos documentos paralelos são emparelhadas ou alinhadas. Tradutor personalizado aprende frase traduções por vez, ao ler uma sentença, a tradução desta frase. Em seguida, alinha palavras e expressões nessas duas frases entre si.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 8d2933f6a3bbab792acb708f2a59cad4eb2cabf7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777163"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Emparelhamento de sentença e alinhamento em documentos paralelos

Durante o treinamento, frases presentes nos documentos paralelos são emparelhadas ou alinhadas. Tradutor personalizado relata o número de frases conseguiu emparelhar como frases alinhado em cada um dos conjuntos de dados.

## <a name="pairing-and-alignment-process"></a>Processo de emparelhamento e alinhamento

Tradutor personalizado aprende traduções de frase frases cada vez. Ele leituras uma frase de origem e, em seguida, a tradução desta frase de destino. Em seguida, alinha palavras e expressões nessas duas frases entre si. Este processo permite-lo criar um mapa das palavras e frases numa frase para o equivalentes palavras e frases na tradução desta frase. Alinhamento tenta Certifique-se de que o sistema prepara em frases que são as traduções umas das outras.

## <a name="pre-aligned-documents"></a>Pré-alinhados documentos

Se souber que tem documentos paralelos, pode substituir o alinhamento de sentença, fornecendo os arquivos de texto previamente alinhados. Pode extrair todas as frases de ambos os documentos no ficheiro de texto, frase organizado por linha e o carregamento com um `.align` extensão. O `.align` extensão sinaliza Translator personalizada que ele deve ignorar o alinhamento da sentença.

Para obter melhores resultados, tente certificar-se de que tem uma frase por linha nos seus ficheiros. Não tem os carateres de nova linha numa frase como isso fará com que alinhamentos fraco.

## <a name="suggested-minimum-number-of-extracted-and-aligned-sentences"></a>Número mínimo de frases extraídos e alinhados de sugerida

Para um treinamento tenha êxito, a tabela abaixo mostra o número mínimo de frases extraídas e alinhadas frases necessárias em cada conjunto de dados. O número mínimo sugerido de frases extraídos é muito maior do que o número mínimo sugerido de frases alinhados levar em conta o fato de que o alinhamento de sentença talvez não consiga alinhar com êxito a todas as frases extraídas.

| Conjunto de dados   | Sugestões de contagem de sentença extraídos mínimo | Sugestões de contagem de sentença alinhados mínimo | Contagem de sentença alinhados máximo |
|------------|--------------------------------------------|------------------------------------------|--------------------------------|
| Formação   | 10,000                                     | 2.000                                    | Sem limite superior                 |
| Ajuste     | 2.000                                      | 500                                      | 2,500                          |
| Testes    | 2.000                                      | 500                                      | 2,500                          |
| Dicionário | 0                                          | 0                                        | Sem limite superior                 |

## <a name="next-steps"></a>Passos Seguintes

- Saiba como utilizar um [dicionário](what-is-dictionary.md) no Translator personalizado.
