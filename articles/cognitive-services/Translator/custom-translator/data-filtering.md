---
title: Filtragem de dados - Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Quando submete documentos para serem utilizados para a formação de um sistema personalizado, os documentos passam por uma série de etapas de processamento e filtragem para se prepararem para a formação.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 1076a4fe3a460fa07e061e9ec0ec41b088ec7eca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88507268"
---
# <a name="data-filtering"></a>Filtragem de dados

Quando submete documentos para serem utilizados para a formação de um sistema personalizado, os documentos passam por uma série de etapas de processamento e filtragem para se prepararem para a formação. Estes passos são explicados aqui. O conhecimento da filtragem pode ajudá-lo a compreender a contagem de frases exibida no Tradutor Personalizado, bem como os passos que pode tomar para preparar os documentos para a formação com o Tradutor Personalizado.

## <a name="sentence-alignment"></a>Alinhamento de frases
Se o seu documento não estiver no formato XLIFF, TMX ou ALIGN, o Custom Tradutor alinha as frases da sua fonte e os documentos-alvo entre si, frase a frase. O Custom Tradutor não realiza alinhamento de documentos – segue o seu nome dos documentos para encontrar o documento correspondente do outro idioma. Dentro do documento, o Tradutor Personalizado tenta encontrar a frase correspondente na outra língua. Utiliza a marcação de documentos como tags HTML incorporadas para ajudar no alinhamento.  

Se vir uma grande discrepância entre o número de frases na fonte e os documentos laterais alvo, o seu documento pode não ter sido paralelo em primeiro lugar, ou por outras razões não poderia estar alinhado. O documento emparelha com uma grande diferença (>10%) de frases de cada lado garantem um segundo olhar para ter certeza de que são realmente paralelos. O Tradutor Personalizado mostra um aviso ao lado do documento se a contagem da frase diferir de forma suspeita.  


## <a name="deduplication"></a>Eliminação de duplicados
O Tradutor Personalizado remove as frases presentes no teste e a afinação de documentos dos dados de formação.A remoção ocorre dinamicamente dentro do treino, não na etapa de processamento de dados. O Tradutor Personalizado informa a contagem da sentença para si na visão geral do projeto antes de tal remoção.  

## <a name="length-filter"></a>Filtro de comprimento
* Retire as frases com apenas uma palavra de cada lado.
* Retire as frases com mais de 100 palavras de cada lado.Chineses, japoneses, coreanos estão isentos.
* Remova as frases com menos de 3 caracteres. Chineses, japoneses, coreanos estão isentos.
* Remova as frases com mais de 2000 caracteres para chineses, japoneses, coreanos.
* Remova as frases com caracteres alfa inferiores a 1%.
* Remova as entradas do dicionário que contenham mais de 50 palavras.

## <a name="white-space"></a>Espaço em branco
* Substitua qualquer sequência de caracteres em espaço branco, incluindo separadores e sequências de CR/LF por um único personagem espacial.
* Remova o espaço de liderança ou de fuga na frase

## <a name="sentence-end-punctuation"></a>Pontuação final da frase
Substitua os caracteres de pontuação final de várias frases por uma única instância.  

## <a name="japanese-character-normalization"></a>Normalização do caráter japonês
Converter letras e dígitos de largura total para caracteres de meia largura.

## <a name="unescaped-xml-tags"></a>Etiquetas XML nãoscapedas
Filtragem transforma tags nãoscaped em etiquetas escapadas:
* `&lt;` torna-se `&amp;lt;`
* `&gt;` torna-se `&amp;gt;`
* `&amp;` torna-se `&amp;amp;`

## <a name="invalid-characters"></a>Caracteres inválidos
O Custom Tradutor remove as frases que contêm o personagem Unicode U+FFFD. O personagem U+FFFD indica uma conversão de codificação falhada.

## <a name="next-steps"></a>Passos seguintes

- [Treine um modelo](how-to-train-model.md) em Tradutor Personalizado.
