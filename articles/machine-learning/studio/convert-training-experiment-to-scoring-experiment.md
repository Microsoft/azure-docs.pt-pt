---
title: Preparar modelo para implantação
titleSuffix: ML Studio (classic) - Azure
description: Como preparar seu modelo treinado para implantação como um serviço Web convertendo seu teste de treinamento Machine Learning Studio (clássico) em um experimento de previsão.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 03/28/2017
ms.openlocfilehash: e397cfaa9ce521ebe3c2f46ef6cc015bff3112f7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454830"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio-classic"></a>Como preparar seu modelo para implantação no Azure Machine Learning Studio (clássico)

Azure Machine Learning Studio (clássico) fornece as ferramentas necessárias para desenvolver um modelo de análise preditiva e, em seguida, operacionalá-lo implantando-o como um serviço Web do Azure.

Para fazer isso, use o Studio (clássico) para criar um experimento chamado teste de *treinamento* -onde você treina, classifica e edita seu modelo. Quando estiver satisfeito, obter seu modelo pronto para implementar ao converter a sua experimentação de preparação para um *experimentação preditiva* que está configurado para dados de utilizador de pontuação.

Você pode ver um exemplo desse processo no [tutorial 1: prever o risco de crédito](tutorial-part1-credit-risk.md).

Este artigo analisa detalhadamente os detalhes de como uma experimentação de preparação é convertida numa experimentação preditiva e, como esse experimentação preditiva for implementada. Ao compreender estes detalhes, pode saber como configurar o seu modelo implementado para torná-lo mais eficiente.



## <a name="overview"></a>Visão geral 

O processo de conversão de uma experimentação de preparação para uma experimentação preditiva envolve três etapas:

1. Substitua o machine learning módulos de algoritmo com o seu modelo treinado.
2. Corte a experimentação para apenas esses módulos que são necessários para a classificação. Uma experimentação de preparação inclui um número de módulos que são necessários para treinamento, mas não são necessários quando é preparado o modelo.
3. Defina como o seu modelo irá aceitar dados do usuário de serviço web e os dados que vão ser devolvidos.

> [!TIP]
> Na sua experimentação de preparação estiver preocupado com a preparação e classificação de seu modelo usando seus próprios dados. Mas depois de implementada, os utilizadores irão enviar novos dados ao seu modelo e irá devolver resultados da predição. Assim, quando converter a sua experimentação de preparação para uma experimentação preditiva para prepará-la para a implementação, tenha em atenção como o modelo será utilizado por outras pessoas.
> 
> 

## <a name="set-up-web-service-button"></a>Botão de segurança de serviço Web
Depois de executar a experimentação (clique em **execute** na parte inferior da tela de experimentação), clique no **no serviço Web** botão (selecione o **serviço da Web preditiva** opção). **Segurança de serviço Web** executa para, as três etapas da conversão de sua experimentação de preparação para uma experimentação preditiva:

1. Isso economiza o seu modelo preparado na **modelos de formação** secção da paleta do módulo (para a esquerda da tela da experimentação). Em seguida, ele substitui o algoritmo de aprendizado de máquina e treina os módulos de [modelo][train-model] com o modelo treinado salvo.
2. Ele analisa a sua experimentação e remove os módulos que claramente foram utilizados apenas para fins de formação e já não são necessários.
3. Ele insere _entrada de serviço da Web_ e _saída_ módulos em localizações predefinidas na sua experimentação (estes módulos de aceitarem e retornam dados de utilizador).

Por exemplo, a experimentação seguinte prepara um modelo de árvore de decisões elevada de duas classes com dados de censo de exemplo:

![Experimentação de preparação](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

Os módulos Nesse experimento executam basicamente quatro funções diferentes:

![Funções de módulo](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Quando converter esta experimentação de preparação para uma experimentação preditiva, alguns destes módulos já não são necessários, ou que agora servem um objetivo diferente:

* **Dados** -os dados este conjunto de dados de exemplo não são utilizados durante a classificação - o utilizador do web service irá fornecer os dados para ser classificados. No entanto, os metadados deste conjunto de dados, tais como tipos de dados, é utilizado pelo modelo treinado. Por isso terá de manter o conjunto de dados na experimentação preditiva para que ele pode fornecer esses metadados.

* **PREP** - consoante os dados de utilizador que irá ser submetido para a classificação, estes módulos podem ou não podem ser necessários para processar os dados de entrada. O **no serviço de Web** botão não touch estes - precisa decidir como deseja manipulá-las.
  
    Por exemplo, neste exemplo, o conjunto de dados de exemplo pode ter valores ausentes e, portanto, um módulo [limpeza ausente][clean-missing-data] foi incluído para lidar com eles. Além disso, o conjunto de dados de exemplo inclui colunas que não são necessários para preparar o modelo. Portanto, um módulo [selecionar colunas no conjunto][select-columns] de dados foi incluído para excluir as colunas extras do fluxo de dado. Se você souber que os dados que serão enviados para pontuação por meio do serviço Web não terão valores ausentes, você poderá remover o módulo [limpar dados ausentes][clean-missing-data] . No entanto, como o módulo [selecionar colunas no conjunto][select-columns] de dados ajuda a definir as colunas que o modelo treinado espera, esse módulo precisa permanecer.

* **Treinar** -estes módulos são utilizados para preparar o modelo. Quando clica em **no serviço de Web**, estes módulos são substituídos por um módulo único que contém o modelo treinado por. Este novo módulo é guardado na **modelos de formação** secção da paleta do módulo.

* **Pontuação** -neste exemplo, o módulo [dividir dados][split] é usado para dividir o fluxo de dados em dados de teste e dados de treinamento. No experimento de previsão, não estamos mais treinando, portanto, [os dados divididos][split] podem ser removidos. Da mesma forma, o segundo módulo [modelo de Pontuação][score-model] e o módulo [modelo de avaliação][evaluate-model] são usados para comparar os resultados dos dados de teste, de modo que esses módulos não são necessários no experimento de previsão. O módulo [modelo de Pontuação][score-model] restante, no entanto, é necessário para retornar um resultado de pontuação por meio do serviço Web.

Eis o aspeto do nosso exemplo depois de clicar em **no serviço de Web**:

![Converter a experimentação preditiva](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

O trabalho realizado pelo **no serviço de Web** pode ser suficiente para preparar a sua experimentação para ser implementado como um serviço web. No entanto, pode querer fazer algum trabalho adicional específico à sua experimentação.

### <a name="adjust-input-and-output-modules"></a>Ajustar os módulos de entrada e saídos
Na sua experimentação de preparação, utilizou um conjunto de dados de treinamento e, em seguida, fizemos algum processamento para obter os dados de forma que o algoritmo de aprendizagem necessários. Se os dados esperados para receber através do serviço web não serão necessário este processamento, pode ignorá-lo: ligue a saída dos **módulo de entrada do serviço Web** para um módulo diferentes na sua experimentação. Os dados do utilizador agora vão deparar-se no modelo nesta localização.

Por exemplo, por predefinição **segurança de serviço Web** coloca o **entrada do serviço da Web** módulo na parte superior do seu fluxo de dados, conforme mostrado na figura acima. Mas podemos manualmente posicionar os **entrada de serviço da Web** anteriores os módulos de processamento de dados:

![Mover a entrada do serviço web](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

Os dados de entrada fornecidos através do serviço web agora passará diretamente para o módulo de modelo de pontuação sem qualquer tipo de pré-processamento.

Da mesma forma, por predefinição **no serviço de Web** coloca o módulo de saída do serviço Web na parte inferior do seu fluxo de dados. Neste exemplo, o serviço Web retornará ao usuário a saída do módulo [modelo de Pontuação][score-model] , que inclui o vetor de dados de entrada completo, além dos resultados da pontuação.
No entanto, se desejar retornar algo diferente, em seguida, pode adicionar módulos adicionais antes do **saída de serviço da Web** módulo. 

Por exemplo, para retornar apenas os resultados da pontuação e não o vetor inteiro de dados de entrada, adicione um módulo [selecionar colunas no conjunto][select-columns] de dados para excluir todas as colunas, exceto os resultados da pontuação. Em seguida, mova o módulo **saída do serviço Web** para a saída do módulo [selecionar colunas no conjunto de DataSet][select-columns] . A experimentação tem esta aparência:

![Mover a saída do serviço web](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>Adicionar ou remover módulos de processamento de dados adicionais
Se existirem mais módulos na experimentação que sabe que não serão necessários durante a classificação, estes podem ser removidos. Por exemplo, como mudamos o módulo **entrada do serviço Web** para um ponto após os módulos de processamento de dados, podemos remover o módulo [limpar dados ausentes][clean-missing-data] do teste de previsão.

Nosso experimentação preditiva agora esta aparência:

![Remover o módulo adicional](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Adicionar parâmetros do serviço Web opcional
Em alguns casos, pode querer permitir que o usuário do seu serviço web alterar o comportamento de módulos, quando o serviço for acedido. *Parâmetros de serviço da Web* permitem-lhe fazê-lo.

Um exemplo comum é a configuração de um módulo [importar dados][import-data] para que o usuário do serviço Web implantado possa especificar uma fonte de dados diferente quando o serviço Web for acessado. Ou configurar um módulo [exportar dados][export-data] para que um destino diferente possa ser especificado.

Pode definir parâmetros do serviço Web e associá-las com um ou mais parâmetros do módulo, e pode especificar se são necessárias ou opcionais. O utilizador do web service fornece valores para estes parâmetros quando o serviço é acedido e as ações de módulo são modificadas em conformidade.

Para obter mais informações sobre quais parâmetros de serviço Web são e como usá-los, consulte [usando Azure Machine Learning parâmetros de serviço Web][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Implementar a experimentação preditiva como um serviço web
Agora que a experimentação preditiva foi suficientemente preparada, pode implementá-lo como um serviço web do Azure. Usando o web service, os utilizadores podem enviar dados para o seu modelo e o modelo retornará seu previsões.

Para obter mais informações sobre o processo de implantação completo, consulte [implantar um serviço web Azure Machine Learning][deploy]

[deploy]: deploy-a-machine-learning-web-service.md

<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
