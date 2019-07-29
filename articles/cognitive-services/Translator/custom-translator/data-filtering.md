---
title: Filtragem de dados-Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Quando você envia documentos a serem usados para treinar um sistema personalizado, os documentos passam por uma série de etapas de processamento e filtragem para se preparar para o treinamento.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 1028443eaaf6c483cd7cd57289b0dcf2a9f11902
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595899"
---
# <a name="data-filtering"></a>Filtragem de dados

Quando você envia documentos a serem usados para treinar um sistema personalizado, os documentos passam por uma série de etapas de processamento e filtragem para se preparar para o treinamento. Essas etapas são explicadas aqui. O conhecimento da filtragem pode ajudá-lo a entender a contagem de sentenças exibida no Tradutor personalizado, bem como as etapas que você pode usar para preparar os documentos para treinamento com o tradutor personalizado.

## <a name="sentence-alignment"></a>Alinhamento de frases
Se o documento não estiver no formato XLIFF, TMX ou ALIGN, o tradutor personalizado alinhará as frases dos documentos de origem e de destino entre si, sentença por frase. O tradutor não realiza alinhamento de documentos – ele segue a nomenclatura dos documentos para localizar o documento correspondente do outro idioma. No documento, o tradutor personalizado tenta localizar a sentença correspondente no outro idioma. Ele usa marcação de documento como marcas HTML inseridas para ajudar com o alinhamento.  

Se você vir uma grande discrepância entre o número de sentenças nos documentos do lado de origem e de destino, o documento poderá não ter sido paralelo em primeiro lugar ou, por outros motivos, não poderá ser alinhado. Os pares de documentos com grande diferença (> 10%) de frases em cada lado garante uma segunda aparência para garantir que elas sejam realmente paralelas. O tradutor personalizado mostrará um aviso ao lado do documento se a contagem de frases diferir de forma suspeita.  


## <a name="deduplication"></a>Eliminação de duplicados
O tradutor personalizado remove as frases que estão presentes no teste e no ajuste de documentos de dados de treinamento. A remoção ocorre dinamicamente dentro da execução do treinamento, não na etapa de processamento de dados. O tradutor personalizado relata a contagem de sentenças para você na visão geral do projeto antes dessa remoção.  

## <a name="length-filter"></a>Filtro de comprimento
* Remova as frases com apenas uma palavra de cada lado.
* Remova frases com mais de 100 palavras em ambos os lados.  Chinês, japonês, coreano são isentos.
* Remova frases com menos de 3 caracteres. Chinês, japonês, coreano são isentos.
* Remova frases com mais de 2000 caracteres para chinês, japonês, coreano.
* Remova frases com menos de 1% de caracteres alfa.
* Remova as entradas de dicionário que contêm mais de 50 palavras.

## <a name="white-space"></a>Espaço em branco
* Substitua qualquer sequência de caracteres de espaço em branco, incluindo as seqüências de tabulações e CR/LF por um único caractere de espaço.
* Remover o espaço à esquerda ou à direita na sentença

## <a name="sentence-end-punctuation"></a>Pontuação final da frase
Substitua vários caracteres de Pontuação de final da frase por uma única instância.  

## <a name="japanese-character-normalization"></a>Normalização de caractere japonês
Converta letras de largura inteira e dígitos em caracteres de meia largura.

## <a name="unescaped-xml-tags"></a>Marcas XML sem escape
A filtragem transforma marcas sem escape em marcas de escape:
* `&lt;`ficará`&amp;lt;`
* `&gt;`ficará`&amp;gt;`
* `&amp;`ficará`&amp;amp;`

## <a name="invalid-characters"></a>Carateres inválidos
O tradutor personalizado remove frases que contêm o caractere Unicode U + FFFD. O caractere U + FFFD indica uma conversão de codificação com falha.

## <a name="next-steps"></a>Passos Seguintes

- [Treinar um modelo](how-to-train-model.md) no Tradutor personalizado.
