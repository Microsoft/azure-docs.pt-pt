---
title: Treinar um modelo para o serviço de Fala Personalizada-fala
titleSuffix: Azure Cognitive Services
description: Treinar um modelo de fala a texto pode melhorar a precisão do reconhecimento do modelo de linha de base da Microsoft ou de um modelo personalizado. Um modelo é treinado usando transcrições de rótulo humano e texto relacionado.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 0f28d984cfc29e67c3b5c3a90137ee09868b471c
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806033"
---
# <a name="train-a-model-for-custom-speech"></a>Treinar um modelo para Fala Personalizada

Treinar um modelo de fala a texto pode melhorar a precisão do reconhecimento do modelo de linha de base da Microsoft ou de um modelo personalizado que você planeja criar. Um modelo é treinado usando transcrições de rótulo humano e texto relacionado. Esses conjuntos de dados, juntamente com as informações de áudio carregadas anteriormente, são usados para refinar e treinar o modelo de fala a texto para reconhecer palavras, frases, acrônimos, nomes e outros termos específicos do produto. Quanto mais conjuntos de dados no domínio você fornecer (os que estão relacionados ao que os usuários irão dizer e o que você espera reconhecer), mais preciso será o modelo, o que resultará em um reconhecimento aprimorado. Tenha em mente que, ao alimentar dados não relacionados ao seu treinamento, você pode reduzir ou prejudicar a precisão de seu modelo.

## <a name="use-training-to-resolve-accuracy-issues"></a>Use o treinamento para resolver problemas de precisão

Se você estiver encontrando problemas de reconhecimento com seu modelo, usar transcrições com rótulo humano e dados relacionados para treinamento adicional pode ajudar a melhorar a precisão. Use esta tabela para determinar qual conjunto de de que será usado para resolver seus problemas:

| Caso de utilização | Data type |
| -------- | --------- |
| Melhore a precisão do reconhecimento no vocabulário e na gramática específicos do setor, como terminologia médica ou jargão de ti. | Texto relacionado (sentenças/declarações) |
| Defina a forma fonética e exibida de uma palavra ou termo que tenha pronúncia não padrão, como nomes de produtos ou acrônimos. | Texto relacionado (pronúncia) |
| Melhore a precisão do reconhecimento em estilos de fala, ênfases ou ruídos de fundo específicos. | Áudio + transcrições com rótulo humano |

> [!IMPORTANT]
> Se você ainda não carregou um conjunto de dados, consulte [preparar e testar seus dados](how-to-custom-speech-test-data.md). Este documento fornece instruções para carregar dados e diretrizes para a criação de DataSets de alta qualidade.

## <a name="train-and-evaluate-a-model"></a>Treinar e avaliar um modelo

A primeira etapa para treinar um modelo é carregar dados de treinamento. Use [preparar e testar seus dados](how-to-custom-speech-test-data.md) para obter instruções passo a passo para preparar transcrições com rótulo humano e texto relacionado (declarações e pronúncias). Depois de carregar os dados de treinamento, siga estas instruções para começar a treinar seu modelo:

1. Entre no portal de [fala personalizada](https://speech.microsoft.com/customspeech).
2. Navegue até a **> de fala para texto fala personalizada treinamento de >** .
3. Clique em **treinar modelo**.
4. Em seguida, dê um **nome** e uma **Descrição**ao seu treinamento.
5. No menu suspenso **cenário e modelo de linha de base** , selecione o cenário que melhor se adapta ao seu domínio. Se você não tiver certeza de qual cenário escolher, selecione **geral**. O modelo de linha de base é o ponto de partida para treinamento. Se você não tiver uma preferência, poderá usar o mais recente.
6. Na página **selecionar dados de treinamento** , escolha um ou vários conjuntos de itens de áudio e de transcrição com rótulo humano que você gostaria de usar para treinamento.
7. Quando o treinamento for concluído, você poderá optar por executar o teste de exatidão no modelo treinado recentemente. Este passo é opcional.
8. Selecione **criar** para criar seu modelo personalizado.

A tabela de treinamento exibe uma nova entrada que corresponde a esse modelo recém-criado. A tabela também exibe o status: processamento, com êxito, com falha.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Avaliar a precisão de um modelo treinado

Você pode inspecionar os dados e avaliar a precisão do modelo usando estes documentos:

- [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
- [Avalie seus dados](how-to-custom-speech-evaluate-data.md)

Se você optar por testar a precisão, é importante selecionar um conjunto de um banco de uma diferente do que você usou com seu modelo para obter uma noção realista do desempenho do modelo.

## <a name="next-steps"></a>Passos seguintes

- [Implantar seu modelo](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Preparar e testar seus dados](how-to-custom-speech-test-data.md)
- [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
- [Avalie seus dados](how-to-custom-speech-evaluate-data.md)
- [Treinar seu modelo](how-to-custom-speech-train-model.md)
