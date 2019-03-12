---
title: Perguntas mais frequentes sobre - Translator personalizado
titleSuffix: Azure Cognitive Services
description: Fornece respostas às perguntas mais frequentes sobre o tradutor personalizado.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: reference
ms.openlocfilehash: d3e3d12e572c939fb2ddd5bc671aede434ca7d8a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760329"
---
# <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Este artigo contém respostas para perguntas mais frequentes sobre [Translator personalizado](https://portal.customtranslator.azure.ai).

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>Quais são as restrições atuais no Translator personalizado?

Existem restrições e limites em relação ao tamanho do ficheiro, a preparação de modelos e a implementação do modelo. Mantenha essas restrições em mente ao configurar seu treinamento para criar um modelo no Translator personalizado.

- Ficheiros enviados tem de ser inferior a 100 MB de tamanho.
- Dados monolíngües não são suportados.

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>Quando posso pedir a implementação para um sistema de tradução que foi treinado?

Pode demorar vários treinamentos para criar o sistema de tradução ideal para o seu projeto. Talvez queira tentar usar mais dados de treinamento ou dados mais cuidadosamente filtrados, se a pontuação de BLEU e / ou os resultados do teste não sejam satisfatórias. Deve ser rígidas e cuidado em conceber o conjunto de ajuste e de seu teste definido, para ser totalmente caracteriza a terminologia e o estilo de material que pretende converter. Pode ser mais liberal no compor os dados de treinamento e experimentar as diferentes opções. Pedir uma implementação de sistema, quando estiver satisfeito com as traduções nos resultados de teste seu sistema, não tem mais dados para adicionar à formação para melhorar o seu sistema preparado, e desejar acessar o modelo preparado através de APIs.

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>Quantos sistemas com formação podem ser implementados num projeto?

Pode ser implementado apenas um sistema com formação por projeto. Poderá demorar vários treinamentos para criar um sistema de tradução adequado para o seu projeto e também o incentivamos a implementação de um treinamento que lhe dá o melhor resultado do pedido. Pode determinar a qualidade do treinamento, a pontuação de BLEU (superior é melhor) e por consultoria com os revisores antes de decidir que a qualidade das traduções é adequada para a implementação.

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>Quando posso esperar meu treinamentos para ser implantado?

A implementação geralmente leva menos de uma hora.

## <a name="how-do-you-access-a-deployed-system"></a>Como acessa um sistema implantado?

Sistemas implantados podem ser acedidos através de V3 de API de texto do Microsoft Translator, especificando o CategoryID. Obter mais informações sobre a API de texto do Translator podem ser encontradas no [referência da API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) página Web.

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>Como ignorar alinhamento e frase recentes se meus dados já estão a ser frase alinhado?

O tradutor personalizado ignora a frase alinhamento e pesquisa de última hora de frase para ficheiros TMX e para os ficheiros de texto com o `.align` extensão. `.align` ficheiros dar aos utilizadores uma opção para ignorar a frase do Translator personalizado recentes e o processo de alinhamento para os ficheiros que são perfeitamente alinhada e que precisam sem processamento adicional. Recomendamos que utilize `.align` extensão apenas para ficheiros que são perfeitamente alinhada.

Se o número de frases extraídos não coincide com os dois ficheiros com o mesmo nome de base, o tradutor personalizado ainda será executado aligner a frase `.align` ficheiros.

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>Tentei carregar meu TMX, mas ele diz que "Falha de processamento de documentos".

Certifique-se de que o TMX está em conformidade com o TMX 1.4b especificação em <https://www.gala-global.org/tmx-14b>.
