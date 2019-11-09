---
title: Caso de uso da análise de sentimentos
titleSuffix: ML Studio (classic) - Azure
description: Como criar modelos de análise de texto no Azure Machine Learning Studio (clássico) usando módulos para o pré-processamento de texto, N-gramas ou hash de recurso
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 2e5cebb05549c2bd3cd810da58930efdae1422e4
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838624"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio-classic"></a>Criar um modelo de análise de sentimentos no Azure Machine Learning Studio (clássico)

Você pode usar Azure Machine Learning Studio (clássico) para criar e colocar em operação modelos de análise de texto. Esses modelos podem ajudá-lo a resolver, por exemplo, a classificação de documentos ou problemas de análise de sentimentos.

Em um experimento de análise de texto, você normalmente:

1. Limpar e pré-processar o conjunto de texto
2. Extrair vetores de recurso numéricos do texto pré-processado
3. Treinar modelo de classificação ou regressão
4. Pontuar e validar o modelo
5. Implantar o modelo na produção

Neste tutorial, você aprende essas etapas à medida que percorremos um modelo de análise de sentimentos usando o conjunto de registros do Amazon Book Reviews (consulte este documento de pesquisa "biografias, Bollywood, caixas de entrada e misturadores: adaptação de domínio para classificação de sentimentos" por John Blitzer, Mark Dredze e Fernando Pereira; Associação de linguísticos computacionais (ACL), 2007.) Esse conjunto de informações consiste em pontuações de revisão (1-2 ou 4-5) e um texto de forma livre. O objetivo é prever a pontuação de revisão: baixa (1-2) ou alta (4-5).

Você pode encontrar os experimentos abordados neste tutorial em Galeria de IA do Azure:

