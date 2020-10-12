---
title: 'ML Studio (clássico) tutorial 3: Implementar modelos de risco de crédito - Azure'
description: Um tutorial detalhado que mostra como criar uma solução de análise preditiva para a avaliação do risco de crédito no Azure Machine Learning Studio (clássico). Este tutorial é parte três de uma série tutorial em três partes. Mostra como implementar um modelo como um serviço web.
keywords: risco de crédito, solução de análise preditiva,avaliação de risco, implantação, serviço web
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: 148a3deb23219036f2fd9d8fe4dad93e9fb96b9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91348770"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio-classic"></a>Tutorial 3: Implementar modelo de risco de crédito - Azure Machine Learning Studio (clássico)

**APLICA-SE A:** ![ sim ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) no ![ ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  


Neste tutorial, você dá uma olhada alargada no processo de desenvolvimento de uma solução de análise preditiva. Desenvolve-se um modelo simples no Machine Learning Studio (clássico).  Em seguida, implementa o modelo como um serviço web Azure Machine Learning.  Este modelo implementado pode fazer previsões usando novos dados. Este tutorial é **parte três de uma série tutorial em três partes.**

Suponhamos que precisa de prever o risco de crédito de um indivíduo com base nas informações fornecidas sobre uma aplicação de crédito.  

A avaliação do risco de crédito é um problema complexo, mas este tutorial irá simplificar um pouco. Você vai usá-lo como um exemplo de como você pode criar uma solução de análise preditiva usando Microsoft Azure Machine Learning Studio (clássico). Você usará o Azure Machine Learning Studio (clássico) e um serviço web machine learning para esta solução. 

Neste tutorial em três partes, começa-se com dados de risco de crédito disponíveis ao público.  Depois desenvolve-se e treina-se um modelo preditivo.  Finalmente implementa o modelo como um serviço web.

Na [primeira parte do tutorial,](tutorial-part1-credit-risk.md)criou um espaço de trabalho do Machine Learning Studio (clássico), carregou dados e criou uma experiência.

Na [segunda parte do tutorial,](tutorial-part2-credit-risk-train.md)treinaste e avaliaste modelos.

Nesta parte do tutorial:

> [!div class="checklist"]
> * Preparar para a implementação
> * Implementar o serviço Web
> * Testar o serviço Web
> * Gerir o serviço web
> * Aceder ao serviço web

## <a name="prerequisites"></a>Pré-requisitos

Complete [a segunda parte do tutorial.](tutorial-part2-credit-risk-train.md)

## <a name="prepare-for-deployment"></a>Preparar para a implementação
Para dar aos outros a oportunidade de usar o modelo preditivo que desenvolveu neste tutorial, pode implantá-lo como um serviço web no Azure.

Até agora tens andado a experimentar treinar o nosso modelo. Mas o serviço implantado já não vai fazer treino - vai gerar novas previsões ao marcar a entrada do utilizador com base no nosso modelo. Vamos preparar-nos para converter esta experiência de uma experiência de ***treino*** para uma experiência ***preditiva.*** 

A preparação para a implantação é um processo em três etapas:  

1. Remova um dos modelos
1. Converta a experiência de *treino* que criou numa *experiência preditiva*
1. Implementar a experiência preditiva como um serviço web

### <a name="remove-one-of-the-models"></a>Remova um dos modelos

Primeiro, tens de aparar um pouco esta experiência. atualmente tem dois modelos diferentes na experiência, mas só pretende usar um modelo quando implementar este como um serviço web.  

Digamos que decidiu que o modelo de árvores impulsionadas teve um desempenho melhor do que o modelo SVM. Assim, a primeira coisa a fazer é remover o módulo [de máquina de vetor de suporte de duas classes][two-class-support-vector-machine] e os módulos que foram usados para treiná-lo. É possível que queira fazer uma cópia da experiência primeiro clicando em **Save As** na parte inferior da tela de experiência.

é necessário eliminar os seguintes módulos:  

* [Máquina de Vetores de Suporte de Duas Classes][two-class-support-vector-machine]
* [Modelo de trem][train-model] e módulos de modelo de [pontuação][score-model] que estavam ligados a ele
* [Normalizar os Dados][normalize-data] (ambos)
* [Avaliar Modelo][evaluate-model] (porque terminamos de avaliar os modelos)

Selecione cada módulo e prima a tecla Eliminar ou clique à direita no módulo e selecione **Delete**. 

![Destaques que módulos eliminar para remover o modelo da máquina de vetor de suporte](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

O nosso modelo deve agora ser mais ou menos assim:

![Experiência resultante quando o modelo de máquina de vetor de suporte é eliminado](./media/tutorial-part3-credit-risk-deploy/publish3.png)

Agora estamos prontos para implementar este modelo usando a [Árvore de Decisão Reforçada de Duas Classes.][two-class-boosted-decision-tree]

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter a experiência de treino numa experiência preditiva

Para preparar este modelo, é necessário converter esta experiência de treino numa experiência preditiva. Isto envolve três etapas:

1. Salve o modelo que treinou e, em seguida, substitua os nossos módulos de treino
1. Corte a experiência para remover módulos que só eram necessários para o treino
1. Definir onde o serviço web aceitará a entrada e onde gera a saída

você poderia fazê-lo manualmente, mas felizmente todos os três passos podem ser realizados clicando em **Configurar** o Serviço Web na parte inferior da tela de experiência (e selecionando a opção **De Serviço Web Preditivo).**

> [!TIP]
> Se quiser mais detalhes sobre o que acontece quando converte uma experiência de treino para uma experiência preditiva, consulte [Como preparar o seu modelo para implantação no Azure Machine Learning Studio (clássico)](deploy-a-machine-learning-web-service.md).

Ao clicar em **Configurar o Serviço Web,** várias coisas acontecem:

* O modelo treinado é convertido para um único módulo **Model Treinado** e armazenado na paleta de módulos à esquerda da tela de experiência (pode encontrá-lo em **Modelos Treinados)**
* Os módulos utilizados para a formação são removidos; especificamente:
  * [Árvore de Decisões Elevada de Duas Classes][two-class-boosted-decision-tree]
  * [Preparar Modelo][train-model]
  * [Dividir Dados][split]
  * o segundo módulo [executo R Script][execute-r-script] que foi usado para dados de teste
* O modelo treinado salvo é adicionado de volta à experiência
* **São** adicionados módulos de entrada de serviço web e de saída de **serviço Web** (estes identificam onde os dados do utilizador entrarão no modelo e quais os dados devolvidos, quando o serviço web é acedido)

> [!NOTE]
> Pode ver que a experiência é guardada em duas partes sob separadores que foram adicionadas no topo da tela experimentos. A experiência de treino original está sob a **experiência tab Training**, e a experiência preditiva recém-criada está sob experiência **preditiva.** A experiência preditiva é aquela que vais implementar como um serviço web.

você precisa dar um passo adicional com esta experiência em particular.
adicionou dois módulos [executar R Script][execute-r-script] para fornecer uma função de ponderação aos dados. Era um truque que precisavas para treinar e testar, para que pudesses tirar esses módulos no modelo final.
O Machine Learning Studio (clássico) removeu um módulo [executo R script][execute-r-script] quando removeu o módulo [Split.][split] Agora pode remover o outro e ligar o [Editor de Metadados][metadata-editor] diretamente ao [Modelo de Pontuação][score-model].    

A nossa experiência deve agora ser assim:  

![Marcando o modelo treinado](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> Pode estar a perguntar-se por que deixou o conjunto de dados do cartão de crédito alemão da UCI na experiência preditiva. O serviço vai obter os dados do utilizador, não o conjunto de dados original, então por que deixar o conjunto de dados original no modelo?
> 
> É verdade que o serviço não precisa dos dados originais do cartão de crédito. Mas precisa do esquema para esses dados, que inclui informações como quantas colunas existem e quais as colunas numéricas. Esta informação de esquema é necessária para interpretar os dados do utilizador. deixa estes componentes ligados de modo a que o módulo de pontuação tenha o esquema de conjunto de dados quando o serviço está em funcionamento. Os dados não são usados, só o esquema.  
> 
>Uma coisa importante a notar é que se o seu conjunto de dados original continha o rótulo, então o esquema esperado da entrada web também esperará uma coluna com a etiqueta! Uma forma de contornar isto é remover o rótulo, e quaisquer outros dados que estavam no conjunto de dados de treino, mas não estarão nas entradas da web, antes de ligar o conjunto de dados de entrada e treino da web a um módulo comum. 
> 

Executar a experiência uma última vez (clique em **Executar**.) Se pretender verificar se o modelo ainda está a funcionar, clique na saída do módulo ['Modelo de Pontuação'][score-model] e selecione **Ver Resultados**. Pode ver que os dados originais são apresentados, juntamente com o valor de risco de crédito ("Etiquetas Pontuadas") e o valor de probabilidade de pontuação ("Probabilidades Pontuadas".) 

## <a name="deploy-the-web-service"></a>Implementar o serviço Web
Pode implementar a experiência como um serviço web clássico, ou como um novo serviço web baseado no Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Implementar como um serviço web clássico
Para implementar um serviço web clássico derivado da nossa experiência, clique em **Implementar o Serviço Web** abaixo da tela e selecione Implementar o Serviço Web **[Clássico]**. O Machine Learning Studio (clássico) implementa a experiência como um serviço web e leva-o ao painel de instrumentos para esse serviço web. A partir desta página, pode voltar à experiência **(Ver instantâneo** ou **ver o mais recente)** e realizar um teste simples do serviço web (ver **testar o serviço web abaixo).** Há também informação aqui para criar aplicações que podem aceder ao serviço web (mais sobre isso no próximo passo deste tutorial).

![Painel de atendimento web](./media/tutorial-part3-credit-risk-deploy/publish6.png)


Pode configurar o serviço clicando no **separador CONFIGURAÇÃO.** Aqui pode modificar o nome de serviço (foi dado o nome da experiência por padrão) e dar-lhe uma descrição. Também pode dar etiquetas mais amigáveis para os dados de entrada e saída.  

![Configure o serviço web](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>Implementar como um novo serviço web

> [!NOTE] 
> Para implementar um Novo serviço web tem de ter permissões suficientes na subscrição para a qual está a implementar o serviço web. Para obter mais informações, consulte [Gerir um serviço web utilizando o portal Azure Machine Learning Web Services](manage-new-webservice.md). 

Para implementar um novo serviço web derivado da nossa experiência:

1. Clique em Implementar o **Serviço Web** abaixo da tela e selecione Implementar o Serviço **Web [Novo]**. O Machine Learning Studio (clássico) transfere-o para a página Azure Machine Learning web Services **Deploy Experiment.**

1. Insira um nome para o serviço web. 

1. Para **o Plano de Preços,** pode selecionar um plano de preços existente, ou selecionar "Criar novo" e dar um nome ao novo plano e selecionar a opção de plano mensal. Os níveis de plano não são padrão para os planos para a sua região padrão e o seu serviço web é implantado nessa região.

1. Clique em **Implementar**.

Após alguns minutos, abre a página **Quickstart** para o seu serviço web.

Pode configurar o serviço clicando no **separador Configurar.** Aqui pode modificar o título de serviço e dar-lhe uma descrição. 

Para testar o serviço web, clique no separador **Teste** (ver **teste o serviço web abaixo).** Para obter informações sobre a criação de aplicações que possam aceder ao serviço web, clique no **separador Consumir** (o próximo passo neste tutorial será mais detalhado).

> [!TIP]
> Pode atualizar o serviço web depois de o ter implantado. Por exemplo, se quiser alterar o seu modelo, então pode editar a experiência de treino, ajustar os parâmetros do modelo e clicar em **Implementar o Serviço Web,** selecionando **o Serviço Web de Implementação [Classic]** ou **implementar o Serviço Web [Novo]**. Quando volta a implementar a experiência, substitui o serviço web, utilizando agora o seu modelo atualizado.  
> 
> 

## <a name="test-the-web-service"></a>Testar o serviço Web

Quando o serviço web é acedido, os dados do utilizador entram através do módulo de entrada de **serviço Web** onde é passado para o módulo ['Modelo de Pontuação'][score-model] e pontuado. Da forma como configura a experiência preditiva, o modelo espera dados no mesmo formato que o conjunto original de dados de risco de crédito.
Os resultados são devolvidos ao utilizador a partir do serviço web através do módulo de saída do **serviço Web.**

> [!TIP]
> Da forma como tem a experiência preditiva configurada, todos os resultados do módulo ['Modelo de Pontuação'][score-model] são devolvidos. Isto inclui todos os dados de entrada mais o valor de risco de crédito e a probabilidade de pontuação. Mas pode devolver algo diferente se quiser - por exemplo, pode devolver apenas o valor de risco de crédito. Para isso, [insira][select-columns] um módulo Select Columns entre o [Modelo de Pontuação][score-model] e a saída do **serviço Web** para eliminar colunas que não pretende que o serviço web devolva. 
> 
> 

Você pode testar um serviço web clássico no **Machine Learning Studio (clássico)** ou no portal **Azure Machine Learning Web Services.**
Só é possível testar um novo serviço web no portal **Machine Learning Web Services.**

> [!TIP]
> Ao testar no portal Azure Machine Learning Web Services, pode ter o portal a criar dados de amostra que pode utilizar para testar o serviço de Request-Response. Na página **Configurar,** selecione "Sim" para **dados de amostra ativados?** Quando abre o separador Request-Response na página **Teste,** o portal preenche os dados da amostra retirados do conjunto de dados originais de risco de crédito.

### <a name="test-a-classic-web-service"></a>Teste um serviço web clássico

Você pode testar um serviço web clássico no Machine Learning Studio (clássico) ou no portal Machine Learning Web Services. 

#### <a name="test-in-machine-learning-studio-classic"></a>Teste no Estúdio de Aprendizagem automática (clássico)

1. Na página **DASHBOARD** para o serviço web, clique no botão **Teste** no **Ponto final predefinido**. Um diálogo aparece e pede-lhe os dados de entrada para o serviço. Estas são as mesmas colunas que apareceram no conjunto de dados originais de risco de crédito.  

1. Introduza um conjunto de dados e, em seguida, clique **em OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Teste no portal machine learning web services

1. Na página **DASHBOARD** para o serviço web, clique no link **de pré-visualização do Teste** no Ponto final **predefinido**. A página de teste no portal Azure Machine Learning Web Services para o ponto final do serviço web abre e pede-lhe os dados de entrada para o serviço. Estas são as mesmas colunas que apareceram no conjunto de dados originais de risco de crédito.

2. Clique em **Teste de Resposta ao Pedido**de Teste . 

### <a name="test-a-new-web-service"></a>Teste um novo serviço web

Só é possível testar um novo serviço web no portal Machine Learning Web Services.

1. No portal [Azure Machine Learning Web Services,](https://services.azureml.net/quickstart) clique em **Testar** no topo da página. A página **De Teste** abre e pode inserir dados para o serviço. Os campos de entrada apresentados correspondem às colunas que apareceram no conjunto de dados de risco de crédito original. 

1. Introduza um conjunto de dados e, em seguida, clique em **Test Request-Response**.

Os resultados do teste são apresentados no lado direito da página na coluna de saída. 


## <a name="manage-the-web-service"></a>Gerir o serviço web

Uma vez implementado o seu serviço web, seja clássico ou novo, pode geri-lo a partir do portal [Microsoft Azure Machine Learning Web Services.](https://services.azureml.net/quickstart)

Para monitorizar o desempenho do seu serviço web:

1. Inscreva-se no portal [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart)
1. Clique **nos serviços web**
1. Clique no seu serviço web
1. Clique no **Painel de Instrumentos**

## <a name="access-the-web-service"></a>Aceder ao serviço web

No passo anterior neste tutorial, implementou um serviço web que utiliza o seu modelo de previsão de risco de crédito. Agora os utilizadores podem enviar dados para os mesmos e receber resultados. 

O serviço Web é um serviço web Azure que pode receber e devolver dados usando APIs REST de uma de duas maneiras:  

* **Pedido/Resposta** - O utilizador envia uma ou mais linhas de dados de crédito para o serviço através de um protocolo HTTP, e o serviço responde com um ou mais conjuntos de resultados.
* **Execução do Lote** - O utilizador armazena uma ou mais linhas de dados de crédito numa bolha Azure e, em seguida, envia a localização do blob para o serviço. O serviço marca todas as linhas de dados na bolha de entrada, armazena os resultados em outra bolha, e devolve o URL desse recipiente. 

> [!NOTE]
> Os nomes das colunas de recurso no Studio (clássico) são **sensíveis a casos.** Certifique-se de que os seus dados de entrada para invocar o serviço web têm os mesmos nomes de colunas que no conjunto de dados de treino.

Para obter mais informações sobre o acesso e consumo do serviço web, consulte [Consume a Azure Machine Learning Web service com um modelo de aplicação web](/azure/machine-learning/studio/consume-web-services).



## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, completou estes passos:

> [!div class="checklist"]
> * Preparar para a implementação
> * Implementar o serviço Web
> * Testar o serviço Web
> * Gerir o serviço web
> * Aceder ao serviço web

Também pode desenvolver uma aplicação personalizada para aceder ao serviço web usando o código de arranque fornecido para si em linguagens de programação R, C#e Python.

> [!div class="nextstepaction"]
> [Consumir um serviço Web de Aprendizagem automática Azure](consume-web-services.md)

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
