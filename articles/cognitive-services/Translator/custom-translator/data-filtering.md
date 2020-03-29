---
title: Filtragem de dados - Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Quando submete documentos para treinar um sistema personalizado, os documentos passam por uma série de passos de processamento e filtragem para se prepararem para o treino.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 1028443eaaf6c483cd7cd57289b0dcf2a9f11902
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595899"
---
# <a name="data-filtering"></a>Filtragem de dados

Quando submete documentos para treinar um sistema personalizado, os documentos passam por uma série de passos de processamento e filtragem para se prepararem para o treino. Estes passos são explicados aqui. O conhecimento da filtragem pode ajudá-lo a compreender a contagem de frases exibida em tradutor personalizado, bem como os passos que pode tomar para preparar os documentos para treino com tradutor personalizado.

## <a name="sentence-alignment"></a>Alinhamento de frases
Se o seu documento não estiver no formato XLIFF, TMX ou ALIGN, o Custom Tradutor alinha as frases da sua fonte e os documentos-alvo entre si, sentença a frase. Tradutor não executa o alinhamento documental – segue o nome dos documentos para encontrar o documento correspondente da outra língua. Dentro do documento, o Tradutor Personalizado tenta encontrar a frase correspondente na outra língua. Utiliza marcação de documento como tags HTML incorporadas para ajudar no alinhamento.  

Se vir uma grande discrepância entre o número de frases na fonte e os documentos laterais do alvo, o seu documento pode não ter sido paralelo em primeiro lugar, ou por outras razões não poderia estar alinhado. O documento combina com uma grande diferença (>10%) de frases de cada lado justificam um segundo olhar para ter certeza de que são mesmo paralelos. Tradutor personalizado mostra um aviso ao lado do documento se a contagem de frases difere de forma suspeita.  


## <a name="deduplication"></a>Eliminação de duplicados
Tradutor Personalizado remove as frases que estão presentes em documentos de teste e afinação de dados de formação.A remoção acontece dinamicamente dentro do treino, não na etapa de processamento de dados. O Tradutor Personalizado informa a contagem de frases para si na visão geral do projeto antes de tal remoção.  

## <a name="length-filter"></a>Filtro de comprimento
* Remova as frases com apenas uma palavra de cada lado.
* Remova frases com mais de 100 palavras de cada lado.Chineses, japoneses, coreanos estão isentos.
* Remova as frases com menos de 3 caracteres. Chineses, japoneses, coreanos estão isentos.
* Remova frases com mais de 2000 caracteres para chineses, japoneses, coreanos.
* Remova frases com caracteres alfa inferiores a 1%.
* Remova as entradas do dicionário contendo mais de 50 palavras.

## <a name="white-space"></a>Espaço branco
* Substitua qualquer sequência de caracteres de espaço branco, incluindo separadores e sequências de CR/LF por um único personagem espacial.
* Remova o espaço de liderança ou de rasto na frase

## <a name="sentence-end-punctuation"></a>Pontuação final da sentença
Substitua os caracteres de pontuação final de frase múltiplas por uma única instância.  

## <a name="japanese-character-normalization"></a>Normalização de caracteres japoneses
Converta letras e dígitos de largura total em caracteres de meia largura.

## <a name="unescaped-xml-tags"></a>Tags XML não escapadas
A filtragem transforma etiquetas não escapadas em etiquetas escapadas:
* `&lt;`torna-se`&amp;lt;`
* `&gt;`torna-se`&amp;gt;`
* `&amp;`torna-se`&amp;amp;`

## <a name="invalid-characters"></a>Caracteres inválidos
Tradutor personalizado remove frases que contêm o caracteres Unicode U+FFFD. O personagem U+FFFD indica uma conversão de codificação falhada.

## <a name="next-steps"></a>Passos seguintes

- [Treine um modelo](how-to-train-model.md) em Tradutor Personalizado.
