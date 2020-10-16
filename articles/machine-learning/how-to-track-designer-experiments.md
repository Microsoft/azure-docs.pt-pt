---
title: Registar métricas no designer
titleSuffix: Azure Machine Learning
description: Monitorize as suas experiências de designers Azure ML. Ativar o registo utilizando o módulo Execut Python Script e ver os resultados registados no estúdio.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: bffbf32cf5faa936a00444f1f39facaf226b8ef2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90885974"
---
# <a name="enable-logging-in-azure-machine-learning-designer-pipelines"></a>Permitir o login nos oleodutos de designers de machine learning do Azure


Neste artigo, aprende-se a adicionar código de registo aos pipelines de design. Também aprende a visualizar esses registos usando o portal web do estúdio Azure Machine Learning.

Para obter mais informações sobre métricas de registo utilizando a experiência de autoria SDK, consulte [as métricas e métricas da experiência Monitor Azure ML](how-to-track-experiments.md).

## <a name="enable-logging-with-execute-python-script"></a>Ativar o registo com executar o script python

Utilize o módulo __de script Execute Python__ para permitir o registo em pipelines de design. Embora possa registar qualquer valor com este fluxo de trabalho, é especialmente útil registar métricas a partir do módulo __De Modelo avaliar__ para acompanhar o desempenho do modelo através de corridas.

O exemplo a seguir mostra como registar o erro médio quadrado de dois modelos treinados utilizando os módulos de Avaliação de Scripts Python e Desematado.

1. Ligue um módulo __de script de Python executado__ à saída do módulo Modelo __avaliar.__

    ![Conecte o módulo de script python executado para avaliar o módulo do modelo](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. Cole o seguinte código no editor de código __execute Python__ para registar o erro absoluto médio para o seu modelo treinado. Pode utilizar um padrão semelhante para registar qualquer outro valor no designer:

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.

        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        run.parent.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])

        # Log right output port result of Evaluate Model.
        run.parent.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])
    
        return dataframe1,
    ```
    
Este código utiliza o Azure Machine Learning Python SDK para registar valores. Utiliza Run.get_context para obter o contexto da corrente. Em seguida, regista valores para esse contexto com o método run.parent.log(). `parent`Utiliza-se para registar valores para o gasoduto dos pais em vez do funcionado do módulo.

Para obter mais informações sobre como utilizar o Python SDK para registar valores, consulte [Ativar o registo em treinos Azure ML](how-to-track-experiments.md).

## <a name="view-logs"></a>Ver registos

Depois de concluído o curso do gasoduto, pode ver o *Mean_Absolute_Error* na página Experimentos.

1. Navegue para a secção **de Experiências.**
1. Selecione a sua experiência.
1. Selecione a execução da sua experiência que pretende visualizar.
1. Selecione **Métricas**.

    ![Ver métricas de execução no estúdio](./media/how-to-track-experiments/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar registos no designer. Para os próximos passos, consulte estes artigos relacionados:


* Saiba como resolver os oleodutos de designers de resolução de problemas, consulte [debug & os oleodutos ML de resolução de problemas.](how-to-debug-pipelines.md#azure-machine-learning-designer)
* Aprenda a utilizar o Python SDK para registar métricas na experiência de autoria SDK, consulte [Enable logging in Azure ML training runs](how-to-track-experiments.md).
