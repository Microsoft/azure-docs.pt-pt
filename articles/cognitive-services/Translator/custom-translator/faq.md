---
title: Perguntas frequentes - Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Este artigo contém respostas a perguntas frequentes sobre o Tradutor Personalizado de Serviços Cognitivos Azure.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: reference
ms.openlocfilehash: fefd3fcd82454d505099f83944b0e251b71410f0
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996912"
---
# <a name="custom-translator-frequently-asked-questions"></a>Tradutor personalizado frequentemente fez perguntas

Este artigo contém respostas a perguntas frequentes sobre [tradutor personalizado.](https://portal.customtranslator.azure.ai)

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>Quais são as restrições atuais no Tradutor Personalizado?

Existem restrições e limites no que diz respeito ao tamanho do ficheiro, formação de modelos e implantação de modelos. Tenha em mente estas restrições ao configurar o seu treino para construir um modelo em Tradutor Personalizado.

- Os ficheiros submetidos devem ter menos de 100 MB de tamanho.
- Os dados monolingues não são suportados.

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>Quando devo solicitar a implantação de um sistema de tradução que foi treinado?

Pode ser preciso vários treinos para criar o sistema de tradução ideal para o seu projeto. É melhor tentar utilizar mais dados de treino ou dados mais cuidadosamente filtrados, se a pontuação bleu e/ou os resultados dos testes não forem satisfatórios. Deve ser rigoroso e cuidadoso ao conceber o seu conjunto de afinação e o seu conjunto de testes, para ser totalmente representativo da terminologia e estilo de material que pretende traduzir. Pode ser mais liberal na composição dos seus dados de formação e experimentar diferentes opções. Solicite uma implementação do sistema quando estiver satisfeito com as traduções nos resultados dos testes do seu sistema, não tenha mais dados para adicionar ao treino para melhorar o seu sistema treinado, e pretende aceder ao modelo treinado através de APIs.

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>Quantos sistemas treinados podem ser implantados num projeto?

Apenas um sistema treinado pode ser implantado por projeto. Pode ser preciso vários treinos para criar um sistema de tradução adequado para o seu projeto e encorajamo-lo a solicitar a implementação de uma formação que lhe dê o melhor resultado. Pode determinar a qualidade da formação pela pontuação BLEU (mais alta é melhor), e consultando os revisores antes de decidir que a qualidade das traduções é adequada para a implantação.

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>Quando posso esperar que os meus treinos sejam implementados?

O destacamento geralmente demora menos de uma hora.

## <a name="how-do-you-access-a-deployed-system"></a>Como se acede a um sistema implantado?

Os sistemas implantados podem ser acedidos através do Microsoft Tradutor V3 especificando o CategoryID. Mais informações sobre o Tradutor podem ser encontradas na página web da [API Reference.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>Como posso ignorar o alinhamento e a quebra de frases se os meus dados já estão alinhados?

O Tradutor Personalizado ignora o alinhamento de frases e a quebra de frases para ficheiros TMX e para ficheiros de texto com a `.align` extensão. `.align`os ficheiros dão aos utilizadores a opção de ignorar o processo de quebra e alinhamento de frases do Tradutor Personalizado para os ficheiros que estão perfeitamente alinhados, e não precisam de mais processamento. Recomendamos a utilização de `.align` extensão apenas para ficheiros perfeitamente alinhados.

Se o número de frases extraídas não coincidir com os dois ficheiros com o mesmo nome base, o Custom Tradutor continuará a executar o alinhamento da frase em `.align` ficheiros.

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>Tentei carregar o meu TMX, mas diz que o processamento de documentos falhou.

Certifique-se de que o TMX está em conformidade com a especificação TMX 1.4b a <https://www.gala-global.org/tmx-14b> .
