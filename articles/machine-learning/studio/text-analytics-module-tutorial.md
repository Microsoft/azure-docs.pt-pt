---
title: Caso de utilização de análise de sentimento
titleSuffix: ML Studio (classic) - Azure
description: Como criar modelos de análise de texto no Azure Machine Learning Studio (clássico) utilizando módulos para pré-processamento de texto, N-grams ou hashing de recursos
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 89fc5196977f53e040e1a6553b46ca57f39b18b4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79217889"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio-classic"></a>Crie um modelo de análise de sentimentos no Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Pode utilizar o Azure Machine Learning Studio (clássico) para construir e operacionalizar modelos de análise de texto. Estes modelos podem ajudá-lo a resolver, por exemplo, problemas de classificação de documentos ou de análise de sentimentos.

Numa experiência de análise de texto, normalmente:

1. Conjunto de dados de texto limpo e pré-processado
2. Extrair vetores de característica numérica do texto pré-processado
3. Modelo de classificação ou regressão do comboio
4. Marque e valide o modelo
5. Implementar o modelo para a produção

Neste tutorial, aprende-se estes passos à medida que caminhamos através de um modelo de análise de sentimentos utilizando o conjunto de dados da Amazon Book Reviews (veja este trabalho de investigação "Biografias, Bollywood, Boom-boxes e Blenders: Adaptação de Domínio para Classificação de Sentimentos" de John Blitzer, Mark Dredze e Fernando Pereira; Associação de Linguística Computacional (ACL), 2007.) Este conjunto de dados consiste em pontuações de revisão (1-2 ou 4-5) e um texto de forma livre. O objetivo é prever o resultado da revisão: baixo (1-2) ou alto (4-5).

Você pode encontrar experiências cobertas neste tutorial na Galeria Azure AI:

