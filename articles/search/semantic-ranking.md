---
title: Ranking semântico
titleSuffix: Azure Cognitive Search
description: Descreve o algoritmo de classificação semântica na Pesquisa Cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: a008551ac6f149617feedd01e256b637f83e975d
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103235062"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Ranking semântico em Pesquisa Cognitiva Azure

> [!IMPORTANT]
> As funcionalidades de pesquisa semântica estão em pré-visualização pública, disponíveis apenas através da pré-visualização REST API. As funcionalidades de pré-visualização são oferecidas como está, nos [Termos Complementares de Utilização,](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)e não estão garantidas a mesma implementação em disponibilidade geral. Para mais informações, consulte [Disponibilidade e preços.](semantic-search-overview.md#availability-and-pricing)

O ranking semântico é uma extensão do pipeline de execução de consultas que melhora a precisão e a recuperação, relembr o topo de um conjunto de resultados iniciais. O ranking semântico é apoiado por modelos de compreensão de leitura de máquinas profundas de última geração, treinados para consultas expressas em linguagem natural em oposição à correspondência linguística em palavras-chave. Em contraste com o [algoritmo de classificação de semelhança padrão,](index-ranking-similarity.md)o ranker semântico usa o contexto e significado das palavras para determinar a relevância.

## <a name="how-semantic-ranking-works"></a>Como funciona o ranking semântico

O ranking semântico é tanto de recursos como de tempo intensivo. Para completar o processamento dentro da latência esperada de uma operação de consulta, o modelo toma como entrada apenas os 50 documentos mais altos devolvidos do algoritmo de [classificação](index-ranking-similarity.md)de semelhança padrão . Os resultados do ranking inicial podem incluir mais de 50 partidas, mas apenas os primeiros 50 serão reclassily. 

Para o ranking semântico, o modelo utiliza a compreensão da leitura da máquina e a aprendizagem de transferência para re-pontuar os documentos com base no quão bem cada um corresponde à intenção da consulta.

1. Para cada documento, o ranker semântico avalia os campos no parâmetro searchFields por ordem, consolidando o conteúdo numa grande cadeia.

1. A corda é então aparada para garantir que o comprimento total não seja superior a 20.000 fichas. Se tiver documentos muito grandes, com um campo de conteúdos ou merged_content que tenha muitas páginas de conteúdo, apenas serão utilizados os primeiros 20.000 tokens.

1. Cada um dos 50 documentos é agora representado por uma única longa corda que é até 20.000 fichas. Esta cadeia é enviada para o modelo de resumo. O modelo de resumo produz legendas (e respostas), utilizando a compreensão da leitura da máquina para identificar passagens que parecem resumir o conteúdo ou responder à pergunta. A saída do modelo de resumo é uma corda mais reduzida, que será no máximo 128 fichas.

1. A corda mais pequena torna-se a legenda do documento, e representa as passagens mais relevantes encontradas na corda maior. O conjunto de 50 (ou menos) legendas é então classificado por ordem de relevância. 

A relevância conceptual e semântica é estabelecida através da representação vetorial e dos clusters de termo. Enquanto um algoritmo de semelhança de palavras-chave pode dar igual peso a qualquer termo na consulta, o modelo semântico foi treinado para reconhecer a interdependência e as relações entre palavras que de outra forma não estão relacionadas na superfície. Como resultado, se uma cadeia de consulta inclui termos do mesmo cluster, um documento contendo ambos será classificado acima de um que não.

:::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Representação vetorial para o contexto" border="true":::

## <a name="next-steps"></a>Passos seguintes

O ranking semântico é oferecido nos níveis Standard, em regiões específicas. Para mais informações e para se inscrever, consulte [Disponibilidade e preços.](semantic-search-overview.md#availability-and-pricing)

Um novo tipo de consulta permite o ranking de relevância e as estruturas de resposta da pesquisa semântica. [Crie uma consulta semântica](semantic-how-to-query-request.md) para começar.

Em alternativa, reveja qualquer um dos seguintes artigos para obter informações relacionadas.

+ [Adicionar verificação ortográfica aos termos de consulta](speller-how-to-add.md)
+ [Devolva uma resposta semântica](semantic-answers.md)