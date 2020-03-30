---
title: 'Modelo de comboio: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo **Train Model** em Azure Machine Learning para treinar um modelo de classificação ou regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 0a9728e05aee27e74054a77e2c9be7dc08968207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455864"
---
# <a name="train-model-module"></a>Módulo Modelo de Comboio

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para treinar um modelo de classificação ou regressão. A formação ocorre depois de definir um modelo e definir os seus parâmetros, e requer dados marcados. Também pode utilizar **o Train Model** para retreinar um modelo existente com novos dados. 

## <a name="how-the-training-process-works"></a>Como funciona o processo de formação

No Azure Machine Learning, criar e utilizar um modelo de machine learning é tipicamente um processo de três etapas. 

1. Configura um modelo, escolhendo um tipo específico de algoritmo, e definindo os seus parâmetros ou hiperparâmetros. Escolha qualquer um dos seguintes tipos de modelos: 

    + **Modelos de classificação,** baseados em redes neurais, árvores de decisão e florestas de decisão, e outros algoritmos.
    + **Modelos de regressão,** que podem incluir regressão linear padrão, ou que usam outros algoritmos, incluindo redes neurais e regressão bayesiana.  

2. Forneça um conjunto de dados que esteja rotulado e tenha dados compatíveis com o algoritmo. Ligue os dados e o modelo ao **Modelo de Comboio**.

    O que a formação produz é um formato binário específico, o iLearner, que encapsula os padrões estatísticos aprendidos com os dados. Não é possível modificar ou ler diretamente este formato; no entanto, outros módulos podem usar este modelo treinado. 
    
    Também pode ver propriedades do modelo. Para mais informações, consulte a secção resultados.

3. Após o treino estar concluído, utilize o modelo treinado com um dos módulos de [pontuação,](./score-model.md)para fazer previsões sobre novos dados.

## <a name="how-to-use-train-model"></a>Como usar o Modelo de Comboio 
  
1.  No Azure Machine Learning, configure um modelo de classificação ou modelo de regressão.
    
2. Adicione o módulo **Modelo de Comboio** ao gasoduto.  Pode encontrar este módulo na categoria **Machine Learning.** Expanda **o Comboio**e, em seguida, arraste o módulo **Modelo de Comboio** para o seu oleoduto.
  
3.  Na entrada esquerda, prenda o modo destreinado. Fixe o conjunto de dados de treino à entrada direita do Modelo do **Comboio**.

    O conjunto de dados de treino deve conter uma coluna de etiquetas. Quaisquer filas sem rótulos são ignoradas.
  
4.  Para **a coluna Label,** clique na **coluna Editar** no painel certo do módulo e escolha uma única coluna que contenha resultados que o modelo possa utilizar para treinar.
  
    - Para problemas de classificação, a coluna de etiquetas deve conter valores **categóricos** ou valores **discretos.** Alguns exemplos podem ser uma classificação sim/não, um código ou nome de classificação da doença, ou um grupo de rendimentos.  Se escolher uma coluna não categórica, o módulo devolverá um erro durante o treino.
  
    -   Para problemas de regressão, a coluna do rótulo deve conter dados **numéricos** que representem a variável de resposta. Idealmente, os dados numéricos representam uma escala contínua. 
    
    Exemplos podem ser uma pontuação de risco de crédito, o tempo projetado para o insucesso de um disco rígido, ou o número previsto de chamadas para um call center em um determinado dia ou hora.  Se não escolher uma coluna numérica, pode ter um erro.
  
    -   Se não especificar qual a coluna de etiquetas a utilizar, o Azure Machine Learning tentará inferir qual é a coluna de etiqueta apropriada, utilizando os metadados do conjunto de dados. Se escolher a coluna errada, utilize o seletor de colunas para corrigi-la.
  
    > [!TIP] 
    > Se tiver problemas em utilizar o Seletor de Colunas, consulte o artigo [Selecione Colunas no Conjunto](./select-columns-in-dataset.md) de Dados para obter dicas. Descreve alguns cenários e dicas comuns para a utilização das **opções COM REGRAS** E **POR NOME.**
  
5.  Submeta o oleoduto. Se tiver muitos dados, isto pode demorar um pouco.

## <a name="results"></a><a name="bkmk_results"></a>Resultados

Depois do modelo ser treinado:


+ Para utilizar o modelo noutros pipelines, selecione o módulo e selecione o ícone de conjunto de **dados Register** sob o separador **Saídas** no painel direito. Pode aceder a modelos guardados na paleta de módulos em **Conjuntos**de Dados .

+ Para utilizar o modelo na previsão de novos valores, conecte-o ao módulo ['Modelo de Pontuação',](./score-model.md) juntamente com novos dados de entrada.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 