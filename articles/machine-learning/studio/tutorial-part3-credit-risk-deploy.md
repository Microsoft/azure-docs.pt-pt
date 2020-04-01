---
title: 'Tutorial 3: Implementar modelo de risco de crédito'
titleSuffix: Azure Machine Learning Studio (classic)
description: Um tutorial detalhado que mostra como criar uma solução de análise preditiva para avaliação de risco de crédito no Azure Machine Learning Studio (clássico). Este tutorial é a terceira parte de uma série tutorial em três partes. Mostra como implementar um modelo como um serviço web.
keywords: risco de crédito, solução de análise preditiva,avaliação de risco, implantação, serviço web
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 9fb0b59374edf322e5e2221b90e912ee2c665bac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79204158"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio-classic"></a>Tutorial 3: Implementar modelo de risco de crédito - Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Neste tutorial, você olha para o processo de desenvolvimento de uma solução de análise preditiva. Desenvolve-se um modelo simples no Machine Learning Studio (clássico).  Em seguida, implementa o modelo como um serviço web Azure Machine Learning.  Este modelo implementado pode fazer previsões usando novos dados. Este tutorial é **a terceira parte de uma série tutorial em três partes.**

Suponhamos que precisa de prever o risco de crédito de um indivíduo com base nas informações fornecidas sobre uma aplicação de crédito.  

A avaliação do risco de crédito é um problema complexo, mas este tutorial vai simplificar um pouco. Vais usá-lo como um exemplo de como podes criar uma solução de análise preditiva utilizando o Microsoft Azure Machine Learning Studio (clássico). Você usará o Azure Machine Learning Studio (clássico) e um serviço web machine learning para esta solução. 

Neste tutorial em três partes, começa com dados de risco de crédito disponíveis ao público.  Depois desenvolves e treinas um modelo preditivo.  Finalmente implementa o modelo como um serviço web.

Na [primeira parte do tutorial,](tutorial-part1-credit-risk.md)criou um espaço de trabalho do Machine Learning Studio (clássico), carregou dados e criou uma experiência.

Na [segunda parte do tutorial,](tutorial-part2-credit-risk-train.md)treinou e avaliou modelos.

Nesta parte do tutorial:

> [!div class="checklist"]
> * Preparar para a implementação
> * Implementar o serviço web
> * Testar o serviço Web
> * Gerir o serviço web
> * Aceder ao serviço web

## <a name="prerequisites"></a>Pré-requisitos

Complete a [segunda parte do tutorial.](tutorial-part2-credit-risk-train.md)

## <a name="prepare-for-deployment"></a>Preparar para a implementação
Para dar a outros a oportunidade de usar o modelo preditivo que desenvolveu neste tutorial, pode implantá-lo como um serviço web no Azure.

Até agora tens andado a experimentar treinar o nosso modelo. Mas o serviço implantado já não vai fazer treino - vai gerar novas previsões marcando a entrada do utilizador com base no nosso modelo. Vamos preparar-nos para converter esta experiência de uma experiência de ***treino*** para uma experiência ***preditiva.*** 

A preparação para a implantação é um processo em três etapas:  

1. Remova um dos modelos
1. Converta a experiência de *treino* que criou numa *experiência preditiva*
1. Implementar a experiência preditiva como um serviço web

### <a name="remove-one-of-the-models"></a>Remova um dos modelos

Primeiro, tens de reduzir um pouco esta experiência. atualmente tem dois modelos diferentes na experiência, mas só quer usar um modelo quando implementa este como um serviço web.  

Digamos que decidiu que o modelo de árvore impulsionado teve um desempenho melhor do que o modelo SVM. Portanto, a primeira coisa a fazer é remover o módulo de máquina de [vetoria][two-class-support-vector-machine] de suporte de duas classes e os módulos que foram usados para treiná-lo. É possível que queira fazer uma cópia da experiência primeiro clicando em **Guardar como** na parte inferior da tela da experiência.

é necessário eliminar os seguintes módulos:  

* [Máquina de Vetores de Suporte de Duas Classes][two-class-support-vector-machine]
* [Modelo de comboio][train-model] e módulos de modelo de [pontuação][score-model] que estavam ligados a ele
* [Normalizar os dados][normalize-data] (ambos)
* [Avaliar Modelo][evaluate-model] (porque estamos acabados de avaliar os modelos)

Selecione cada módulo e prima a tecla Eliminar ou clique no módulo e selecione **Delete**. 

