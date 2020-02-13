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
ms.date: 10/17/2019
ms.openlocfilehash: be3a07eb2d4232d27a5b41ddc4258c89b79729bf
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169041"
---
# <a name="what-is-machine-learning-studio-classic"></a>O que é o Estúdio de Aprendizagem automática (clássico)?

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

O Microsoft Azure Machine Learning Studio (clássico) é uma ferramenta colaborativa, de drag-and-drop que pode usar para construir, testar e implementar soluções de análise preditiva nos seus dados. O Azure Machine Learning Studio (clássico) publica modelos como serviços web que podem ser facilmente consumidos por aplicações personalizadas ou ferramentas BI como o Excel.

O Machine Learning Studio (clássico) é onde a ciência dos dados, a análise preditiva, os recursos na nuvem e os seus dados se encontram.

## <a name="the-machine-learning-studio-classic--interactive-workspace"></a>O Espaço de Trabalho interativo machine learning Studio (clássico)
Para desenvolver um modelo de análise preditiva, normalmente utiliza dados de uma ou mais fontes, transforma e analisa esses dados através de várias funções estatísticas e manipulação de dados, e gera um conjunto de resultados. Desenvolver um modelo como este é um processo iterativo. À medida que modifica nas várias funções e os respetivos parâmetros, os seus resultados convergem até achar que tem um modelo preparado e eficaz.

O Azure Machine Learning Studio (clássico) dá-lhe um espaço de trabalho interativo e visual para construir, testar e iterar facilmente num modelo de análise preditiva. Você arrasta e larga ***módulos*** de dados e ***módulos*** de análise numa tela interativa, conectando-os para formar uma ***experiência***, que executa no Machine Learning Studio (clássico). Para iterar o design do modelo, edite a experimentação, guarde uma cópia se assim pretender e execute-a novamente. Quando estiver pronto, pode converter a ***experimentação de preparação*** numa ***experimentação preditiva*** e, em seguida, publicá-la como um ***serviço Web*** para que o modelo possa ser acedido por outras pessoas.

Não é necessária programação, ligue visualmente conjuntos de dados e módulos para construir o seu modelo de análise preditiva.

![Diagrama do Azure Machine Learning Studio (clássico): Criar experiências, ler dados para muitas fontes, escrever dados pontuados, escrever modelos.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

<a name="compare"></a>
## <a name="how-does-machine-learning-studio-classic-differ-from-azure-machine-learning"></a>Como é que o Machine Learning Studio (clássico) difere do Azure Machine Learning?

[O Azure Machine Learning](../overview-what-is-azure-ml.md) fornece tanto sDKs **-e-** o designer de machine learning Azure (pré-visualização), para preparar rapidamente dados, treinar e implementar modelos de machine learning. O designer proporciona uma experiência de drag-and-drop semelhante ao Studio (clássico). No entanto, ao contrário da plataforma computacional proprietária do Studio (clássico), o designer utiliza os seus próprios recursos computacionais e está totalmente integrado no Azure Machine Learning.

Aqui está uma comparação rápida:

|| Machine Learning Studio (clássico) | Azure Machine Learning |
|---| --- | --- |
| Arrastar e largar interface | Sim | Sim - Designer de [Machine Learning Azure (pré-visualização)](../concept-designer.md) |
| Experimentação | Escalável (limite de dados de formação de 10 GB) | Escala com meta de cálculo |
| Módulos para interface arrastar e largar | Muitos | Conjunto inicial de [módulos populares](../algorithm-module-reference/module-reference.md)|
|Metas de computação de formação| Alvo de computação proprietário, suporte cpu apenas| Suporta a computação azure machine learning (GPU ou CPU) e Os VMs de Caderno.<br/>(Outros[cálculos suportados em SDK)](../concept-compute-target.md#train)|
|Metas de inferência da computação| Formato de serviço web proprietário, não personalizável |  Serviço Azure Kubernetes e AmL Compute <br/>(Outros[cálculos suportados em SDK)](../how-to-deploy-and-where.md) |
| Gasoduto ML | Não suportado | [Gasodutos](../concept-ml-pipelines.md) suportados |
| MLOps | Gestão e implantação de modelos básicos | Implementação configurável - versão e rastreio de modelos e gasodutos |
| Formato modelo | Formato proprietário, Estúdio (clássico) apenas | Formato padrão dependendo do tipo de trabalho de formação |
|Treinamento automático de modelos e afinação de hiperparâmetros | Não | Ainda não está no designer. <br/> [(Suportado na página de aterragem do SDK e](../concept-automated-ml.md)do espaço de trabalho) | 

Experimente o designer com [Tutorial: Preveja o preço](../tutorial-designer-automobile-price-train-score.md) do automóvel com o designer

> [!NOTE]
> Os modelos criados em Studio (clássico) não podem ser implantados ou geridos pela Azure Machine Learning. No entanto, os modelos criados e implantados no designer podem ser geridos através do espaço de trabalho Azure Machine Learning.

## <a name="download-the-machine-learning-studio-classic-overview-diagram"></a>Descarregue o diagrama de visão geral do Estúdio de Aprendizagem automática (clássico)
Descarregue o diagrama de Visão Geral do **Microsoft Azure Machine Learning Studio (clássico) Capabilities** E obtenha uma visão de alto nível das capacidades do Machine Learning Studio (clássico). Para o ter à mão, pode imprimir o diagrama no tamanho de tabloide (11 x 17 pol.).

**Descarregue o diagrama aqui: [Microsoft Azure Machine Learning Studio (clássico) Capabilities Overview](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf) **
![Microsoft Azure Machine Learning Studio (clássico) Capabilitis Overview](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


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

* [Converter para ARFF][convert-to-arff] - Converte um conjunto de dados serializado .NET para formato de ficheiro de relação de atributo (ARFF).
* [Compute Elementary Statistics][elementary-statistics] - Calcula estatísticas elementares como média, desvio padrão, etc.
* [Regressão Linear][linear-regression] - Cria um modelo de regressão linear baseado em gradiente on-line.
* [Modelo de Pontuação][score-model] - Pontua rés um modelo de classificação ou regressão treinado.

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
