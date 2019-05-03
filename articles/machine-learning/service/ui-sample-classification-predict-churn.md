---
title: 'Classificação: Prever o volume de alterações, apetência e de segurança de vendas '
titleSuffix: Azure Machine Learning service
description: Este exemplo de experiência de visual interface mostra a predição de classificador binário de alterações a dados, uma tarefa comum para o gerenciamento de relacionamento de cliente (CRM).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 1cb533348236905b7c4e9b58968041745af0e71b
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028444"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Exemplo 5 - classificação: Prever o volume de alterações, apetência e de segurança de vendas 

Este exemplo de experiência de visual interface mostra a predição de classificador binário de fluxo de dados, apetência e up-vender, uma tarefa comum para o gerenciamento de relacionamento de cliente (CRM).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecione o **aberto** botão para a experimentação de exemplo 5.

    ![Abra a experimentação](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Dados

Os dados que usamos para esta fase experimental são da KDD Cup 2009. O conjunto de dados tem 50 000 linhas e colunas de funcionalidades 230. A tarefa é prever o volume de alterações, apetência e venda de segurança para os clientes que utilizam estas funcionalidades. Consulte a [Web site do concurso KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009) para obter mais detalhes sobre os dados e a tarefa.

## <a name="experiment-summary"></a>Resumo de experimentação

Este é o gráfico de experimentação concluída:

![Gráfico de experimentação](./media/ui-sample-classification-predict-churn/experiment-graph.png)

Em primeiro lugar, podemos fazer algum processamento de dados simple.

- O conjunto de dados não processado contém muitos valores em falta. Vamos utilizar o **Clean Missing Data** módulo para a falta de substituir os valores com 0.

    ![Limpar o conjunto de dados](./media/ui-sample-classification-predict-churn/cleaned-dataset.png)

- As funcionalidades e correspondentes de alterações, apetência, e as etiquetas de vendas de up estão em diferentes conjuntos de dados. Vamos utilizar o **adicionar colunas** módulo para acrescentar as colunas de etiqueta para as colunas de funcionalidade. A primeira coluna **Col1**, é a coluna de etiqueta. O resto das colunas, **Var1**, **Var2**e assim por diante, são colunas de funcionalidades.
 
    ![Adicionar o conjunto de dados de coluna](./media/ui-sample-classification-predict-churn/added-column1.png)

- Vamos utilizar o **Split Data** módulo para dividir o conjunto de dados em treinar e testar conjuntos.


    Em seguida, usamos o classificador binário da árvore de decisões elevada com os parâmetros de padrão para criar modelos de previsão. Criamos um modelo por tarefa, ou seja, um modelo de cada para prever vendas de up apetência e alterações a dados.

## <a name="results"></a>Resultados

Visualizar os resultados do **Evaluate Model** módulo para ver o desempenho do modelo do conjunto de teste. Para a tarefa de cópia de segurança de vendas, a curva cor MULTICLASSE mostra que o modelo não faz melhor do que um modelo aleatório. A área abaixo da curva (AUC) é 0.857. Com o limiar 0,5, a precisão é 0,7, a solicitação de recolhimento é 0.463 e a pontuação de F1 é 0.545.

![Avaliar os resultados](./media/ui-sample-classification-predict-churn/evaluate-result.png)

 Pode mover o **limiar** controlo de deslize e veja as métricas de alterar para a tarefa de classificação binária.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Explore os outros exemplos disponíveis para a interface visual:

- [Exemplo 1 - regressão: Prever o preço de um automóvel](ui-sample-regression-predict-automobile-price-basic.md)
- [Exemplo 2 - regressão: Compare os algoritmos de previsão de preços de automóveis](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemplo 3 - classificação: Prever o risco de crédito](ui-sample-classification-predict-credit-risk-basic.md)
- [Exemplo 4 - classificação: Prever o risco de crédito (custo confidencial)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
