---
title: Preparar modelo para implantação
titleSuffix: Azure Machine Learning Studio (classic)
description: Como preparar seu modelo treinado para implantação como um serviço Web convertendo seu teste de treinamento Machine Learning Studio (clássico) em um experimento de previsão.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 03/28/2017
ms.openlocfilehash: caaed83417ac1eaadc407fb12dc8bb360aae45ec
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493269"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio-classic"></a>Como preparar seu modelo para implantação no Azure Machine Learning Studio (clássico)

Azure Machine Learning Studio (clássico) fornece as ferramentas necessárias para desenvolver um modelo de análise preditiva e, em seguida, operacionalá-lo implantando-o como um serviço Web do Azure.

Para fazer isso, você usa a versão clássica do Studio para criar um experimento chamado teste de *treinamento* -onde você treina, classifica e edita seu modelo. Quando estiver satisfeito, você terá seu modelo pronto para implantar convertendo seu teste de treinamento em um *experimento de previsão* configurado para pontuar os dados do usuário.

Você pode ver um exemplo desse processo no [tutorial 1: prever o risco de crédito](tutorial-part1-credit-risk.md).

Este artigo aprofunda-se nos detalhes de como um teste de treinamento é convertido em um experimento de previsão e como esse experimento de previsão é implantado. Ao compreender esses detalhes, você pode aprender a configurar seu modelo implantado para torná-lo mais eficiente.



## <a name="overview"></a>Descrição geral 

O processo de converter um teste de treinamento em um experimento de previsão envolve três etapas:

1. Substitua os módulos de algoritmo de aprendizado de máquina pelo modelo treinado.
2. Corte o experimento para apenas os módulos que são necessários para pontuação. Um teste de treinamento inclui vários módulos que são necessários para treinamento, mas que não são necessários quando o modelo é treinado.
3. Defina como seu modelo aceitará dados do usuário do serviço Web e quais dados serão retornados.

> [!TIP]
> No teste de treinamento, você esteve preocupado com o treinamento e a pontuação de seu modelo usando seus próprios dados. Mas, uma vez implantado, os usuários enviarão novos dados para seu modelo e ele retornará resultados de previsão. Assim, à medida que você converte seu teste de treinamento em um experimento de previsão para prepará-lo para implantação, tenha em mente como o modelo será usado por outras pessoas.
> 
> 

## <a name="set-up-web-service-button"></a>Botão configurar serviço Web
Depois de executar o experimento (clique em **executar** na parte inferior da tela do experimento), clique no botão **configurar serviço Web** (selecione a opção **serviço Web de previsão** ). **Configurar o serviço Web** realiza as três etapas para converter seu teste de treinamento em um experimento de previsão:

1. Ele salva seu modelo treinado na seção **modelos treinados** da paleta de módulos (à esquerda da tela do experimento). Em seguida, ele substitui o algoritmo de aprendizado de máquina e treina os módulos de [modelo][train-model] com o modelo treinado salvo.
2. Ele analisa seu experimento e remove módulos que foram claramente usados apenas para treinamento e que não são mais necessários.
3. Ele insere módulos de entrada e _saída_ do _serviço Web_ em locais padrão em seu experimento (esses módulos aceitam e retornam dados do usuário).

Por exemplo, o experimento a seguir treina um modelo de árvore de decisão aumentada de duas classes usando dados de exemplo de censo:

