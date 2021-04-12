---
title: Utilize parâmetros de gasoduto no designer para construir oleodutos versáteis
titleSuffix: Azure Machine Learning
description: Como utilizar parâmetros de pipeline no designer de aprendizagem automática Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 04/09/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 30ae737a170c337fe6be51521aeb358cdcebd44b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107346"
---
# <a name="use-pipeline-parameters-in-the-designer-to-build-versatile-pipelines"></a>Utilize parâmetros de gasoduto no designer para construir oleodutos versáteis

Utilize parâmetros de gasoduto para construir gasodutos flexíveis no designer. Os parâmetros do pipeline permitem-lhe definir valores dinamicamente em tempo de execução para encapsular a lógica do gasoduto e reutilizar os ativos.

Os parâmetros do gasoduto são especialmente úteis quando reenviam uma corrida de [gasodutos, modelos de reconversão](how-to-retrain-designer.md)ou [efetuar previsões de lotes](how-to-run-batch-predictions-designer.md).

Neste artigo, aprende-se a fazer o seguinte:

> [!div class="checklist"]
> * Criar parâmetros de gasoduto
> * Eliminar e gerir os parâmetros do gasoduto
> * O gasoduto aciona enquanto ajusta os parâmetros do gasoduto

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning. Ver [Criar um espaço de trabalho de aprendizagem de máquinas Azure](how-to-manage-workspace.md).

* Para uma introdução guiada ao designer, complete o tutorial do [designer.](tutorial-designer-automobile-price-train-score.md) 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-pipeline-parameter"></a>Criar parâmetro de pipeline

Existem três formas de criar um parâmetro de pipeline no designer:
- Crie um parâmetro de pipeline no painel de definições e ligue-o a um módulo.
- Promover um parâmetro do módulo para um parâmetro de pipeline.
- Promover um conjunto de dados para um parâmetro de pipeline

> [!NOTE]
> Os parâmetros do gasoduto só suportam tipos básicos de dados `int` `float` como , e `string` .

### <a name="option-1-create-a-pipeline-parameter-in-the-settings-panel"></a>Opção 1: Criar um parâmetro de pipeline no painel de definições

Nesta secção, cria-se um parâmetro de tubagem no painel de definições.

Neste exemplo, cria-se um parâmetro de pipeline que define como um gasoduto preenche dados em falta utilizando o módulo **de dados em falta.**

1. Ao lado do nome do projeto do seu pipeline, selecione o ícone de **engrenagem** para abrir o painel **definições.**

1. Na secção **de parâmetros Pipeline,** selecione o **+** ícone.

1.  Introduza um nome para o parâmetro e um valor predefinido. 

    Por exemplo, insira `replace-missing-value` como nome de parâmetro e como valor `0` predefinido.

   ![Screenshot que mostra como criar um parâmetro de pipeline](media/how-to-use-pipeline-parameter/create-pipeline-parameter.png)


