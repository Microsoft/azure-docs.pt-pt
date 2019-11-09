---
title: Perguntas frequentes-Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Este artigo contém respostas para perguntas frequentes sobre o tradutor personalizado dos serviços cognitivas do Azure.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: reference
ms.openlocfilehash: 97d399f4a8ec704fd90eb6c49f0835be7e9e4537
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73836603"
---
# <a name="custom-translator-frequently-asked-questions"></a>Perguntas frequentes sobre o tradutor personalizado

Este artigo contém respostas para perguntas frequentes sobre o [Tradutor personalizado](https://portal.customtranslator.azure.ai).

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>Quais são as restrições atuais no Tradutor personalizado?

Há restrições e limites em relação ao tamanho do arquivo, ao treinamento do modelo e à implantação do modelo. Tenha essas restrições em mente ao configurar seu treinamento para criar um modelo no Tradutor personalizado.

- Os arquivos enviados devem ter menos de 100 MB de tamanho.
- Não há suporte para dados multilíngues.

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>Quando devo solicitar a implantação de um sistema de tradução treinado?

Pode levar vários treinamentos para criar o sistema de tradução ideal para seu projeto. Talvez você queira tentar usar mais dados de treinamento ou dados filtrados com mais cuidado, se a pontuação de BLEU e/ou os resultados de teste não forem satisfatórios. Você deve ser estrito e cuidadoso ao criar seu conjunto de ajuste e seu conjunto de testes, para ser totalmente representativo da terminologia e do estilo de material que você deseja traduzir. Você pode ser mais liberal em compor seus dados de treinamento e experimentar opções diferentes. Solicite uma implantação de sistema quando estiver satisfeito com as traduções nos resultados do teste do sistema, não tenha mais dados para adicionar ao treinamento para melhorar seu sistema treinado e você quiser acessar o modelo treinado por meio de APIs.

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>Quantos sistemas treinados podem ser implantados em um projeto?

Somente um sistema treinado pode ser implantado por projeto. Pode levar vários treinamentos para criar um sistema de tradução adequado para seu projeto e incentivamos você a solicitar a implantação de um treinamento que oferece o melhor resultado. Você pode determinar a qualidade do treinamento pela pontuação BLEU (mais alta é melhor) e consultando com revisores antes de decidir que a qualidade das traduções é adequada para a implantação.

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>Quando posso esperar que meus treinamentos sejam implantados?

A implantação geralmente leva menos de uma hora.

## <a name="how-do-you-access-a-deployed-system"></a>Como acessar um sistema implantado?

Os sistemas implantados podem ser acessados por meio do Microsoft API de Tradução de Texto v3 especificando o CategoryID. Mais informações sobre o API de Tradução de Texto podem ser encontradas na página da Web de [referência de API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) .

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>Como fazer ignorar o alinhamento e a sentença de quebra se meus dados já estiverem alinhados na frase?

O tradutor personalizado ignora o alinhamento da frase e a quebra de frase para arquivos TMX e para arquivos de texto com a extensão `.align`. `.align` arquivos oferecem aos usuários uma opção para ignorar o processo de quebra de frase e alinhamento do tradutor personalizado para os arquivos que estão perfeitamente alinhados e não precisam de nenhum processamento adicional. É recomendável usar a extensão `.align` somente para arquivos perfeitamente alinhados.

Se o número de sentenças extraídas não corresponder aos dois arquivos com o mesmo nome base, o tradutor personalizado ainda executará o alinhador de sentença em arquivos de `.align`.

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>Tentei carregar meu TMX, mas ele diz "falha no processamento de documentos".

Verifique se o TMX está em conformidade com a especificação do TMX 1.4 b em <https://www.gala-global.org/tmx-14b>.
