---
title: 'Tutorial 3: implantar o modelo de risco de crédito'
titleSuffix: Azure Machine Learning Studio (classic)
description: Um tutorial detalhado que mostra como criar uma solução de análise preditiva para avaliação de risco de crédito no Azure Machine Learning Studio (clássico). Este tutorial é a parte três de uma série de tutoriais de três partes. Ele mostra como implantar um modelo como um serviço Web.
keywords: risco de crédito, solução de análise preditiva, avaliação de risco, implantação, serviço Web
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 9353e5749906aca1f0c91b622c93ba6028cd3bef
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432202"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio-classic"></a>Tutorial 3: implantar o modelo de risco de crédito-Azure Machine Learning Studio (clássico)

Neste tutorial, você tem uma visão detalhada do processo de desenvolvimento de uma solução de análise preditiva. Você desenvolve um modelo simples no Machine Learning Studio (clássico).  Em seguida, você implanta o modelo como um serviço Web Azure Machine Learning.  Esse modelo implantado pode fazer previsões usando novos dados. Este tutorial é a **parte três de uma série de tutoriais de três partes**.

Suponhamos que precisa de prever o risco de crédito de um indivíduo com base nas informações fornecidas sobre uma aplicação de crédito.  

A avaliação de risco de crédito é um problema complexo, mas este tutorial vai simplificar um pouco. Você o usará como um exemplo de como é possível criar uma solução de análise preditiva usando Microsoft Azure Machine Learning Studio (clássico). Você usará Azure Machine Learning Studio (clássico) e um serviço Web Machine Learning para esta solução. 

Neste tutorial de três partes, você começa com dados de risco de crédito disponíveis publicamente.  Em seguida, você desenvolve e treina um modelo de previsão.  Por fim, você implanta o modelo como um serviço Web.

Na [parte um do tutorial](tutorial-part1-credit-risk.md), você criou um espaço de trabalho Machine Learning Studio (clássico), carregou dados e criou um experimento.

Na [parte dois do tutorial](tutorial-part2-credit-risk-train.md), você treinou e avaliou modelos.

Nesta parte do tutorial, você:

> [!div class="checklist"]
> * Preparar para implementar
> * Implementar o serviço web
> * Testar o serviço web
> * Gerir o serviço web
> * Aceder ao serviço web

## <a name="prerequisites"></a>Pré-requisitos

Conclua [a parte dois do tutorial](tutorial-part2-credit-risk-train.md).

## <a name="prepare-for-deployment"></a>Preparar para implementar
Para dar aos outros a oportunidade de usar o modelo de previsão que você desenvolveu neste tutorial, você pode implantá-lo como um serviço Web no Azure.

Até este ponto, você experimentou o treinamento de nosso modelo. Mas o serviço implementado já não vai fazer o treinamento - vai gerar novas predições, a entrada do usuário com base no nosso modelo de classificação. Então, vamos fazer uma preparação para converter esta experiência de um ***treinamento*** experimentar para um ***preditiva*** experimentar. 

A preparação para a implantação é um processo de três etapas:  

1. Remover um dos modelos
1. Converter o *teste de treinamento* que você criou em um *experimento de previsão*
1. Implementar a experimentação preditiva como um serviço web

### <a name="remove-one-of-the-models"></a>Remover um dos modelos

Primeiro, você precisa cortar esse experimento um pouco. Atualmente, você tem dois modelos diferentes no experimento, mas só deseja usar um modelo ao implantá-lo como um serviço Web.  

Digamos que você tenha decidido que o modelo de árvore ampliado foi executado melhor do que o modelo SVM. Portanto, a primeira coisa a fazer é remover o módulo [máquina de vetor de suporte de duas classes][two-class-support-vector-machine] e os módulos que foram usados para treinamento. Talvez queira fazer uma cópia da experimentação primeiro clicando **guardar como** na parte inferior da tela de experimentação.

Você precisa excluir os seguintes módulos:  

* [Computador de vetor de suporte de duas classes][two-class-support-vector-machine]
* Módulos [treinar modelo][train-model] e [modelo de Pontuação][score-model] que estavam conectados a ele
* [Normalizar dados][normalize-data] (ambos)
* [Avaliar o modelo][evaluate-model] (porque terminamos de avaliar os modelos)

Selecione cada módulo e prima a tecla Delete, ou o módulo com o botão direito e selecione **eliminar**. 