Depois de criar um parâmetro de pipeline, deve [fixá-lo ao parâmetro do módulo](#attach-module-parameter-to-pipeline-parameter) que pretende definir dinamicamente.

### <a name="option-2-promote-a-module-parameter"></a>Opção 2: Promover um parâmetro de módulo

A forma mais simples de criar um parâmetro de pipeline para um valor de módulo é promover um parâmetro do módulo. Utilize os seguintes passos para promover um parâmetro do módulo para um parâmetro de gasoduto:

1. Selecione o módulo a que pretende anexar um parâmetro de pipeline.
1. No painel de detalhes do módulo, o rato sobre o parâmetro que pretende especificar.
1. Selecione as elipses **(...**) que aparecem.
1. **Selecione Adicionar ao parâmetro do gasoduto**.

    ![Screenshot que mostra como promover o parâmetro do módulo para o parâmetro do pipeline1](media/how-to-use-pipeline-parameter/promote-module-para-to-pipeline-para.png)

1. Introduza um nome de parâmetro e valor predefinido.
1. Selecione **Guardar**

Pode agora especificar novos valores para este parâmetro sempre que submeter este pipeline.

### <a name="option-3-promote-a-dataset-to-a-pipeline-parameter"></a>Opção 3: Promover um conjunto de dados para um parâmetro de pipeline

Se pretender submeter o seu pipeline com conjuntos de dados variáveis, deve promover o seu conjunto de dados para um parâmetro de pipeline:

1. Selecione o conjunto de dados que pretende transformar num parâmetro de pipeline.

1. No painel de pormenor do conjunto de dados, verifique **o conjunto como parâmetro do pipeline**.

   ![Screenshot que mostra como definir conjunto de dados como parâmetro de pipeline](media/how-to-use-pipeline-parameter/set-dataset-as-pipeline-parameter.png)

Pode agora especificar um conjunto de dados diferente utilizando o parâmetro do pipeline da próxima vez que executar o pipeline.

## <a name="attach-and-detach-module-parameter-to-pipeline-parameter"></a>Fixe e desprenda o parâmetro do módulo ao parâmetro do gasoduto 

Nesta secção, aprenderá a anexar e separar o parâmetro do módulo ao parâmetro do gasoduto.

### <a name="attach-module-parameter-to-pipeline-parameter"></a>Fixe o parâmetro do módulo ao parâmetro do gasoduto

Pode fixar os mesmos parâmetros do módulo duplicado ao mesmo parâmetro do gasoduto se quiser alterar o valor ao desencadear o percurso do gasoduto.

O exemplo a seguir tem duplicado o módulo **de dados em falta** de limpeza. Para cada módulo **de dados em falta,** fixe o **valor de substituição** ao parâmetro do pipeline **substitua o valor em falta:**

1. Selecione o módulo **de dados em falta.**

1. No painel de detalhes do módulo, à direita da tela, coloque o **modo de Limpeza** em "Valor de substituição personalizado".

1. Rato sobre o campo **de valor substituto.**

1. Selecione as elipses **(...**) que aparecem.

1. Selecione o parâmetro do gasoduto `replace-missing-value` .

   ![Screenshot que mostra como anexar um parâmetro de pipeline](media/how-to-use-pipeline-parameter/attach-replace-value-to-pipeline-parameter.png)

Colocou com sucesso o campo **de valor de substituição** ao seu parâmetro de pipeline. 


### <a name="detach-module-parameter-to-pipeline-parameter"></a>Desprender o parâmetro do módulo para o parâmetro do gasoduto

Depois de fixar **o valor de substituição** ao parâmetro do gasoduto, não é accuível.

Pode separar o parâmetro do módulo para o parâmetro do gasoduto clicando nas elipses **(...**) ao lado do parâmetro do módulo e selecionar **separar-se do parâmetro do gasoduto**.

 ![Screenshot que mostra não accutilizável após anexar ao parâmetro do gasoduto](media/how-to-use-pipeline-parameter/non-actionable-module-parameter.png)

## <a name="update-and-delete-pipeline-parameters"></a>Atualizar e eliminar parâmetros de gasoduto

Nesta secção, aprende-se a atualizar e eliminar parâmetros de pipeline.

### <a name="update-pipeline-parameters"></a>Atualizar parâmetros de gasoduto

Utilize os seguintes passos para atualizar um parâmetro do pipeline do módulo:

1. Na parte superior da tela, selecione o ícone de engrenagem.
1. Na secção **de parâmetros Pipeline,** pode visualizar e atualizar o nome e o valor predefinido para todo o seu parâmetro de pipeline.

### <a name="delete-a-dataset-pipeline-parameter"></a>Eliminar um parâmetro de pipeline de conjunto de dados

Utilize os seguintes passos para eliminar um parâmetro do pipeline do conjunto de dados:

1. Selecione o módulo de conjunto de dados.
1. Desmarque a opção **Definir como parâmetro de gasoduto**.


### <a name="delete-module-pipeline-parameters"></a>Eliminar parâmetros de pipeline de módulos

Utilize os seguintes passos para eliminar um parâmetro do pipeline do módulo:

1. Na parte superior da tela, selecione o ícone de engrenagem.

1. Selecione as elipses **(...**) ao lado do parâmetro do gasoduto.

    Esta vista mostra-lhe quais os módulos a que o parâmetro do gasoduto está ligado.

    ![Screenshot que mostra o parâmetro do pipeline atual aplicado a um módulo](media/how-to-use-pipeline-parameter/delete-pipeline-parameter2.png)

1. **Selecione Eliminar o parâmetro** para eliminar o parâmetro do pipeline.

    > [!NOTE]
    > A eliminação de um parâmetro de gasoduto fará com que todos os parâmetros do módulo ligados sejam separados e o valor dos parâmetros do módulo separados manterá o valor atual do parâmetro do gasoduto.     

## <a name="trigger-a-pipeline-run-with-pipeline-parameters"></a>Desencadear um gasoduto com parâmetros de gasoduto 

Nesta secção, aprende-se a submeter uma corrida de gasodutos enquanto define os parâmetros do gasoduto.

### <a name="resubmit-a-pipeline-run"></a>Reenviar uma corrida de gasoduto

Depois de submeter um oleoduto com parâmetros de gasoduto, pode reenviar uma conduta com diferentes parâmetros:

1. Vá para a página de detalhes do pipeline. Na janela **de visão geral do pipeline run,** pode verificar os parâmetros e valores do gasoduto atuais.

1. Selecione **Resubmit**.
1. Na **execução do gasoduto de configuração,** especifique os seus novos parâmetros de conduta. 

![Screenshot que mostra o oleoduto de resubmissão com parâmetros de pipeline](media/how-to-use-pipeline-parameter/resubmit-pipeline-run.png)

### <a name="use-published-pipelines"></a>Utilizar oleodutos publicados

Também pode publicar um oleoduto para utilizar os seus parâmetros de gasoduto. Um **oleoduto publicado** é um oleoduto que foi implantado para um recurso computacional, que as aplicações do cliente podem invocar através de um ponto final REST.

Os pontos finais publicados são especialmente úteis para cenários de reconversão e previsão de lotes. Para obter mais informações, consulte [Como reconverter os modelos no designer](how-to-retrain-designer.md) ou executar [previsões de lotes no designer.](how-to-run-batch-predictions-designer.md)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a criar parâmetros de pipeline no designer. Em seguida, veja como pode utilizar parâmetros de gasoduto para [retreinar modelos](how-to-retrain-designer.md) ou realizar [previsões de lotes](how-to-run-batch-predictions-designer.md).

Também pode aprender a [utilizar pipelines programáticamente com o SDK.](how-to-deploy-pipelines.md)
