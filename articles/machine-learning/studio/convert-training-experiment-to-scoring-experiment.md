---
title: Preparar modelo para implantação
titleSuffix: ML Studio (classic) - Azure
description: Como preparar o seu modelo treinado para a implantação como serviço web, convertendo a sua experiência de treino do Machine Learning Studio (clássico) para uma experiência preditiva.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.date: 03/28/2017
ms.openlocfilehash: 82db8fb2d8b8fc38542e202cc9e590663dc8bbab
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77150064"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio-classic"></a>Como preparar o seu modelo para implantação no Azure Machine Learning Studio (clássico)

O Azure Machine Learning Studio (clássico) dá-lhe as ferramentas necessárias para desenvolver um modelo de análise preditiva e, em seguida, operacionalizá-lo implantando-o como um serviço web Azure.

Para isso, você usa Studio (clássico) para criar uma experiência - chamada experiência de *treino* - onde treina, marca e edita o seu modelo. Uma vez satisfeito, você tem o seu modelo pronto a ser implementado, convertendo a sua experiência de treino para uma *experiência preditiva* que está configurada para marcar dados do utilizador.

Pode ver um exemplo deste processo no [Tutorial 1: Prever o risco](tutorial-part1-credit-risk.md)de crédito.

Este artigo analisa detalhadamente os detalhes de como uma experimentação de preparação é convertida numa experimentação preditiva e, como esse experimentação preditiva for implementada. Ao compreender estes detalhes, pode saber como configurar o seu modelo implementado para torná-lo mais eficiente.



## <a name="overview"></a>Descrição geral 

O processo de conversão de uma experimentação de preparação para uma experimentação preditiva envolve três etapas:

1. Substitua o machine learning módulos de algoritmo com o seu modelo treinado.
2. Corte a experimentação para apenas esses módulos que são necessários para a classificação. Uma experimentação de preparação inclui um número de módulos que são necessários para treinamento, mas não são necessários quando é preparado o modelo.
3. Defina como o seu modelo irá aceitar dados do usuário de serviço web e os dados que vão ser devolvidos.

> [!TIP]
> Na sua experimentação de preparação estiver preocupado com a preparação e classificação de seu modelo usando seus próprios dados. Mas depois de implementada, os utilizadores irão enviar novos dados ao seu modelo e irá devolver resultados da predição. Assim, quando converter a sua experimentação de preparação para uma experimentação preditiva para prepará-la para a implementação, tenha em atenção como o modelo será utilizado por outras pessoas.
> 
> 

## <a name="set-up-web-service-button"></a>Botão de segurança de serviço Web
Depois de executar a sua experiência (clique em **RUN** na parte inferior da tela de experiência), clique no botão **Configurar** o Serviço Web (selecione a opção **Predictive Web Service).** **O Serviço Web configurar** executa para si os três passos de conversão da sua experiência de treino para uma experiência preditiva:

1. Guarda o seu modelo treinado na secção **Modelos Treinados** da paleta de módulos (à esquerda da tela de experimentação). Em seguida, substitui o algoritmo de aprendizagem automática e os módulos [Train Model][train-model] pelo modelo treinado.
2. Ele analisa a sua experimentação e remove os módulos que claramente foram utilizados apenas para fins de formação e já não são necessários.
3. Insere os módulos de entrada e _saída_ do _serviço Web_ em locais predefinidos na sua experiência (estes módulos aceitam e devolvem os dados do utilizador).

Por exemplo, a experimentação seguinte prepara um modelo de árvore de decisões elevada de duas classes com dados de censo de exemplo:

