---
title: Como um modelo de machine learning torna-se um serviço web
titleSuffix: Azure Machine Learning Studio
description: Uma visão geral sobre a mecânica como sua progride de modelo do Azure Machine Learning Studio de desenvolvimento de um experimentação para um serviço Web operacionalizado.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: 808883fc96369538a26a06a7dadb5f0ecd6628d5
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56823404"
---
# <a name="how-a-machine-learning-studio-model-progresses-from-an-experiment-to-an-operationalized-web-service"></a>Como um modelo de Machine Learning Studio evolui de uma experimentação para um serviço Web operacionalizado
O Azure Machine Learning Studio dispõe numa tela interativa que permite que desenvolver, executar, testar e iterar um ***experimentar*** que representa um modelo de Análise Preditiva. Há uma grande variedade de módulos disponíveis que pode:

* Dados de entrada na sua experimentação
* Manipular os dados
* Preparar um modelo com algoritmos de machine learning
* Pontuar o modelo
* Avaliar os resultados
* Valores de finais de saída

Quando estiver satisfeito com a sua experimentação, pode implementá-la como uma ***serviço Web de Aprendizado de máquina do clássico do Azure*** ou uma ***serviço novo do Azure Machine Learning Web*** para que os utilizadores possam enviar dados novos e voltar a receber resultados.

Neste artigo, vamos dar uma visão geral sobre a mecânica como sua progride de modelo de Machine Learning de desenvolvimento de um experimentação para um serviço Web operacionalizado.

> [!NOTE]
> Existem outras formas de desenvolver e implementar modelos de machine learning, mas este artigo se concentra em como utilizar o Machine Learning Studio. Por exemplo, para ler uma descrição de como criar um serviço Web preditivo clássico com o R, veja a mensagem de blogue [compilação & implementar preditiva Web aplicações usando o r Studio e do Azure Machine Learning studio](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).
>
>

Enquanto o Azure Machine Learning Studio foi projetado para ajudar a desenvolver e implementar um *modelo de Análise Preditiva*, é possível utilizar o Studio para desenvolver uma experimentação que não inclui um modelo de Análise Preditiva. Por exemplo, uma experimentação pode apenas dados de entrada, manipulá-lo e, em seguida, enviar os resultados. Assim como uma experimentação de Análise Preditiva, pode implementar esta experiência não preditiva como um serviço Web, mas é um processo mais simples porque a experimentação não é de treinamento ou um modelo de machine learning de classificação. Embora não seja o típicas para utilizar o Studio desta forma, irá incluímos-lo na discussão, de modo que podemos dar uma explicação completa do funcionamento do Studio.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Desenvolvendo e implantando um serviço Web preditivo
Aqui estão as fases que se segue uma solução típica à medida que desenvolve e implementá-la com o Machine Learning Studio:

