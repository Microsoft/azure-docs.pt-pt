---
title: Como um modelo se torna um serviço Web
titleSuffix: Azure Machine Learning Studio (classic)
description: Uma visão geral da mecânica de como seu modelo de Azure Machine Learning Studio (clássico) progride de um experimento de desenvolvimento para um serviço Web.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: c82e636b06d33140ca6dae606d39855084aac04d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496837"
---
# <a name="how-a-machine-learning-studio-classic-model-progresses-from-an-experiment-to-a-web-service"></a>Como um modelo de Machine Learning Studio (clássico) progride de um experimento para um serviço Web
Azure Machine Learning Studio (clássico) fornece uma tela interativa que permite desenvolver, executar, testar e iterar um ***experimento*** que representa um modelo de análise preditiva. Há uma ampla variedade de módulos disponíveis que podem:

* Dados de entrada em seu experimento
* Manipular os dados
* Treinar um modelo usando algoritmos de aprendizado de máquina
* Pontuar o modelo
* Avaliar os resultados
* Valores finais de saída

Quando estiver satisfeito com seu experimento, você poderá implantá-lo como um ***serviço Web clássico Azure Machine Learning*** ou um ***novo Azure Machine Learning Web Service*** para que os usuários possam enviar novos dados e receber resultados.

Neste artigo, fornecemos uma visão geral da mecânica de como seu modelo de Machine Learning progride de um experimento de desenvolvimento para um serviço Web operacional.