![Realça quais módulos excluir para remover o modelo de máquina de vetor de suporte](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

O nosso modelo deverá agora ser semelhante ao seguinte:

![Teste resultante quando o modelo de máquina do vetor de suporte é excluído](./media/tutorial-part3-credit-risk-deploy/publish3.png)

Agora estamos prontos para implantar esse modelo usando a [árvore de decisão aumentada de duas classes][two-class-boosted-decision-tree].

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter a experimentação de preparação para uma experimentação preditiva

Para preparar esse modelo para implantação, você precisa converter este teste de treinamento em um experimento de previsão. Isto envolve três passos:

1. Salve o modelo treinado e, em seguida, substitua nossos módulos de treinamento
1. Cortar a experimentação para remover módulos que eram necessários apenas para formação
1. Definir em que o serviço web aceitará a entrada e em que gera a saída

Você pode fazer isso manualmente, mas felizmente as três etapas podem ser realizadas clicando em **configurar serviço Web** na parte inferior da tela do experimento (e selecionando a opção **serviço Web de previsão** ).

> [!TIP]
> Se você quiser obter mais detalhes sobre o que acontece quando você converte um teste de treinamento em um experimento de previsão, consulte [como preparar seu modelo para implantação no Azure Machine Learning Studio (clássico)](convert-training-experiment-to-scoring-experiment.md).

Quando clica em **no serviço de Web**, várias coisas acontecem:

* O modelo preparado é convertido numa única **modelo preparado** módulo e armazenado na paleta do módulo para a esquerda da tela de experimentação (pode encontrá-lo sob **modelos de formação**)
* Módulos que eram usados para treinamento são removidos; especificamente:
  * [Árvore de decisão aumentada de duas classes][two-class-boosted-decision-tree]
  * [Modelo de treinamento][train-model]
  * [Dividir dados][split]
  * o segundo módulo [Executar script R][execute-r-script] que foi usado para dados de teste
* O modelo treinado guardado é adicionado novamente para a experimentação
* **Entrada do serviço da Web** e **saída de serviço da Web** módulos são adicionados (estes identificam onde os dados do utilizador irão introduzir o modelo e quais dados são retornados, quando o serviço web é acessado)

> [!NOTE]
> Pode ver que a experimentação é guardada em duas partes dentro de separadores que foram adicionados na parte superior da tela de experimentação. A experimentação de preparação original é no separador **experimentação de preparação**, e a experimentação preditiva recentemente criada está sob **experimentação preditiva**. O experimento de previsão é aquele que você implantará como um serviço Web.

Você precisa executar uma etapa adicional com esse experimento específico.
Você adicionou dois módulos [Executar script R][execute-r-script] para fornecer uma função de ponderação aos dados. Esse era apenas um truque necessário para treinamento e teste, para que você possa retirar esses módulos no modelo final.
Machine Learning Studio (clássico) removeu um módulo [Executar script R][execute-r-script] quando ele removeu o módulo [dividir][split] . Agora você pode remover o outro e conectar o [Editor de metadados][metadata-editor] diretamente ao modelo de [Pontuação][score-model].    

Nossa experiência deve agora ter um aspeto semelhante a esta:  

![O modelo treinado de classificação](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> Você deve estar se perguntando por que saiu do conjunto de dados do cartão de crédito do UCI alemão no experimento de previsão. O serviço vai classificar os dados do utilizador, não o conjunto de dados original, por isso, por que motivo, deixe o conjunto de dados original no modelo?
> 
> É verdade que o serviço não tem dos dados originais do cartão de crédito. Mas é necessário o esquema para esses dados, que inclui informações como o número de colunas existem e quais colunas são numéricas. Estas informações de esquema são necessárias para interpretar os dados do utilizador. Deixe esses componentes conectados para que o módulo de Pontuação tenha o esquema do conjunto de módulos quando o serviço estiver em execução. Os dados não for usados, apenas o esquema.  
> 
>Uma coisa importante a observar é que se o conjunto de dados original contido a etiqueta, em seguida, o schema esperado da entrada web será também esperar que uma coluna com a etiqueta! Uma maneira de evitar isso é remover a etiqueta e quaisquer outros dados que estava a ser o conjunto de dados de treinamento, mas não será nas entradas de web, antes de ligar a entrada de web e o conjunto de dados de treinamento num módulo comum. 
> 

Execute o experimento uma última vez (clique em **executar**.) Se você quiser verificar se o modelo ainda está funcionando, clique na saída do módulo [modelo de Pontuação][score-model] e selecione **exibir resultados**. Pode ver que os dados originais são apresentados, juntamente com o valor de risco de crédito ("etiquetas classificadas") e o valor de probabilidade de classificação ("classificada probabilidades".) 

## <a name="deploy-the-web-service"></a>Implementar o serviço web
Pode implementar a experimentação, como a um serviço web clássico ou como um novo serviço web baseado no Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Implementar como um serviço web clássico
Para implementar um serviço da web clássico derivado a partir de nossa experiência, clique em **implementar serviço Web** abaixo de tela e selecione **implementar o serviço Web [clássica]** . Machine Learning Studio (clássico) implanta o teste como um serviço Web e o leva para o painel desse serviço Web. Nessa página, você pode retornar ao experimento (**Exibir instantâneo** ou **Exibir mais recente**) e executar um teste simples do serviço Web (consulte **testar o serviço Web** abaixo). Também há informações aqui para criar aplicativos que podem acessar o serviço Web (mais sobre isso na próxima etapa deste tutorial).

![Dashboard de serviço da Web](./media/tutorial-part3-credit-risk-deploy/publish6.png)


Você pode configurar o serviço clicando na guia **configuração** . Aqui você pode modificar o nome do serviço (ele recebe o nome do experimento por padrão) e dar a ele uma descrição. Pode também dar mais etiquetas amigáveis para os dados de entrada e saídos.  

![Configurar o serviço web](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>Implementar como um serviço web novo

> [!NOTE] 
> Para implementar um novo serviço web tem de ter permissões suficientes na subscrição que pretende implementar o serviço web. Para obter mais informações, consulte [gerir um serviço web através do portal do Azure Machine Learning Web Services](manage-new-webservice.md). 

Para implementar um novo serviço web derivado de nossa experiência:

1. Clique em **implementar serviço Web** abaixo de tela e selecione **implementar o Web Service [novo]** . Machine Learning Studio (clássico) transfere para a página de teste do Azure Machine Learning Web Services **Deploy** .

1. Introduza um nome para o serviço web. 

1. Para **plano de preços**, pode selecionar um plano de preços existente, ou selecione "Criar nova" e dê um nome ao novo plano e selecione a opção de plano mensal. A predefinição de escalões do plano para os planos para a sua região predefinida e o seu serviço web é implementada nessa região.

1. Clique em **implementar**.

Após alguns minutos, o **guia de introdução** é aberta a página do web Service.

Você pode configurar o serviço clicando na guia **Configurar** . Aqui você pode modificar o título do serviço e dar a ele uma descrição. 

Para testar o serviço web, clique nas **testar** separador (consulte **testar o serviço web** abaixo). Para obter informações sobre como criar aplicativos que podem acessar o serviço Web, clique na guia **consumir** (a próxima etapa neste tutorial entrará em mais detalhes).

> [!TIP]
> Depois de implementar isso, é possível atualizar o serviço web. Por exemplo, se pretender alterar o seu modelo, em seguida, pode editar a experimentação de preparação, ajustar os parâmetros de modelo e clique em **implementar serviço Web**, ao selecionar **implementar o serviço Web [clássica]** ou **Implementar serviço da Web [novo]** . Quando implementar novamente a experimentação, ele substitui o serviço web, agora a utilizar o seu modelo atualizado.  
> 
> 

## <a name="test-the-web-service"></a>Testar o serviço web

Quando o serviço Web é acessado, os dados do usuário entram no módulo de **entrada do serviço Web** , onde ele é passado para o módulo [modelo de Pontuação][score-model] e pontuado. Da maneira como você configurou o experimento de previsão, o modelo espera dados no mesmo formato que o conjunto de dado de risco de crédito original.
Os resultados são devolvidos ao utilizador do serviço web através da **saída de serviço da Web** módulo.

> [!TIP]
> Da maneira como você tem o experimento de previsão configurado, todos os resultados do módulo [modelo de Pontuação][score-model] são retornados. Isto inclui todos os dados de entrada e o valor de risco de crédito e a probabilidade de classificação. Mas pode retornar algo diferente se pretender que, por exemplo, pode devolver apenas o valor de risco de crédito. Para fazer isso, insira um módulo [selecionar colunas][select-columns] entre o [modelo de Pontuação][score-model] e a saída do **serviço Web** para eliminar as colunas que você não deseja que o serviço Web retorne. 
> 
> 

Você pode testar um serviço Web clássico em **Machine Learning Studio (clássico)** ou no portal de **serviços Azure Machine Learning Web** .
Pode testar uma nova web service apenas na **serviços Web Machine Learning** portal.

> [!TIP]
> Ao testar no portal do Azure Machine Learning Web Services, pode fazer com o portal criar dados de exemplo que pode utilizar para testar o serviço de solicitação-resposta. Sobre o **configurar** , selecione "Sim" para **ativada de dados de exemplo?** . Ao abrir o separador de solicitação-resposta sobre o **teste** página, o portal preenche os dados de exemplo retirados do conjunto de dados de risco de crédito original.

### <a name="test-a-classic-web-service"></a>Testar um serviço web clássico

Você pode testar um serviço Web clássico no Machine Learning Studio (clássico) ou no portal de serviços Machine Learning Web. 

#### <a name="test-in-machine-learning-studio-classic"></a>Testar em Machine Learning Studio (clássico)

1. Sobre o **DASHBOARD** para o serviço web, clique no **teste** botão sob **ponto final predefinido**. Uma caixa de diálogo será exibida e solicita os dados de entrada para o serviço. Estas são as mesmas colunas que apareceu do conjunto de dados de risco de crédito original.  

1. Introduza um conjunto de dados e, em seguida, clique em **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Testar no portal do serviços Web Machine Learning

1. Na **DASHBOARD** para o serviço web, clique no **pré-visualização de teste** ligação sob **ponto final predefinido**. A página de teste no portal do Azure Machine Learning Web Services para o ponto de final de serviço web é aberto e solicita os dados de entrada para o serviço. Estas são as mesmas colunas que apareceu do conjunto de dados de risco de crédito original.

2. Clique em **teste de solicitação-resposta**. 

### <a name="test-a-new-web-service"></a>Testar um serviço web novo

Pode testar um novo serviço web apenas no portal de serviços Web Machine Learning.

1. Na [serviços da Web do Azure Machine Learning](https://services.azureml.net/quickstart) portal, clique **teste** na parte superior da página. O **teste** é aberta a página e pode inserir dados para o serviço. Os campos de entrada apresentados correspondem às colunas que apareceu do conjunto de dados de risco de crédito original. 

1. Introduza um conjunto de dados e, em seguida, clique em **teste de solicitação-resposta**.

Os resultados do teste são exibidos no lado direito da página da coluna de saída. 


## <a name="manage-the-web-service"></a>Gerir o serviço web

Assim que tiver implantado o serviço da web, se novos ou clássico, pode geri-lo a partir da [serviços de Web do Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) portal.

Para monitorizar o desempenho do seu serviço web:

1. Inicie sessão para o [serviços de Web do Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) portal
1. Clique em **serviços Web**
1. Clique em seu serviço web
1. Clique no **Dashboard**

## <a name="access-the-web-service"></a>Aceder ao serviço web

Na etapa anterior deste tutorial, você implantou um serviço Web que usa seu modelo de previsão de risco de crédito. Agora os utilizadores podem enviar dados para o mesmo e receba resultados. 

O serviço Web é um serviço web do Azure que possa receber e devolver dados com REST APIs de uma de duas formas:  

* **Pedido/resposta** - o utilizador envia uma ou mais linhas de dados de crédito para o serviço utilizando um protocolo HTTP e o serviço responde com um ou mais conjuntos de resultados.
* **Execução de lote** – o utilizador armazena um ou mais linhas de dados de crédito no Azure blob e, em seguida, envia a localização do blob para o serviço. O serviço pontua todas as linhas de dados no blob de entrada, armazena os resultados em outro blob e devolve o URL nesse contentor.  

A forma mais rápida e fácil para aceder a um serviço web clássico é através da [aplicação de Web do serviço de solicitação-resposta do Azure ML](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) ou [modelo de aplicação ao Web do serviço de execução do Azure ML Batch](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/).

Estes modelos de aplicação web podem criar uma aplicação web personalizado que sabe que os dados de entrada do seu serviço da web e o que irá devolver. Tudo o que precisa fazer é fornecer acesso ao serviço web e aos dados e o modelo faz o resto.

Para obter mais informações sobre como utilizar os modelos de aplicação web, consulte [consumir um serviço Web do Azure Machine Learning com um modelo de aplicação web](/azure/machine-learning/studio/consume-web-services).



## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você concluiu estas etapas:

> [!div class="checklist"]
> * Preparar para implementar
> * Implementar o serviço web
> * Testar o serviço web
> * Gerir o serviço web
> * Aceder ao serviço web

Também pode desenvolver um aplicativo personalizado para aceder ao serviço da web usando o código de inicialização que lhe é fornecido em R, C#, e linguagens de programação Python.

> [!div class="nextstepaction"]
> [Consumir um serviço Web Azure Machine Learning](consume-web-services.md)

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
