---
title: 'Tutorial: Predict automobile price with the designer'
titleSuffix: Azure Machine Learning
description: Learn how to train, score, and deploy a machine learning model by using a drag-and-drop interface. This tutorial is part one of a two-part series on predicting automobile prices by using linear regression.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: ee08ba61aec23078227c40b92771d1728040c4cf
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228418"
---
# <a name="tutorial-predict-automobile-price-with-the-designer-preview"></a>Tutorial: Predict automobile price with the designer (preview)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

In this two-part tutorial, you learn how to use the Azure Machine Learning designer to develop and deploy a predictive analytics solution that predicts the price of any car. 

In part one, you set up your environment, drag modules onto an interactive canvas, and connect them together to create an Azure Machine Learning pipeline.

In part one of the tutorial, you'll learn how to:

> [!div class="checklist"]
> * Create a new pipeline.
> * Import data.
> * Prepare data.
> * Train a machine learning model.
> * Evaluate a machine learning model.

In [part two](tutorial-designer-automobile-price-deploy.md) of the tutorial, you'll learn how to deploy your predictive model as a real-time inferencing endpoint to predict the price of any car based on technical specifications you send it. 

> [!NOTE]
>A completed version of this tutorial is available as a sample pipeline.
>
>To find it, go to the designer in your workspace. In the **New pipeline** section, select **Sample 1 - Regression: Automobile Price Prediction(Basic)** .

## <a name="create-a-new-pipeline"></a>Create a new pipeline

Azure Machine Learning pipelines organize multiple, dependent machine learning and data processing steps into a single resource. Pipelines help you organize, manage, and reuse complex machine learning workflows across projects and users. To create an Azure Machine Learning pipeline, you need an Azure Machine Learning workspace. In this section, you learn how to create both these resources.

### <a name="create-a-new-workspace"></a>Create a new workspace

If you have an Azure Machine Learning workspace with an Enterprise edition, [skip to the next section](#create-the-pipeline).

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal-enterprise.md)]

### <a name="create-the-pipeline"></a>Criar o pipeline