> [!NOTE]
> Há outras maneiras de desenvolver e implantar modelos de aprendizado de máquina, mas este artigo se concentra em como você usa Machine Learning Studio (clássico). Por exemplo, para ler uma descrição de como criar um serviço Web de previsão clássico com R, consulte a postagem de blog [Build & implantar aplicativos Web de previsão usando o RStudio e o Azure Machine Learning Studio](https://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).
>
>

Embora Azure Machine Learning Studio (clássico) seja projetado para ajudá-lo a desenvolver e implantar um *modelo de análise preditiva*, é possível usar o estúdio (clássico) para desenvolver um experimento que não inclua um modelo de análise preditiva. Por exemplo, um experimento pode apenas inserir dados, manipulá-los e, em seguida, gerar os resultados. Assim como um experimento de análise preditiva, você pode implantar esse experimento não preditiva como um serviço Web, mas é um processo mais simples porque o experimento não está treinando ou pontuando um modelo de aprendizado de máquina. Embora não seja o típico de usar o Studio (clássico) dessa forma, vamos incluí-lo na discussão para que possamos dar uma explicação completa de como o Studio (clássico) funciona.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Desenvolvendo e implantando um serviço Web de previsão
Aqui estão os estágios que uma solução típica segue à medida que você desenvolve e implanta usando Machine Learning Studio (clássico):

![Fluxo de implantação](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Figura 1-estágios de um modelo típico de análise preditiva*

### <a name="the-training-experiment"></a>O experimento de treinamento
O ***teste de treinamento*** é a fase inicial de desenvolvimento de seu serviço Web no Machine Learning Studio (clássico). A finalidade do teste de treinamento é oferecer a você um local para desenvolver, testar, iterar e eventualmente treinar um modelo de aprendizado de máquina. Você pode até mesmo treinar vários modelos simultaneamente à medida que procura a melhor solução, mas quando terminar de experimentar, você selecionará um único modelo treinado e eliminará o restante do experimento. Para obter um exemplo de desenvolvimento de um experimento de análise preditiva, consulte [desenvolver uma solução de análise preditiva para avaliação de risco de crédito em Azure Machine Learning Studio (clássico)](tutorial-part1-credit-risk.md).

### <a name="the-predictive-experiment"></a>O experimento de previsão
Depois de ter um modelo treinado em seu experimento de treinamento, clique em **configurar serviço Web** e selecione **serviço Web de previsão** no Machine Learning Studio (clássico) para iniciar o processo de conversão do teste de treinamento em uma ***previsão Experimente***. A finalidade do experimento de previsão é usar seu modelo treinado para pontuar novos dados, com o objetivo de acabar ficando operacionalmente como um serviço Web do Azure.

Essa conversão é feita para você por meio das seguintes etapas:

* Converter o conjunto de módulos usados para treinamento em um único módulo e salvá-lo como um modelo treinado
* Eliminar quaisquer módulos estranhos não relacionados à Pontuação
* Adicionar portas de entrada e saída que o serviço Web eventual usará

Pode haver mais alterações que você deseja fazer para obter seu experimento de previsão pronto para implantar como um serviço Web. Por exemplo, se você quiser que o serviço Web gere apenas um subconjunto de resultados, poderá adicionar um módulo de filtragem antes da porta de saída.

Nesse processo de conversão, o teste de treinamento não é Descartado. Quando o processo for concluído, você terá duas guias no Studio (clássico): uma para o teste de treinamento e outra para o experimento de previsão. Dessa forma, você pode fazer alterações no teste de treinamento antes de implantar seu serviço Web e recriar o experimento de previsão. Ou você pode salvar uma cópia do teste de treinamento para iniciar outra linha de experimentação.

> [!NOTE]
> Ao clicar em **serviço Web de previsão** , você inicia um processo automático para converter seu teste de treinamento em um experimento de previsão e isso funciona bem na maioria dos casos. Se o seu experimento de treinamento for complexo (por exemplo, se você tiver vários caminhos para treinamento que ingressar juntos), talvez prefira fazer essa conversão manualmente. Para obter mais informações, consulte [como preparar seu modelo para implantação no Azure Machine Learning Studio (clássico)](convert-training-experiment-to-scoring-experiment.md).
>
>

### <a name="the-web-service"></a>O serviço Web
Quando estiver satisfeito com o experimento de previsão estar pronto, você poderá implantar seu serviço como um serviço Web clássico ou um novo serviço Web baseado em Azure Resource Manager. Para colocar em operação seu modelo implantando-o como um *serviço web Machine Learning clássico*, clique em **implantar serviço** Web e selecione **implantar serviço Web [clássico]** . Para implantar como *novo serviço web Machine Learning*, clique em **implantar serviço Web** e selecione **implantar serviço Web [novo]** . Agora, os usuários podem enviar dados para seu modelo usando a API REST do serviço Web e receber os resultados novamente. Para obter mais informações, veja [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Como consumir um serviço Web do Azure Machine Learning).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>O caso não típico: Criando um serviço Web não preditiva
Se seu experimento não treinar um modelo de análise preditiva, você não precisará criar um teste de treinamento e um experimento de Pontuação – há apenas um experimento e você pode implantá-lo como um serviço Web. Machine Learning Studio (clássico) detecta se o experimento contém um modelo de previsão analisando os módulos que você usou.

Depois de iterar em seu experimento e estar satisfeito com ele:

1. Clique em **configurar serviço Web** e selecione **retreinando serviço Web** -nós de entrada e saída são adicionados automaticamente
2. Clique em **executar**
3. Clique em **implantar serviço Web** e selecione **implantar serviço Web [clássico]** ou **implantar serviço Web [novo]** dependendo do ambiente no qual você deseja implantar.

Seu serviço Web agora está implantado e você pode acessá-lo e gerenciá-lo assim como um serviço Web de previsão.

## <a name="updating-your-web-service"></a>Atualizando seu serviço Web
Agora que você implantou seu experimento como um serviço Web, e se precisar atualizá-lo?

Isso depende do que você precisa para atualizar:

**Você deseja alterar a entrada ou saída ou deseja modificar como o serviço Web manipula os dados**

Se você não estiver alterando o modelo, mas estiver apenas alterando como o serviço Web lida com os dados, poderá editar o teste de previsão e clicar em **implantar serviço** Web e selecionar **implantar serviço Web [clássico]** ou **implantar serviço Web [novo]** novamente. O serviço Web é interrompido, o experimento de previsão atualizado é implantado e o serviço Web é reiniciado.

Veja um exemplo: Suponha que seu experimento de previsão retorne toda a linha de dados de entrada com o resultado previsto. Você pode decidir que deseja que o serviço Web retorne apenas o resultado. Portanto, você pode adicionar um módulo **colunas de projeto** no experimento de previsão, logo antes da porta de saída, para excluir colunas que não sejam o resultado. Quando você clica em **implantar serviço Web** e seleciona **implantar serviço Web [clássico]** ou **implantar serviço Web [novo]** novamente, o serviço Web é atualizado.

**Você deseja treinar novamente o modelo com novos dados**

Se você quiser manter seu modelo de aprendizado de máquina, mas quiser retreiná-lo com novos dados, terá duas opções:

1. **Treinar novamente o modelo enquanto o serviço Web estiver em execução** -se você quiser treinar novamente seu modelo enquanto o serviço Web de previsão estiver em execução, faça isso fazendo algumas modificações no teste de treinamento para torná-lo um experimento de novo ***treinamento***e, em seguida, Você pode implantá-lo como um **serviço *Web*** de novo treinamento. Para obter instruções sobre como fazer isso, consulte [readaptação de modelos de Machine Learning programaticamente](/azure/machine-learning/studio/retrain-machine-learning-model).
2. Volte **para o experimento de treinamento original e use dados de treinamento diferentes para desenvolver seu modelo** -seu experimento de previsão está vinculado ao serviço Web, mas o teste de treinamento não está diretamente vinculado dessa maneira. Se você modificar o experimento de treinamento original e clicar em **configurar serviço Web**, ele criará um *novo* teste de previsão que, quando implantado, criará um *novo* serviço Web. Ele não atualiza apenas o serviço Web original.

   Se você precisar modificar o teste de treinamento, abra-o e clique em **salvar como** para fazer uma cópia. Isso deixará intacto o experimento de treinamento original, o experimento de previsão e o serviço Web. Agora você pode criar um novo serviço Web com suas alterações. Depois de implantar o novo serviço Web, você pode decidir se deseja parar o serviço Web anterior ou mantê-lo em execução junto com o novo.

**Você deseja treinar um modelo diferente**

Se você quiser fazer alterações em seu experimento de previsão original, como selecionar um algoritmo de aprendizado de máquina diferente, tentar um método de treinamento diferente, etc., você precisa seguir o segundo procedimento descrito acima para treinar novamente seu modelo: abrir o teste de treinamento, clique em **salvar como** para fazer uma cópia e, em seguida, inicie o novo caminho para desenvolver seu modelo, criando o teste de previsão e implantando o serviço Web. Isso criará um novo serviço Web não relacionado ao original-você pode decidir qual deles, ou ambos, será mantido em execução.

## <a name="next-steps"></a>Passos seguintes
Para obter mais detalhes sobre o processo de desenvolvimento e teste, consulte os seguintes artigos:

* convertendo o experimento- [como preparar seu modelo para implantação no Azure Machine Learning Studio (clássico)](convert-training-experiment-to-scoring-experiment.md)
* Implantando o serviço Web- [implantar um serviço web Azure Machine Learning](publish-a-machine-learning-web-service.md)
* retreinando o modelo- [readaptação de modelos de Machine Learning de forma programática](/azure/machine-learning/studio/retrain-machine-learning-model)

Para obter exemplos de todo o processo, consulte:

* [Tutorial de aprendizado de máquina: criar seu primeiro experimento no Azure Machine Learning Studio (clássico)](create-experiment.md)
* [Walkthrough: desenvolver uma solução de análise preditiva para avaliação de risco de crédito no Azure Machine Learning](tutorial-part1-credit-risk.md)

