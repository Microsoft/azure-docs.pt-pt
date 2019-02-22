---
title: 'Tutorial 3: Implementar o modelo de risco de crédito'
titleSuffix: Azure Machine Learning Studio
description: Um tutorial detalhado que mostra como criar uma solução de Análise Preditiva para avaliação de riscos de crédito no Azure Machine Learning Studio. Este tutorial é a parte três de uma série de tutoriais de três partes. Ele mostra como implementar um modelo como um serviço web.
keywords: risco, solução de Análise Preditiva, avaliação de riscos de crédito, implantar, serviço web
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 1d860b14a878aa7daf43404744eb897f3bd91e13
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587196"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio"></a>Tutorial 3: Implementar o modelo de risco de crédito - Azure Machine Learning Studio

Neste tutorial, demorar mais aprofundadamente o processo de desenvolvimento de uma solução de Análise Preditiva. Desenvolver um modelo simples no Machine Learning Studio.  Em seguida, implementar o modelo como um serviço web do Azure Machine Learning.  Este modelo implementado pode efetuar predições utilizando novos dados. Este tutorial é **parte três de uma série de tutoriais de três partes**.

Suponhamos que precisa de prever o risco de crédito de um indivíduo com base nas informações fornecidas sobre uma aplicação de crédito.  

Avaliação de riscos de crédito é um problema complexo, mas este tutorial irá simplificá-lo um pouco. Irá utilizá-lo como um exemplo de como pode criar uma solução de Análise Preditiva com o Microsoft Azure Machine Learning Studio. Irá utilizar o Azure Machine Learning Studio e um serviço web do Machine Learning para esta solução. 

Neste tutorial de três partes, começar com dados de risco de crédito publicamente disponíveis.  Em seguida, desenvolver e formar um modelo preditivo.  Por fim implementa o modelo como um serviço web.

Na [primeira parte do tutorial](tutorial-part1-credit-risk.md), criou uma área de trabalho do Machine Learning Studio, carregou dados e criar uma experimentação.

Na [parte do tutorial](tutorial-part2-credit-risk-train.md), formação e avaliar modelos.

Nesta parte do tutorial:

> [!div class="checklist"]
> * Preparar para a implementação
> * Implementar o serviço web
> * Testar o serviço web
> * Gerir o serviço web
> * Aceder ao serviço web

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="prerequisites"></a>Pré-requisitos

Concluída [parte do tutorial](tutorial-part2-credit-risk-train.md).

## <a name="prepare-for-deployment"></a>Preparar para a implementação
Para atribuir a outras pessoas oportunidade de se usar o modelo preditivo que desenvolveu neste tutorial, pode implementá-lo como um serviço web do Azure.

Até agora já fizemos experiências em torno nosso modelo de formação. Mas o serviço implementado já não vai fazer o treinamento - vai gerar novas predições, a entrada do usuário com base no nosso modelo de classificação. Então, vamos fazer uma preparação para converter esta experiência de um ***treinamento*** experimentar para um ***preditiva*** experimentar. 

Preparação para a implantação é um processo em três etapas:  

1. Remover um dos modelos
1. Converter os *experimentação de preparação* que criou num *experimentação preditiva*
1. Implementar a experimentação preditiva como um serviço web

### <a name="remove-one-of-the-models"></a>Remover um dos modelos

Em primeiro lugar, terá de cortar desta experiência um pouco. tem atualmente dois modelos diferentes na experimentação, mas pretende utilizar um modelo ao implementar isso como um serviço web.  

Digamos que decidiu que o modelo de árvore elevada tem melhor desempenho do que o modelo SVM. Portanto, a primeira coisa a fazer é remover os [máquina de Vetor com suporte a duas classes] [ two-class-support-vector-machine] módulo e os módulos que eram usados para treiná-lo. Talvez queira fazer uma cópia da experimentação primeiro clicando **guardar como** na parte inferior da tela de experimentação.

tem de eliminar os seguintes módulos:  

