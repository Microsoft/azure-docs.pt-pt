---
title: Criar um modelo de análise de sentimentos
titleSuffix: Azure Machine Learning Studio
description: Como criar modelos de análise de texto no Azure Machine Learning Studio a utilização de módulos para o processamento prévio de texto, N-grams ou hashing de funcionalidade
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 4491c6ec101e6865b19f4ea1bc47d59f90248a04
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56816373"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio"></a>Criar um modelo de análise de sentimentos no Azure Machine Learning Studio

Pode utilizar o Azure Machine Learning Studio para criar e operacionalizar modelos de análise de texto. Esses modelos podem ajudá-lo a resolver, por exemplo, problemas de análise de classificação ou sentimentos do documento.

Numa experiência de análise de texto, normalmente seria:

1. Limpar e pré-processar o conjunto de dados de texto
2. Extrair vetores de funcionalidade numérico de texto de pré-processado
3. Preparar modelo de classificação ou regressão
4. Pontuar e validar o modelo
5. Implementar o modelo para produção

Neste tutorial, ficará a saber estes passos enquanto percorremos um modelo de análise de sentimentos com o conjunto de dados do Amazon resenha de livro (consulte este artigo de pesquisa "biografias, Bollywood, caixas de Boom e Blenders: Domínio adaptação para classificação de sentimento"por John Blitzer, marcar Dredze e Fernando Pereira; Associação de computacional linguística (ACL), 2007.) Este conjunto de dados consiste em pontuações de revisão (1 a 2 ou 4 e 5) e um texto de forma livre. O objetivo é prever a classificação de revisão: baixa (1 - 2) ou alto (4-5).

Pode encontrar experimentações abrangidas neste tutorial na Galeria de IA do Azure:

