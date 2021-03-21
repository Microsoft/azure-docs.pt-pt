---
title: 'Modelo de comboio: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Saiba como utilizar o módulo **Train Model** em Azure Machine Learning para treinar um modelo de classificação ou regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2021
ms.openlocfilehash: 77927472dae6c8e7e6fddacf9088b479636edd37
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103224345"
---
# <a name="train-model-module"></a>Módulo modelo de trem

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para treinar um modelo de classificação ou regressão. A formação ocorre depois de definir um modelo e definir os seus parâmetros, e requer dados marcados. Também pode utilizar **o Train Model** para reconverter um modelo existente com novos dados. 

## <a name="how-the-training-process-works"></a>Como funciona o processo de formação

No Azure Machine Learning, criar e utilizar um modelo de aprendizagem automática é tipicamente um processo de três etapas. 

1. Configura um modelo, escolhendo um tipo específico de algoritmo, e definindo os seus parâmetros ou hiperparímetros. Escolha qualquer um dos seguintes modelos: 

    + **Modelos de classificação,** baseados em redes neurais, árvores de decisão e florestas de decisão, e outros algoritmos.
    + **Modelos de regressão,** que podem incluir regressão linear padrão, ou que usam outros algoritmos, incluindo redes neurais e regressão bayesiana.  

2. Forneça um conjunto de dados que esteja rotulado e tenha dados compatíveis com o algoritmo. Ligue tanto os dados como o modelo ao **Modelo de Comboio.**

    O que o treino produz é um formato binário específico, o iLearner, que encapsula os padrões estatísticos aprendidos com os dados. Não é possível modificar ou ler diretamente este formato; no entanto, outros módulos podem usar este modelo treinado. 
    
    Também pode ver as propriedades do modelo. Para mais informações, consulte a secção Resultados.

3. Após o treino concluído, utilize o modelo treinado com um dos [módulos de pontuação,](./score-model.md)para fazer previsões sobre novos dados.

## <a name="how-to-use-train-model"></a>Como usar o modelo de comboio 
    
1. Adicione o módulo **Modelo de Comboio** ao gasoduto.  Pode encontrar este módulo na categoria **Machine Learning.** Expanda **o Comboio** e, em seguida, arraste o módulo **Train Model** para o seu oleoduto.
  
1.  Na entrada esquerda, fixe o modo destreinado. Anexar o conjunto de dados de treino à entrada direita do **Modelo de Comboio**.

    O conjunto de dados de formação deve conter uma coluna de etiqueta. Quaisquer filas sem etiquetas são ignoradas.
  
1.  Para **a coluna Label**, clique em **Editar** a coluna no painel direito do módulo e escolha uma única coluna que contenha resultados que o modelo possa utilizar para o treino.
  
    - Para problemas de classificação, a coluna de etiqueta deve conter valores **categóricos** ou valores **discretos.** Alguns exemplos podem ser uma classificação sim/não, um código de classificação da doença ou nome, ou um grupo de rendimentos.  Se escolher uma coluna não-categórica, o módulo retornará um erro durante o treino.
  
    -   Para problemas de regressão, a coluna do rótulo deve conter dados **numéricos** que representem a variável de resposta. Idealmente, os dados numéricos representam uma escala contínua. 
    
    Exemplos podem ser uma pontuação de risco de crédito, o tempo projetado para falhar para um disco rígido, ou o número previsto de chamadas para um call center em um determinado dia ou hora.  Se não escolher uma coluna numérica, poderá ter um erro.
  
    -   Se não especificar qual a coluna de etiquetas a utilizar, o Azure Machine Learning tentará inferir qual é a coluna de etiquetas apropriada, utilizando os metadados do conjunto de dados. Se escolher a coluna errada, utilize o seletor de colunas para corrigi-la.
  
    > [!TIP] 
    > Se tiver problemas em utilizar o Seletor de Colunas, consulte o artigo [Selecione colunas no conjunto de dados](./select-columns-in-dataset.md) para obter dicas. Descreve alguns cenários e dicas comuns para a utilização das opções **COM REGRAS** e **POR NOME.**
  
1.  Envie o oleoduto. Se tiver muitos dados, pode demorar um pouco.

    > [!IMPORTANT] 
    > Se tiver uma coluna de ID que é o ID de cada linha, ou uma coluna de texto, que contém demasiados valores únicos, **o Modelo de Comboio** pode atingir um erro como "Número de valores únicos na coluna: "{column_name}" é maior do que o permitido.
    >
    > Isto porque a coluna atingiu o limiar de valores únicos, e pode causar fora da memória. Pode utilizar [Os Metadados de Edição](edit-metadata.md) para marcar essa coluna como **funcionalidade Clear** e não será utilizada no treino, ou extrair [funcionalidades N-Gram do módulo de texto para pré-processar](extract-n-gram-features-from-text.md) coluna de texto. Consulte [o código de erro do Designer](././designer-error-codes.md) para obter mais detalhes de erro.

## <a name="model-interpretability"></a>Interpretação do modelo

A interpretação do modelo proporciona a possibilidade de compreender o modelo ML e apresentar a base subjacente à tomada de decisões de uma forma compreensível para os seres humanos.

**Atualmente, o** módulo Train Model suporta a utilização de pacote de interpretação para explicar os [modelos ML.](https://docs.microsoft.com/azure/machine-learning/how-to-machine-learning-interpretability-aml#generate-feature-importance-values-via-remote-runs) Os seguintes algoritmos incorporados são suportados:

- Regressão Linear
- Regressão da Rede Neural
- Regressão Logística de Duas Classes
- Máquina de Vetores de Suporte de Duas Classes
- Floresta de Decisão multi-classes

Para gerar explicações de modelo, pode selecionar **True** na lista de drop-down do **módulo Model Explanation** no Modelo de Comboio. Por predefinição, é definido como Falso no módulo **Modelo de Comboio.** Por favor, note que gerar explicação requer custo extra de computação.

![Screenshot mostrando caixa de verificação de explicação de modelo](./media/module/train-model-explanation-checkbox.png)

Após o funcionado do pipeline, pode visitar o **separador Explicações** no painel direito do módulo **Train Model** e explorar o desempenho do modelo, conjunto de dados e importância de característica.

![Screenshot mostrando gráficos de explicação do modelo](./media/module/train-model-explanations-tab.gif)

Para saber mais sobre a utilização de explicações de modelos em Azure Machine Learning, consulte o artigo sobre [os modelos Interpret ML](https://docs.microsoft.com/azure/machine-learning/how-to-machine-learning-interpretability-aml#generate-feature-importance-values-via-remote-runs).

## <a name="results"></a>Resultados

Depois de o modelo ser treinado:


+ Para utilizar o modelo em outros oleodutos, selecione o módulo e selecione o ícone **do conjunto de dados 'Registar'** sob o separador **Saídas** no painel direito. Pode aceder a modelos guardados na paleta de módulos em **Datasets**.

+ Para utilizar o modelo na previsão de novos valores, conecte-o ao módulo ['Modelo de Pontuação',](./score-model.md) juntamente com novos dados de entrada.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 