![Fluxo de implementação](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Figura 1 – fases de um modelo de Análise Preditiva típico*

### <a name="the-training-experiment"></a>A experimentação de preparação
O ***experimentação de preparação*** é a fase inicial de desenvolvimento de seu serviço Web no Machine Learning Studio. O objetivo a experimentação de preparação é fornecer a um local para desenvolver, testar, iterar e, eventualmente, preparar um modelo de machine learning. Pode até mesmo preparar os vários modelos de simultaneamente como procurar a melhor solução, mas quando tiver terminado a experimentar selecionará um único preparado modelar e eliminar o resto da experimentação. Para obter um exemplo de desenvolvimento de uma experimentação de Análise Preditiva, veja [desenvolver uma solução de Análise Preditiva para a avaliação de risco de crédito no Azure Machine Learning Studio](tutorial-part1-credit-risk.md).

### <a name="the-predictive-experiment"></a>A experimentação preditiva
Depois de ter um modelo preparado na experimentação de preparação, clique em **no serviço Web** e selecione **serviço de Web preditiva** no Machine Learning Studio para iniciar o processo de conversão de seu treinamento fazer uma experiência para uma ***experimentação preditiva***. O objetivo da experimentação preditiva é usar o modelo preparado para classificar dados novos, com o objetivo de tornar-se, eventualmente, operacionalizados como um serviço Web do Azure.

Esta conversão é feita para os seguintes passos:

* Converter o conjunto de módulos usada para treinamento num único módulo e salvá-la como um modelo preparado
* Eliminar quaisquer módulos estranhos não relacionados à classificação
* Adicionar as portas de entrada e saídas que irá utilizar o serviço Web eventual

Pode haver mais alterações, que deve certificar-se obter a sua experimentação preditiva pronto para implementar como um serviço Web. Por exemplo, se pretender que o serviço Web para apenas um subconjunto de resultados de saída, pode adicionar um módulo de filtragem antes da porta de saída.

Nesse processo de conversão, a experimentação de preparação não é descartada. Quando o processo estiver concluído, tem dois separadores no Studio: um para a experimentação de preparação e outro para a experimentação preditiva. Desta forma, que pode efetuar alterações para a experimentação de preparação antes de implementar o serviço da Web e reconstruir a experimentação preditiva. Ou pode salvar uma cópia da experimentação de preparação para iniciar outra linha de experimentação.

> [!NOTE]
> Quando clica em **preditiva Web Service** iniciar um processo automático para converter a sua experimentação de preparação para uma experimentação preditiva, e isso funciona bem na maioria dos casos. Se a sua experimentação de preparação é complexa (por exemplo, tem vários caminhos para treinamento que participe em conjunto), poderá preferir fazer essa conversão manualmente. Para obter mais informações, consulte [como preparar o seu modelo para a implementação no Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).
>
>

### <a name="the-web-service"></a>O serviço Web
Quando estiver satisfeito que sua experimentação preditiva estiver pronta, pode implementar o serviço como um serviço de Web clássico ou um serviço Web novo com base no Azure Resource Manager. Para operacionalizar o seu modelo ao implementar-o como um *serviço Web clássico de Aprendizado de máquina*, clique em **implementar serviço Web** e selecione **implementar o serviço Web [clássica]**. Para implementar como *serviço nova Web do Machine Learning*, clique em **implementar serviço Web** e selecione **implementar o Web Service [novo]**. Os utilizadores agora podem enviar dados para o seu modelo usando o REST API do serviço Web e receber os resultados de volta. Para obter mais informações, veja [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Como consumir um serviço Web do Azure Machine Learning).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>O cenário não típico: criar um serviço Web não preditiva
Se a sua experimentação não preparar um modelo de Análise Preditiva, em seguida, não precisa de criar uma experimentação de preparação e de uma experimentação de classificação – há apenas uma experimentação e pode implementá-la como um serviço Web. O Machine Learning Studio Deteta se a sua experimentação contém um modelo preditivo analisando os módulos que utilizou.

Depois de já iterados na sua experimentação e estiver satisfeito com o mesmo:

1. Clique em **segurança de serviço Web** e selecione **reparametrização do Web Service** - entrada e saída nós são adicionados automaticamente
2. Clique em **executar**
3. Clique em **implementar serviço Web** e selecione **implementar o serviço Web [clássica]** ou **implementar o Web Service [novo]** dependendo do ambiente para o qual pretende implementar.

O serviço da Web agora é implementado e pode aceder e geri-la tal como um serviço Web preditivo.

## <a name="updating-your-web-service"></a>A atualizar o seu serviço Web
Agora que implementou a sua experimentação como um serviço Web, e se precisar atualizá-la?

Que depende o que precisa para atualizar:

**Para alterar a entrada ou saída, ou se pretender modificar a forma como o serviço Web manipula dados**

Se não estiver a alterar o modelo, mas são apenas alterar a forma como o serviço Web processa os dados, pode editar a experimentação preditiva e, em seguida, clique em **implementar serviço Web** e selecione **implementar o serviço Web [clássica]** ou **implementar o Web Service [novo]** novamente. O serviço Web está parado, a experimentação preditiva atualizada é implementada e o serviço Web é reiniciado.

Segue-se um exemplo: Suponha que sua experimentação preditiva devolve a linha inteira de dados de entrada com o resultado previsto. Pode decidir que pretende que o serviço Web para simplesmente retornar o resultado. Para que possa acrescentar uma **colunas do projeto** módulo na experimentação preditiva, logo antes da porta de saída, para excluir colunas que não seja o resultado. Quando clica em **implementar serviço Web** e selecione **implementar o serviço Web [clássica]** ou **implementar o Web Service [novo]** novamente, o serviço Web é atualizado.

**Pretende voltar a preparar o modelo com dados novos**

Se pretender manter o seu modelo de machine learning, mas gostaria de voltar a preparar com novos dados, tem duas opções:

1. **Voltar a preparar o modelo, enquanto o serviço Web está em execução** -se de que pretende voltar a preparar seu modelo, enquanto o serviço Web preditivo está em execução, pode fazê-lo fazendo algumas modificações para a experimentação de preparação para o tornar um ***reparametrização Experimente***, em seguida, pode implementá-la como uma  ***web reparametrização* serviço**. Para obter instruções sobre como fazer isso, consulte [Retrain Machine Learning dos modelos](/azure/machine-learning/studio/retrain-machine-learning-model).
2. **Voltar para a experimentação de preparação original e utilizar dados de treinamento diferentes para desenvolver o seu modelo** – sua experimentação preditiva está ligada ao serviço da Web, mas a experimentação de preparação não está diretamente ligada dessa forma. Se modificar a experimentação de preparação original e clique em **no serviço Web**, irá criar um *novo* preditiva de experimentação que, quando implementado, irá criar um *novo* Web serviço. Apenas não atualiza o Web service original.

   Se precisar de modificar a experimentação de preparação, abra-o e clique em **guardar como** para fazer uma cópia. Isso deixar intacto a experimentação de preparação original, a experimentação preditiva e serviço Web. Agora, pode criar um novo serviço Web com as suas alterações. Depois de implementar o novo serviço Web, em seguida, pode decidir se pretende parar o serviço Web anterior ou manter a funcionar em conjunto com o novo.

**Pretende preparar um modelo diferente**

Se pretender efetuar alterações à sua experimentação preditiva original, como a seleção de um outro algoritmo de machine learning, um método diferente de treinamento, etc. a tentar, então tem de seguir o segundo procedimento descrito acima para a reparametrização do modelo: Abra o treinamento de experimentação, clique em **guardar como** para fazer uma cópia e, em seguida, inicie o novo caminho de desenvolver o seu modelo, a experimentação preditiva a criar e implementar o serviço web. Esta ação irá criar um novo Web service não relacionados das original - pode decidir qual deles, ou ambos, continue a ser executada.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais detalhes sobre o processo de desenvolvimento e experimentação, consulte os artigos seguintes:

* converter a experimentação - [como preparar o seu modelo para a implementação no Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md)
* implementar o serviço Web - [implementar um serviço web do Azure Machine Learning](publish-a-machine-learning-web-service.md)
* reparametrização do modelo - [Retrain Machine Learning dos modelos](/azure/machine-learning/studio/retrain-machine-learning-model)

Para obter exemplos de todo o processo, consulte:

* [Tutorial do Machine learning: Criar a sua primeira experiência no Azure Machine Learning Studio](create-experiment.md)
* [Passo a passo: Desenvolver uma solução de Análise Preditiva para a avaliação de risco de crédito no Azure Machine Learning](tutorial-part1-credit-risk.md)

