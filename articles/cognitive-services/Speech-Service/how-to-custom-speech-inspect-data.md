---
title: Inspecione a qualidade dos dados para discurso personalizado - serviço de discurso
titleSuffix: Azure Cognitive Services
description: O Custom Speech fornece ferramentas que lhe permitem inspecionar visualmente a qualidade de reconhecimento de um modelo, comparando os dados áudio com o resultado de reconhecimento correspondente. Pode reproduzir o áudio enviado e determinar se o resultado do reconhecimento fornecido está correto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: 60b415f69800885f37ee53cc4f090098f5ebf1f1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74806084"
---
# <a name="inspect-custom-speech-data"></a>Inspecionar dados da Voz Personalizada

> [!NOTE]
> Esta página assume que leu os dados do [teste preparem os dados para a Fala Personalizada](how-to-custom-speech-test-data.md) e tenha enviado um conjunto de dados para inspeção.

O Custom Speech fornece ferramentas que lhe permitem inspecionar visualmente a qualidade de reconhecimento de um modelo, comparando os dados áudio com o resultado de reconhecimento correspondente. A partir do [portal Custom Speech,](https://speech.microsoft.com/customspeech)pode reproduzir o áudio carregado e determinar se o resultado de reconhecimento fornecido está correto. Esta ferramenta permite-lhe inspecionar rapidamente a qualidade do modelo de discurso-texto da Microsoft ou de um modelo personalizado treinado sem ter de transcrever quaisquer dados áudio.

Neste documento, você aprenderá a inspecionar visualmente a qualidade de um modelo usando os dados de treino que você já carregou anteriormente.

Nesta página, você aprenderá a inspecionar visualmente a qualidade do modelo de discurso-texto da Microsoft e/ou um modelo personalizado que treinou. Utilizará os dados que carregou para o separador **Dados** para testes.

## <a name="create-a-test"></a>Criar um teste

Siga estas instruções para criar um teste:

1. Inscreva-se no [portal da Fala Personalizada.](https://speech.microsoft.com/customspeech)
2. Navegue para **o discurso-a-texto > discurso personalizado > teste**.
3. Clique no **Teste adicionar**.
4. **Selecione Inspecione a qualidade (dados apenas áudio)**. Dê ao teste um nome, descrição e selecione o seu conjunto de dados áudio.
5. Selecione até dois modelos que gostaria de testar.
6. Clique em **Criar**.

Depois de um teste ter sido criado com sucesso, pode comparar os modelos lado a lado.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Comparações de modelos lado a lado

Quando o estado do teste for _bem sucedido,_ clique no nome do item de teste para ver os detalhes do teste. Esta página de detalhes lista todas as expressões no seu conjunto de dados, indicando os resultados de reconhecimento dos dois modelos juntamente com a transcrição do conjunto de dados submetido.

Para ajudar a inspecionar a comparação lado a lado, pode alternar vários tipos de erros, incluindo inserção, eliminação e substituição. Ao ouvir o áudio e comparar os resultados do reconhecimento em cada coluna (mostrando a transcrição com rótulo humano e os resultados de dois modelos de fala a texto), pode decidir qual o modelo que satisfaz as suas necessidades e onde são necessárias melhorias.

Inspecionar testes de qualidade é útil para validar se a qualidade de um ponto final de reconhecimento de voz for suficiente para uma aplicação. Para uma medida objetiva de precisão, requerendo áudio transcrito, siga as instruções encontradas na [Precisão de Avaliação](how-to-custom-speech-evaluate-data.md).

## <a name="next-steps"></a>Passos seguintes

- [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)
- [Preparar o seu modelo](how-to-custom-speech-train-model.md)
- [Implante o seu modelo](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Preparar dados de teste para discurso personalizado](how-to-custom-speech-test-data.md)
