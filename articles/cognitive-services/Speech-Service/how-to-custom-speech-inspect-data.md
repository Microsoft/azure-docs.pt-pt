---
title: Inspecionar a qualidade de dados para o serviço de Fala Personalizada-fala
titleSuffix: Azure Cognitive Services
description: Fala Personalizada fornece ferramentas que permitem inspecionar visualmente a qualidade de reconhecimento de um modelo, comparando dados de áudio com o resultado de reconhecimento correspondente. Você pode reproduzir áudio carregado e determinar se o resultado de reconhecimento fornecido está correto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: cf0deffb6fe484f7e3ee38b95f285bb4b7cc809c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072563"
---
# <a name="inspect-custom-speech-data"></a>Inspecionar dados de Fala Personalizada

> [!NOTE]
> Esta página pressupõe que você tenha lido [preparar dados de teste para fala personalizada](how-to-custom-speech-test-data.md) e ter carregado um DataSet para inspeção.

Fala Personalizada fornece ferramentas que permitem inspecionar visualmente a qualidade de reconhecimento de um modelo, comparando dados de áudio com o resultado de reconhecimento correspondente. No [portal de fala personalizada](https://speech.microsoft.com/customspeech), você pode reproduzir áudio carregado e determinar se o resultado de reconhecimento fornecido está correto. Essa ferramenta permite que você inspecione rapidamente a qualidade do modelo de fala-para-texto de linha de base da Microsoft ou de um modelo personalizado treinado sem precisar transcrever os dados de áudio.

Neste documento, você aprenderá a inspecionar visualmente a qualidade de um modelo usando os dados de treinamento carregados anteriormente.

Nesta página, você aprenderá a inspecionar visualmente a qualidade do modelo de fala-a-texto de linha de base da Microsoft e/ou um modelo personalizado que você tenha treinado. Você usará os dados carregados na guia **dados** para teste.

## <a name="create-a-test"></a>Criar um teste

Siga estas instruções para criar um teste:

1. Entre no portal de [fala personalizada](https://speech.microsoft.com/customspeech).
2. Navegue até a **> de fala para texto fala personalizada teste de >** .
3. Clique em **Adicionar teste**.
4. Selecione **inspecionar qualidade (dados somente de áudio)** . Dê ao teste um nome, uma descrição e selecione seu conjunto de dado de áudio.
5. Selecione até dois modelos que você gostaria de testar.
6. Clique em **Criar**.

Depois que um teste tiver sido criado com êxito, você poderá comparar os modelos lado a lado.

## <a name="side-by-side-model-comparisons"></a>Comparações de modelo lado a lado

Quando o status do teste for _bem-sucedido_, clique no nome do item de teste para ver os detalhes do teste. Essa página de detalhes lista todos os declarações no conjunto de seus conjuntos de anotações, indicando os resultados de reconhecimento dos dois modelos junto com a transcrição do conjunto de resultados enviado.

Para ajudar a inspecionar a comparação lado a lado, você pode alternar vários tipos de erro, incluindo inserção, exclusão e substituição. Ao ouvir o áudio e comparar os resultados de reconhecimento em cada coluna (mostrando a transcrição com rótulo humano e os resultados de dois modelos de fala em texto), você pode decidir qual modelo atende às suas necessidades e onde são necessárias melhorias.

Inspecionar testes de qualidade é útil para validar se a qualidade de um ponto de extremidade de reconhecimento de fala é suficiente para um aplicativo. Para uma medida objetiva de precisão, exigindo áudio transcrita, siga as instruções encontradas em [avaliar exatidão](how-to-custom-speech-evaluate-data.md).

## <a name="next-steps"></a>Passos seguintes

- [Avalie seus dados](how-to-custom-speech-evaluate-data.md)
- [Treinar seu modelo](how-to-custom-speech-train-model.md)
- [Implantar seu modelo](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Preparar dados de teste para Fala Personalizada](how-to-custom-speech-test-data.md)