* [Máquina de Vetor com suporte de duas classes][two-class-support-vector-machine]
* [Preparar modelo] [ train-model] e [Score Model] [ score-model] módulos que foram conectados a ele
* [Normalizar dados] [ normalize-data] (ambos)
* [Avaliar modelo] [ evaluate-model] (porque estamos avaliando os modelos de terminado)

Selecione cada módulo e prima a tecla Delete, ou o módulo com o botão direito e selecione **eliminar**. 

![Remover o modelo SVM](./media/tutorial-part3-credit-risk-deploy/publish3a.png)


O nosso modelo deverá agora ser semelhante ao seguinte:

![Remover o modelo SVM](./media/tutorial-part3-credit-risk-deploy/publish3.png)

Agora, estamos prontos para implementar este modelo com o [árvore de decisões elevada de duas classes][two-class-boosted-decision-tree].

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter a experimentação de preparação para uma experimentação preditiva

Para obter esse modelo pronto para implantação, terá de converter este experimentação de preparação para uma experimentação preditiva. Isto envolve três passos:

1. Guarde o modelo Treinou e, em seguida, substituir nosso módulos de treinamento
1. Cortar a experimentação para remover módulos que eram necessários apenas para formação
1. Definir em que o serviço web aceitará a entrada e em que gera a saída

Pode fazer isso manualmente, mas Felizmente a todos os três passos podem ser feitos clicando **no serviço Web** na parte inferior da tela de experimentação (e selecionando a **preditiva Web Service** opção).

