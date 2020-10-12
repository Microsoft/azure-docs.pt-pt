---
title: Treine um modelo para discurso personalizado - serviço de fala
titleSuffix: Azure Cognitive Services
description: A formação de um modelo de voz para texto pode melhorar a precisão de reconhecimento para o modelo de base da Microsoft ou um modelo personalizado. Um modelo é treinado usando transcrições com rótulo humano e texto relacionado.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: bf9209e0c256412ccb06ea62a197046a7b012e00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84629028"
---
# <a name="train-a-model-for-custom-speech"></a>Preparar um modelo para Voz Personalizada

A formação de um modelo de voz para texto pode melhorar a precisão de reconhecimento para o modelo de base da Microsoft. Um modelo é treinado usando transcrições com rótulo humano e texto relacionado. Estes conjuntos de dados, juntamente com dados áudio previamente carregados, são usados para refinar e treinar o modelo de fala-a-texto.

## <a name="use-training-to-resolve-accuracy-issues"></a>Use a formação para resolver problemas de precisão

Se estiver a encontrar problemas de reconhecimento com o seu modelo, usar transcrições com rótulo humano e dados relacionados para treino adicional pode ajudar a melhorar a precisão. Utilize esta tabela para determinar que conjunto de dados utilizar para resolver os seus problemas):

| Caso de utilização | Tipo de dados |
| -------- | --------- |
| Melhorar a precisão do reconhecimento no vocabulário e gramática específicos da indústria, como terminologia médica ou jargão de TI. | Texto relacionado (frases/expressões) |
| Defina a forma fonética e exibida de uma palavra ou termo que tenha pronúncia não padrão, como nomes de produtos ou siglas. | Texto relacionado (pronúncia) |
| Melhore a precisão do reconhecimento em estilos de fala, acentos ou ruídos de fundo específicos. | Transcrições com rótulo humano + áudio + |

> [!IMPORTANT]
> Se não tiver carregado um conjunto de dados, consulte [Prepare-se e teste os seus dados.](how-to-custom-speech-test-data.md) Este documento fornece instruções para o upload de dados e diretrizes para a criação de conjuntos de dados de alta qualidade.

## <a name="train-and-evaluate-a-model"></a>Treine e avalie um modelo

O primeiro passo para treinar um modelo é carregar dados de treino. Utilize [prepare e teste os seus dados](how-to-custom-speech-test-data.md) para obter instruções passo a passo para preparar transcrições com rótulo humano e texto relacionado (expressões e pronúncias). Depois de ter carregado os dados de treino, siga estas instruções para começar a treinar o seu modelo:

1. Inscreva-se no [portal de Discurso Personalizado](https://speech.microsoft.com/customspeech).
2. Navegue **para discurso-a-texto > discurso personalizado > [nome do projeto] > Training**.
3. Clique **no modelo train**.
4. Em seguida, dê ao seu treino um **nome** e **descrição.**
5. A partir do menu de drop-down **do modelo Scenario e Baseline,** selecione o cenário que melhor se adequa ao seu domínio. Se não tem a certeza de qual cenário escolher, selecione **General**. O modelo de base é o ponto de partida para o treino. O modelo mais recente é geralmente a melhor escolha.
6. A partir da página **de dados de treino Select,** escolha um ou vários conjuntos de dados de transcrição com rótulo humano que gostaria de usar para treinar.
7. Uma vez concluído o treino, pode optar por realizar testes de precisão no modelo recém-treinado. Este passo é opcional.
8. Selecione **Criar** para construir o seu modelo personalizado.

A tabela Training apresenta uma nova entrada que corresponde a este modelo recém-criado. A tabela também apresenta o estado: Processamento, Sucesso, Falhado.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Avaliar a precisão de um modelo treinado

Pode inspecionar os dados e avaliar a precisão do modelo utilizando estes documentos:

- [Fiscalizar os seus dados](how-to-custom-speech-inspect-data.md)
- [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)

Se optar por testar a precisão, é importante selecionar um conjunto de dados acústico que seja diferente daquele que usou com o seu modelo para obter uma noção realista do desempenho do modelo.

## <a name="next-steps"></a>Passos seguintes

- [Implemente o seu modelo](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Prepare e teste os seus dados](how-to-custom-speech-test-data.md)
- [Fiscalizar os seus dados](how-to-custom-speech-inspect-data.md)
- [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)
- [Preparar o seu modelo](how-to-custom-speech-train-model.md)
