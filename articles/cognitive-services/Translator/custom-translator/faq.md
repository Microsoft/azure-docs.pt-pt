---
title: Perguntas frequentes - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Este artigo contém respostas a perguntas frequentes sobre o Tradutor Personalizado de Serviços Cognitivos Azure.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: reference
ms.openlocfilehash: 001314817b0c18a8023258d01bcfb02eaaffe79b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895837"
---
# <a name="custom-translator-frequently-asked-questions"></a>Tradutor personalizado frequentemente fez perguntas

Este artigo contém respostas a perguntas frequentes sobre [Tradutor Personalizado.](https://portal.customtranslator.azure.ai)

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>Quais são as restrições atuais no Tradutor Personalizado?

Existem restrições e limites no que diz respeito ao tamanho do ficheiro, formação de modelos e implementação de modelos. Tenha em mente estas restrições ao configurar o seu treino para construir um modelo em Custom Tradutor.

- Os ficheiros submetidos devem ter menos de 100 MB de tamanho.
- Os dados monolingues não são suportados.

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>Quando devo solicitar a implantação de um sistema de tradução treinado?

Pode ser necessário várias formações para criar o sistema de tradução ideal para o seu projeto. Pode tentar utilizar mais dados de treino ou dados mais cuidadosamente filtrados, se a pontuação BLEU e/ou os resultados dos testes não forem satisfatórios. Deve ser rigoroso e cuidadoso ao conceber o seu conjunto de afinação e o seu conjunto de testes, para ser totalmente representativo da terminologia e estilo de material que pretende traduzir. Você pode ser mais liberal na composição dos seus dados de treino, e experimentar com diferentes opções. Solicite uma implementação do sistema quando estiver satisfeito com as traduções nos resultados dos testes do seu sistema, não tenha mais dados para adicionar à formação para melhorar o seu sistema treinado, e pretende aceder ao modelo treinado através de APIs.

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>Quantos sistemas treinados podem ser implantados num projeto?

Apenas um sistema treinado pode ser implementado por projeto. Pode ser necessário várias formações para criar um sistema de tradução adequado para o seu projeto e encorajamo-lo a solicitar a implementação de uma formação que lhe dê o melhor resultado. Pode determinar a qualidade do treino pela pontuação BLEU (mais alto é melhor), e consultando os revisores antes de decidir que a qualidade das traduções é adequada para a implementação.

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>Quando posso esperar que os meus treinos sejam destacados?

A implantação geralmente leva menos de uma hora.

## <a name="how-do-you-access-a-deployed-system"></a>Como se acede a um sistema implantado?

Os sistemas implantados podem ser acedidos através do Texto API V3 do Texto do Tradutor da Microsoft, especificando a categoriaID. Mais informações sobre a API de Texto de Tradutor podem ser encontradas na página de Referência da [API.](../reference/v3-0-reference.md)

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>Como é que eu ignoro o alinhamento e a quebra de frases se os meus dados já estão alinhados?

O Tradutor Personalizado ignora o alinhamento da frase e a quebra da frase para ficheiros TMX e para ficheiros de texto com a `.align` extensão. `.align` os ficheiros dão aos utilizadores a opção de ignorar o processo de quebra e alinhamento da frase do Tradutor Personalizado para os ficheiros que estão perfeitamente alinhados e que não precisam de mais processamento. Recomendamos a `.align` utilização de extensão apenas para ficheiros perfeitamente alinhados.

Se o número de frases extraídas não corresponder aos dois ficheiros com o mesmo nome base, o Custom Tradutor continuará a executar o alinhamento de frases em `.align` ficheiros.

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>Tentei carregar o meu TMX, mas diz que "o processamento de documentos falhou"


Certifique-se de que o TMX está em conformidade com a Especificação TMX 1.4b em <https://www.gala-global.org/tmx-14b> .