[Prever resenha de livro](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Prever resenha de livro - experimentação preditiva](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Passo 1: Limpar e pré-processar o conjunto de dados de texto
Começamos a experimentação, dividindo as pontuações de revisão em categóricos buckets de baixas e elevados para formular o problema como classificação de duas classes. Usamos [Editar metadados](https://msdn.microsoft.com/library/azure/dn905986.aspx) e [valores Categóricos do grupo](https://msdn.microsoft.com/library/azure/dn906014.aspx) módulos.

![Criar etiqueta](./media/text-analytics-module-tutorial/create-label.png)

Em seguida, vamos limpar o texto usando [pré-processar texto](https://msdn.microsoft.com/library/azure/mt762915.aspx) módulo. A limpeza reduz o ruído no conjunto de dados, ajudam a encontrar os recursos mais importantes e melhorar a precisão do modelo final. Podemos remover palavras de paragem - palavras comuns, como "a" ou "a" - e números, carateres especiais, caracteres duplicados, endereços de e-mail e URLs. Podemos também converter o texto em minúsculas, lemmatize as palavras e detetar os limites de sentença que, em seguida, são indicados pelo "|||" símbolo no texto processado previamente.

![Pré-processar texto](./media/text-analytics-module-tutorial/preprocess-text.png)

E se pretende utilizar uma lista personalizada de palavras de paragem? Pode passá-lo como entrada opcional. Também pode utilizar personalizada C# expressão regular de sintaxe para substituir subcadeias de carateres e remover palavras por parte de voz: substantivos, verbos ou os adjetivos.

Depois do processamento prévio de estiver concluída, vamos dividir os dados em train e conjuntos de teste.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Passo 2: Extrair vetores de funcionalidade numérico de texto de pré-processado
Para criar um modelo para os dados de texto, normalmente, tem de converter o texto de forma livre em vetores de funcionalidade numérico. Neste exemplo, utilizamos [funcionalidades de grama-N extrair texto de](https://msdn.microsoft.com/library/azure/mt762916.aspx) módulo para transformar os dados de texto para tal formato. Este módulo usa uma coluna de palavras separados por espaços em branco e computa um dicionário de palavras ou N-grams de palavras, o que são apresentados no seu conjunto de dados. Em seguida, ele conta quantas vezes cada palavra ou N-gram, é apresentado em cada registo e cria os vetores de funcionalidade das contagem. Neste tutorial, vamos definir o tamanho de grama-N como 2, pelo nosso vetores de funcionalidade incluem palavras individuais e combinações de duas palavras subsequentes.

![Extrair N-grams](./media/text-analytics-module-tutorial/extract-ngrams.png)

Aplicamos TF * contagens de equilibrar a N-grama IDF (termo frequência inversa documento frequência). Esta abordagem adiciona o peso de palavras que são apresentados frequentemente num único registro, mas são raros em todo o conjunto de dados. Outras opções incluem o binário, TF e criar um gráfico de pesar.

Tais recursos de texto, muitas vezes, têm dimensionalidade elevada. Por exemplo, se seu corpus tiver 100.000 palavras exclusivas, seu espaço de funcionalidade teria dimensões 100 000 ou mais se N-grams são utilizados. O módulo de extrair funcionalidades de grama-N-lhe um conjunto de opções para reduzir a dimensionalidade. Pode optar por excluir palavras que são curto ou longo, ou demasiado incomum ou demasiado frequente ter um valor significativo preditivo. Neste tutorial, EXCLUÍMOS N-grams que aparecem em menos de 5 registos ou em mais de 80% dos registos.

Além disso, pode utilizar a seleção de funcionalidades para selecionar apenas as funcionalidades que são mais correlacionado com o destino de predição. Seleção de funcionalidades de qui-quadrado são utilizadas para selecionar 1000 funcionalidades. Pode ver o vocabulário de palavras selecionadas ou N-grams ao clicar no resultado correto do módulo de extração. o N-grams.

Como uma abordagem alternativa para usar recursos de grama-N extrair, pode utilizar o módulo de Hashing de funcionalidade. No entanto, observe que [Hashing de funcionalidade](https://msdn.microsoft.com/library/azure/dn906018.aspx) não tem recursos de seleção de funcionalidade de compilação ou TF * IDF pesar.

## <a name="step-3-train-classification-or-regression-model"></a>Passo 3: Preparar modelo de classificação ou regressão
Agora, o texto ter sido transformado para colunas de funcionalidades numéricas. O conjunto de dados contiver colunas de cadeia de caracteres de fases anteriores, pelo que vamos utilizar selecionar colunas no conjunto de dados para os excluir.

Em seguida, usamos [regressão logística de duas classes](https://msdn.microsoft.com/library/azure/dn905994.aspx) para prever o nosso destino: pontuação de revisão de alta ou baixa. Neste momento, o problema de análise de texto tem sido transformado num problema de classificação regular. Pode utilizar as ferramentas disponíveis no Azure Machine Learning Studio para melhorar o modelo. Por exemplo, pode experimentar diferentes classificadores para descobrir o grau de precisão resultados que eles apresentam ou utilizar para melhorar a precisão de otimização de hiper-parâmetros.

![Formação e classificação](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Passo 4: Pontuar e validar o modelo
Como validar o modelo preparado? Estamos a pontuação-lo contra o conjunto de dados de teste e avaliar a precisão. No entanto, o modelo aprendeu o vocabulário de N-grams e seus pesos do conjunto de dados de treinamento. Por isso, devemos usar esse vocabulário e os pesos quando extrair os recursos de dados de teste, em vez de criar o vocabulário uma nova. Portanto, vamos adicionar o módulo de extrair funcionalidades de grama-N para o ramo de classificação da experimentação, ligar o vocabulário de saída a partir do ramo de treinamento e definir o modo de vocabulário como só de leitura. Podemos também desativar a filtragem de N-grams pela frequência ao definir o mínimo de 1 instância e o máximo de 100% e desligar a seleção de funcionalidades.

Depois de ter sido transformada a coluna de texto em dados de teste para colunas de funcionalidades numéricas, podemos excluir as colunas de cadeia de caracteres de fases anteriores, como no ramo de treinamento. Em seguida, usamos o módulo de modelo de pontuação para fazer previsões e módulo de modelo de avaliação para avaliar a precisão.

## <a name="step-5-deploy-the-model-to-production"></a>Passo 5: Implementar o modelo para produção
O modelo está quase pronto para ser implementada para produção. Quando implementada como serviço web, ele usa cadeia de caracteres de texto de forma livre como entrada e retornar uma predição "Alta" ou "baixa". Ele usa o vocabulário de grama-N adquirido para transformar o texto a recursos e o modelo de regressão logística preparado para efetuar uma predição desses recursos. 

Para configurar a experimentação preditiva, primeiro salvamos o vocabulário de grama-N como conjunto de dados e o modelo de regressão logística com formação do ramo de treinamento da experimentação. Em seguida, salvamos a experimentação utilizando "Guardar como" para criar um gráfico de experimentação para experimentação preditiva. Vamos remover o módulo de dividir dados e o ramo de treinamento de experimentação. Em seguida, ligamos a N-grama vocabulário e o modelo guardado anteriormente ao extrair funcionalidades de grama-N e módulos de modelo de pontuação, respectivamente. Também podemos remover o módulo avaliar modelo.

Vamos selecionar colunas de inserção no módulo de conjunto de dados antes de módulo de pré-processar texto para remover a coluna de etiqueta e desmarque a opção de "Coluna de pontuação de acrescentar ao conjunto de dados" no módulo de pontuação. Dessa forma, o serviço web não solicita a etiqueta está a tentar prever e faz não conta com a entrada na resposta de eco.

![Experimentação preditiva](./media/text-analytics-module-tutorial/predictive-text.png)

Agora temos uma experimentação que pode ser publicada como um serviço da web e chamada com a execução de solicitação-resposta ou lote APIs.

## <a name="next-steps"></a>Próximos Passos
Saiba mais sobre os módulos de análise de texto de [documentação do MSDN](https://msdn.microsoft.com/library/azure/dn905886.aspx).

