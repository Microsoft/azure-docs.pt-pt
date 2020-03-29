---
title: Treine um modelo para discurso personalizado - serviço de fala
titleSuffix: Azure Cognitive Services
description: Treinar um modelo de fala-a-texto pode melhorar a precisão de reconhecimento para o modelo de base da Microsoft ou um modelo personalizado. Um modelo é treinado usando transcrições com rótulo humano e texto relacionado.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: a2bc39a35299f56ba52a0143ce123560bd4d88fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77137759"
---
# <a name="train-a-model-for-custom-speech"></a>Treine um modelo para discurso personalizado

Treinar um modelo de fala-a-texto pode melhorar a precisão de reconhecimento para o modelo de base da Microsoft. Um modelo é treinado usando transcrições com rótulo humano e texto relacionado. Estes conjuntos de dados, juntamente com dados áudio previamente carregados, são usados para refinar e treinar o modelo de fala-a-texto para reconhecer palavras, frases, siglas, nomes e outros termos específicos do produto. Quanto mais conjuntos de dados no domínio fornecer (dados relacionados com o que os utilizadores vão dizer e o que espera reconhecer), mais preciso será o seu modelo, o que resulta num melhor reconhecimento. Tenha em mente que, ao alimentar dados não relacionados no seu treino, pode reduzir ou ferir a precisão do seu modelo.

## <a name="use-training-to-resolve-accuracy-issues"></a>Use o treino para resolver problemas de precisão

Se encontrar problemas de reconhecimento com o seu modelo, usar transcrições com rótulo humano e dados relacionados para treino adicional pode ajudar a melhorar a precisão. Utilize este quadro para determinar que conjunto de dados utilizar para resolver o seu ou problema:

| Caso de utilização | Tipo de dados |
| -------- | --------- |
| Melhorar a precisão do reconhecimento no vocabulário e gramática específicos da indústria, como a terminologia médica ou o jargão de TI. | Texto relacionado (frases/expressões) |
| Defina a forma fonética e exibida de uma palavra ou termo que tenha pronúncia não normal, como nomes de produtos ou siglas. | Texto relacionado (pronúncia) |
| Melhore a precisão de reconhecimento em estilos de fala, sotaques ou ruídos de fundo específicos. | Audio + transcrições com rótulo humano |

> [!IMPORTANT]
> Se ainda não fez o upload de um conjunto de dados, consulte [preparar e testar os seus dados](how-to-custom-speech-test-data.md). Este documento fornece instruções para o upload de dados e orientações para a criação de conjuntos de dados de alta qualidade.

## <a name="train-and-evaluate-a-model"></a>Treine e avalie um modelo

O primeiro passo para treinar um modelo é carregar dados de treino. Utilize [Preparar e testar os seus dados](how-to-custom-speech-test-data.md) para obter instruções passo a passo para preparar transcrições e textos relacionados com rótulo humano (pronunciações e pronúncias). Depois de ter enviado dados de treino, siga estas instruções para começar a treinar o seu modelo:

1. Inscreva-se no [portal da Fala Personalizada.](https://speech.microsoft.com/customspeech)
2. Navegue para **discurso-a-texto > discurso personalizado > formação**.
3. Clique no **modelo de comboio**.
4. Em seguida, dê ao seu treino um **nome** e **descrição.**
5. A partir do menu de drop-down do **modelo Cenário e Baseline,** selecione o cenário que melhor se adequa ao seu domínio. Se não tem a certeza de qual cenário escolher, selecione **General**. O modelo de base é o ponto de partida para o treino. Se não tem preferência, pode usar as últimas.
6. Na página de dados de **treino Select,** escolha um ou vários conjuntos de dados de transcrição de áudio + com rótulo humano que gostaria de utilizar para treinar.
7. Uma vez concluído o treino, pode optar por realizar testes de precisão no modelo recém-treinado. Este passo é opcional.
8. Selecione **Criar** para construir o seu modelo personalizado.

A tabela formativa apresenta uma nova entrada que corresponde a este modelo recém-criado. A tabela também mostra o estado: Processamento, Sucesso, Falhado.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Avaliar a precisão de um modelo treinado

Pode inspecionar os dados e avaliar a precisão do modelo utilizando estes documentos:

- [Inspecione os seus dados](how-to-custom-speech-inspect-data.md)
- [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)

Se optou por testar a precisão, é importante selecionar um conjunto de dados acústico diferente daquele que usou com o seu modelo para ter uma noção realista do desempenho do modelo.

## <a name="next-steps"></a>Passos seguintes

- [Implante o seu modelo](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Prepare e teste os seus dados](how-to-custom-speech-test-data.md)
- [Inspecione os seus dados](how-to-custom-speech-inspect-data.md)
- [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)
- [Preparar o seu modelo](how-to-custom-speech-train-model.md)
