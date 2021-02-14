---
title: O que posso fazer com o Machine Learning Studio (clássico) - Azure
description: O Machine Learning Studio (clássico) é uma ferramenta de arrastar e largar para construir rapidamente modelos a partir de uma biblioteca de algoritmos e módulos prontos a usar.
services: machine-learning
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: overview
ms.date: 08/19/2020
ms.openlocfilehash: ad0550dc20edfbc10fb8877ad049c27fc2edf086
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517540"
---
# <a name="what-can-i-do-with-machine-learning-studio-classic"></a>O que posso fazer com o Machine Learning Studio (clássico)?

**APLICA-SE A:** ![ Esta é uma marca de verificação, o que significa que este artigo se aplica ao Machine Learning Studio (clássico). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Este é um X, o que significa que este artigo se aplica ao Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

O Machine Learning Studio (clássico) é uma ferramenta de arrastar e largar que pode usar para construir, testar e implementar modelos de machine learning. O Estúdio (clássico) publica modelos como serviços web, que podem ser facilmente consumidos por aplicações personalizadas ou ferramentas BI como o Excel.


## <a name="studio-classic--interactive-workspace"></a>Espaço de trabalho interativo estúdio (clássico)

Para desenvolver um modelo de análise preditiva, normalmente utiliza dados de uma ou mais fontes, transforma e analisa esses dados através de várias funções de manipulação de dados e estatísticas, e gera um conjunto de resultados. Desenvolver um modelo como este é um processo iterativo. À medida que modifica as várias funções e os respetivos parâmetros, os seus resultados convergem até achar que tem um modelo preparado e eficaz.

O Machine Learning Studio (clássico) dá-lhe um espaço de trabalho interativo e visual para facilmente construir, testar e iterar num modelo de análise preditiva. Arrasta-e-drop ***conjuntos** de dados _ e _*_módulos_*_ de análise numa tela interativa, conectando-os para formar uma _*_experiência,_*_ que você corre no Machine Learning Studio (clássico). Para iterar o design do modelo, edite a experimentação, guarde uma cópia se assim pretender e execute-a novamente. Quando estiver pronto, pode converter a sua _*_experiência de treino_*_ numa experiência _*_preditiva_*_ e depois publicá-la como um *__ serviço web_* para que o seu modelo possa ser acedido por outros.

Não é necessária programação, ligar visualmente conjuntos de dados e módulos para construir o seu modelo de análise preditiva.

![Diagrama do Machine Learning Studio (clássico): Criar experiências, ler dados para muitas fontes, escrever dados pontuados, escrever modelos.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-ml-studio-classic-overview-diagram"></a>Descarregue o diagrama de visão geral do ML Studio (clássico)
Descarregue o diagrama do **Microsoft ML Studio (clássico) Capabilities Overview** e obtenha uma visão de alto nível das capacidades do Machine Learning Studio (clássico). Para o ter à mão, pode imprimir o diagrama no tamanho de tabloide (11 x 17 pol.).

**Descarregue o diagrama aqui: [Microsoft Machine Learning Studio (clássico) Capabilities Overview](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)** 
 ![ Visão geral do Microsoft Machine Learning Studio (clássico)](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Componentes de uma experiência studio (clássica)
Uma experimentação é constituída por conjuntos de dados que fornecem dados aos módulos analíticos, que ligar em conjunto para construir um modelo de análise preditiva. Especificamente, uma experimentação válida tem estas características:

* A experimentação tem, pelo menos, um conjunto de dados e um módulo
* Os conjuntos de dados só podem ser ligados aos módulos
* Os módulos poderão estar ligados a conjuntos de dados ou a outros módulos
* Todas as portas de entrada de módulos têm de ter alguma ligação ao fluxo de dados
* Devem ser definidos todos os parâmetros necessários para cada módulo

Pode criar uma experimentação de raiz, ou pode utilizar uma experimentação de exemplo existente como um modelo. Para obter mais informações, veja [Copy example experiments to create new machine learning experiments](sample-experiments.md) (Copiar experimentações de exemplo para criar novas experimentações de aprendizagem automática).

Para um exemplo de criação de uma experiência, consulte [Criar uma experiência no Machine Learning Studio (clássico)](create-experiment.md).

Para uma caminhada mais completa da criação de uma solução de análise preditiva, consulte [Desenvolver uma solução preditiva com o Machine Learning Studio (clássico)](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Conjuntos de dados
Um conjunto de dados são dados que foram enviados para o Machine Learning Studio (clássico) para que possa ser usado no processo de modelação. Vários conjuntos de dados de amostras estão incluídos no Machine Learning Studio (clássico) para você experimentar, e você pode carregar mais conjuntos de dados à medida que precisa deles. Seguem-se alguns exemplos de conjuntos de dados incluídos:

* **Dados MPG para vários automóveis** - quilómetros por litro (MPG) valores para automóveis identificados pelo número de cilindros, potência de cavalos, etc.
* **Dados sobre cancro da mama** - Dados de diagnóstico sobre cancro da mama.
* **Dados sobre incêndios florestais** - tamanhos dos incêndios florestais no nordeste de Portugal.

Ao construir uma experiência, pode escolher entre a lista de conjuntos de dados disponíveis à esquerda da tela.

Para obter uma lista de conjuntos de dados de amostras incluídos no Machine Learning Studio (clássico), consulte [utilizar os conjuntos de dados da amostra no Machine Learning Studio (clássico)](use-sample-datasets.md).

### <a name="modules"></a>Módulos
Um módulo é um algoritmo que pode utilizar nos seus dados. O Machine Learning Studio (clássico) tem uma série de módulos que vão desde funções de entrada de dados até processos de treino, pontuação e validação. Seguem-se alguns exemplos de módulos incluídos:

* [Converter em ARFF][convert-to-arff] - converte um conjunto de dados .NET em série para o formato de ficheiro de relação de atributo (ARFF).
* [Calcular estatísticas básicas][elementary-statistics] - calcula estatísticas básicas como a média, o desvio padrão, etc.
* [Regressão linear][linear-regression] - cria um modelo de regressão linear online descendente.
* [Modelo de pontuação][score-model] - Pontua um modelo de classificação ou regressão preparado.

Ao construir uma experiência, pode escolher entre a lista de módulos disponíveis à esquerda da tela.

Um módulo pode ter um conjunto de parâmetros que pode utilizar para configurar algoritmos internos do módulo. Quando seleciona um módulo na tela, os parâmetros do módulo são apresentados no painel **Propriedades** à direita da tela. Pode modificar os parâmetros nesse painel para otimizar o seu modelo.

Para alguma ajuda a navegar através da grande biblioteca de algoritmos de machine learning disponíveis, consulte [Como escolher algoritmos para o Microsoft Machine Learning Studio (clássico)](../how-to-select-algorithms.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Implementar um serviço web de análise preditiva
Uma vez que o seu modelo de análise preditiva esteja pronto, pode implantá-lo como um serviço web a partir do Machine Learning Studio (clássico). Para obter mais informações sobre este processo, consulte [implementar um serviço web Azure Machine Learning](deploy-a-machine-learning-web-service.md).

## <a name="next-steps"></a>Passos seguintes
Você pode aprender os fundamentos da análise preditiva e da aprendizagem automática usando um [quickstart passo-a-passo](create-experiment.md) e [baseando-se em amostras](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: /azure/machine-learning/studio-module-reference/convert-to-arff
[elementary-statistics]: /azure/machine-learning/studio-module-reference/compute-elementary-statistics
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression
[score-model]: /azure/machine-learning/studio-module-reference/score-model