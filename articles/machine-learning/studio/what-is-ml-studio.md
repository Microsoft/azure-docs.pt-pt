---
title: O que é ML Studio (clássico)
titleSuffix: Azure
description: Azure Machine Learning Studio (clássico) é uma ferramenta de arrastar e soltar para a criação rápida de modelos de uma biblioteca de algoritmos e módulos pronta para uso.
services: machine-learning
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.topic: overview
ms.date: 10/17/2019
ms.openlocfilehash: 327bc40e5989cc3bca148f415113de32b19a7022
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500120"
---
# <a name="what-is-machine-learning-studio-classic"></a>O que é Machine Learning Studio (clássico)?

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Microsoft Azure Machine Learning Studio (clássico) é uma ferramenta colaborativa, de arrastar e soltar, que você pode usar para criar, testar e implantar soluções de análise preditiva em seus dados.  A versão clássica do Machine Learning Studio publica modelos como serviços Web que podem ser facilmente consumidos por aplicativos personalizados ou ferramentas de BI, como o Excel.

Machine Learning Studio (clássico) é onde a ciência de dados, a análise preditiva, os recursos de nuvem e os dados se encontram.

## <a name="the-machine-learning-studio-classic--interactive-workspace"></a>O espaço de trabalho interativo Machine Learning Studio (clássico)
Para desenvolver um modelo de análise preditiva, você normalmente usa dados de uma ou mais fontes, transforma-os e analisa esses dados por meio de várias funções de manipulação de dados e estatísticas e gera um conjunto de resultados. Desenvolver um modelo como este é um processo iterativo. À medida que modifica nas várias funções e os respetivos parâmetros, os seus resultados convergem até achar que tem um modelo preparado e eficaz.

A versão clássica do Azure Machine Learning Studio fornece um espaço de trabalho visual interativo para criar, testar e iterar facilmente em um modelo de análise preditiva. Você arrasta e solta ***conjuntos*** de testes e ***módulos*** de análise em uma tela interativa, conectando-os para formar um ***experimento***, que é executado no Machine Learning Studio (clássico). Para iterar o design do modelo, edite a experimentação, guarde uma cópia se assim pretender e execute-a novamente. Quando estiver pronto, pode converter a ***experimentação de preparação*** numa ***experimentação preditiva*** e, em seguida, publicá-la como um ***serviço Web*** para que o modelo possa ser acedido por outras pessoas.

Não há nenhuma programação necessária, conecte visualmente conjuntos de valores e módulos para construir seu modelo de análise preditiva.

