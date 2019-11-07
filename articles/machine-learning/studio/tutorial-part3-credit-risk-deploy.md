---
title: 'Tutorial 3: implantar o modelo de risco de crédito'
titleSuffix: ML Studio (classic) Azure
description: Um tutorial detalhado que mostra como criar uma solução de análise preditiva para avaliação de risco de crédito na versão clássica do Azure Machine Learning Studio. Este tutorial é a parte três de uma série de tutoriais de três partes. Ele mostra como implantar um modelo como um serviço Web.
keywords: risco de crédito, solução de análise preditiva, avaliação de risco, implantação, serviço Web
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 0f4ee6cfebcb5edb38b1cc7a11b070dab5d2098c
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73619021"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio-classic"></a>Tutorial 3: implantar o modelo de risco de crédito-Azure Machine Learning Studio (clássico)

Neste tutorial, você tem uma visão detalhada do processo de desenvolvimento de uma solução de análise preditiva. Você desenvolve um modelo simples no Machine Learning Studio (clássico).  Em seguida, você implanta o modelo como um serviço Web Azure Machine Learning.  Esse modelo implantado pode fazer previsões usando novos dados. Este tutorial é a **parte três de uma série de tutoriais de três partes**.

Suponhamos que precisa de prever o risco de crédito de um indivíduo com base nas informações fornecidas sobre uma aplicação de crédito.  

A avaliação de risco de crédito é um problema complexo, mas este tutorial vai simplificar um pouco. Você o usará como um exemplo de como é possível criar uma solução de análise preditiva usando Microsoft Azure Machine Learning Studio (clássico). Você usará a versão clássica do Azure Machine Learning Studio e um serviço Web Machine Learning para essa solução. 

Neste tutorial de três partes, você começa com dados de risco de crédito disponíveis publicamente.  Em seguida, você desenvolve e treina um modelo de previsão.  Por fim, você implanta o modelo como um serviço Web.

Na [parte um do tutorial](tutorial-part1-credit-risk.md), você criou um espaço de trabalho Machine Learning Studio (clássico), carregou dados e criou um experimento.

Na [parte dois do tutorial](tutorial-part2-credit-risk-train.md), você treinou e avaliou modelos.

Nesta parte do tutorial, você:

> [!div class="checklist"]
> * Preparar para a implementação
> * Implementar o serviço web
> * Testar o serviço Web
> * Gerenciar o serviço Web
> * Aceder ao serviço web

## <a name="prerequisites"></a>Pré-requisitos

Conclua [a parte dois do tutorial](tutorial-part2-credit-risk-train.md).

## <a name="prepare-for-deployment"></a>Preparar para a implementação
Para dar aos outros a oportunidade de usar o modelo de previsão que você desenvolveu neste tutorial, você pode implantá-lo como um serviço Web no Azure.

Até este ponto, você experimentou o treinamento de nosso modelo. Mas o serviço implantado não fará mais o treinamento. ele vai gerar novas previsões, pontuando a entrada do usuário com base em nosso modelo. Vamos então fazer alguma preparação para converter esse experimento de um teste de ***treinamento*** em um experimento de ***previsão*** . 

A preparação para a implantação é um processo de três etapas:  

1. Remover um dos modelos
1. Converter o *teste de treinamento* que você criou em um *experimento de previsão*
1. Implantar o teste de previsão como um serviço Web

### <a name="remove-one-of-the-models"></a>Remover um dos modelos

Primeiro, você precisa cortar esse experimento um pouco. Atualmente, você tem dois modelos diferentes no experimento, mas só deseja usar um modelo ao implantá-lo como um serviço Web.  

Digamos que você tenha decidido que o modelo de árvore ampliado foi executado melhor do que o modelo SVM. Portanto, a primeira coisa a fazer é remover o módulo [máquina de vetor de suporte de duas classes][two-class-support-vector-machine] e os módulos que foram usados para treinamento. Você pode querer fazer uma cópia do experimento primeiro clicando em **salvar como** na parte inferior da tela do experimento.

Você precisa excluir os seguintes módulos:  

* [Computador de vetor de suporte de duas classes][two-class-support-vector-machine]
* Módulos [treinar modelo][train-model] e [modelo de Pontuação][score-model] que estavam conectados a ele
* [Normalizar dados][normalize-data] (ambos)
* [Avaliar o modelo][evaluate-model] (porque terminamos de avaliar os modelos)

Selecione cada módulo e pressione a tecla Delete ou clique com o botão direito do mouse no módulo e selecione **excluir**. 

