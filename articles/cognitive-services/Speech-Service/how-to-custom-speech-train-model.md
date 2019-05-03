---
title: Preparar um modelo de voz personalizada - serviços de voz
titlesuffix: Azure Cognitive Services
description: Um conversão de voz em texto de preparação é necessário para melhorar a precisão de reconhecimento de modelo de linha de base da Microsoft ou um modelo personalizado que estiver a planear criar. É preparado um modelo com transcrições de etiqueta humanos e texto relacionado. Estes conjuntos de dados, juntamente com dados de áudio carregados anteriormente, são utilizados para refinar e preparar o modelo de voz em texto para reconhecer palavras, frases, acrônimos, nomes e outros termos específicos de produtos.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 194ae477bb3cba4ac7e3350da6b793c6fea6ecdb
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025860"
---
# <a name="train-a-model-for-custom-speech"></a>Preparar um modelo de voz personalizada

Um conversão de voz em texto de preparação é necessário para melhorar a precisão de reconhecimento de modelo de linha de base da Microsoft ou um modelo personalizado que estiver a planear criar. É preparado um modelo com transcrições de etiqueta humanos e texto relacionado. Estes conjuntos de dados, juntamente com dados de áudio carregados anteriormente, são utilizados para refinar e preparar o modelo de voz em texto para reconhecer palavras, frases, acrônimos, nomes e outros termos específicos de produtos. Os mais em domínios conjuntos de dados fornecidas por si (dados que estejam relacionados com o que os utilizadores serão apresentada a mensagem e o que esperar reconhecer), mais precisos será o seu modelo, que resulta em reconhecimento aperfeiçoado. Tenha em mente, que por alimentando os dados não relacionados em seu treinamento, pode reduzir ou prejudicar a precisão do seu modelo.

## <a name="use-training-to-resolve-accuracy-issues"></a>Utilizar o treinamento para resolver problemas de precisão

Se estiver com problemas de reconhecimento com o seu modelo, utilizando a etiqueta humanos transcrições e dados relacionados de treinamento adicional podem ajudar a melhorar a precisão. Utilize esta tabela para determinar o conjunto de dados a serem seguidas para resolver o problema (s):

| Caso de utilização | Tipo de dados | Quantidade de dados |
|----------|-----------|---------------|
| Nomes apropriados são misrecognized | Se relacionam com texto (frases/expressões com) | MB de 10 a 500 MB |
| Palavras são misrecognized devido a um destaque | Introduza texto relacionado com (pronúncia) | Fornecer as palavras misrecognized |
| Palavras comuns são eliminadas ou misrecognized | Transcrições áudio + com etiqueta humanos | horas de transcrição de 10 para 1000 |

> [!IMPORTANT]
> Se ainda não carregou um conjunto de dados, veja [preparar e testar seus dados](how-to-custom-speech-test-data.md). Este documento fornece instruções para carregar dados e diretrizes para criar conjuntos de dados de alta qualidade.

## <a name="train-and-evaluate-a-model"></a>Dar formação e avaliar um modelo

É o primeiro passo para preparar um modelo carregar dados de treinamento. Uso [preparar e testar seus dados](how-to-custom-speech-test-data.md) para obter instruções passo a passo preparar com etiqueta humanos transcrições e texto relacionado (expressões e pronunciations). Depois de carregar dados de treinamento, siga estas instruções para iniciar o seu modelo de preparação:

1. Navegue para **voz em texto > voz personalizada > treinamento**.
2. Clique em **preparar modelo**.
3. Em seguida, atribua seu treinamento uma **Name** e **Descrição**.
4. Do **modelo de linha de base e de cenário** menu pendente, selecione o cenário que melhor se adequa a seu domínio. Se tiver a certeza de qual o cenário de escolher, selecione **gerais**. O modelo de linha de base é o ponto de partida para treinamento. Se não tiver uma preferência, pode usar a versão mais recente.
5. Partir do **selecionar dados de treinamento** página, selecione um ou vários transcrição áudio + com etiqueta humanos conjuntos de dados que pretende utilizar para formação.
6. Assim que o treinamento estiver concluído, pode optar por executar o teste no modelo treinado recentemente de precisão. Este passo é opcional.
7. Selecione **criar** para criar o seu modelo personalizado.

A tabela de treinamento apresenta uma nova entrada que corresponde a este criada de novo modelo. A tabela também apresenta o estado:  Processamento, com êxito, com falhas.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Avaliar a precisão de um modelo preparado

Pode inspecionar os dados e avaliar a precisão do modelo utilizam esses documentos:

* [Inspecione os seus dados](how-to-custom-speech-inspect-data.md)
* [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)


Se optar por testar a precisão, é importante selecionar um conjunto de dados acústico que é diferente da utilizada com o seu modelo para obter uma noção realista de desempenho do modelo.

## <a name="next-steps"></a>Passos Seguintes

* [Implementar o seu modelo](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Preparar e testar seus dados](how-to-custom-speech-test-data.md)
* [Inspecione os seus dados](how-to-custom-speech-inspect-data.md)
* [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)
* [Preparar o seu modelo](how-to-custom-speech-train-model.md)
