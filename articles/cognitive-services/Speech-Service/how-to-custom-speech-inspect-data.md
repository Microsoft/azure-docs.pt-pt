---
title: Inspecionar a qualidade dos dados de voz personalizada - serviços de voz
titlesuffix: Azure Cognitive Services
description: Conversão de voz personalizada fornece ferramentas que permitem-lhe inspecionar visualmente a qualidade de reconhecimento de um modelo através da comparação de dados de áudio com o resultado correspondente do reconhecimento. No portal de voz personalizada, pode reproduzir áudio carregado e determinar se o resultado do reconhecimento fornecido está correto.  Esta ferramenta permite-lhe rapidamente inspecionar qualidade do modelo de voz em texto de linha de base da Microsoft ou um modelo personalizado preparado sem ter de transcrever quaisquer dados de áudio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 682f3df362a7fbb0e95a07aa8a8f3a068367eef2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025821"
---
# <a name="inspect-custom-speech-data"></a>Inspecionar os dados de voz personalizada

> [!NOTE]
> Essa página assume que já leu [dados de teste de preparação para a conversão de voz personalizada](how-to-custom-speech-test-data.md) e tiver carregado um conjunto de dados para inspeção.

Conversão de voz personalizada fornece ferramentas que permitem-lhe inspecionar visualmente a qualidade de reconhecimento de um modelo através da comparação de dados de áudio com o resultado correspondente do reconhecimento. No portal de voz personalizada, pode reproduzir áudio carregado e determinar se o resultado do reconhecimento fornecido está correto. Esta ferramenta permite-lhe rapidamente inspecionar qualidade do modelo de voz em texto de linha de base da Microsoft ou um modelo personalizado preparado sem ter de transcrever quaisquer dados de áudio.

Neste documento, aprenderá como inspecionar visualmente a qualidade de um modelo a utilizar os dados de treinamento carregado anteriormente.

Nesta página, irá aprender a inspecionar visualmente a qualidade do modelo de voz em texto de linha de base da Microsoft e/ou um modelo personalizado que Treinou. Usará os dados carregados para o **dados** separador para fins de teste.

## <a name="create-a-test"></a>Criar um teste

Siga estas instruções para criar um teste:

1. Navegue para **voz em texto > voz personalizada > teste**.
2. Clique em **adicionar teste**.
3. Selecione **inspecionar qualidade (dados só de áudio)**. Dê um nome, descrição, ao teste e selecione o conjunto de dados de áudio.
4. Selecione até dois modelos que pretende testar.
5. Clique em **Criar**.

Depois de um teste tiver sido criado com êxito, pode comparar os modelos de lado a lado.

## <a name="side-by-side-model-comparisons"></a>Comparações de modelo de lado a lado

Quando o estado de teste é *bem-sucedido*, clique no nome do item de teste para ver os detalhes do teste. Esta página de detalhes apresenta uma lista de todas as expressões no conjunto de dados, que indica os resultados de reconhecimento de dois modelos, juntamente com a transcrição do conjunto de dados submetido.

Para ajudar a inspecionar a comparação lado a lado, pode alternar vários tipos de erro incluindo inserção, exclusão e de substituição. Ao ouvir o áudio e comparar resultados de reconhecimento em cada coluna (Mostrar transcrição de etiqueta humanos e os resultados de dois modelos de voz em texto), pode decidir que modelo atenda às suas necessidades e quais melhorias são necessários.

Inspecionar a qualidade de teste é útil para validar se a qualidade de um ponto de final de reconhecimento de voz é suficiente para uma aplicação.  Para um Objetiva de medida de precisão, a necessidade de áudio transcrito, siga as instruções nos testes: Avalie a precisão.

## <a name="next-steps"></a>Passos Seguintes

* [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)
* [Preparar o seu modelo](how-to-custom-speech-train-model.md)
* [Implementar o seu modelo](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Preparar os dados de teste para conversão de voz personalizada](how-to-custom-speech-test-data.md)
