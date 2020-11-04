---
title: 'ML Studio (clássico): iniciar experiências a partir de exemplos - Azure'
description: Aprenda a usar experiências de aprendizagem automática de exemplo para criar novas experiências com a Azure AI Gallery e o Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: sample
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/05/2018
ms.openlocfilehash: cfee8a32c54bff5aecb1e298db8b4814255418b0
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325746"
---
# <a name="create-azure-machine-learning-studio-classic-experiments-from-working-examples-in-azure-ai-gallery"></a>Criar experiências do Azure Machine Learning Studio (clássico) a partir de exemplos de trabalho na Galeria Azure AI

**APLICA-SE A:** ![ Aplica-se a. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Não se aplica a. ](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  




Saiba como iniciar experimentações de exemplo a partir da [Galeria de IA do Azure](https://gallery.azure.ai/), em vez de criar experimentações de machine learning do zero. Pode utilizar os exemplos para criar a sua própria solução de machine learning.

A galeria tem experiências exemplo da equipa do Microsoft Azure Machine Learning Studio (clássico), bem como exemplos partilhados pela comunidade machine learning. Também pode colocar questões ou publicar comentários sobre experimentações.

Para saber como utilizar a galeria, veja o vídeo de 3 minutos [Copy other people's work to do data science (Copiar o trabalho de outras pessoas para efetuar ciência de dados)](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) da série [Data Science for Beginners (Ciência de Dados para Principiantes)](data-science-for-beginners-the-5-questions-data-science-answers.md).



## <a name="find-an-experiment-to-copy-in-azure-ai-gallery"></a>Encontrar uma experimentação para copiar na Galeria de IA do Azure
Para ver as experimentações que estão disponíveis, aceda à [Galeria](https://gallery.azure.ai/) e clique em **Experimentações** na parte superior da página.

### <a name="find-the-newest-or-most-popular-experiments"></a>Localizar as experimentações mais recentes ou mais populares
Nesta página, pode ver as experimentações **Recentemente adicionadas** ou deslocar para baixo para ver **O que é popular** ou as **experimentações Microsoft populares** mais recentes.

### <a name="look-for-an-experiment-that-meets-specific-requirements"></a>Procurar uma experimentação que cumpra os requisitos específicos
Para procurar todas as experimentações:

1. Clique em **Procurar tudo** na parte superior da página.
2. No lado esquerdo, em **Refinar por** na secção **Categorias** , selecione **Experimentação** para ver todas as experimentações na Galeria.
3. Pode encontrar experimentações que cumprem os requisitos de duas formas diferentes:
   * **Selecione filtros no lado esquerdo.** Por exemplo, para procurar experimentações que utilizem um algoritmo de deteção de anomalias baseado em PCA: em **Categorias** , clique em **Experimentação**. Em seguida, em **Algoritmos Utilizados** , clique em **Mostrar tudo** e, na caixa de diálogo, escolha **Deteção de Anomalias Baseada em PCA**. Poderá ter de deslocar para baixo para vê-la.<br></br>
     ![Selecionar filtros](./media/sample-experiments/choose-an-algorithm.png)
   * **Utilize a caixa de pesquisa.** Por exemplo, para procurar experimentações que tenham feito parte da Microsoft e estejam relacionadas com o reconhecimento de dígitos que utilizam um algoritmo da máquina de vetor com suporte de classe dois, introduza "reconhecimento de dígitos" na caixa de pesquisa. Em seguida, selecione os filtros **Experiment,** **apenas conteúdo da Microsoft** e **máquina de vetor de suporte de duas classes** :<br></br>
     ![Utilizar a caixa de pesquisa](./media/sample-experiments/search-for-experiments.png)
4. Clique numa experimentação para saber mais acerca da mesma.
5. Para executar e/ou modificar a experimentação, clique em **Abrir no Studio** na página de experimentação. <br></br>

    ![Experimentação de exemplo](./media/sample-experiments/example-experiment.png)

## <a name="create-a-new-experiment-using-an-example-as-a-template"></a>Criar uma nova experimentação com um exemplo como modelo
Você também pode criar uma nova experiência no Machine Learning Studio (clássico) usando um exemplo de Galeria como um modelo.

1. Inicie sessão com as credenciais da sua conta Microsoft no [Studio](https://studio.azureml.net) e, em seguida, clique em **Novo** para criar uma experimentação.
2. Navegue no conteúdo de exemplos e clique num deles.

Uma nova experiência é criada no seu espaço de trabalho Machine Learning Studio (clássico) usando a experiência de exemplo como modelo.

## <a name="next-steps"></a>Passos seguintes
* [Importar dados de várias origens](import-data.md)
* [Implementar um serviço Web Machine Learning](deploy-a-machine-learning-web-service.md)