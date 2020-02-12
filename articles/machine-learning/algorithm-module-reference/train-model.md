---
title: 'Modelo de treinamento: referência de módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo **Train Model** em Azure Machine Learning para treinar um modelo de classificação ou regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 3141795ed9ccfdabc9fd462a1cd09a74690f9c5b
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137924"
---
# <a name="train-model-module"></a>Módulo do modelo de formação

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para treinar um modelo de classificação ou regressão. O treinamento ocorre depois que você define um modelo e define seus parâmetros e requer dados marcados. Também pode utilizar **o Train Model** para retreinar um modelo existente com novos dados. 

## <a name="how-the-training-process-works"></a>Como funciona o processo de treinamento

Em Azure Machine Learning, criar e usar um modelo de aprendizado de máquina normalmente é um processo de três etapas. 

1. Você configura um modelo, escolhendo um tipo específico de algoritmo e definindo seus parâmetros ou hiperparâmetros. Escolha qualquer um dos seguintes tipos de modelo: 

    + **Modelos de classificação,** baseados em redes neurais, árvores de decisão e florestas de decisão, e outros algoritmos.
    + **Modelos de regressão,** que podem incluir regressão linear padrão, ou que usam outros algoritmos, incluindo redes neurais e regressão bayesiana.  

2. Forneça um conjunto de dados que seja rotulado e que tenha um dado compatível com o algoritmo. Ligue os dados e o modelo ao **Modelo de Comboio**.

    O que o treinamento produz é um formato binário específico, o iLearner, que encapsula os padrões estatísticos aprendidos dos dados. Você não pode modificar ou ler este formato diretamente; no entanto, outros módulos podem usar esse modelo treinado. 
    
    Você também pode exibir as propriedades do modelo. Para obter mais informações, consulte a seção resultados.

3. Após o treino estar concluído, utilize o modelo treinado com um dos módulos de [pontuação,](./score-model.md)para fazer previsões sobre novos dados.

## <a name="how-to-use-train-model"></a>Como usar o Modelo de Comboio 
  
1.  Em Azure Machine Learning, configure um modelo de classificação ou um modelo de regressão.
    
2. Adicione o módulo **Modelo de Comboio** ao gasoduto.  Pode encontrar este módulo na categoria **Machine Learning.** Expanda **o Comboio**e, em seguida, arraste o módulo **Modelo de Comboio** para o seu oleoduto.
  
3.  Na entrada à esquerda, anexe o modo não treinado. Fixe o conjunto de dados de treino à entrada direita do Modelo do **Comboio**.

    O conjunto de os de treinamento deve conter uma coluna de rótulo. Qualquer linha sem rótulos é ignorada.
  
4.  Para **a coluna Label,** clique na **coluna Editar** no painel certo do módulo e escolha uma única coluna que contenha resultados que o modelo possa utilizar para treinar.
  
    - Para problemas de classificação, a coluna de etiquetas deve conter valores **categóricos** ou valores **discretos.** Alguns exemplos podem ser uma classificação Sim/Não, um código ou nome de classificação de doença ou um grupo de renda.  Se você escolher uma coluna não categórica, o módulo retornará um erro durante o treinamento.
  
    -   Para problemas de regressão, a coluna do rótulo deve conter dados **numéricos** que representem a variável de resposta. O ideal é que os dados numéricos representem uma escala contínua. 
    
    Exemplos podem ser uma pontuação de risco de crédito, o tempo projetado para a falha de um disco rígido ou o número previsto de chamadas para um centro de atendimento em um determinado dia ou hora.  Se você não escolher uma coluna numérica, poderá receber um erro.
  
    -   Se você não especificar qual coluna de rótulo usar, Azure Machine Learning tentará inferir qual é a coluna de rótulo apropriada, usando os metadados do conjunto de um. Se escolher a coluna errada, use o seletor de coluna para corrigi-la.
  
    > [!TIP] 
    > Se tiver problemas em utilizar o Seletor de Colunas, consulte o artigo [Selecione Colunas no Conjunto](./select-columns-in-dataset.md) de Dados para obter dicas. Descreve alguns cenários e dicas comuns para a utilização das **opções COM REGRAS** E **POR NOME.**
  
5.  Executar o pipeline. Se você tiver muitos dados, isso pode levar algum tempo.

## <a name="bkmk_results"></a>Resultados

Depois que o modelo for treinado:


+ Para utilizar o modelo noutros pipelines, selecione o módulo e selecione o ícone de conjunto de **dados Register** sob o separador **Saídas** no painel direito. Pode aceder a modelos guardados na paleta de módulos em **Conjuntos**de Dados .

+ Para utilizar o modelo na previsão de novos valores, conecte-o ao módulo ['Modelo de Pontuação',](./score-model.md) juntamente com novos dados de entrada.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 