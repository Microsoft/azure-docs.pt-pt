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
ms.openlocfilehash: adbc21c3e963a98a8482de0c26bf5e257f43013e
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675461"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Emparelhamento e alinhamento de sentença em documentos paralelos

Durante o treinamento, as frases presentes em documentos paralelos são emparelhadas ou alinhadas. O tradutor personalizado relata o número de frases que foi capaz de emparelhar como as sentenças alinhadas em cada um dos conjuntos de dados.

## <a name="pairing-and-alignment-process"></a>Processo de emparelhamento e alinhamento

O tradutor personalizado aprende traduções de sentenças uma frase por vez. Ele leituras uma frase da origem e, em seguida, a tradução desta frase do destino. Em seguida, ele alinha palavras e frases nessas duas frases entre si. Esse processo permite que ele crie um mapa das palavras e frases em uma frase para as palavras e frases equivalentes na tradução desta frase. O alinhamento tenta garantir que o sistema se treina em frases que são traduções umas das outras.

## <a name="pre-aligned-documents"></a>Documentos alinhados previamente

Se você souber que tem documentos paralelos, poderá substituir o alinhamento da frase fornecendo arquivos de texto alinhados. Você pode extrair todas as frases de ambos os documentos para o arquivo de texto, organizar uma frase por linha e carregar com uma extensão de `.align`. A extensão de `.align` sinaliza ao Tradutor personalizado que ele deve ignorar o alinhamento da frase.

Para obter melhores resultados, tente certificar-se de que você tem uma frase por linha em seus arquivos. Não têm caracteres de nova linha dentro de uma frase, pois isso causará alinhamentos inadequados.

## <a name="suggested-minimum-number-of-sentences"></a>Número mínimo sugerido de frases

Para que um treinamento tenha sucesso, a tabela a seguir mostra o número mínimo de frases necessárias em cada tipo de documento. Essa limitação é uma rede de segurança para garantir que suas frases paralelas contenham um vocabulário exclusivo suficiente para treinar com êxito um modelo de tradução. A diretriz geral é ter mais frases paralelas no domínio de qualidade de tradução humana deve produzir modelos de alta qualidade.

| Tipo de documento   | Contagem de sentenças mínimas sugeridas | Contagem máxima de sentenças |
|------------|--------------------------------------------|--------------------------------|
| Formação   | 10,000                                     | Sem limite superior                 |
| Orienta     | 5000                                      | 2\.500                          |
| Testes    | 5000                                      | 2\.500                          |
| Dicionário | 0                                          | Sem limite superior                 |

> [!NOTE]
> - O treinamento não será iniciado e falhará se a contagem mínima de sentenças 10.000 para treinamento não for atendida. 
> - O ajuste e o teste são opcionais. Se você não os fornecer, o sistema removerá uma porcentagem apropriada do treinamento a ser usado para validação e teste. 
> - Você pode treinar um modelo usando apenas dados de dicionário. Veja [o que é o dicionário](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/what-is-dictionary).

## <a name="next-steps"></a>Passos seguintes

- Saiba como usar um [dicionário](what-is-dictionary.md) no Tradutor personalizado.
