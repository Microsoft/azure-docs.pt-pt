---
title: 'Preparar o modelo: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o **modelo de formação** módulo no serviço do Azure Machine Learning para preparar um modelo de classificação ou regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 29d0f698456b83c1520a92bc7df47b26540325f4
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028114"
---
# <a name="train-model-module"></a>Módulo do modelo de formação

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para preparar um modelo de classificação ou regressão. Treinamento ocorre depois de ter definido um modelo e definir os parâmetros e requer que os dados marcados. Também pode utilizar **Train Model** para voltar a preparar um modelo existente com novos dados. 

## <a name="how-the-training-process-works"></a>Como funciona o processo de treinamento

No Azure Machine Learning, criando e usando um modelo de machine learning é, normalmente, um processo em três etapas. 

1. Configurar um modelo, ao escolher um determinado tipo de algoritmo e definir seu parâmetros ou hiperparâmetros. Escolha qualquer um dos seguintes tipos de modelo: 

    + **Classificação** modelos, com base em redes neurais, árvores de decisão e as florestas de decisão e outros algoritmos.
    + **Regressão** modelos, que pode incluir a regressão linear padrão ou outros algoritmos, incluindo redes neurais e regressão Bayesianos, que utilizar.  

2. Forneça um conjunto de dados que tem o nome e tem dados compatíveis com o algoritmo. Ligar os dados e o modelo para **Train Model**.

    Qual é o treinamento produz é um formato binário específico, iLearner, que encapsula os padrões de estatísticos, aprendidos a partir dos dados. Não é possível modificar diretamente ou ler esse formato; No entanto, outros módulos podem utilizar este modelo preparado. 
    
    Também pode ver as propriedades do modelo. Para obter mais informações, consulte a secção de resultados.

3. Após a conclusão do treinamento, utilizar o modelo preparado com uma da [módulos de classificação](./score-model.md), para fazer previsões sobre novos dados.

## <a name="how-to-use-train-model"></a>Como utilizar **Train Model**  
  
1.  No Azure Machine Learning, configure um modelo de classificação ou um modelo de regressão.
    
2. Adicionar a **Train Model** módulo para a experimentação.  Pode encontrar este módulo sob o **Machine Learning** categoria. Expanda **Train**e, em seguida, arraste o **Train Model** módulo na sua experimentação.
  
3.  Na entrada da esquerda, anexe o modo inoportuno. Anexar o conjunto de dados de formação à entrada da direita de **Train Model**.

    O conjunto de dados de treinamento tem de conter uma coluna de etiqueta. Quaisquer linhas sem rótulos são ignoradas.
  
4.  Para **coluna de etiqueta**, clique em **iniciar Seletor de colunas**e escolha uma única coluna que contém o modelo pode utilizar para formação de resultados.
  
    - Para problemas de classificação, a coluna de etiqueta tem de conter **categóricos** valores ou **discretos** valores. Alguns exemplos podem ser uma classificação de Sim/Não, um código de classificação de doenças ou nome ou um grupo de renda.  Se escolher uma coluna noncategorical, o módulo irá devolver um erro durante o treinamento.
  
    -   Para problemas de regressão, a coluna de etiqueta tem de conter **numérico** dados que representa a variável de resposta. O ideal é que os dados numéricos representam um dimensionamento contínuo. 
    
    Exemplos podem ser uma pontuação de risco de crédito, a hora prevista a falhas para uma unidade de disco rígido ou o número previsto de chamadas para um call center num determinado dia ou hora.  Se não escolher uma coluna numérica, poderá receber um erro.
  
    -   Se não especificar as colunas de etiqueta a utilizar, o Azure Machine Learning irá tentar inferir o que é a coluna de etiqueta adequada, utilizando os metadados do conjunto de dados. Se ele escolhe a coluna errada, utilize o Seletor de coluna para corrigi-lo.
  
    > [!TIP] 
    > Se tiver problemas ao utilizar o Seletor de colunas, consulte o artigo [Select Columns in Dataset](./select-columns-in-dataset.md) para obter dicas. Ele descreve alguns cenários e dicas para usar comuns a **com regras** e **pelo nome** opções.
  
5.  Execute a experimentação. Se tiver uma grande quantidade de dados, isto pode demorar algum tempo.

## <a name="bkmk_results"></a> Resultados

Depois do modelo é preparado:

+ Para ver os parâmetros de modelo e os pesos de funcionalidade, clique com botão direito de saída e selecione **Visualize**.
+ Para utilizar o modelo no outras experiências, o modelo com o botão direito e selecione **guardar modelo**. Escreva um nome para o modelo. 

    Esta ação guarda o modelo como um instantâneo que não é atualizado pelo repetidas execuções da experimentação.
+ Para utilizar o modelo de previsão novos valores, ligá-la para o [modelo de pontuação](./score-model.md) módulo, juntamente com dados de entrada novo.


## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 