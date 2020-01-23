---
title: 'Modelo de treinamento: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo **treinar modelo** em Azure Machine Learning para treinar um modelo de classificação ou regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: c9d02490a5627ead502429ead06608b4fb621058
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548412"
---
# <a name="train-model-module"></a>Módulo do modelo de formação

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para treinar um modelo de classificação ou regressão. O treinamento ocorre depois que você define um modelo e define seus parâmetros e requer dados marcados. Você também pode usar **treinar modelo** para treinar novamente um modelo existente com novos dados. 

## <a name="how-the-training-process-works"></a>Como funciona o processo de treinamento

Em Azure Machine Learning, criar e usar um modelo de aprendizado de máquina normalmente é um processo de três etapas. 

1. Você configura um modelo, escolhendo um tipo específico de algoritmo e definindo seus parâmetros ou hiperparâmetros. Escolha qualquer um dos seguintes tipos de modelo: 

    + Modelos de **classificação** , com base em redes neurais, árvores de decisão e florestas de decisão e outros algoritmos.
    + Modelos de **regressão** , que podem incluir regressão linear padrão ou que usam outros algoritmos, incluindo redes neurais e regressão bayesiana.  

2. Forneça um conjunto de dados que seja rotulado e que tenha um dado compatível com o algoritmo. Conecte os dados e o modelo para **treinar o modelo**.

    O que o treinamento produz é um formato binário específico, o iLearner, que encapsula os padrões estatísticos aprendidos dos dados. Você não pode modificar ou ler este formato diretamente; no entanto, outros módulos podem usar esse modelo treinado. 
    
    Você também pode exibir as propriedades do modelo. Para obter mais informações, consulte a seção resultados.

3. Após a conclusão do treinamento, use o modelo treinado com um dos [módulos de Pontuação](./score-model.md)para fazer previsões sobre novos dados.

## <a name="how-to-use-train-model"></a>Como usar o **modelo de treinamento**  
  
1.  Em Azure Machine Learning, configure um modelo de classificação ou um modelo de regressão.
    
2. Adicione o módulo **modelo de treinamento** ao pipeline.  Você pode encontrar esse módulo na categoria **Machine Learning** . Expanda **treinar**e arraste o módulo **modelo de treinamento** para o pipeline.
  
3.  Na entrada à esquerda, anexe o modo não treinado. Anexe o conjunto de dados de treinamento à entrada à direita do **modelo de treinamento**.

    O conjunto de os de treinamento deve conter uma coluna de rótulo. Qualquer linha sem rótulos é ignorada.
  
4.  Para **coluna de rótulo**, clique em **Iniciar seletor de coluna**e escolha uma única coluna que contenha resultados que o modelo pode usar para treinamento.
  
    - Para problemas de classificação, a coluna de rótulo deve conter valores **categóricos** ou valores **discretos** . Alguns exemplos podem ser uma classificação Sim/Não, um código ou nome de classificação de doença ou um grupo de renda.  Se você escolher uma coluna não categórica, o módulo retornará um erro durante o treinamento.
  
    -   Para problemas de regressão, a coluna de rótulo deve conter dados **numéricos** que representam a variável de resposta. O ideal é que os dados numéricos representem uma escala contínua. 
    
    Exemplos podem ser uma pontuação de risco de crédito, o tempo projetado para a falha de um disco rígido ou o número previsto de chamadas para um centro de atendimento em um determinado dia ou hora.  Se você não escolher uma coluna numérica, poderá receber um erro.
  
    -   Se você não especificar qual coluna de rótulo usar, Azure Machine Learning tentará inferir qual é a coluna de rótulo apropriada, usando os metadados do conjunto de um. Se escolher a coluna errada, use o seletor de coluna para corrigi-la.
  
    > [!TIP] 
    > Se você tiver problemas ao usar o seletor de coluna, consulte o artigo [selecionar colunas no conjunto de linhas](./select-columns-in-dataset.md) para obter dicas. Ele descreve alguns cenários comuns e dicas para usar as opções **with Rules** e **by Name** .
  
5.  Executar o pipeline. Se você tiver muitos dados, isso pode levar algum tempo.

## <a name="bkmk_results"></a>Da

Depois que o modelo for treinado:


+ Para usar o modelo em outros pipelines, selecione o módulo e selecione o ícone **registrar conjunto de registros** na guia **saídas** no painel direito. Você pode acessar modelos salvos na paleta de módulo em **conjuntos**de os.

+ Para usar o modelo na previsão de novos valores, conecte-o ao módulo [modelo de Pontuação](./score-model.md) , junto com os novos dados de entrada.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 