> [!TIP]
> Se quiser saber mais sobre o que acontece quando converter uma experimentação de preparação para uma experimentação preditiva, veja [como preparar o seu modelo para a implementação no Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

Quando clica em **no serviço de Web**, várias coisas acontecem:

* O modelo preparado é convertido numa única **modelo preparado** módulo e armazenado na paleta do módulo para a esquerda da tela de experimentação (pode encontrá-lo sob **modelos de formação**)
* Módulos que eram usados para treinamento são removidos; especificamente:
  * [Árvore de decisões elevada de duas classes][two-class-boosted-decision-tree]
  * [Preparar modelo][train-model]
  * [Dividir os dados][split]
  * a segunda [executar Script R] [ execute-r-script] módulo que foi utilizado para dados de teste
* O modelo treinado guardado é adicionado novamente para a experimentação
* **Entrada do serviço da Web** e **saída de serviço da Web** módulos são adicionados (estes identificam onde os dados do utilizador irão introduzir o modelo e quais dados são retornados, quando o serviço web é acessado)

> [!NOTE]
> Pode ver que a experimentação é guardada em duas partes dentro de separadores que foram adicionados na parte superior da tela de experimentação. A experimentação de preparação original é no separador **experimentação de preparação**, e a experimentação preditiva recentemente criada está sob **experimentação preditiva**. A experimentação preditiva é o que vai implementar como um serviço web.

precisa de uma etapa adicional com esta experiência específica.
adicionou dois [executar Script R] [ execute-r-script] módulos para fornecer uma função de peso para os dados. Essa foi apenas um truque que precisava para formação e teste, para que possa tirar o esses módulos no modelo final.
O Machine Learning Studio removido um [executar Script R] [ execute-r-script] módulo quando ele removido o [Split] [ split] módulo. Agora, pode remover o outro e ligue-se [Editor de metadados] [ metadata-editor] diretamente à [Score Model][score-model].    

Nossa experiência deve agora ter um aspeto semelhante a esta:  

![O modelo treinado de classificação](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> Deve estar imaginando por que marcou o conjunto de dados de dados de cartão de crédito de alemão de UCI na experimentação preditiva. O serviço vai classificar os dados do utilizador, não o conjunto de dados original, por isso, por que motivo, deixe o conjunto de dados original no modelo?
> 
> É verdade que o serviço não tem dos dados originais do cartão de crédito. Mas é necessário o esquema para esses dados, que inclui informações como o número de colunas existem e quais colunas são numéricas. Estas informações de esquema são necessárias para interpretar os dados do utilizador. Deixe estes componentes ligados para que o módulo de pontuação tem o esquema de conjunto de dados quando o serviço está em execução. Os dados não for usados, apenas o esquema.  
> 
>Uma coisa importante a observar é que se o conjunto de dados original contido a etiqueta, em seguida, o schema esperado da entrada web será também esperar que uma coluna com a etiqueta! Uma maneira de evitar isso é remover a etiqueta e quaisquer outros dados que estava a ser o conjunto de dados de treinamento, mas não será nas entradas de web, antes de ligar a entrada de web e o conjunto de dados de treinamento num módulo comum. 
> 

Execute a experimentação pela última vez (clique em **executar**.) Se quiser verificar se o modelo ainda está funcionando, clique na saída do [modelo de pontuação] [ score-model] módulo e selecione **ver resultados**. Pode ver que os dados originais são apresentados, juntamente com o valor de risco de crédito ("etiquetas classificadas") e o valor de probabilidade de classificação ("classificada probabilidades".) 

## <a name="deploy-the-web-service"></a>Implementar o serviço web
Pode implementar a experimentação, como a um serviço web clássico ou como um novo serviço web baseado no Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Implementar como um serviço web clássico
Para implementar um serviço da web clássico derivado a partir de nossa experiência, clique em **implementar serviço Web** abaixo de tela e selecione **implementar o serviço Web [clássica]**. O Machine Learning Studio implementa a experimentação como um serviço web e leva-o para o dashboard para esse serviço da web. Nesta página, pode retornar para a experimentação (**ver instantâneo** ou **ver mais recente**) e executar um teste simples do serviço web (veja **testar o serviço web** abaixo). Também há informações para a criação de aplicativos que podem aceder ao serviço web (mais sobre isso no próximo passo do tutorial).

![Dashboard de serviço da Web](./media/tutorial-part3-credit-risk-deploy/publish6.png)


Pode configurar o serviço clicando a **configuração** separador. Aqui pode modificar o nome do serviço (que tenha dado o nome de experimentação por padrão) e conceda-lhe uma descrição. Pode também dar mais etiquetas amigáveis para os dados de entrada e saídos.  

![Configurar o serviço web](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>Implementar como um serviço web novo

> [!NOTE] 
> Para implementar um novo serviço web tem de ter permissões suficientes na subscrição que pretende implementar o serviço web. Para obter mais informações, consulte [gerir um serviço web através do portal do Azure Machine Learning Web Services](manage-new-webservice.md). 

Para implementar um novo serviço web derivado de nossa experiência:

1. Clique em **implementar serviço Web** abaixo de tela e selecione **implementar o Web Service [novo]**. O Machine Learning Studio transfere para os serviços web do Azure Machine Learning **experimentação implementar** página.

1. Introduza um nome para o serviço web. 

1. Para **plano de preços**, pode selecionar um plano de preços existente, ou selecione "Criar nova" e dê um nome ao novo plano e selecione a opção de plano mensal. A predefinição de escalões do plano para os planos para a sua região predefinida e o seu serviço web é implementada nessa região.

1. Clique em **implementar**.

Após alguns minutos, o **guia de introdução** é aberta a página do web Service.

Pode configurar o serviço clicando a **configurar** separador. Aqui pode modificar o serviço do título e fornecer uma descrição. 

Para testar o serviço web, clique nas **testar** separador (consulte **testar o serviço web** abaixo). Para obter informações sobre a criação de aplicativos que podem aceder ao serviço web, clique a **Consume** separador (a próxima etapa neste tutorial irá entrar em mais detalhes).

> [!TIP]
> Depois de implementar isso, é possível atualizar o serviço web. Por exemplo, se pretender alterar o seu modelo, em seguida, pode editar a experimentação de preparação, ajustar os parâmetros de modelo e clique em **implementar serviço Web**, ao selecionar **implementar o serviço Web [clássica]** ou **Implementar serviço da Web [novo]**. Quando implementar novamente a experimentação, ele substitui o serviço web, agora a utilizar o seu modelo atualizado.  
> 
> 

## <a name="test-the-web-service"></a>Testar o serviço web

Quando o serviço da web é acedido, os dados do utilizador inserem através da **entrada do serviço da Web** módulo onde ela é passada para o [Score Model] [ score-model] módulo e pontuadas. A forma como configurou a experimentação preditiva, o modelo espera que os dados no mesmo formato que o conjunto de dados de risco de crédito original.
Os resultados são devolvidos ao utilizador do serviço web através da **saída de serviço da Web** módulo.

> [!TIP]
> A forma como tem a experimentação preditiva configurada, toda a resulta dos [modelo de pontuação] [ score-model] módulo são devolvidos. Isto inclui todos os dados de entrada e o valor de risco de crédito e a probabilidade de classificação. Mas pode retornar algo diferente se pretender que, por exemplo, pode devolver apenas o valor de risco de crédito. Para tal, inserir um [colunas do projeto] [ project-columns] módulo entre [Score Model] [ score-model] e a **Web saída de serviço**para eliminar colunas não pretende que o serviço web para retornar. 
> 
> 

Pode testar um web clássico de serviço no **Machine Learning Studio** ou no **serviços da Web do Azure Machine Learning** portal.
Pode testar uma nova web service apenas na **serviços Web Machine Learning** portal.

> [!TIP]
> Ao testar no portal do Azure Machine Learning Web Services, pode fazer com o portal criar dados de exemplo que pode utilizar para testar o serviço de solicitação-resposta. Sobre o **configurar** , selecione "Sim" para **ativada de dados de exemplo?**. Ao abrir o separador de solicitação-resposta sobre o **teste** página, o portal preenche os dados de exemplo retirados do conjunto de dados de risco de crédito original.

### <a name="test-a-classic-web-service"></a>Testar um serviço web clássico

Pode testar um serviço web clássico no Machine Learning Studio ou no portal de serviços Web Machine Learning. 

#### <a name="test-in-machine-learning-studio"></a>Testar no Machine Learning Studio

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

No passo anterior neste tutorial, implementou um serviço web que utiliza o seu modelo de previsão do risco de crédito. Agora os utilizadores podem enviar dados para o mesmo e receba resultados. 

O serviço Web é um serviço web do Azure que possa receber e devolver dados com REST APIs de uma de duas formas:  

* **Pedido/resposta** - o utilizador envia uma ou mais linhas de dados de crédito para o serviço utilizando um protocolo HTTP e o serviço responde com um ou mais conjuntos de resultados.
* **Execução de lote** – o utilizador armazena um ou mais linhas de dados de crédito no Azure blob e, em seguida, envia a localização do blob para o serviço. O serviço pontua todas as linhas de dados no blob de entrada, armazena os resultados em outro blob e devolve o URL nesse contentor.  

A forma mais rápida e fácil para aceder a um serviço web clássico é através da [aplicação de Web do serviço de solicitação-resposta do Azure ML](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) ou [modelo de aplicação ao Web do serviço de execução do Azure ML Batch](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/).

Estes modelos de aplicação web podem criar uma aplicação web personalizado que sabe que os dados de entrada do seu serviço da web e o que irá devolver. Tudo o que precisa fazer é fornecer acesso ao serviço web e aos dados e o modelo faz o resto.

Para obter mais informações sobre como utilizar os modelos de aplicação web, consulte [consumir um serviço Web do Azure Machine Learning com um modelo de aplicação web](consume-web-service-with-web-app-template.md).



## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, concluiu estes passos:

> [!div class="checklist"]
> * Preparar para a implementação
> * Implementar o serviço web
> * Testar o serviço web
> * Gerir o serviço web
> * Aceder ao serviço web

Também pode desenvolver um aplicativo personalizado para aceder ao serviço da web usando o código de inicialização que lhe é fornecido em R, C#, e linguagens de programação Python.

> [!div class="nextstepaction"]
> [Consumir um serviço Web do Azure Machine Learning](consume-web-services.md)

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
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/