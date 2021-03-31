---
title: 'ML Studio (clássico): Como um modelo se torna um serviço web - Azure'
description: Uma visão geral da mecânica de como o seu modelo Azure Machine Learning Studio (clássico) progride de uma experiência de desenvolvimento para um serviço Web.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: 4e0f5786047977a319825aae9f3c7b89c0aa118b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100518628"
---
# <a name="how-a-machine-learning-studio-classic-model-progresses-from-an-experiment-to-a-web-service"></a>Como um modelo de Machine Learning Studio (clássico) progride de uma experiência para um serviço Web

**APLICA-SE A:** ![ Esta é uma marca de verificação, o que significa que este artigo se aplica ao Machine Learning Studio (clássico). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Este é um X, o que significa que este artigo se aplica ao Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

O Azure Machine Learning Studio (clássico) fornece uma tela interativa que permite desenvolver, executar, testar e iterar uma ***experiência*** que representa um modelo de análise preditiva. Há uma grande variedade de módulos disponíveis que podem:

* Inserir dados na sua experiência
* Manipular os dados
* Treine um modelo usando algoritmos de aprendizagem automática
* Classificar o modelo
* Avaliar os resultados
* Valores finais de saída

Uma vez satisfeito com a sua experiência, pode implantá-lo como um ***Classic Azure Machine Learning Web service** _ ou um _ New *_Azure Machine Learning Web service_** para que os utilizadores possam enviar-lhe novos dados e receber resultados de volta.

Neste artigo, damos uma visão geral da mecânica de como o seu modelo de Machine Learning progride de uma experiência de desenvolvimento para um serviço Web operacionalizado.

> [!NOTE]
> Existem outras formas de desenvolver e implementar modelos de machine learning, mas este artigo está focado na forma como usa o Machine Learning Studio (clássico). Por exemplo, para ler uma descrição de como criar um serviço web preditivo clássico com R, consulte o post de blog [Build & Deploy Predictive Web Apps Usando o estúdio RStudio e Azure Machine Learning](/archive/blogs/machinelearning/build-deploy-predictive-web-apps-using-rstudio-and-azure-ml).
>
>

Enquanto o Azure Machine Learning Studio (clássico) é projetado para ajudá-lo a desenvolver e implementar um *modelo de análise preditiva,* é possível usar o Studio (clássico) para desenvolver uma experiência que não inclua um modelo de análise preditiva. Por exemplo, uma experiência pode apenas inserir dados, manipulá-lo e, em seguida, obter os resultados. Tal como uma experiência de análise preditiva, pode implementar esta experiência não preditiva como um serviço Web, mas é um processo mais simples porque a experiência não é treinar ou marcar um modelo de aprendizagem automática. Embora não seja o típico de usar o Studio (clássico) desta forma, vamos incluí-lo na discussão para que possamos dar uma explicação completa de como o Studio (clássico) funciona.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Desenvolvimento e implantação de um serviço Web preditivo
Aqui estão as fases que uma solução típica segue à medida que a desenvolve e implementa usando o Machine Learning Studio (clássico):

![Fluxo de implantação](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Figura 1 - Estágios de um modelo típico de análise preditiva*

### <a name="the-training-experiment"></a>A experiência de treino
A ***experiência de formação*** é a fase inicial de desenvolvimento do seu serviço Web no Machine Learning Studio (clássico). O objetivo da experiência de treino é dar-lhe um lugar para desenvolver, testar, iterar e, eventualmente, treinar um modelo de aprendizagem automática. Pode até treinar vários modelos em simultâneo à medida que procura a melhor solução, mas assim que terminar de experimentar, irá selecionar um único modelo treinado e eliminar o resto da experiência. Para um exemplo de desenvolvimento de uma experiência de análise preditiva, consulte [Desenvolver uma solução de análise preditiva para avaliação de risco de crédito no Azure Machine Learning Studio (clássico)](tutorial-part1-credit-risk.md).

### <a name="the-predictive-experiment"></a>A experiência preditiva
Assim que tiver um modelo treinado na sua experiência de treino, clique em **Configurar o Serviço Web** e selecione **o Serviço Web Preditivo** no Machine Learning Studio (clássico) para iniciar o processo de conversão da sua experiência de treino para uma **_experiência preditiva._** O objetivo da experiência preditiva é usar o seu modelo treinado para obter novos dados, com o objetivo de eventualmente se tornar operacionalizado como um serviço Web Azure.

Esta conversão é feita para si através dos seguintes passos:

* Converter o conjunto de módulos utilizados para treinar num único módulo e guardá-lo como um modelo treinado
* Elimine quaisquer módulos extra-existentes não relacionados com a pontuação
* Adicione portas de entrada e saída que o eventual serviço Web irá utilizar

Pode haver mais alterações que pretende fazer para ter a sua experiência preditiva pronta a ser implementada como um serviço Web. Por exemplo, se pretender que o serviço Web produza apenas um subconjunto de resultados, pode adicionar um módulo de filtragem antes da porta de saída.

Neste processo de conversão, a experiência de formação não é descartada. Quando o processo estiver concluído, você tem dois separadores em Studio (clássico): um para a experiência de treino e um para a experiência preditiva. Desta forma, pode fazer alterações na experiência de treino antes de implementar o seu serviço Web e reconstruir a experiência preditiva. Ou pode guardar uma cópia da experiência de treino para iniciar outra linha de experimentação.

> [!NOTE]
> Quando clica no **Serviço Web Preditivo** inicia um processo automático para converter a sua experiência de treino numa experiência preditiva, e isso funciona bem na maioria dos casos. Se a sua experiência de treino for complexa (por exemplo, tem múltiplos caminhos para treinar que se juntam), talvez prefira fazer esta conversão manualmente. Para obter mais informações, consulte [Como preparar o seu modelo para implantação no Azure Machine Learning Studio (clássico)](deploy-a-machine-learning-web-service.md).
>
>

### <a name="the-web-service"></a>O serviço web
Uma vez que esteja satisfeito que a sua experiência preditiva esteja pronta, pode implementar o seu serviço como um serviço Web Clássico ou um novo serviço Web baseado no Azure Resource Manager. Para operacionalizar o seu modelo implantando-o como um *serviço Web de aprendizagem automática clássica,* clique em **Implementar serviço web** e selecione Implementar o Serviço **Web [Clássico]**. Para implementar como *novo serviço Web de aprendizagem automática,* clique em Implementar o Serviço **Web** e selecione Implementar o Serviço **Web [Novo]**. Os utilizadores podem agora enviar dados para o seu modelo utilizando o Serviço Web REST API e receber de volta os resultados. Para obter mais informações, veja [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Como consumir um serviço Web do Azure Machine Learning).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>O caso não típico: criar um serviço Web não preditivo
Se a sua experiência não treina um modelo de análise preditiva, então não precisa de criar uma experiência de treino e uma experiência de pontuação - há apenas uma experiência, e pode implantá-lo como um serviço Web. O Machine Learning Studio (clássico) deteta se a sua experiência contém um modelo preditivo analisando os módulos que utilizou.

Depois de ter iterado na sua experiência e ficar satisfeito com ela:

1. Clique **em Configurar o Serviço Web** e selecione o Serviço Web de **reconversão** - os nosdes de entrada e saída são adicionados automaticamente
2. Clique **em Executar**
3. Clique **em Implementar o Serviço Web** e selecione Implementar o Serviço Web **[Clássico]** ou implementar o Serviço Web **[Novo]** dependendo do ambiente para o qual pretende implementar.

O seu serviço Web está agora implantado e pode aceder e geri-lo tal como um serviço Web preditivo.

## <a name="updating-your-web-service"></a>Atualizar o seu serviço web
Agora que implementou a sua experiência como serviço Web, e se precisar de atualizá-la?

Depende do que precisa de atualizar:

**Pretende alterar a entrada ou saída, ou quer modificar a forma como o serviço Web manipula os dados**

Se não estiver a alterar o modelo, mas estiver apenas a alterar a forma como o serviço Web lida com os dados, pode editar a experiência preditiva e, em seguida, clicar em **Implementar o Serviço Web** e selecionar implementar o Serviço Web **[Classic]** ou **implementar o Serviço Web [Novo]** novamente. O serviço Web é interrompido, a experiência preditiva atualizada é implementada e o serviço Web é reiniciado.

Aqui está um exemplo: Suponha que a sua experiência preditiva retorne toda a linha de dados de entrada com o resultado previsto. Pode decidir que pretende que o serviço Web apenas devolva o resultado. Assim, pode adicionar um módulo **De Colunas** de Projeto na experiência preditiva, mesmo antes da porta de saída, para excluir colunas que não o resultado. Quando clica em **Implementar o Serviço Web** e seleciona implementar o Serviço Web **[Classic]** ou implementar novamente o **Serviço Web [Novo],** o serviço Web é atualizado.

**Quer voltar a treinar o modelo com novos dados**

Se quiser manter o seu modelo de aprendizagem automática, mas gostaria de o retreinar com novos dados, tem duas opções:

1. **Retreine o modelo enquanto o serviço Web está em execução** - Se pretender reconverter o seu modelo enquanto o serviço Web preditivo está em funcionamento, pode fazê-lo fazendo algumas modificações na experiência de treino para torná-lo uma **_experiência de reconversão_*_, então pode implantá-lo como um serviço* web de _reciclagem_**. Para obter instruções sobre como fazê-lo, consulte [os modelos de Retrain Machine Learning programáticamente.](./retrain-machine-learning-model.md)
2. **Volte à experiência de treino original e use diferentes dados de treino para desenvolver o seu modelo** - A sua experiência preditiva está ligada ao serviço Web, mas a experiência de treino não está diretamente ligada desta forma. Se modificar a experiência de treino original e clicar em **Configurar o Serviço Web,** criará uma *nova*     experiência preditiva que, quando implementada, criará um *novo* serviço Web. Não só atualiza o serviço Web original.

   Se precisar modificar a experiência de treino, abra-a e clique em **Guardar Como** para fazer uma cópia. Isto deixará intacta a experiência de treino original, a experiência preditiva e o serviço Web. Pode agora criar um novo serviço Web com as suas alterações. Uma vez implementado o novo serviço Web, pode então decidir se para parar o serviço Web anterior ou mantê-lo em funcionamento ao lado do novo.

**Quer treinar um modelo diferente**

Se quiser fazer alterações na sua experiência preditiva original, como selecionar um algoritmo de aprendizagem automática diferente, experimentar um método de treino diferente, etc., então precisa seguir o segundo procedimento descrito acima para retreinar o seu modelo: abrir a experiência de treino, clicar **em Save As** para fazer uma cópia e, em seguida, iniciar o novo caminho de desenvolvimento do seu modelo, criar a experiência preditiva e implantar o serviço web. Isto irá criar um novo serviço Web não relacionado com o original - você pode decidir qual, ou ambos, para continuar a executar.

## <a name="next-steps"></a>Passos seguintes
Para obter mais detalhes sobre o processo de desenvolvimento e experimentação, consulte os seguintes artigos:

* converter a experiência - [Como preparar o seu modelo para implantação no Azure Machine Learning Studio (clássico)](deploy-a-machine-learning-web-service.md)
* implementação do serviço Web - [Implementar um serviço web Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* requalificação do modelo - [Retrain Machine Learning modelos programáticamente](./retrain-machine-learning-model.md)

Por exemplo, de todo o processo, consulte:

* [Tutorial de machine learning: Crie a sua primeira experiência no Azure Machine Learning Studio (clássico)](create-experiment.md)
* [Walkthrough: Desenvolver uma solução de análise preditiva para avaliação de risco de crédito em Azure Machine Learning](tutorial-part1-credit-risk.md)