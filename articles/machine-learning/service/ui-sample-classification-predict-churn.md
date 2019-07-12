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
ms.date: 05/10/2019
ms.openlocfilehash: 7d10d996febd0e31c9085bf5cb82324cce101c80
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606146"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Exemplo 5 - classificação: Prever o volume de alterações, apetência e de segurança de vendas 

Saiba como criar uma experimentação complexos de machine learning sem ter de escrever uma única linha de código usando a interface visual.

Esta experiência prepara três, **árvore de decisões elevada de duas classes** classificadores para prever a tarefas comuns para sistemas (CRM gestão) de relação do cliente: fluxo de dados, apetência e venda de cópia de segurança. Os valores de dados e as etiquetas são divididas em várias origens de dados e Misturou para tornar anónima a informação de cliente, no entanto, podemos ainda pode usar a interface visual para combinar conjuntos de dados e formar um modelo utilizando os valores ilegível.

Uma vez que estamos tentando responder à pergunta "Qual delas?" Isso é chamado um problema de classificação. No entanto, pode aplicar os mesmos passos Nesse experimento para lidar com qualquer tipo de problema do machine learning, independentemente de serem regressão, classificação, clustering e assim por diante.

Este é o gráfico concluído para esta fase experimental:

![Gráfico de experimentação](./media/ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecione o **aberto** botão para a experimentação de exemplo 5.

    ![Abra a experimentação](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Data

Os dados que usamos para esta fase experimental são da KDD Cup 2009. O conjunto de dados tem 50 000 linhas e colunas de funcionalidades 230. A tarefa é prever o volume de alterações, apetência e venda de segurança para os clientes que utilizam estas funcionalidades. Para obter mais informações sobre os dados e a tarefa, consulte a [Web site do concurso KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="experiment-summary"></a>Resumo de experimentação

Este exemplo de experiência de visual interface mostra a predição de classificador binário de fluxo de dados, apetência e up-vender, uma tarefa comum para o gerenciamento de relacionamento de cliente (CRM).

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
- [Exemplo 6 - classificação: Prever a atrasos de voos](ui-sample-classification-predict-flight-delay.md)