![Destaques quais os módulos a eliminar para remover o modelo da máquina de vetores de suporte](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

O nosso modelo deve agora ser parecido com isto:

![Experiência resultante quando o modelo da máquina de vetor de suporte é eliminado](./media/tutorial-part3-credit-risk-deploy/publish3.png)

Agora estamos prontos para implementar este modelo usando a [Árvore de Decisão Impulsionada de Duas Classes.][two-class-boosted-decision-tree]

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter a experiência de treino numa experiência preditiva

Para preparar este modelo para a implantação, precisa converter esta experiência de treino numa experiência preditiva. Isto envolve três passos:

1. Guarde o modelo que treinou e, em seguida, substitua os nossos módulos de treino
1. Corte a experiência para remover módulos que só eram necessários para o treino
1. Defina onde o serviço web irá aceitar a entrada e onde gera a saída

poderia fazê-lo manualmente, mas felizmente todos os três passos podem ser realizados clicando em **Configurar** o Serviço Web na parte inferior da tela de experiência (e selecionando a opção **Predictive Web Service).**

> [!TIP]
> Se quiser mais detalhes sobre o que acontece quando converte uma experiência de treino para uma experiência preditiva, veja [como preparar o seu modelo para implantação no Azure Machine Learning Studio (clássico)](convert-training-experiment-to-scoring-experiment.md).

Quando clica em **Configurar o Serviço Web,** várias coisas acontecem:

* O modelo treinado é convertido num único módulo **Modelo Treinado** e armazenado na paleta de módulos à esquerda da tela de experiência (pode encontrá-lo em **Modelos Treinados)**
* Os módulos utilizados para o treino são removidos; especificamente:
  * [Árvore de Decisões Elevada de Duas Classes][two-class-boosted-decision-tree]
  * [Modelo de comboio][train-model]
  * [Dividir Dados][split]
  * o segundo módulo [execute R Script][execute-r-script] que foi usado para dados de teste
* O modelo treinado salvo é adicionado de volta à experiência
* Os módulos de entrada de **serviço web** e de saída do **serviço Web** são adicionados (estes identificam onde os dados do utilizador vão introduzir o modelo, e que dados são devolvidos, quando o serviço web é acedido)

> [!NOTE]
> Pode ver que a experiência é guardada em duas partes sob separadores que foram adicionados no topo da tela de experimentação. A experiência de treino original está sob a experiência de **treino**do separador , e a experiência preditiva recém-criada está sob **experiência preditiva.** A experiência preditiva é aquela que vai supor como um serviço web.

precisa dar um passo adicional com esta experiência em particular.
adicionou dois módulos [execute R Script][execute-r-script] para fornecer uma função de ponderação aos dados. Era só um truque que precisavas para treinar e testar, para que pudesses eliminar esses módulos no modelo final.
O Machine Learning Studio (clássico) removeu um módulo [execute R Script][execute-r-script] quando removeu o módulo [Split.][split] Agora pode remover o outro e ligar o Editor de [Metadados][metadata-editor] diretamente ao [Modelo de Pontuação][score-model].    

A nossa experiência deve agora ser assim:  

![Pontuar o modelo treinado](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> Pode estar a perguntar-se por que deixou o conjunto de dados do Cartão de Crédito Alemão UCI na experiência preditiva. O serviço vai marcar os dados do utilizador, não o conjunto de dados original, então por que deixar o conjunto de dados original no modelo?
> 
> É verdade que o serviço não precisa dos dados originais do cartão de crédito. Mas precisa do esquema para esses dados, que inclui informações como quantas colunas existem e quais as colunas numéricas. Esta informação de esquema é necessária para interpretar os dados do utilizador. deixa estes componentes ligados de modo a que o módulo de pontuação tenha o esquema de conjunto de dados quando o serviço está em execução. Os dados não são usados, apenas o esquema.  
> 
>Uma coisa importante a notar é que se o seu conjunto de dados original contivesse a etiqueta, então o esquema esperado da entrada web também esperaria uma coluna com a etiqueta! Uma forma de contornar esta é remover a etiqueta, e quaisquer outros dados que estavam no conjunto de dados de treino, mas não estarão nas inputs web, antes de ligar a entrada web e treinar o conjunto de dados num módulo comum. 
> 

Executar a experiência uma última vez (clique **em Executar**.) Se quiser verificar se o modelo ainda está a funcionar, clique na saída do módulo ['Modelo de Pontuação'][score-model] e selecione Resultados de **Visualização**. Pode ver que os dados originais são apresentados, juntamente com o valor de risco de crédito ("Etiquetas Pontuadas") e o valor da probabilidade de pontuação ("Probabilidades Pontuadas".) 

## <a name="deploy-the-web-service"></a>Implementar o serviço web
Você pode implementar a experiência como um serviço web clássico, ou como um novo serviço web que é baseado em Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Implemente como um serviço web clássico
Para implementar um serviço web clássico derivado da nossa experiência, clique em Implementar o **Serviço Web** abaixo da tela e selecione Deploy Web **Service [Classic]**. O Machine Learning Studio (clássico) implementa a experiência como um serviço web e leva-o ao painel de instrumentos para esse serviço web. A partir desta página, pode voltar à experiência **(Ver snapshot** ou **Ver mais recente)** e fazer um teste simples do serviço web (ver **Testar o serviço web** abaixo). Há também aqui informação para a criação de aplicações que possam aceder ao serviço web (mais sobre isso no próximo passo deste tutorial).

![Painel de instrumentos de serviço web](./media/tutorial-part3-credit-risk-deploy/publish6.png)


Pode configurar o serviço clicando no separador **CONFIGURAÇÃO.** Aqui pode modificar o nome de serviço (é dado o nome da experiência por padrão) e dar-lhe uma descrição. Também pode dar etiquetas mais amigáveis para os dados de entrada e saída.  

![Configure o serviço web](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>Implementar como um novo serviço web

> [!NOTE] 
> Para implementar um novo serviço web deve ter permissões suficientes na subscrição à qual está a implementar o serviço web. Para mais informações, consulte [Gerir um serviço web utilizando o portal Azure Machine Learning Web Services](manage-new-webservice.md). 

Para implementar um novo serviço web derivado da nossa experiência:

1. Clique em implementar o **Serviço Web** abaixo da tela e selecione Implementar o Serviço **Web [Novo]**. O Machine Learning Studio (clássico) transfere-o para a página de implementação de experiências de implementação de serviços web De implantação de serviços de **aprendizagem** automática do Azure Machine Learning.

1. Insira um nome para o serviço web. 

1. Para **o Plano de Preços,** pode selecionar um plano de preços existente, ou selecionar "Criar novo" e dar um nome ao novo plano e selecionar a opção de plano mensal. Os níveis de plano sem acordo para os planos para a sua região padrão e o seu serviço web estão implantados nessa região.

1. Clique em **Implementar**.

Após alguns minutos, abre a página **Quickstart** para o seu serviço web.

Pode configurar o serviço clicando no separador **Configure.** Aqui pode modificar o título de serviço e dar-lhe uma descrição. 

Para testar o serviço web, clique no separador **Teste** (ver **Testar o serviço web** abaixo). Para obter informações sobre a criação de aplicações que possam aceder ao serviço web, clique no separador **Consumir** (o próximo passo neste tutorial entrará em mais detalhes).

> [!TIP]
> Pode atualizar o serviço web depois de o ter implementado. Por exemplo, se quiser alterar o seu modelo, pode editar a experiência de treino, ajustar os parâmetros do modelo e clicar em **Implementar o Web Service,** selecionando o **Deploy Web Service [Classic]** ou **implementar o Web Service [Novo]**. Quando volta a implementar a experiência, substitui o serviço web, utilizando agora o seu modelo atualizado.  
> 
> 

## <a name="test-the-web-service"></a>Testar o serviço Web

Quando o serviço web é acedido, os dados do utilizador entram através do módulo de entrada do **serviço Web** onde é passado para o módulo ['Modelo de Pontuação'][score-model] e pontuado. Da forma como configuraste a experiência preditiva, o modelo espera dados no mesmo formato que o conjunto original de dados de risco de crédito.
Os resultados são devolvidos ao utilizador a partir do serviço web através do módulo de saída do **serviço Web.**

> [!TIP]
> A forma como tem a experiência preditiva configurada, os resultados completos do módulo [Score Model][score-model] são devolvidos. Isto inclui todos os dados de entrada mais o valor do risco de crédito e a probabilidade de pontuação. Mas pode devolver algo diferente se quiser - por exemplo, pode devolver apenas o valor do risco de crédito. Para tal, insira um módulo [Select Columns][select-columns] entre o [Score Model][score-model] e a saída do serviço **Web** para eliminar colunas que não deseja que o serviço web regresse. 
> 
> 

Pode testar um serviço web Clássico, quer no **Machine Learning Studio (clássico)** quer no portal **Azure Machine Learning Web Services.**
Só pode testar um novo serviço web no portal **machine learning Web Services.**

> [!TIP]
> Ao testar no portal Azure Machine Learning Web Services, pode fazer com que o portal crie dados de amostra que possa utilizar para testar o serviço de Resposta a Pedidos. Na página **Configure,** selecione "Sim" para **dados de amostra ativados?** Ao abrir o separador Request-Response na página **de Teste,** o portal preenche os dados da amostra recolhidos do conjunto original de dados de risco de crédito.

### <a name="test-a-classic-web-service"></a>Teste um serviço web clássico

Pode testar um serviço web Clássico no Machine Learning Studio (clássico) ou no portal machine learning Web Services. 

#### <a name="test-in-machine-learning-studio-classic"></a>Teste em Machine Learning Studio (clássico)

1. Na página **DASHBOARD** para o serviço web, clique no botão **Teste** em **predefinição**. Um diálogo aparece e pede-lhe os dados de entrada para o serviço. Estas são as mesmas colunas que apareceram no conjunto original de dados de risco de crédito.  

1. Introduza um conjunto de dados e, em seguida, clique **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Teste no portal de Serviços Web de Aprendizagem Automática

1. Na página **DASHBOARD** para o serviço web, clique no link de **pré-visualização do Teste** em **predefinição**. A página de teste no portal Azure Machine Learning Web Services para o ponto final do serviço web abre e pede-lhe os dados de entrada para o serviço. Estas são as mesmas colunas que apareceram no conjunto original de dados de risco de crédito.

2. Clique no **teste de resposta ao pedido**. 

### <a name="test-a-new-web-service"></a>Teste um novo serviço web

Só pode testar um novo serviço web no portal machine learning Web Services.

1. No portal [Azure Machine Learning Web Services,](https://services.azureml.net/quickstart) clique em **Testar** no topo da página. A página **de Teste** abre e pode inserir dados para o serviço. Os campos de entrada apresentados correspondem às colunas que apareceram no conjunto original de dados de risco de crédito. 

1. Introduza um conjunto de dados e, em seguida, clique em **Teste pedido-resposta**.

Os resultados do teste são apresentados no lado direito da página na coluna de saída. 


## <a name="manage-the-web-service"></a>Gerir o serviço web

Depois de ter implementado o seu serviço web, seja Classic ou New, pode geri-lo a partir do portal [microsoft Azure Machine Learning Web Services.](https://services.azureml.net/quickstart)

Para monitorizar o desempenho do seu serviço web:

1. Inscreva-se no portal [de Serviços Web](https://services.azureml.net/quickstart) de Aprendizagem automática do Microsoft Azure
1. Clique em **serviços Web**
1. Clique no seu serviço web
1. Clique no **Dashboard**

## <a name="access-the-web-service"></a>Aceder ao serviço web

No passo anterior neste tutorial, implementou um serviço web que utiliza o seu modelo de previsão de risco de crédito. Agora os utilizadores podem enviar-lhe dados e receber resultados. 

O serviço Web é um serviço web Azure que pode receber e devolver dados usando APIs REST de uma de duas maneiras:  

* **Pedido/Resposta** - O utilizador envia uma ou mais linhas de dados de crédito para o serviço utilizando um protocolo HTTP, e o serviço responde com um ou mais conjuntos de resultados.
* **Execução de Lote** - O utilizador armazena uma ou mais linhas de dados de crédito numa bolha Azure e envia a localização blob para o serviço. O serviço marca todas as linhas de dados na bolha de entrada, armazena os resultados em outra bolha, e devolve o URL desse recipiente.  

Para obter mais informações sobre o acesso e o consumo do serviço web, consulte [Consumir um serviço Web de Aprendizagem Automática Azure com um modelo de aplicação web](/azure/machine-learning/studio/consume-web-services).



## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, completou estes passos:

> [!div class="checklist"]
> * Preparar para a implementação
> * Implementar o serviço web
> * Testar o serviço Web
> * Gerir o serviço web
> * Aceder ao serviço web

Também pode desenvolver uma aplicação personalizada para aceder ao serviço web utilizando o código de arranque fornecido para si em linguagens de programação R, C#e Python.

> [!div class="nextstepaction"]
> [Consumir um serviço Web de aprendizagem automática Azure](consume-web-services.md)

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