![Realça quais módulos excluir para remover o modelo de máquina de vetor de suporte](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

Agora, nosso modelo deve ser semelhante a este:

![Teste resultante quando o modelo de máquina do vetor de suporte é excluído](./media/tutorial-part3-credit-risk-deploy/publish3.png)

Agora estamos prontos para implantar esse modelo usando a [árvore de decisão aumentada de duas classes][two-class-boosted-decision-tree].

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter o teste de treinamento em um experimento de previsão

Para preparar esse modelo para implantação, você precisa converter este teste de treinamento em um experimento de previsão. Isso envolve três etapas:

1. Salve o modelo treinado e, em seguida, substitua nossos módulos de treinamento
1. Cortar o experimento para remover os módulos que foram necessários apenas para treinamento
1. Definir onde o serviço Web aceitará entrada e onde ele gerará a saída

Você pode fazer isso manualmente, mas felizmente as três etapas podem ser realizadas clicando em **configurar serviço Web** na parte inferior da tela do experimento (e selecionando a opção **serviço Web de previsão** ).

> [!TIP]
> Se você quiser obter mais detalhes sobre o que acontece quando você converte um teste de treinamento em um experimento de previsão, consulte [como preparar seu modelo para implantação no Azure Machine Learning Studio (clássico)](convert-training-experiment-to-scoring-experiment.md).

Quando você clica em **configurar serviço Web**, várias coisas acontecem:

* O modelo treinado é convertido em um único módulo de **modelo treinado** e armazenado na paleta de módulo à esquerda da tela do experimento (você pode encontrá-lo em **modelos treinados**)
* Os módulos que foram usados para treinamento são removidos; especificamente
  * [Árvore de decisão aumentada de duas classes][two-class-boosted-decision-tree]
  * [Modelo de treinamento][train-model]
  * [Dividir dados][split]
  * o segundo módulo [Executar script R][execute-r-script] que foi usado para dados de teste
* O modelo treinado salvo é adicionado de volta ao experimento
* Os módulos **entrada de serviço Web** e **saída de serviço Web** são adicionados (eles identificam onde os dados do usuário entrarão no modelo e quais dados são retornados, quando o serviço Web é acessado)

> [!NOTE]
> Você pode ver que o experimento é salvo em duas partes em guias que foram adicionadas na parte superior da tela do experimento. O experimento de treinamento original está sob a guia **teste de treinamento**e o experimento de previsão criado recentemente está sob experimento de **previsão**. O experimento de previsão é aquele que você implantará como um serviço Web.

Você precisa executar uma etapa adicional com esse experimento específico.
Você adicionou dois módulos [Executar script R][execute-r-script] para fornecer uma função de ponderação aos dados. Esse era apenas um truque necessário para treinamento e teste, para que você possa retirar esses módulos no modelo final.
Machine Learning Studio (clássico) removeu um módulo [Executar script R][execute-r-script] quando ele removeu o módulo [dividir][split] . Agora você pode remover o outro e conectar o [Editor de metadados][metadata-editor] diretamente ao modelo de [Pontuação][score-model].    

Nosso experimento agora deve ser assim:  

![Pontuação do modelo treinado](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> Você deve estar se perguntando por que saiu do conjunto de dados do cartão de crédito do UCI alemão no experimento de previsão. O serviço vai pontuar os dados do usuário, e não o DataSet original, então, por que deixar o DataSet original no modelo?
> 
> É verdade que o serviço não precisa dos dados originais do cartão de crédito. Mas ele precisa do esquema para esses dados, o que inclui informações como quantas colunas existem e quais colunas são numéricas. Essas informações de esquema são necessárias para interpretar os dados do usuário. Deixe esses componentes conectados para que o módulo de Pontuação tenha o esquema do conjunto de módulos quando o serviço estiver em execução. Os dados não são usados, apenas o esquema.  
> 
>Uma coisa importante a observar é que, se o conjunto de dados original contiver o rótulo, o esquema esperado da entrada da Web também esperará uma coluna com o rótulo! Uma maneira de resolver isso é remover o rótulo e todos os outros dados que estavam no conjunto do dados de treinamento, mas não estarão nas entradas da Web, antes de conectar a entrada da Web e o conjunto de dados de treinamento em um módulo comum. 
> 

Execute o experimento uma última vez (clique em **executar**.) Se você quiser verificar se o modelo ainda está funcionando, clique na saída do módulo [modelo de Pontuação][score-model] e selecione **exibir resultados**. Você pode ver que os dados originais são exibidos, juntamente com o valor de risco de crédito ("rótulos pontuados") e o valor de probabilidade de Pontuação ("probabilidades de Pontuação".) 

## <a name="deploy-the-web-service"></a>Implementar o serviço web
Você pode implantar o experimento como um serviço Web clássico ou como um novo serviço Web baseado em Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Implantar como um serviço Web clássico
Para implantar um serviço Web clássico derivado de nosso experimento, clique em **implantar serviço Web** abaixo da tela e selecione **implantar serviço Web [clássico]** . Machine Learning Studio (clássico) implanta o teste como um serviço Web e o leva para o painel desse serviço Web. Nessa página, você pode retornar ao experimento (**Exibir instantâneo** ou **Exibir mais recente**) e executar um teste simples do serviço Web (consulte **testar o serviço Web** abaixo). Também há informações aqui para criar aplicativos que podem acessar o serviço Web (mais sobre isso na próxima etapa deste tutorial).

![Painel do serviço Web](./media/tutorial-part3-credit-risk-deploy/publish6.png)


Você pode configurar o serviço clicando na guia **configuração** . Aqui você pode modificar o nome do serviço (ele recebe o nome do experimento por padrão) e dar a ele uma descrição. Você também pode fornecer rótulos mais amigáveis para os dados de entrada e saída.  

![Configurar o serviço Web](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>Implantar como um novo serviço Web

> [!NOTE] 
> Para implantar um novo serviço Web, você deve ter permissões suficientes na assinatura na qual você está implantando o serviço Web. Para obter mais informações, consulte [gerenciar um serviço Web usando o portal de serviços Web do Azure Machine Learning](manage-new-webservice.md). 

Para implantar um novo serviço Web derivado de nosso experimento:

1. Clique em **implantar serviço Web** abaixo da tela e selecione **implantar serviço Web [novo]** . Machine Learning Studio (clássico) transfere para a página de teste do Azure Machine Learning Web Services **Deploy** .

1. Insira um nome para o serviço Web. 

1. Para o **plano de preços**, você pode selecionar um plano de preços existente ou selecionar "criar novo" e atribuir um nome ao novo plano e selecionar a opção plano mensal. As camadas de plano assumem como padrão os planos para sua região padrão e seu serviço Web é implantado nessa região.

1. Clique em **implantar**.

Após alguns minutos, a página de **início rápido** do seu serviço Web é aberta.

Você pode configurar o serviço clicando na guia **Configurar** . Aqui você pode modificar o título do serviço e dar a ele uma descrição. 

Para testar o serviço Web, clique na guia **teste** (consulte **testar o serviço Web** abaixo). Para obter informações sobre como criar aplicativos que podem acessar o serviço Web, clique na guia **consumir** (a próxima etapa neste tutorial entrará em mais detalhes).

> [!TIP]
> Você pode atualizar o serviço Web depois de implantá-lo. Por exemplo, se você quiser alterar seu modelo, poderá editar o teste de treinamento, ajustar os parâmetros do modelo e clicar em **implantar serviço Web**, selecionar **implantar serviço Web [clássico]** ou **implantar serviço Web [novo]** . Quando você implanta o experimento novamente, ele substitui o serviço Web, agora usando seu modelo atualizado.  
> 
> 

## <a name="test-the-web-service"></a>Testar o serviço Web

Quando o serviço Web é acessado, os dados do usuário entram no módulo de **entrada do serviço Web** , onde ele é passado para o módulo [modelo de Pontuação][score-model] e pontuado. Da maneira como você configurou o experimento de previsão, o modelo espera dados no mesmo formato que o conjunto de dado de risco de crédito original.
Os resultados são retornados para o usuário do serviço Web por meio do módulo **saída do serviço Web** .

> [!TIP]
> Da maneira como você tem o experimento de previsão configurado, todos os resultados do módulo [modelo de Pontuação][score-model] são retornados. Isso inclui todos os dados de entrada mais o valor de risco de crédito e a probabilidade de pontuação. Mas você pode retornar algo diferente se desejar-por exemplo, pode retornar apenas o valor de risco de crédito. Para fazer isso, insira um módulo [selecionar colunas][select-columns] entre o [modelo de Pontuação][score-model] e a saída do **serviço Web** para eliminar as colunas que você não deseja que o serviço Web retorne. 
> 
> 

Você pode testar um serviço Web clássico em **Machine Learning Studio (clássico)** ou no portal de **serviços Azure Machine Learning Web** .
Você pode testar um novo serviço Web somente no portal do **Machine Learning Web Services** .

> [!TIP]
> Ao testar o Azure Machine Learning Portal de serviços Web, você pode fazer com que o portal crie dados de exemplo que você pode usar para testar o serviço de solicitação-resposta. Na página **Configurar** , selecione "Sim" para **dados de exemplo habilitados?** . Quando você abre a guia solicitação-resposta na página de **teste** , o portal preenche os dados de exemplo extraídos do DataSet de risco de crédito original.

### <a name="test-a-classic-web-service"></a>Testar um serviço Web clássico

Você pode testar um serviço Web clássico no Machine Learning Studio (clássico) ou no portal de serviços Machine Learning Web. 

#### <a name="test-in-machine-learning-studio-classic"></a>Testar em Machine Learning Studio (clássico)

1. Na página **painel** do serviço Web, clique no botão **testar** em ponto de **extremidade padrão**. Uma caixa de diálogo é exibida e solicita os dados de entrada para o serviço. Essas são as mesmas colunas que apareciam no conjunto de linhas de risco de crédito original.  

1. Insira um conjunto de dados e clique em **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Teste no portal de serviços Web Machine Learning

1. Na página **painel** do serviço Web, clique no link **visualização de teste** em **ponto de extremidade padrão**. A página de teste no portal de serviços Web Azure Machine Learning para o ponto de extremidade do serviço Web é aberta e solicita os dados de entrada para o serviço. Essas são as mesmas colunas que apareciam no conjunto de linhas de risco de crédito original.

2. Clique em **testar solicitação-resposta**. 

### <a name="test-a-new-web-service"></a>Testar um novo serviço Web

Você pode testar um novo serviço Web somente no portal do Machine Learning Web Services.

1. No portal de [Serviços Web do Azure Machine Learning](https://services.azureml.net/quickstart) , clique em **testar** na parte superior da página. A página de **teste** é aberta e você pode inserir dados para o serviço. Os campos de entrada exibidos correspondem às colunas que apareciam no conjunto de dados de risco de crédito original. 

1. Insira um conjunto de dados e clique em **testar solicitação-resposta**.

Os resultados do teste são exibidos no lado direito da página na coluna de saída. 


## <a name="manage-the-web-service"></a>Gerenciar o serviço Web

Depois de implantar seu serviço Web, seja clássico ou novo, você pode gerenciá-lo no portal de [serviços da web Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) .

Para monitorar o desempenho do seu serviço Web:

1. Entre no portal de [Serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/quickstart)
1. Clique em **Serviços Web**
1. Clique em seu serviço Web
1. Clique no **painel**

## <a name="access-the-web-service"></a>Aceder ao serviço web

Na etapa anterior deste tutorial, você implantou um serviço Web que usa seu modelo de previsão de risco de crédito. Agora, os usuários podem enviar dados para ele e receber resultados. 

O serviço Web é um serviço Web do Azure que pode receber e retornar dados usando APIs REST de uma das duas maneiras:  

* **Solicitação/resposta** – o usuário envia uma ou mais linhas de dados de crédito para o serviço usando um protocolo http e o serviço responde com um ou mais conjuntos de resultados.
* **Execução em lote** -o usuário armazena uma ou mais linhas de dados de crédito em um blob do Azure e, em seguida, envia o local do blob para o serviço. O serviço classifica todas as linhas de dados no blob de entrada, armazena os resultados em outro blob e retorna a URL desse contêiner.  

A maneira mais rápida e fácil de acessar um serviço Web clássico é por meio do [aplicativo Web do serviço de solicitação-resposta do Azure ml](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) ou do [modelo de aplicativo Web do serviço de execução em lote do Azure ml](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/).

Esses modelos de aplicativos Web podem criar um aplicativo Web personalizado que conheça os dados de entrada do serviço Web e o que ele retornará. Tudo o que você precisa fazer é fornecer acesso ao serviço Web e aos dados, e o modelo faz o resto.

Para obter mais informações sobre como usar os modelos de aplicativo Web, consulte [consumir um serviço web Azure Machine Learning com um modelo de aplicativo Web](/azure/machine-learning/studio/consume-web-services).



## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você concluiu estas etapas:

> [!div class="checklist"]
> * Preparar para a implementação
> * Implementar o serviço web
> * Testar o serviço Web
> * Gerenciar o serviço Web
> * Aceder ao serviço web

Você também pode desenvolver um aplicativo personalizado para acessar o serviço Web usando o código inicial fornecido para você nas linguagens C#de programação R, e Python.

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
