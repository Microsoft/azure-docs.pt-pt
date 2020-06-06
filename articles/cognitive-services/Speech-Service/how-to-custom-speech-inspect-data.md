---
title: Inspecione a qualidade dos dados para discurso personalizado - serviço de fala
titleSuffix: Azure Cognitive Services
description: A Custom Speech fornece ferramentas que permitem inspecionar visualmente a qualidade de reconhecimento de um modelo, comparando os dados áudio com o resultado correspondente do reconhecimento. Pode reproduzir o áudio carregado e determinar se o resultado do reconhecimento fornecido está correto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: 7a8fec876556d943d29756a38ffc27ae8095e3c4
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2020
ms.locfileid: "84466243"
---
# <a name="inspect-custom-speech-data"></a>Inspecionar dados da Voz Personalizada

> [!NOTE]
> Esta página assume que leu [Preparar os dados](how-to-custom-speech-test-data.md) do teste para a Fala Personalizada e ter enviado um conjunto de dados para inspeção.

A Custom Speech fornece ferramentas que permitem inspecionar visualmente a qualidade de reconhecimento de um modelo, comparando os dados áudio com o resultado correspondente do reconhecimento. A partir do [portal Discurso Personalizado,](https://speech.microsoft.com/customspeech)pode reproduzir áudio carregado e determinar se o resultado do reconhecimento fornecido está correto. Esta ferramenta ajuda-o a inspecionar a qualidade do modelo de linha de base da Microsoft, a inspecionar um modelo personalizado treinado ou a comparar a transcrição por dois modelos.

Neste documento, você vai aprender a inspecionar visualmente a qualidade de um modelo usando os dados de treino que você já carregou.

Nesta página, você aprenderá a inspecionar visualmente a qualidade do modelo de fala-texto da Microsoft e/ou um modelo personalizado que treinou. Utilizará os dados que carregou no separador **Dados** para testes.

## <a name="create-a-test"></a>Criar um teste

Siga estas instruções para criar um teste:

1. Inscreva-se no [portal de Discurso Personalizado](https://speech.microsoft.com/customspeech).
2. Navegue **para testes de > de discurso > personalizados.**
3. Clique **em Adicionar Teste**.
4. **Selecione Inspecionar a qualidade (dados áudio-only)**. Dê ao teste um nome, descrição e selecione o seu conjunto de dados áudio.
5. Selecione até dois modelos que gostaria de testar.
6. Clique **em Criar**.

Depois de um teste ter sido criado com sucesso, pode ver como um modelo transcreve o conjunto de dados áudio especificado ou compara os resultados de dois modelos lado a lado.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Comparações de modelos lado a lado

Quando o estado do teste _for bem sucedido,_ clique no nome do item de teste para ver os detalhes do teste. Esta página de detalhe lista todas as expressões no seu conjunto de dados, e mostra os resultados de reconhecimento dos dois modelos que está a comparar.

Para ajudar a inspecionar a comparação lado a lado, pode alternar vários tipos de erros, incluindo inserção, eliminação e substituição. Ao ouvir o áudio e comparar os resultados de reconhecimento em cada coluna (mostrando transcrição com rótulo humano e os resultados de dois modelos de discurso a texto), pode decidir qual o modelo que satisfaz as suas necessidades e onde são necessárias melhorias.

Os testes de modelo lado a lado são úteis para validar qual o modelo de reconhecimento de voz melhor para uma aplicação. Para uma medida objetiva de precisão, requerendo áudio transcrito, siga as instruções encontradas na [Avaliação de Precisão](how-to-custom-speech-evaluate-data.md).

## <a name="next-steps"></a>Próximos passos

- [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)
- [Preparar o seu modelo](how-to-custom-speech-train-model.md)
- [Melhore o seu modelo](how-to-custom-speech-improve-accuracy.md)
- [Implemente o seu modelo](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Preparar dados de teste para discurso personalizado](how-to-custom-speech-test-data.md)
