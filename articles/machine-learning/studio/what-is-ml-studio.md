---
title: O que é
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio é uma ferramenta de arrastar e soltar para a criação rápida de modelos de uma biblioteca pronta para uso de algoritmos e módulos.
services: machine-learning
documentationcenter: ''
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/20/2019
ms.openlocfilehash: 4ec9cff652bf1badf526d490547ad78de31ac5da
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678000"
---
# <a name="what-is-azure-machine-learning-studio"></a>O que é o Azure Machine Learning Studio?
O Microsoft Azure Machine Learning Studio é uma ferramenta de colaboração, de arrastar e largar que pode utilizar para criar, testar e implementar soluções de análise preditiva nos seus dados. Machine Learning Studio publica modelos como serviços web que podem facilmente ser consumidos por aplicações personalizadas ou ferramentas de BI como o Excel.

Machine Learning Studio é onde se reúnem a ciência de dados, a análise preditiva, os recursos de nuvem e os dados.


## <a name="the-machine-learning-studio-interactive-workspace"></a>A área de trabalho interativa do Machine Learning Studio
Para desenvolver um modelo de análise preditiva, você normalmente usa dados de uma ou mais fontes, transforma-os e analisa esses dados por meio de várias funções de manipulação de dados e estatísticas e gera um conjunto de resultados. Desenvolver um modelo como este é um processo iterativo. À medida que modifica nas várias funções e os respetivos parâmetros, os seus resultados convergem até achar que tem um modelo preparado e eficaz.

O **Azure Machine Learning Studio** fornece uma área de trabalho interativa e visual para facilmente criar, testar e iterar um modelo de análise preditiva. Pode arrastar e largar ***conjuntos de dados*** e ***módulos*** de análise em telas interativas, ligá-las entre si para formar uma ***experimentação***, que executa no Machine Learning Studio. Para iterar o design do modelo, edite a experimentação, guarde uma cópia se assim pretender e execute-a novamente. Quando estiver pronto, pode converter a ***experimentação de preparação*** numa ***experimentação preditiva*** e, em seguida, publicá-la como um ***serviço Web*** para que o modelo possa ser acedido por outras pessoas.

Não existe nenhuma programação necessária, basta ligar visualmente os conjuntos de dados e módulos para construir o seu modelo de análise preditiva.