![Diagrama de Azure Machine Learning Studio: Crie experimentos, leia dados de várias fontes, escreva dados pontuados, escreva modelos.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

<a name="compare"></a>
## <a name="how-does-machine-learning-studio-classic-differ-from-azure-machine-learning"></a>Como Machine Learning Studio (clássico) difere da Azure Machine Learning?

[Azure Machine Learning](../service/overview-what-is-azure-ml.md) fornece SDKs **-e-** o designer de Azure Machine Learning (versão prévia), para preparar dados rapidamente, treinar e implantar modelos de aprendizado de máquina. O designer fornece uma experiência de arrastar e soltar semelhante ao Studio (clássico). No entanto, diferentemente da plataforma de computação proprietária do estúdio (clássico), o designer usa seus próprios recursos de computação e é totalmente integrado ao Azure Machine Learning.

Aqui está uma comparação rápida:

|| Machine Learning Studio (clássico) | Azure Machine Learning |
|---| --- | --- |
| Arrastar e soltar interface | Sim | Sim- [Designer de Azure Machine Learning (versão prévia)](../service/concept-designer.md) |
| Testes | Escalonável (limite de dados de treinamento de 10 GB) | Dimensionar com destino de computação |
| Módulos para interface do tipo "arrastar e soltar" | Muitos | Conjunto inicial de [módulos](../algorithm-module-reference/module-reference.md) populares|
|Treinamento de destinos de computação| Destino de computação proprietário, somente suporte de CPU| Dá suporte a Azure Machine Learning computação (GPU ou CPU) e VMs de notebook.<br/>([Outros cálculos com suporte no SDK](../service/concept-compute-target.md#train))|
|Destinos de computação do inferência| Formato de serviço da Web proprietário, não personalizável |  Serviço kubernetes do Azure e computação AML <br/>([Outros cálculos com suporte no SDK](../service/how-to-deploy-and-where.md)) |
| Pipeline ML | Não suportado | [Pipelines](../service/concept-ml-pipelines.md) com suporte |
| MLOps | Gerenciamento e implantação de modelos básicos | Implantação configurável-modelo e controle de versão e acompanhamento de pipeline |
| Formato do modelo | Formato proprietário, somente estúdio | Formato padrão dependendo do tipo de trabalho de treinamento |
|Treinamento de modelo automatizado e ajuste de hiperparâmetro | Não | Ainda não está no designer <br/> ([Com suporte na página de aterrissagem do SDK e do espaço de trabalho](../service/concept-automated-ml.md)) | 

Experimente o designer com [tutorial: prever o preço do automóvel com o designer](../service/tutorial-designer-automobile-price-train-score.md)

> [!NOTE]
> Os modelos criados no estúdio (clássico) não podem ser implantados ou gerenciados pelo Azure Machine Learning. No entanto, os modelos criados e implantados no designer podem ser gerenciados por meio do espaço de trabalho Azure Machine Learning.

## <a name="download-the-machine-learning-studio-classic-overview-diagram"></a>Baixar o diagrama de visão geral de Machine Learning Studio (clássico)
Baixe o diagrama de **visão geral de recursos do Microsoft Azure Machine Learning Studio (clássico)** e obtenha uma exibição de alto nível dos recursos do Machine Learning Studio (clássico). Para o ter à mão, pode imprimir o diagrama no tamanho de tabloide (11 x 17 pol.).

**Baixe o diagrama aqui: [Microsoft Azure Machine Learning Studio visão geral dos recursos (clássico)](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf) **
![Microsoft Azure Machine Learning Studio visão geral dos recursos](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Componentes de um experimento do estúdio (clássico)
Uma experimentação é constituída por conjuntos de dados que fornecem dados aos módulos analíticos, que ligar em conjunto para construir um modelo de análise preditiva. Especificamente, uma experimentação válida tem estas características:

* A experimentação tem, pelo menos, um conjunto de dados e um módulo
* Os conjuntos de dados só podem ser ligados aos módulos
* Os módulos poderão estar ligados a conjuntos de dados ou a outros módulos
* Todas as portas de entrada de módulos têm de ter alguma ligação ao fluxo de dados
* Devem ser definidos todos os parâmetros necessários para cada módulo

Pode criar uma experimentação de raiz, ou pode utilizar uma experimentação de exemplo existente como um modelo. Para obter mais informações, veja [Copy example experiments to create new machine learning experiments](sample-experiments.md) (Copiar experimentações de exemplo para criar novas experimentações de aprendizagem automática).

Para obter um exemplo de como criar um experimento, consulte [criar um experimento simples no Azure Machine Learning Studio (clássico)](create-experiment.md).

Para obter uma explicação mais completa da criação de uma solução de análise preditiva, consulte [desenvolver uma solução preditiva com Azure Machine Learning Studio (clássico)](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Conjuntos de dados
Um conjunto de dados é dado que foi carregado para Machine Learning Studio (clássico) para que possa ser usado no processo de modelagem. Vários conjuntos de exemplos de conjunto de informações estão incluídos com Machine Learning Studio (clássico) para você experimentar, e você pode carregar mais conjuntos de informações conforme necessário. Seguem-se alguns exemplos de conjuntos de dados incluídos:

* **Dados MPG para vários automóveis** - quilómetros por litro (MPG) valores para automóveis identificados pelo número de cilindros, potência de cavalos, etc.
* **Dados sobre cancro da mama** - Dados de diagnóstico sobre cancro da mama.
* **Dados sobre incêndios florestais** - tamanhos dos incêndios florestais no nordeste de Portugal.

À medida que cria um experimento, você pode escolher na lista de conjuntos de valores disponíveis à esquerda da tela.

Para obter uma lista de conjuntos de dados de exemplo incluídos no Machine Learning Studio (clássico), consulte [usar os conjuntos de dados de exemplo em Azure Machine Learning Studio (clássico)](use-sample-datasets.md).

### <a name="modules"></a>Módulos
Um módulo é um algoritmo que pode utilizar nos seus dados.  A versão clássica do Machine Learning Studio tem vários módulos que variam de funções de entrada de dados para treinamento, pontuação e processos de validação. Seguem-se alguns exemplos de módulos incluídos:

* [Converter em ARFF][convert-to-arff] – converte um conjunto de um DataSet serializado do .net para o formato de arquivo de relação de atributo (ARFF).
* [Calcular estatísticas elementares][elementary-statistics] -calcula as estatísticas elementares, como média, desvio padrão, etc.
* [Regressão linear][linear-regression] -cria um modelo de regressão linear baseado em descendente de gradiente online.
* [Modelo de Pontuação][score-model] – pontua um modelo de classificação ou regressão treinado.

Ao criar um experimento, você pode escolher na lista de módulos disponíveis à esquerda da tela.

Um módulo pode ter um conjunto de parâmetros que pode utilizar para configurar algoritmos internos do módulo. Quando seleciona um módulo na tela, os parâmetros do módulo são apresentados no painel **Propriedades** à direita da tela. Pode modificar os parâmetros nesse painel para otimizar o seu modelo.

Para obter ajuda para navegar pela biblioteca grande de algoritmos de aprendizado de máquina disponíveis, consulte [como escolher algoritmos para Microsoft Azure Machine Learning Studio (clássico)](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Implementar um serviço web de análise preditiva
Quando seu modelo de análise preditiva estiver pronto, você poderá implantá-lo como um serviço Web diretamente do Machine Learning Studio (clássico). Para obter mais informações sobre esse processo, consulte [implantar um serviço web Azure Machine Learning](publish-a-machine-learning-web-service.md).

## <a name="next-steps"></a>Passos seguintes
Você pode aprender as noções básicas de análise preditiva e aprendizado de máquina usando um guia de [início rápido](create-experiment.md) passo a passo e [criando exemplos](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