![Teste de treinamento](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

Os módulos nesse experimento executam basicamente quatro funções diferentes:

![Funções de módulo](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Quando você converte esse teste de treinamento em um experimento de previsão, alguns desses módulos não são mais necessários ou agora têm uma finalidade diferente:

* **Dados** -os dados neste conjunto de dado de exemplo não são usados durante a pontuação-o usuário do serviço Web fornecerá os dados a serem pontuados. No entanto, os metadados desse conjunto de dados, como tipos de dado, são usados pelo modelo treinado. Portanto, você precisa manter o conjunto de um no teste de previsão para que ele possa fornecer esses metadados.

* **Prep** -dependendo dos dados do usuário que serão enviados para pontuação, esses módulos poderão ou não ser necessários para processar os dados de entrada. O botão **configurar serviço Web** não toca em: você precisa decidir como deseja tratá-los.
  
    Por exemplo, neste exemplo, o conjunto de dados de exemplo pode ter valores ausentes e, portanto, um módulo [limpeza ausente][clean-missing-data] foi incluído para lidar com eles. Além disso, o conjunto de exemplos inclui colunas que não são necessárias para treinar o modelo. Portanto, um módulo [selecionar colunas no conjunto][select-columns] de dados foi incluído para excluir as colunas extras do fluxo de dado. Se você souber que os dados que serão enviados para pontuação por meio do serviço Web não terão valores ausentes, você poderá remover o módulo [limpar dados ausentes][clean-missing-data] . No entanto, como o módulo [selecionar colunas no conjunto][select-columns] de dados ajuda a definir as colunas que o modelo treinado espera, esse módulo precisa permanecer.

* **Treinar** -esses módulos são usados para treinar o modelo. Quando você clica em **configurar serviço Web**, esses módulos são substituídos por um único módulo que contém o modelo treinado. Esse novo módulo é salvo na seção **modelos treinados** da paleta de módulos.

* **Pontuação** -neste exemplo, o módulo [dividir dados][split] é usado para dividir o fluxo de dados em dados de teste e dados de treinamento. No experimento de previsão, não estamos mais treinando, portanto, [os dados divididos][split] podem ser removidos. Da mesma forma, o segundo módulo [modelo de Pontuação][score-model] e o módulo [modelo de avaliação][evaluate-model] são usados para comparar os resultados dos dados de teste, de modo que esses módulos não são necessários no experimento de previsão. O módulo [modelo de Pontuação][score-model] restante, no entanto, é necessário para retornar um resultado de pontuação por meio do serviço Web.

Veja como o nosso exemplo aparece depois de clicar em **configurar serviço Web**:

![Teste de previsão convertido](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

O trabalho feito por **Configurar o serviço Web** pode ser suficiente para preparar seu experimento para ser implantado como um serviço Web. No entanto, talvez você queira fazer algum trabalho adicional específico para seu experimento.

### <a name="adjust-input-and-output-modules"></a>Ajustar módulos de entrada e saída
No teste de treinamento, você usou um conjunto de dados de treinamento e, em seguida, fez algum processamento para obter os dados em um formato necessário para o algoritmo de aprendizado de máquina. Se os dados que você espera receber por meio do serviço Web não precisarem desse processamento, você poderá ignorá-los: Conecte a saída do **módulo de entrada do serviço Web** a um módulo diferente em seu experimento. Agora, os dados do usuário chegarão ao modelo neste local.

Por exemplo, por padrão, **Configurar o serviço Web** coloca o módulo **entrada do serviço Web** na parte superior do fluxo de dados, conforme mostrado na figura acima. Porém, podemos posicionar manualmente a **entrada do serviço Web** após os módulos de processamento de dados:

![Movendo a entrada do serviço Web](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

Os dados de entrada fornecidos por meio do serviço Web agora passarão diretamente para o módulo modelo de Pontuação sem nenhum pré-processamento.

Da mesma forma, por padrão, a **configuração do serviço Web** coloca o módulo saída do serviço Web na parte inferior do fluxo de dados. Neste exemplo, o serviço Web retornará ao usuário a saída do módulo [modelo de Pontuação][score-model] , que inclui o vetor de dados de entrada completo, além dos resultados da pontuação.
No entanto, se você preferir retornar algo diferente, poderá adicionar mais módulos antes do módulo **saída do serviço Web** . 

Por exemplo, para retornar apenas os resultados da pontuação e não o vetor inteiro de dados de entrada, adicione um módulo [selecionar colunas no conjunto][select-columns] de dados para excluir todas as colunas, exceto os resultados da pontuação. Em seguida, mova o módulo **saída do serviço Web** para a saída do módulo [selecionar colunas no conjunto de DataSet][select-columns] . O experimento tem a seguinte aparência:

![Movendo a saída do serviço Web](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>Adicionar ou remover módulos de processamento de dados adicionais
Se houver mais módulos em seu experimento que você sabe que não serão necessários durante a pontuação, eles poderão ser removidos. Por exemplo, como mudamos o módulo **entrada do serviço Web** para um ponto após os módulos de processamento de dados, podemos remover o módulo [limpar dados ausentes][clean-missing-data] do teste de previsão.

Nosso experimento de previsão agora tem esta aparência:

![Removendo módulo adicional](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Adicionar parâmetros opcionais do serviço Web
Em alguns casos, talvez você queira permitir que o usuário do serviço Web altere o comportamento dos módulos quando o serviço for acessado. Os *parâmetros de serviço Web* permitem que você faça isso.

Um exemplo comum é a configuração de um módulo [importar dados][import-data] para que o usuário do serviço Web implantado possa especificar uma fonte de dados diferente quando o serviço Web for acessado. Ou configurar um módulo [exportar dados][export-data] para que um destino diferente possa ser especificado.

Você pode definir parâmetros de serviço Web e associá-los a um ou mais parâmetros de módulo, e pode especificar se eles são obrigatórios ou opcionais. O usuário do serviço Web fornece valores para esses parâmetros quando o serviço é acessado e as ações do módulo são modificadas de acordo.

Para obter mais informações sobre quais parâmetros de serviço Web são e como usá-los, consulte [usando Azure Machine Learning parâmetros de serviço Web][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Implantar o teste de previsão como um serviço Web
Agora que o experimento de previsão foi suficientemente preparado, você pode implantá-lo como um serviço Web do Azure. Usando o serviço Web, os usuários podem enviar dados para seu modelo e o modelo retornará suas previsões.

Para obter mais informações sobre o processo de implantação completo, consulte [implantar um serviço web Azure Machine Learning][deploy]

[deploy]: publish-a-machine-learning-web-service.md

<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