![Diagrama do Azure Machine Learning Studio: Crie experimentos, leia dados para várias fontes, escreva dados pontuados, escreva modelos.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-machine-learning-studio-overview-diagram"></a>Transferir o diagrama de descrição geral do Machine Learning Studio
Transfira o diagrama da **Descrição geral das funcionalidades do Microsoft Azure Machine Learning Studio** e obtenha uma vista de alto nível das funcionalidades do Machine Learning Studio. Para o ter à mão, pode imprimir o diagrama no tamanho de tabloide (11 x 17 pol.).

**Baixe o diagrama aqui: [](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)** Visão geral das funcionalidades
deMicrosoftAzureMachineLearningStudioMicrosoftAzureMachineLearningStudiovisãogeral dos recursos![](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)

## <a name="get-started-with-machine-learning-studio"></a>Introdução ao Machine Learning Studio
Ao digitar pela primeira vez [Machine Learning Studio](https://studio.azureml.net), você verá a **Home** Page. Aqui, você pode exibir documentação, vídeos e webinars e encontrar outros recursos valiosos.

Clique no menu do canto superior esquerdo ![Menu](./media/what-is-ml-studio/menu.png) e verá várias opções.
### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
Existem duas opções aqui, **Página Principal**, a página de início, e **Studio**.

Clique em **Studio** e será direcionado para o **Azure Machine Learning Studio**. Em primeiro lugar, ser-lhe-á pedido para iniciar sessão com a sua conta Microsoft ou a sua conta escolar ou profissional. Depois de iniciar sessão, irá ver os seguintes separadores no lado esquerdo:

* **PROJETOS** - Coleções de experimentações, conjuntos de dados, blocos de notas e outros recursos que representam um único projeto
* **EXPERIMENTAÇÕES** - As experimentações que foram criadas ou guardadas como rascunhos
* **SERVIÇOS WEB** - Os serviços web que foram implementados a partir das suas experimentações
* **BLOCOS DE NOTAS** - Blocos de notas Jupyter que criou
* **CONJUNTOS DE DADOS** - Conjuntos de dados que carregou para o Studio
* **MODELOS DE FORMAÇÃO** - Modelos que preparado nas experimentações e guardou no Studio
* **DEFINIÇÕES** - Uma coleção de definições que pode utilizar para configurar a sua conta e recursos.

### <a name="gallery"></a>Galeria
Clique em **Galeria** e será direcionado para a **[Galeria de IA do Azure](https://gallery.azure.ai/)** . A Galeria é um local onde uma comunidade de cientistas de dados e programadores partilham as soluções criadas com componentes do Cortana Intelligence Suite.

Para mais informações sobre a Galeria, veja [Partilhar e detetar soluções na galeria de IA do Azure](gallery-how-to-use-contribute-publish.md).

## <a name="components-of-an-experiment"></a>Componentes de uma experimentação
Uma experimentação é constituída por conjuntos de dados que fornecem dados aos módulos analíticos, que ligar em conjunto para construir um modelo de análise preditiva. Especificamente, uma experimentação válida tem estas características:

* A experimentação tem, pelo menos, um conjunto de dados e um módulo
* Os conjuntos de dados só podem ser ligados aos módulos
* Os módulos poderão estar ligados a conjuntos de dados ou a outros módulos
* Todas as portas de entrada de módulos têm de ter alguma ligação ao fluxo de dados
* Devem ser definidos todos os parâmetros necessários para cada módulo

Pode criar uma experimentação de raiz, ou pode utilizar uma experimentação de exemplo existente como um modelo. Para obter mais informações, veja [Copy example experiments to create new machine learning experiments](sample-experiments.md) (Copiar experimentações de exemplo para criar novas experimentações de aprendizagem automática).

Para obter um exemplo de criação de uma experimentação simples, consulte o artigo [Criar uma experimentação simples no Azure Machine Learning Studio](create-experiment.md).

Para obter uma explicação mais completa da criação de uma solução de análise preditiva, consulte [desenvolver uma solução preditiva com Azure Machine Learning Studio](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Conjuntos de dados
Um conjunto de dados que foi carregado no Machine Learning Studio para que possa ser utilizado no processo de modelação. Um número de conjuntos de dados de exemplo está incluído no Machine Learning Studio para experimentação e pode carregar mais conjuntos de dados conforme seja necessário. Seguem-se alguns exemplos de conjuntos de dados incluídos:

* **Dados MPG para vários automóveis** - quilómetros por litro (MPG) valores para automóveis identificados pelo número de cilindros, potência de cavalos, etc.
* **Dados sobre cancro da mama** - Dados de diagnóstico sobre cancro da mama.
* **Dados sobre incêndios florestais** - tamanhos dos incêndios florestais no nordeste de Portugal.

À medida que cria um experimento, você pode escolher na lista de conjuntos de valores disponíveis à esquerda da tela.

Para obter uma lista de conjuntos de dados de exemplo incluídos no Machine Learning Srudio, consulte o artigo [Utilizar os conjuntos de dados de exemplo no Azure Machine Learning Studio](use-sample-datasets.md).

### <a name="modules"></a>Módulos
Um módulo é um algoritmo que pode utilizar nos seus dados. O Machine Learning Studio tem um número de módulos que se situa entre funções de entrada de dados para formação, classificação e processos de validação. Seguem-se alguns exemplos de módulos incluídos:

* [Converter em ARFF][convert-to-arff] – converte um conjunto de um DataSet serializado do .net para o formato de arquivo de relação de atributo (ARFF).
* [Calcular estatísticas elementares][elementary-statistics] -calcula as estatísticas elementares, como média, desvio padrão, etc.
* [Regressão linear][linear-regression] -cria um modelo de regressão linear baseado em descendente de gradiente online.
* [Modelo de Pontuação][score-model] – pontua um modelo de classificação ou regressão treinado.

À medida que cria uma experimentação, pode escolher a partir de uma lista de módulos disponíveis à esquerda da tela.

Um módulo pode ter um conjunto de parâmetros que pode utilizar para configurar algoritmos internos do módulo. Quando seleciona um módulo na tela, os parâmetros do módulo são apresentados no painel **Propriedades** à direita da tela. Pode modificar os parâmetros nesse painel para otimizar o seu modelo.

Para obter ajuda para navegar pela biblioteca grande de algoritmos de aprendizado de máquina disponíveis, consulte [como escolher algoritmos para Microsoft Azure Machine Learning Studio](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Implementar um serviço web de análise preditiva
Assim que o modelo de análise preditiva estiver pronto, pode implementá-lo como um serviço web a partir do Machine Learning Studio. Consulte o artigo [Implementar um serviço web do Azure Machine Learning](publish-a-machine-learning-web-service.md) para obter mais detalhes.

<a name="compare"></a>
## <a name="how-is-machine-learning-studio-different-from-azure-machine-learning-service"></a>Como Machine Learning Studio é diferente do serviço Azure Machine Learning?

[Azure Machine Learning serviço](../service/overview-what-is-azure-ml.md) fornece SDKs **-e-** uma interface visual (versão prévia), para preparar dados rapidamente, treinar e implantar modelos de aprendizado de máquina. Essa interface visual (visualização) fornece uma experiência de arrastar e soltar semelhante ao estúdio. No entanto, diferentemente da plataforma de computação proprietária do estúdio, a interface visual usa seus próprios recursos de computação e é totalmente integrada ao serviço de Azure Machine Learning.

Aqui está uma comparação rápida.

|| Machine Learning Studio | Serviço de Azure Machine Learning:<br/>Interface visual|
|---| --- | --- |
|| Disponibilidade geral (GA) | Em pré-visualização|
|Módulos para interface| Muitos | Conjunto inicial de módulos populares|
|Treinamento de destinos de computação| Destino de computação proprietário, somente suporte de CPU| Dá suporte a Azure Machine Learning computação, GPU ou CPU.<br/>(Outros cálculos com suporte no SDK)|
|Destinos de computação de implantação| Formato de serviço da Web proprietário, não personalizável | Opções de segurança corporativa & serviço kubernetes do Azure. <br/>([Outros cálculos](../service/how-to-deploy-and-where.md) com suporte no SDK) |
|Treinamento de modelo automatizado e ajuste de hiperparâmetro | Não | Ainda não está na interface visual. <br/> (Com suporte no SDK e portal do Azure.) | 

Experimente a interface visual (versão prévia) com [o tutorial: Prever o preço do automóvel com a interface visual](../service/ui-tutorial-automobile-price-train-score.md)

> [!NOTE]
> Os modelos criados no estúdio não podem ser implantados nem gerenciados pelo serviço Azure Machine Learning. No entanto, os modelos criados e implantados na interface visual do serviço podem ser gerenciados por meio do espaço de trabalho do serviço Azure Machine Learning.

## <a name="free-trial"></a>Avaliação gratuita

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="next-steps"></a>Passos seguintes
Você pode aprender as noções básicas de análise preditiva e aprendizado de máquina usando um guia de [início rápido](create-experiment.md) passo a passo e [criando exemplos](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