![Experimentação de preparação](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

Os módulos Nesse experimento executam basicamente quatro funções diferentes:

![Funções de módulo](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Quando converter esta experimentação de preparação para uma experimentação preditiva, alguns destes módulos já não são necessários, ou que agora servem um objetivo diferente:

* **Dados** - Os dados deste conjunto de dados da amostra não são utilizados durante a pontuação - o utilizador do serviço web fornecerá os dados a serem pontuados. No entanto, os metadados deste conjunto de dados, tais como tipos de dados, é utilizado pelo modelo treinado. Por isso terá de manter o conjunto de dados na experimentação preditiva para que ele pode fornecer esses metadados.

* **Preparação** - Dependendo dos dados do utilizador que serão submetidos para pontuação, estes módulos podem ou não ser necessários para processar os dados que chegam. O botão **'Configurar** o Serviço Web' não toca neles - tem de decidir como pretende manuseá-los.
  
    Por exemplo, neste exemplo, o conjunto de dados da amostra pode ter valores em falta, pelo que foi incluído um módulo de [Dados Em Falta Limpos][clean-missing-data] para lidar com eles. Além disso, o conjunto de dados de exemplo inclui colunas que não são necessários para preparar o modelo. Assim, foi incluída uma [Select Columns no][select-columns] módulo Dataset para excluir essas colunas extra do fluxo de dados. Se souber que os dados que serão submetidos para pontuação através do serviço web não terão valores em falta, então pode remover o módulo De [dados em falta limpos.][clean-missing-data] No entanto, uma vez que as [Colunas Select no][select-columns] módulo Dataset ajudam a definir as colunas de dados que o modelo treinado espera, esse módulo precisa de permanecer.

* **Trem** - Estes módulos são usados para treinar o modelo. Quando clicar em Configurar o **Serviço Web,** estes módulos são substituídos por um único módulo que contém o modelo que treinou. Este novo módulo é guardado na secção **Modelos Treinados** da paleta de módulos.

* **Pontuação** - Neste exemplo, o módulo [Split Data][split] é utilizado para dividir o fluxo de dados em dados de teste e dados de treino. Na experiência preditiva, já não estamos a treinar, por isso [os Dados Divididos][split] podem ser removidos. Da mesma forma, o segundo módulo ['Modelo de Pontuação'][score-model] e o módulo ['Modelo de Avaliação'][evaluate-model] são utilizados para comparar os resultados dos dados do teste, pelo que estes módulos não são necessários na experiência preditiva. No entanto, o restante módulo [De Modelo de Pontuação][score-model] é necessário para devolver um resultado de pontuação através do serviço web.

Aqui está como o nosso exemplo fica depois de clicar em **Configurar O Serviço Web:**

![Converter a experimentação preditiva](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

O trabalho realizado pelo **set Up Web Service** pode ser suficiente para preparar a sua experiência para ser implantado como um serviço web. No entanto, pode querer fazer algum trabalho adicional específico à sua experimentação.

### <a name="adjust-input-and-output-modules"></a>Ajustar os módulos de entrada e saídos
Na sua experimentação de preparação, utilizou um conjunto de dados de treinamento e, em seguida, fizemos algum processamento para obter os dados de forma que o algoritmo de aprendizagem necessários. Se os dados que espera receber através do serviço web não precisarem deste processamento, pode contorná-lo: ligar a saída do módulo de entrada do **serviço Web** a um módulo diferente na sua experiência. Os dados do utilizador agora vão deparar-se no modelo nesta localização.

Por exemplo, por padrão **Configurar** o Web Service coloca o módulo de entrada do **serviço Web** na parte superior do fluxo de dados, como mostra a figura acima. Mas podemos posicionar manualmente a entrada do **serviço Web** para além dos módulos de processamento de dados:

![Mover a entrada do serviço web](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

Os dados de entrada fornecidos através do serviço web agora passará diretamente para o módulo de modelo de pontuação sem qualquer tipo de pré-processamento.

Da mesma forma, por padrão, o Sistema Web **De Configuração** coloca o módulo de saída do serviço Web na parte inferior do fluxo de dados. Neste exemplo, o serviço web devolverá ao utilizador a saída do módulo ['Modelo de Pontuação',][score-model] que inclui o vetor completo de dados de entrada mais os resultados de pontuação.
No entanto, se preferir devolver algo diferente, pode adicionar módulos adicionais antes do módulo de saída do **serviço Web.** 

Por exemplo, para devolver apenas os resultados de pontuação e não todo o vetor de dados de entrada, adicione uma Coluna Select no módulo [Dataset][select-columns] para excluir todas as colunas, exceto os resultados de pontuação. Em seguida, mova o módulo de saída do **serviço Web** para a saída das Colunas Select no módulo [Dataset.][select-columns] A experimentação tem esta aparência:

![Mover a saída do serviço web](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>Adicionar ou remover módulos de processamento de dados adicionais
Se existirem mais módulos na experimentação que sabe que não serão necessários durante a classificação, estes podem ser removidos. Por exemplo, como mudámos o módulo de entrada do **serviço Web** para um ponto após os módulos de processamento de dados, podemos remover o módulo de [Dados Desaparecidos Limpos][clean-missing-data] da experiência preditiva.

Nosso experimentação preditiva agora esta aparência:

![Remover o módulo adicional](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Adicionar parâmetros do serviço Web opcional
Em alguns casos, pode querer permitir que o usuário do seu serviço web alterar o comportamento de módulos, quando o serviço for acedido. *Os parâmetros* do Serviço Web permitem-lhe fazê-lo.

Um exemplo comum é a criação de um módulo [de Dados de Importação][import-data] para que o utilizador do serviço web implementado possa especificar uma fonte de dados diferente quando o serviço web é acedido. Ou configurar um módulo de [Dados de Exportação][export-data] para que um destino diferente possa ser especificado.

Pode definir parâmetros do serviço Web e associá-las com um ou mais parâmetros do módulo, e pode especificar se são necessárias ou opcionais. O utilizador do web service fornece valores para estes parâmetros quando o serviço é acedido e as ações de módulo são modificadas em conformidade.

Para obter mais informações sobre quais são os parâmetros do Serviço Web e como usá-los, consulte utilizar os parâmetros do serviço web de [aprendizagem automática Azure][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Implementar a experimentação preditiva como um serviço web
Agora que a experimentação preditiva foi suficientemente preparada, pode implementá-lo como um serviço web do Azure. Usando o web service, os utilizadores podem enviar dados para o seu modelo e o modelo retornará seu previsões.

Para obter mais informações sobre o processo completo de implementação, consulte [Implementar um serviço web Azure Machine Learning][deploy]

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