[Prever revisões do livro](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Prever análises de livros – experimento de previsão](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Etapa 1: limpar e pré-processar o conjunto de texto
Começamos o experimento dividindo as pontuações de revisão em buckets categóricos baixos e altos para formular o problema como classificação de duas classes. Usamos os módulos [Editar metadados](https://msdn.microsoft.com/library/azure/dn905986.aspx) e [Agrupar valores categóricos](https://msdn.microsoft.com/library/azure/dn906014.aspx) .

![Criar rótulo](./media/text-analytics-module-tutorial/create-label.png)

Em seguida, limpamos o texto usando o módulo de [texto de pré-processamento](https://msdn.microsoft.com/library/azure/mt762915.aspx) . A limpeza reduz o ruído no conjunto de, ajuda você a encontrar os recursos mais importantes e a aumentar a precisão do modelo final. Removemos palavras palavras irrelevantes, como "o" ou "a"-e números, caracteres especiais, caracteres duplicados, endereços de email e URLs. Também convertemos o texto em minúsculas, fazemos as palavras e detectos limites de sentença que são indicados pelo símbolo "| | |" no texto pré-processado.

![Pré-processar Texto](./media/text-analytics-module-tutorial/preprocess-text.png)

E se você quiser usar uma lista personalizada de palavras irrelevantes? Você pode passá-lo como entrada opcional. Você também pode usar a C# expressão regular de sintaxe personalizada para substituir subcadeias de caracteres e remover palavras por parte da fala: substantivos, verbos ou adjetivos.

Depois que o pré-processamento for concluído, dividiremos os dados em conjuntos de treinamento e teste.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Etapa 2: extrair vetores de recursos numéricos do texto pré-processado
Para criar um modelo para dados de texto, você normalmente precisa converter texto de forma livre em vetores de recursos numéricos. Neste exemplo, usamos a [extração dos recursos de N-Gram do](https://msdn.microsoft.com/library/azure/mt762916.aspx) módulo de texto para transformar os dados de texto em tal formato. Esse módulo usa uma coluna de palavras separadas por espaços em branco e computa um dicionário de palavras ou N-gramas de palavras, que aparecem no conjunto de seus conjuntos de seus. Em seguida, ele conta quantas vezes cada palavra ou N-Gram é exibida em cada registro e cria vetores de recurso a partir dessas contagens. Neste tutorial, definimos o tamanho de N-Gram como 2, portanto, os vetores de recurso incluem palavras únicas e combinações de duas palavras subsequentes.

![Extrair N-grams](./media/text-analytics-module-tutorial/extract-ngrams.png)

Aplicamos o peso de TF * IDF (frequência de termo inversa do documento) a contagens de N-Gram. Essa abordagem adiciona peso de palavras que aparecem com frequência em um único registro, mas são raras em todo o conjunto de registros. Outras opções incluem o peso binário, TF e grafo.

Esses recursos de texto geralmente têm alta dimensionalidade. Por exemplo, se seu corpus tiver 100.000 palavras exclusivas, o espaço de recurso teria 100.000 dimensões ou mais se N-grams forem usados. O módulo extrair recursos do N-Gram fornece um conjunto de opções para reduzir a dimensionalidade. Você pode optar por excluir palavras que são curtas ou longas ou muito incomuns ou muito frequentes para ter um valor de previsão significativo. Neste tutorial, excluimos N-grams que aparecem em menos de 5 registros ou em mais de 80% de registros.

Além disso, você pode usar a seleção de recursos para selecionar apenas os recursos que são mais correlacionados com seu destino de previsão. Usamos a seleção de recursos qui-quadrado para selecionar os recursos 1000. Você pode exibir o vocabulário de palavras selecionadas ou N-grams clicando na saída à direita do módulo extrair N-grams.

Como uma abordagem alternativa ao uso da extração de recursos de N-Gram, você pode usar o módulo hash de recurso. No entanto, observe que o [hash de recurso](https://msdn.microsoft.com/library/azure/dn906018.aspx) não tem recursos de seleção de recursos de compilação, ou o peso de TF * IDF.

## <a name="step-3-train-classification-or-regression-model"></a>Etapa 3: treinar o modelo de classificação ou regressão
Agora o texto foi transformado em colunas de recursos numéricos. O conjunto de linhas ainda contém colunas de cadeia de caracteres de estágios anteriores, portanto, usamos colunas SELECT no DataSet para excluí-las.

Em seguida, usamos a [regressão logística de duas classes](https://msdn.microsoft.com/library/azure/dn905994.aspx) para prever nosso destino: Pontuação de revisão alta ou baixa. Neste ponto, o problema de análise de texto foi transformado em um problema de classificação regular. Você pode usar as ferramentas disponíveis na versão clássica do Azure Machine Learning Studio para melhorar o modelo. Por exemplo, você pode fazer experiências com classificadores diferentes para descobrir a precisão dos resultados que eles fornecem ou usar o ajuste de hiperparâmetro para melhorar a precisão.

![Treinar e pontuar](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Etapa 4: pontuar e validar o modelo
Como você validaria o modelo treinado? Nós o classificamos em relação ao conjunto de testes e avaliamos a precisão. No entanto, o modelo aprendeu o vocabulário de N-grams e seus pesos do conjunto de conhecimento de treinamento. Portanto, devemos usar esse vocabulário e os pesos ao extrair recursos de dados de teste, em vez de criar o vocabulário de recriação. Portanto, adicionamos o módulo extrair recursos N-Gram ao Branch de Pontuação do experimento, conectamos o vocabulário de saída do Branch de treinamento e definimos o modo de vocabulário como somente leitura. Também desativamos a filtragem de N-gramas por frequência definindo o mínimo como 1 instância e o máximo como 100% e desativamos a seleção de recursos.

Depois que a coluna de texto nos dados de teste tiver sido transformada em colunas de recursos numéricos, excluiremos as colunas de cadeia de caracteres de estágios anteriores, como na ramificação de treinamento. Em seguida, usamos o módulo modelo de Pontuação para fazer previsões e avaliar o módulo modelo para avaliar a precisão.

## <a name="step-5-deploy-the-model-to-production"></a>Etapa 5: implantar o modelo para produção
O modelo está quase pronto para ser implantado para produção. Quando implantado como serviço Web, ele usa uma cadeia de caracteres de texto de forma livre como entrada e retorna uma previsão "alta" ou "baixa". Ele usa o vocabulário de N-Gram aprendido para transformar o texto em recursos e o modelo de regressão logística treinado para fazer uma previsão desses recursos. 

Para configurar o experimento de previsão, primeiro salvamos o vocabulário de N-Gram como conjunto de e o modelo de regressão logística treinado do Branch de treinamento do experimento. Em seguida, salvamos o experimento usando "salvar como" para criar um grafo de experimento para experimento preditiva. Removemos o módulo dividir dados e a ramificação de treinamento do experimento. Em seguida, conectamos o vocabulário e o modelo de N-grama salvo anteriormente para extrair os recursos de N-Gram e os módulos de modelo de pontuação, respectivamente. Também removemos o módulo avaliar modelo.

Inserimos as colunas SELECT no módulo DataSet antes do módulo de texto pré-processar para remover a coluna rótulo e desmarcamos a opção "acrescentar coluna de classificação ao conjunto de pontos" no módulo de pontuação. Dessa forma, o serviço Web não solicita o rótulo que está tentando prever e não ecoa os recursos de entrada em resposta.

![Experimento de previsão](./media/text-analytics-module-tutorial/predictive-text.png)

Agora temos um experimento que pode ser publicado como um serviço Web e chamado usando APIs de solicitação-resposta ou execução em lote.

## <a name="next-steps"></a>Próximos Passos
Saiba mais sobre os módulos de análise de texto da [documentação do MSDN](https://msdn.microsoft.com/library/azure/dn905886.aspx).