[Prever avaliações de livros](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Previram comentários sobre livros - Experiência Preditiva](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Passo 1: Limpar e pré-processar conjunto de dados de texto
Começamos a experiência dividindo as pontuações de revisão em baldes categóricos baixos e altos para formular o problema como classificação de duas classes. Utilizamos módulos [de Edição](https://msdn.microsoft.com/library/azure/dn905986.aspx) de Metadados e [Valores Categóricos do Grupo.](https://msdn.microsoft.com/library/azure/dn906014.aspx)

![Criar rótulo](./media/text-analytics-module-tutorial/create-label.png)

Em seguida, limpamos o texto utilizando o módulo [de texto pré-processo.](https://msdn.microsoft.com/library/azure/mt762915.aspx) A limpeza reduz o ruído no conjunto de dados, ajuda-o a encontrar as características mais importantes e a melhorar a precisão do modelo final. Removemos as palavras-stop - palavras comuns como "o" ou "a" - e números, caracteres especiais, caracteres duplicados, endereços de e-mail e URLs. Convertemos também o texto em minúsculas, lemmatize as palavras e detetamos limites de frases que depois são indicados pelo símbolo "[/]" em texto pré-processado.

![Pré-processar Texto](./media/text-analytics-module-tutorial/preprocess-text.png)

E se quiser usar uma lista personalizada de palavras-stop? Pode passá-lo como entrada opcional. Também pode usar expressão regular de sintaxe C# personalizada para substituir subcordas e remover palavras por parte da fala: substantivos, verbos ou adjetivos.

Após o pré-processamento estar concluído, dividimos os dados em conjuntos de comboios e testes.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Passo 2: Extrair vetores de característicanumérica do texto pré-processado
Para construir um modelo para dados de texto, você normalmente tem que converter texto de forma livre em vetores de características numéricas. Neste exemplo, utilizamos [as funcionalidades de extrato N-Gram do](https://msdn.microsoft.com/library/azure/mt762916.aspx) módulo Texto para transformar os dados de texto em tal formato. Este módulo pega numa coluna de palavras separadas pelo whitespace e calcula um dicionário de palavras, ou N-gramas de palavras, que aparecem no seu conjunto de dados. Então, conta quantas vezes cada palavra, ou N-gram, aparece em cada registo, e cria vetores de características dessas contagens. Neste tutorial, definimos o tamanho N-gram para 2, por isso os nossos vetores de características incluem palavras simples e combinações de duas palavras subsequentes.

![Extrair N-gramas](./media/text-analytics-module-tutorial/extract-ngrams.png)

Aplicamos a ponderação de TF*IDF (Frequência Inversa de Documentos de Frequência a Prazo) às contagens de N-gram. Esta abordagem adiciona peso de palavras que aparecem frequentemente num único registo, mas são raras em todo o conjunto de dados. Outras opções incluem pesagem binária, TF e gráfico.

Tais características de texto têm muitas vezes alta dimensionalidade. Por exemplo, se o seu corpus tiver 100.000 palavras únicas, o seu espaço de características teria 100.000 dimensões, ou mais, se os N-grams fossem usados. O módulo De extrair N-Gram Features dá-lhe um conjunto de opções para reduzir a dimensionalidade. Pode optar por excluir palavras curtas ou longas, ou demasiado incomuns ou demasiado frequentes para ter um valor preditivo significativo. Neste tutorial, excluímos N-grams que aparecem em menos de 5 registos ou em mais de 80% dos registos.

Além disso, pode utilizar a seleção de funcionalidades para selecionar apenas as funcionalidades mais correlacionadas com o seu alvo de previsão. Utilizamos a seleção de funcionalidades Chi-Squared para selecionar 1000 funcionalidades. Pode ver o vocabulário de palavras selecionadas ou N-grams clicando na saída certa do módulo Extrato N-grams.

Como uma abordagem alternativa à utilização de funcionalidades De extração N-Gram, pode utilizar o módulo de hashing feature. Note que o [Hashing de Recurso](https://msdn.microsoft.com/library/azure/dn906018.aspx) não tem capacidades de seleção de funcionalidades de construção, ou pesagem TF*IDF.

## <a name="step-3-train-classification-or-regression-model"></a>Passo 3: Modelo de classificação ou regressão do comboio
Agora o texto foi transformado em colunas de características numéricas. O conjunto de dados ainda contém colunas de cordas de fases anteriores, por isso usamos Colunas Select em Dataset para excluí-las.

Em seguida, usamos [regressão logística de duas classes](https://msdn.microsoft.com/library/azure/dn905994.aspx) para prever o nosso alvo: pontuação alta ou baixa de revisão. Neste momento, o problema da análise de texto foi transformado num problema de classificação regular. Pode utilizar as ferramentas disponíveis no Azure Machine Learning Studio (clássico) para melhorar o modelo. Por exemplo, pode experimentar diferentes classificadores para descobrir o quão precisos os resultados dão, ou usar a finação do hiperparâmetro para melhorar a precisão.

![Comboio e Pontuação](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Passo 4: Marcar e validar o modelo
Como validaria o modelo treinado? Marcamos contra o conjunto de dados do teste e avaliamos a precisão. No entanto, o modelo aprendeu o vocabulário dos N-grams e os seus pesos a partir do conjunto de dados de treino. Por conseguinte, devemos utilizar esse vocabulário e esses pesos ao extrair características dos dados de teste, em oposição à criação do vocabulário de novo. Por isso, adicionamos o módulo Extract N-Gram Features ao ramo de pontuação da experiência, conectamos o vocabulário de saída do ramo de treino e definimos o modo vocabulário apenas para leitura. Também desativamos a filtragem de N-grams por frequência, definindo o mínimo para 1 instância e máximo para 100%, e desligando a seleção de recursos.

Depois de a coluna de texto nos dados de teste ter sido transformada em colunas de características numéricas, excluímos as colunas de cordas de fases anteriores, como no ramo de treino. Em seguida, usamos o módulo Score Model para fazer previsões e avaliar o módulo Model para avaliar a precisão.

## <a name="step-5-deploy-the-model-to-production"></a>Passo 5: Implementar o modelo para a produção
O modelo está quase pronto para ser implantado para produção. Quando implantado como serviço web, pega na cadeia de texto de forma livre como entrada e devolve uma previsão "alta" ou "baixa". Usa o vocabulário n-grama aprendido para transformar o texto em funcionalidades, e treinou o modelo de regressão logística para fazer uma previsão a partir dessas características. 

Para configurar a experiência preditiva, primeiro guardamos o vocabulário N-gram como conjunto de dados, e o modelo de regressão logística treinado do ramo de treino da experiência. Em seguida, salvamos a experiência usando "Save As" para criar um gráfico de experiência para experiências preditivas. Removemos o módulo Split Data e o ramo de treino da experiência. Em seguida, ligamos o vocabulário e modelo N-gram previamente guardados para extrair funcionalidades N-Gram e módulos de Model de Pontuação, respectivamente. Também removemos o módulo Modelo de Avaliação.

Inserimos Colunas Selecionadas no módulo Dataset antes de pré-processar o módulo de texto para remover a coluna de etiquetas e não selecionar a opção "Coluna de pontuação do apêndice ao conjunto de dados" no Módulo de Pontuação. Desta forma, o serviço web não solicita a etiqueta que está a tentar prever, e não ecoa as funcionalidades de entrada em resposta.

![Experiência Preditiva](./media/text-analytics-module-tutorial/predictive-text.png)

Agora temos uma experiência que pode ser publicada como um serviço web e chamada usando APIs de resposta de pedido ou execução de lote.

## <a name="next-steps"></a>Passos Seguintes
Conheça os módulos de análise de texto a partir da documentação da [MSDN.](https://msdn.microsoft.com/library/azure/dn905886.aspx)

