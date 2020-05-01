---
title: O que é ML Studio (clássico)
titleSuffix: Azure
description: O Azure Machine Learning Studio (clássico) é uma ferramenta de drag-and-drop para construir rapidamente modelos a partir de uma biblioteca pronta a usar de algoritmos e módulos.
services: machine-learning
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.topic: overview
ms.date: 03/24/2020
ms.openlocfilehash: c1772ceb514e46542129759711f2d45db39abf82
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80371980"
---
# <a name="what-is-machine-learning-studio-classic"></a>O que é o Estúdio de Aprendizagem automática (clássico)?

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

O Microsoft Azure Machine Learning Studio (clássico) é uma ferramenta colaborativa, de drag-and-drop que pode usar para construir, testar e implementar soluções de análise preditiva nos seus dados. O Azure Machine Learning Studio (clássico) publica modelos como serviços web que podem ser facilmente consumidos por aplicações personalizadas ou ferramentas BI como o Excel.

O Machine Learning Studio (clássico) é onde a ciência dos dados, a análise preditiva, os recursos na nuvem e os seus dados se encontram.

## <a name="the-machine-learning-studio-classic--interactive-workspace"></a>O Espaço de Trabalho interativo machine learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Para desenvolver um modelo de análise preditiva, normalmente utiliza dados de uma ou mais fontes, transforma e analisa esses dados através de várias funções estatísticas e manipulação de dados, e gera um conjunto de resultados. Desenvolver um modelo como este é um processo iterativo. À medida que modifica nas várias funções e os respetivos parâmetros, os seus resultados convergem até achar que tem um modelo preparado e eficaz.

O Azure Machine Learning Studio (clássico) dá-lhe um espaço de trabalho interativo e visual para construir, testar e iterar facilmente num modelo de análise preditiva. Você arrasta e larga ***módulos*** de dados e ***módulos*** de análise numa tela interativa, conectando-os para formar uma ***experiência***, que executa no Machine Learning Studio (clássico). Para iterar o design do modelo, edite a experimentação, guarde uma cópia se assim pretender e execute-a novamente. Quando estiver pronto, pode converter a ***experimentação de preparação*** numa ***experimentação preditiva*** e, em seguida, publicá-la como um ***serviço Web*** para que o modelo possa ser acedido por outras pessoas.

Não é necessária programação, ligue visualmente conjuntos de dados e módulos para construir o seu modelo de análise preditiva.

![Diagrama do Azure Machine Learning Studio (clássico): Criar experiências, ler dados para muitas fontes, escrever dados pontuados, escrever modelos.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-machine-learning-studio-classic-overview-diagram"></a>Descarregue o diagrama de visão geral do Estúdio de Aprendizagem automática (clássico)
Descarregue o diagrama de Visão Geral do **Microsoft Azure Machine Learning Studio (clássico) Capabilities** E obtenha uma visão de alto nível das capacidades do Machine Learning Studio (clássico). Para o ter à mão, pode imprimir o diagrama no tamanho de tabloide (11 x 17 pol.).

**Descarregue o diagrama aqui: [Microsoft Azure Machine Learning Studio (clássico) Capabilities Overview](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)**
![Microsoft Azure Machine Learning Studio (clássico) Capabilities Overview](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Componentes de uma experiência studio (clássica)
Uma experimentação é constituída por conjuntos de dados que fornecem dados aos módulos analíticos, que ligar em conjunto para construir um modelo de análise preditiva. Especificamente, uma experimentação válida tem estas características:

* A experimentação tem, pelo menos, um conjunto de dados e um módulo
* Os conjuntos de dados só podem ser ligados aos módulos
* Os módulos poderão estar ligados a conjuntos de dados ou a outros módulos
* Todas as portas de entrada de módulos têm de ter alguma ligação ao fluxo de dados
* Devem ser definidos todos os parâmetros necessários para cada módulo

Pode criar uma experimentação de raiz, ou pode utilizar uma experimentação de exemplo existente como um modelo. Para obter mais informações, veja [Copy example experiments to create new machine learning experiments](sample-experiments.md) (Copiar experimentações de exemplo para criar novas experimentações de aprendizagem automática).

Para um exemplo de criação de uma experiência, consulte [Criar uma experiência simples no Azure Machine Learning Studio (clássico)](create-experiment.md).

Para uma passagem mais completa pela criação de uma solução de análise preditiva, consulte [Desenvolver uma solução preditiva com o Azure Machine Learning Studio (clássico)](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Conjuntos de dados
Um conjunto de dados são dados que foram enviados para o Machine Learning Studio (clássico) para que possa ser usado no processo de modelação. Vários conjuntos de dados de amostras estão incluídos no Machine Learning Studio (clássico) para que possa experimentar, e pode fazer upload de mais conjuntos de dados à medida que precisar. Seguem-se alguns exemplos de conjuntos de dados incluídos:

* **Dados MPG para vários automóveis** - quilómetros por litro (MPG) valores para automóveis identificados pelo número de cilindros, potência de cavalos, etc.
* **Dados sobre cancro da mama** - Dados de diagnóstico sobre cancro da mama.
* **Dados sobre incêndios florestais** - tamanhos dos incêndios florestais no nordeste de Portugal.

À medida que constrói uma experiência, pode escolher entre a lista de conjuntos de dados disponíveis à esquerda da tela.

Para obter uma lista de conjuntos de dados de amostras incluídos no Machine Learning Studio (clássico), consulte Utilize os conjuntos de [dados da amostra no Azure Machine Learning Studio (clássico)](use-sample-datasets.md).

### <a name="modules"></a>Módulos
Um módulo é um algoritmo que pode utilizar nos seus dados. O Azure Machine Learning Studio (clássico) tem uma série de módulos que vão desde funções de ingresso de dados a processos de formação, pontuação e validação. Seguem-se alguns exemplos de módulos incluídos:

* [Converter em ARFF][convert-to-arff] - converte um conjunto de dados .NET em série para o formato de ficheiro de relação de atributo (ARFF).
* [Calcular estatísticas básicas][elementary-statistics] - calcula estatísticas básicas como a média, o desvio padrão, etc.
* [Regressão linear][linear-regression] - cria um modelo de regressão linear online descendente.
* [Modelo de pontuação][score-model] - Pontua um modelo de classificação ou regressão preparado.

À medida que constrói uma experiência, pode escolher entre a lista de módulos disponíveis à esquerda da tela.

Um módulo pode ter um conjunto de parâmetros que pode utilizar para configurar algoritmos internos do módulo. Quando seleciona um módulo na tela, os parâmetros do módulo são apresentados no painel **Propriedades** à direita da tela. Pode modificar os parâmetros nesse painel para otimizar o seu modelo.

Para alguma ajuda a navegar através da grande biblioteca de algoritmos de aprendizagem automática disponíveis, consulte Como escolher algoritmos para o [Microsoft Azure Machine Learning Studio (clássico)](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Implementar um serviço web de análise preditiva
Uma vez que o seu modelo de análise preditiva esteja pronto, pode implantá-lo como um serviço web logo a partir do Machine Learning Studio (clássico). Para obter mais informações sobre este processo, consulte [A implantação de um serviço web Azure Machine Learning](deploy-a-machine-learning-web-service.md).

## <a name="next-steps"></a>Passos seguintes
Você pode aprender o básico de análise preditiva e aprendizagem automática usando um [arranque passo a passo](create-experiment.md) e [baseando-se em amostras](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
