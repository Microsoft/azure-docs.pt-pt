---
title: Como um modelo se torna um serviço web
titleSuffix: ML Studio (classic) - Azure
description: Uma visão geral da mecânica de como o seu modelo Azure Machine Learning Studio (clássico) progride de uma experiência de desenvolvimento para um serviço Web.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: e6a8fe7771776a92b3c28a188bc352e3bb105313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217925"
---
# <a name="how-a-machine-learning-studio-classic-model-progresses-from-an-experiment-to-a-web-service"></a>Como um modelo de Machine Learning Studio (clássico) progride de uma experiência para um serviço Web

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
O Azure Machine Learning Studio (clássico) fornece uma tela interativa que lhe permite desenvolver, executar, testar e iterar uma ***experiência*** que representa um modelo de análise preditiva. Há uma grande variedade de módulos disponíveis que podem:

* Insere os dados na sua experiência
* Manipular os dados
* Treine um modelo usando algoritmos de aprendizagem automática
* Pontuar o modelo
* Avaliar os resultados
* Valores finais de saída

Uma vez satisfeito com a sua experiência, pode implementá-lo como um ***serviço Web Classic Azure Machine Learning*** ou um novo serviço Web de ***aprendizagem automática Azure*** para que os utilizadores possam enviá-lo novos dados e receber resultados de volta.

Neste artigo, damos uma visão geral da mecânica de como o seu modelo de Machine Learning evolui de uma experiência de desenvolvimento para um serviço Web operacionalizado.

