---
title: Terminologia - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Lista dos termos utilizados nos artigos do Tradutor Personalizado.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: reference
ms.openlocfilehash: 4461f584e365a5d47e7ceee942e33bc8b101b2d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104657830"
---
# <a name="custom-translator-terminology"></a>Terminologia do Tradutor Personalizado

A tabela que se segue apresenta uma lista de termos que poderá encontrar enquanto trabalha com o [Tradutor Personalizado.](https://portal.customtranslator.azure.ai)

| Palavra ou frase|Definição|
|------------------|-----------|
| Língua de origem | A língua de origem é a língua inicial que pretende converter para outra língua (o "alvo").|
| Língua-alvo| A língua-alvo é a língua que pretende que a tradução automática forneça depois de receber a língua de origem. |
| Arquivo Monolingual | Um ficheiro monolingual tem uma única língua não emparelhada com outro ficheiro de uma língua diferente. |
| Arquivos Paralelos | Um ficheiro paralelo é a combinação de dois ficheiros com o texto correspondente. Um ficheiro tem a linguagem de origem. O outro tem a linguagem alvo.|
| Alinhamento da frase| Conjunto de dados paralelos deve ter frases alinhadas com frases que representam o mesmo texto em ambas as línguas. Por exemplo, num ficheiro paralelo de origem, a primeira frase deve, em teoria, mapear para a primeira frase no ficheiro paralelo alvo.|
| Texto alinhado | Um dos passos mais importantes da validação dos ficheiros é alinhar as frases nos documentos paralelos. As coisas são expressas de forma diferente em diferentes línguas. Também línguas diferentes têm ordens de palavras diferentes. Este passo faz o trabalho de alinhar as frases com o mesmo conteúdo para que possam ser usadas para a formação. Um alinhamento de frases baixas indica que pode haver algo de errado com um ou ambos os ficheiros. |
| Quebra de palavras/ Inquebragem | Quebrar palavras é a função de marcar os limites entre as palavras. Muitos sistemas de escrita usam um espaço para denotar a fronteira entre as palavras. Palavra inquebráveis refere-se à remoção de qualquer marcador visível que possa ter sido inserido entre palavras num passo anterior. |
| Delimiters   | Os delimiters são as formas como uma frase é dividida em segmentos ou delimita a margem entre frases. Por exemplo, em espaços ingleses delimitam palavras, cólons e semi-cólons delimitam cláusulas e períodos delimit de frases. |
| Ficheiros de Formação | Um ficheiro de treino é usado para ensinar o sistema de tradução automática a mapear de uma língua (a fonte) para uma língua-alvo (o alvo). Quanto mais dados fornecer, melhor será o sistema. |
| Ficheiros de sintonização | Estes ficheiros são frequentemente derivados aleatoriamente do conjunto de treino (se não selecionar um conjunto de sintonização). As frases são auto-selecionadas e utilizadas para afinar o sistema e garantir que está a funcionar corretamente. se desejar criar um modelo de tradução para fins gerais e criar os seus próprios ficheiros de afinação, certifique-se de que são um conjunto aleatório de frases em domínios |
| Ficheiros de Teste| Estes ficheiros são frequentemente ficheiros derivados, selecionados aleatoriamente a partir do conjunto de treino (se não selecionar nenhum conjunto de testes). O objetivo destas frases é avaliar a exatidão do modelo de tradução. Uma vez que estas frases são aquelas que pretende certificar-se de que o sistema se traduz com precisão, pode desejar criar um conjunto de testes e enviá-lo para o tradutor. Ao fazê-lo, estas frases serão utilizadas na avaliação do sistema (a geração de uma pontuação BLEU).   |
| Arquivo combo   | Um tipo de ficheiro no qual a origem e as frases traduzidas estão contidas no mesmo ficheiro. Formatos de ficheiros suportados (.tmx, .xliff, .xlf, .ici, .xlsx). |
| Arquivo de arquivo | Um ficheiro que contém outros ficheiros. Formatos de ficheiro suportados (zip, gz, tgz).  |
| Classificação de BLEU   | [A BLEU](what-is-bleu-score.md) é o método padrão da indústria para avaliar a "precisão" ou precisão do modelo de tradução. Embora existam outros métodos de avaliação, o Microsoft Tradutor baseia-se no método BLEU para reportar a precisão aos Proprietários do Projeto.