1. Sign in to [ml.azure.com](https://ml.azure.com), and select the workspace you want to work with.

1. Select **Designer**.

    ![Screenshot of the visual workspace showing how to access the designer](./media/tutorial-designer-automobile-price-train-score/launch-visual-interface.png)

1. Select **Easy-to-use prebuilt modules**.

1. Select the default pipeline name **Pipeline-Created-on** at the top of the canvas. Rename it to something meaningful. An example is *Automobile price prediction*. O nome não tem de ser exclusivo.

## <a name="import-data"></a>Importar dados

There are several sample datasets included in the designer for you to experiment with. For this tutorial, use **Automobile price data (Raw)** . 

1. To the left of the pipeline canvas is a palette of datasets and modules. Select **Datasets**, and then view the **Samples** section to view the available sample datasets.

1. Select the dataset **Automobile price data (Raw)** , and drag it onto the canvas.

   ![Drag data to canvas](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Ver os dados

You can visualize the data to understand the dataset that you'll use.

1. Select the **Automobile price data (Raw)** module.

1. In the properties pane to the right of the canvas, select **Outputs**.

1. Select the graph icon to visualize the data.

    ![Ver os dados](./media/tutorial-designer-automobile-price-train-score/visualize-data.png)

1. Select the different columns in the data window to view information about each one.

    Each row represents an automobile, and the variables associated with each automobile appear as columns. There are 205 rows and 26 columns in this dataset.

## <a name="prepare-data"></a>Preparar dados

Datasets typically require some preprocessing before analysis. You might have noticed some missing values when you inspected the dataset. These missing values must be cleaned so that the model can analyze the data correctly.

### <a name="remove-a-column"></a>Remover uma coluna

When you train a model, you have to do something about the data that's missing. In this dataset, the **normalized-losses** column is missing many values, so you exclude that column from the model altogether.

1. Enter **Select** in the search box at the top of the palette to find the **Select Columns in Dataset** module.

1. Drag the **Select Columns in Dataset** module onto the canvas. Drop the module below the dataset module.

1. Connect the **Automobile price data (Raw)** dataset to the **Select Columns in Dataset** module. Drag from the dataset's output port, which is the small circle at the bottom of the dataset on the canvas, to the input port of **Select Columns in Dataset**, which is the small circle at the top of the module.

    > [!TIP]
    > You create a flow of data through your pipeline when you connect the output port of one module to an input port of another.
    >

    ![Connect modules](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Select the **Select Columns in Dataset** module.

1. In the properties pane to the right of the canvas, select **Parameters** > **Edit column**.

1. Select the **+** to add a new rule.

1. From the drop-down menu, select **Exclude** and **Column names**.
    
1. Enter *normalized-losses* in the text box.

1. In the lower right, select **Save** to close the column selector.

    ![Exclude a column](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)
        
    The properties pane shows that the **normalized-losses** column is excluded.

1. Select the **Select Columns in Dataset** module. 

1. In the properties pane, select **Parameters** > **Comment** and enter *Exclude normalized losses*.

### <a name="clean-missing-data"></a>Clean missing data

Your dataset still has missing values after you remove the **normalized-losses** column. You can remove the remaining missing data by using the **Clean Missing Data** module.

> [!TIP]
> Cleaning the missing values from input data is a prerequisite for using most of the modules in the designer.

1. Enter **Clean** in the search box to find the **Clean Missing Data** module.

1. Drag the **Clean Missing Data** module to the pipeline canvas. Connect it to the **Select Columns in Dataset** module. 

1. In the properties pane, select **Remove entire row** under **Cleaning mode**.

1. In the properties pane **Comment** box, enter *Remove missing value rows*. 

    Your pipeline should now look something like this:
    
    ![Select-column](./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Train a machine learning model

Now that the data is processed, you can train a predictive model.

### <a name="select-an-algorithm"></a>Selecionar um algoritmo

*Classficação* e *regrssão* são dois tipos de algoritmos de machine learning supervisionados. Classification predicts an answer from a defined set of categories, such as a color like red, blue, or green. A regressão é utilizada para prever um número.

Because you want to predict price, which is a number, you can use a regression algorithm. For this example, you use a linear regression model.

### <a name="split-the-data"></a>Split the data

Split your data into two separate datasets for training the model and testing it.

1. Enter **split data** in the search box to find the **Split Data** module. Connect it to the left port of the **Clean Missing Data** module.

1. Select the **Split Data** module.

1. In the properties pane, set the **Fraction of rows in the first output dataset** to 0.7.

    This option splits 70 percent of the data to train the model and 30 percent for testing it.

1. In the properties pane **Comment** box, enter *Split the dataset into training set (0.7) and test set (0.3)* .

### <a name="train-the-model"></a>Formar o modelo

Train the model by giving it a set of data that includes the price. The model scans through the data and looks for correlations between a car's features and its price to construct a model.

1. To select the learning algorithm, clear your module palette search box.

1. Expand **Machine Learning Algorithms**.
    
    This option displays several categories of modules that you can use to initialize learning algorithms.

1. Select **Regression** > **Linear Regression**, and drag it to the pipeline canvas.

1. Find and drag the **Train Model** module to the pipeline canvas. 

1. Connect the output of the **Linear Regression** module to the left input of the **Train Model** module.

1. Connect the training data output (left port) of the **Split Data** module to the right input of the **Train Model** module.

    ![Screenshot showing the correct configuration of the Train Model module. The Linear Regression module connects to left port of Train Model module and the Split Data module connects to right port of Train Model](./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png)

1. Select the **Train Model** module.

1. In the properties pane, select **Edit column** selector.

1. In the **Label column** dialog box, expand the drop-down menu and select **Column names**. 

1. In the text box, enter *price*. Price is the value that your model is going to predict.

    Your pipeline should look like this:

    ![Screenshot showing the correct configuration of the pipeline after adding the Train Model module.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>Evaluate a machine learning model

After you train your model by using 70 percent of the data, you can use it to score the other 30 percent to see how well your model functions.

1. Enter *score model* in the search box to find the **Score Model** module. Drag the module to the pipeline canvas. 

1. Connect the output of the **Train Model** module to the left input port of **Score Model**. Connect the test data output (right port) of the **Split Data** module to the right input port of **Score Model**.

1. Enter *evaluate* in the search box to find the **Evaluate Model** module. Drag the module to the pipeline canvas. 

1. Connect the output of the **Score Model** module to the left input of **Evaluate Model**. 

    The final pipeline should look something like this:

    ![Screenshot showing the correct configuration of the pipeline.](./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png)

### <a name="run-the-pipeline"></a>Executar o pipeline

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

### <a name="view-results"></a>Ver resultados

After the run completes, you can view the results of the pipeline run. 

1. Select the **Score Model** module to view its output.

1. In the properties pane, select **Outputs** > **Visualize**.

    Here you can see the predicted prices and the actual prices from the testing data.

    ![Screenshot of the output visualization highlighting the Scored Label column](./media/tutorial-designer-automobile-price-train-score/score-result.png)

1. Select the **Evaluate Model** module to view its output.

1. In the properties pane, select **Output** > **Visualize**.

The following statistics are shown for your model:

* **Mean Absolute Error (MAE)** : The average of absolute errors. An error is the difference between the predicted value and the actual value.
* **Root Mean Squared Error (RMSE)** : The square root of the average of squared errors of predictions made on the test dataset.
* **Erro relativo absoluto**: A média dos erros absolutos relativos à diferença absoluta entre os valores reais e a média de todos os valores reais.
* **Erro ao quadrado absoluto**: A média dos erros ao quadrado relativos à diferença ao quadrado entre os valores reais e a média de todos os valores reais.
* **Coefficient of Determination**: Also known as the R squared value, this statistical metric indicates how well a model fits the data.

Em cada uma das estatísticas de erros, quanto mais pequeno, melhor. A smaller value indicates that the predictions are closer to the actual values. For the coefficient of determination, the closer its value is to one (1.0), the better the predictions.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

In part one of this tutorial, you completed the following tasks:

* Criar um pipeline
* Preparar os dados
* Formar o modelo
* Score and evaluate the model

In part two, you'll learn how to deploy your model as a real-time endpoint.

> [!div class="nextstepaction"]
> [Continue to deploying models](tutorial-designer-automobile-price-deploy.md)