> [!NOTE]
> Existem outras formas de desenvolver e implementar modelos de machine learning, mas este artigo está focado na forma como utiliza o Machine Learning Studio (clássico). Por exemplo, para ler uma descrição de como criar um serviço Web clássico preditivo com R, consulte o post de blog [Build & implementar aplicações web preditivas utilizando o estúdio RStudio e Azure Machine Learning.](https://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx)
>
>

Enquanto o Azure Machine Learning Studio (clássico) é projetado para ajudá-lo a desenvolver e implementar um modelo de *análise preditiva,* é possível usar o Studio (clássico) para desenvolver uma experiência que não inclua um modelo de análise preditiva. Por exemplo, uma experiência pode apenas inseri-lo dados, manipulá-lo e, em seguida, obter os resultados. Tal como uma experiência de análise preditiva, pode implementar esta experiência não preditiva como um serviço Web, mas é um processo mais simples porque a experiência não está a treinar ou a marcar um modelo de aprendizagem automática. Embora não seja o típico de usar o Studio (clássico) desta forma, vamos incluí-lo na discussão para que possamos dar uma explicação completa de como o Studio (clássico) funciona.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Desenvolvimento e implementação de um serviço Web preditivo
Aqui estão os estágios que uma solução típica segue à medida que o desenvolve e implementa utilizando o Machine Learning Studio (clássico):

![Fluxo de implantação](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Figura 1 - Estágios de um modelo de análise preditiva típico*

### <a name="the-training-experiment"></a>A experiência de treino
A ***experiência de formação*** é a fase inicial de desenvolvimento do seu serviço Web no Machine Learning Studio (clássico). O objetivo da experiência de treino é dar-lhe um lugar para desenvolver, testar, iterar e, eventualmente, treinar um modelo de aprendizagem automática. Pode até treinar vários modelos simultaneamente à medida que procura a melhor solução, mas assim que terminar a experiência, irá selecionar um único modelo treinado e eliminar o resto da experiência. Para um exemplo de desenvolvimento de uma experiência de análise preditiva, consulte [Desenvolver uma solução de análise preditiva para avaliação de risco de crédito no Azure Machine Learning Studio (clássico)](tutorial-part1-credit-risk.md).

### <a name="the-predictive-experiment"></a>A experiência preditiva
Assim que tiver um modelo treinado na sua experiência de treino, clique em **Configurar** o Web Service e selecione **Predictive Web Service** no Machine Learning Studio (clássico) para iniciar o processo de conversão da sua experiência de treino para uma ***experiência preditiva***. O objetivo da experiência preditiva é usar o seu modelo treinado para obter novos dados, com o objetivo de eventualmente se tornar operacionalizado como um serviço Azure Web.

Esta conversão é feita para si através dos seguintes passos:

* Converter o conjunto de módulos utilizados para treinar num único módulo e guardá-lo como um modelo treinado
* Eliminar quaisquer módulos estranhos não relacionados com pontuação
* Adicione portas de entrada e saída que o eventual serviço Web utilizará

Pode haver mais alterações que pretende fazer para ter a sua experiência preditiva pronta a ser implementada como um serviço Web. Por exemplo, se pretender que o serviço Web produza apenas um subconjunto de resultados, pode adicionar um módulo de filtragem antes da porta de saída.

Neste processo de conversão, a experiência de treino não é descartada. Quando o processo estiver concluído, você tem dois separadores em Studio (clássico): um para a experiência de treino e um para a experiência preditiva. Desta forma, pode fazer alterações na experiência de treino antes de implementar o seu serviço Web e reconstruir a experiência preditiva. Ou podes guardar uma cópia da experiência de treino para começar outra linha de experimentação.

> [!NOTE]
> Ao clicar no **Serviço Web Preditivo,** inicia-se um processo automático para converter a sua experiência de treino numa experiência preditiva, e isso funciona bem na maioria dos casos. Se a sua experiência de treino for complexa (por exemplo, tem múltiplos caminhos para o treino que se junta), pode preferir fazer esta conversão manualmente. Para mais informações, consulte [Como preparar o seu modelo para implantação no Azure Machine Learning Studio (clássico)](convert-training-experiment-to-scoring-experiment.md).
>
>

### <a name="the-web-service"></a>O serviço web
Assim que estiver satisfeito com a sua experiência preditiva, pode implementar o seu serviço como um serviço Web Clássico ou um novo serviço Web baseado no Azure Resource Manager. Para operacionalizar o seu modelo implantando-o como um serviço Web clássico de *aprendizagem automática,* clique em **implementar o Serviço Web** e selecione Deploy Web Service **[Classic]**. Para implementar como *novo serviço Web de aprendizagem automática,* clique em implementar o Serviço **Web** e selecione implementar o Serviço **Web [Novo]**. Os utilizadores podem agora enviar dados para o seu modelo utilizando o serviço Web REST API e receber de volta os resultados. Para obter mais informações, veja [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Como consumir um serviço Web do Azure Machine Learning).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>O caso não típico: criar um serviço Web não preditivo
Se a sua experiência não treina um modelo de análise preditiva, então não precisa de criar uma experiência de treino e uma experiência de pontuação - há apenas uma experiência, e pode implantá-lo como um serviço Web. O Machine Learning Studio (clássico) deteta se a sua experiência contém um modelo preditivo analisando os módulos que utilizou.

Depois de ter escadolado a sua experiência e ficar satisfeito com isso:

1. Clique em **Configurar o Serviço Web** e selecione **Retreinar o Serviço Web** - os nódos de entrada e saída são adicionados automaticamente
2. Clique em **Executar**
3. Clique em **Implementar o Web Service** e selecione Implementar o Web Service **[Classic]** ou implementar o **Web Service [Novo]** dependendo do ambiente para o qual pretende implementar.

O seu serviço Web está agora implantado e pode aceder e geri-lo como um serviço Web preditivo.

## <a name="updating-your-web-service"></a>Atualizar o seu serviço web
Agora que implementou a sua experiência como um serviço Web, e se precisar atualizá-la?

Isso depende do que precisa de atualizar:

**Deseja alterar a entrada ou saída, ou pretende modificar a forma como o serviço Web manipula os dados**

Se não estiver a alterar o modelo, mas está apenas a mudar a forma como o serviço Web lida com os dados, pode editar a experiência preditiva e, em seguida, clicar em **Implementar o Web Service** e selecionar o Deploy Web Service **[Classic]** ou implementar o **Web Service [Novo]** novamente. O serviço Web está parado, a experiência preditiva atualizada é implementada e o serviço Web é reiniciado.

Aqui está um exemplo: Suponha que a sua experiência preditiva devolve toda a linha de dados de entrada com o resultado previsto. Pode decidir que deseja que o serviço Web devolva apenas o resultado. Assim, pode adicionar um módulo **de Colunas** de Projeto na experiência preditiva, mesmo antes da porta de saída, para excluir colunas que não o resultado. Quando clicar em **Implementar o Serviço Web** e selecionar implementar o Web Service **[Classic]** ou implementar o **Web Service [Novo]** novamente, o serviço Web é atualizado.

**Você quer retreinar o modelo com novos dados**

Se quiser manter o seu modelo de aprendizagem automática, mas gostaria de retreiná-lo com novos dados, tem duas opções:

1. **Retreine o modelo enquanto o serviço Web está em execução** - Se quiser retreinar o seu modelo enquanto o serviço Web preditivo está em execução, pode fazê-lo fazendo algumas modificações na experiência de treino para torná-lo uma experiência de ***reconversão***, então pode implantá-lo como um serviço web de ** *reconversão.* ** Para obter instruções sobre como fazê-lo, consulte os [modelos Retrain Machine Learning programáticamente](/azure/machine-learning/studio/retrain-machine-learning-model).
2. **Volte à experiência de treino original e use diferentes dados** de treino para desenvolver o seu modelo - A sua experiência preditiva está ligada ao serviço Web, mas a experiência de treino não está diretamente ligada desta forma. Se modificar a experiência de treino original e clicar em Configurar o **Serviço Web,** criará uma *nova* experiência preditiva que, quando implementada, criará um *novo* serviço Web. Não atualiza apenas o serviço web original.

   Se precisar modificar a experiência de treino, abra-a e clique em **Guardar Como** para fazer uma cópia. Isto deixará intacto a experiência de treino original, experiência preditiva e serviço Web. Agora pode criar um novo serviço Web com as suas alterações. Uma vez implementado o novo serviço Web, pode então decidir se deve parar o serviço Web anterior ou mantê-lo a funcionar ao lado do novo.

**Quer treinar um modelo diferente.**

Se quiser fazer alterações na sua experiência preditiva original, como selecionar um algoritmo de aprendizagem automática diferente, experimentar um método de treino diferente, etc., então precisa seguir o segundo procedimento acima descrito para retreinar o seu modelo: abrir a experiência de treino, clicar em **Save As** para fazer uma cópia, e depois começar a seguir o novo caminho de desenvolvimento do seu modelo, criar a experiência preditiva e implementar o serviço web. Isto criará um novo serviço Web não relacionado com o original - pode decidir qual, ou ambos, continuará a funcionar.

## <a name="next-steps"></a>Passos seguintes
Para mais detalhes sobre o processo de desenvolvimento e experimentação, consulte os seguintes artigos:

* converter a experiência - [Como preparar o seu modelo para implantação no Azure Machine Learning Studio (clássico)](convert-training-experiment-to-scoring-experiment.md)
* implementação do serviço Web - [Implementar um serviço web Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* retreinando o modelo - [Retreinar modelos de Machine Learning programáticamente](/azure/machine-learning/studio/retrain-machine-learning-model)

Por exemplo de todo o processo, consulte:

* [Tutorial de aprendizagem automática: Crie a sua primeira experiência no Azure Machine Learning Studio (clássico)](create-experiment.md)
* [Walkthrough: Desenvolver uma solução de análise preditiva para avaliação de risco de crédito em Azure Machine Learning](tutorial-part1-credit-risk.md)

