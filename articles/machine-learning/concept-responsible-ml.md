---
title: Aprendizagem automática responsável (ML)
titleSuffix: Azure Machine Learning
description: Saiba o que é o ML responsável e como usá-lo em Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 05/08/2020
ms.openlocfilehash: 3cef3c2179019f6d84de5596e61abaf8d7d3182c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597661"
---
# <a name="responsible-machine-learning-ml"></a>Aprendizagem automática responsável (ML)

Neste artigo, você vai aprender o que é O ML responsável e as formas de colocá-lo em prática com o Azure Machine Learning.

Ao longo do desenvolvimento e utilização dos sistemas de IA, a confiança deve estar no centro. Confiança na plataforma, processo e modelos. Na Microsoft, a Responsible ML engloba os seguintes valores e princípios:

- Compreender modelos de aprendizagem automática
  - Interpretar e explicar o comportamento do modelo
  - Avaliar e mitigar a injustiça do modelo
- Proteger as pessoas e os seus dados
  - Prevenir a exposição de dados com privacidade diferencial  
- Controlar o processo de aprendizagem automática de ponta a ponta
  - Documentar o ciclo de vida da aprendizagem automática com folhas de dados

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="Pilares ML responsáveis":::

À medida que a inteligência artificial e os sistemas autónomos se integram mais no tecido da sociedade, é importante fazer um esforço proactivo para antecipar e mitigar as consequências não intencionais destas tecnologias.

## <a name="interpret-and-explain-model-behavior"></a>Interpretar e explicar o comportamento do modelo

Os sistemas de caixa preta podem ser problemáticos porque dificulta que as partes interessadas, como desenvolvedores de sistemas, reguladores, utilizadores e decisores empresariais compreendam por que razão os sistemas tomam determinadas decisões. Alguns sistemas de IA são mais explicáveis do que outros e às vezes há uma troca entre um sistema com maior precisão e um que é mais explicável.

Para construir sistemas de IA interpretáveis, utilize o [InterpretML,](https://github.com/interpretml/interpret)um pacote de código aberto construído pela Microsoft. [A InterpretML pode ser usada no interior do Azure Machine Learning](how-to-machine-learning-interpretability.md) para [interpretar e explicar os seus modelos](how-to-machine-learning-interpretability-aml.md)de machine learning, incluindo [modelos automatizados](how-to-machine-learning-interpretability-automl.md)de aprendizagem automática de máquinas.

## <a name="assess-and-mitigate-model-unfairness"></a>Avaliar e mitigar a injustiça do modelo

À medida que os sistemas de IA se envolvem mais na tomada de decisões diárias da sociedade, é de extrema importância que estes sistemas funcionem bem na proporcionação de resultados justos para todos.

A injustiça nos sistemas de IA pode resultar nas seguintes consequências não intencionais:

- Retenção de oportunidades, recursos ou informação de indivíduos.
- Reforçando os preconceitos e estereótipos.

Muitos aspetos da equidade não podem ser capturados ou representados por métricas. Existem ferramentas e práticas que podem melhorar a equidade na conceção e desenvolvimento de sistemas de IA.

Dois passos fundamentais para reduzir a injustiça nos sistemas de IA são a avaliação e a mitigação. Recomendamos o [FairLearn](https://github.com/fairlearn/fairlearn), um pacote de código aberto que possa avaliar e mitigar a potencial injustiça dos sistemas de IA. Para saber mais sobre justiça e o pacote FairLearn, consulte o [artigo Fairness in ML](./concept-fairness-ml.md).

## <a name="prevent-data-exposure-with-differential-privacy"></a>Prevenir a exposição de dados com privacidade diferencial

Quando os dados são utilizados para análise, é importante que os dados se mantenham privados e confidenciais em toda a sua utilização. A privacidade diferencial é um conjunto de sistemas e práticas que ajudam a manter os dados dos indivíduos seguros e privados.

Nos cenários tradicionais, os dados brutos são armazenados em ficheiros e bases de dados. Quando os utilizadores analisam dados, normalmente utilizam os dados brutos. Esta é uma preocupação porque pode infringir a privacidade de um indivíduo. A privacidade diferencial tenta lidar com este problema adicionando "ruído" ou aleatoriedade aos dados para que os utilizadores não possam identificar quaisquer pontos de dados individuais.

A implementação de sistemas diferenciais privados é difícil. [WhiteNoise](https://github.com/opendifferentialprivacy/whitenoise-core) é um projeto de código aberto que contém diferentes componentes para a construção de sistemas privados diferenciais globais. Para saber mais sobre privacidade diferencial e o projeto WhiteNoise, consulte a privacidade dos [dados preservando usando privacidade diferencial e artigo whiteNoise.](./concept-differential-privacy.md)

## <a name="document-the-machine-learning-lifecycle-with-datasheets"></a>Documentar o ciclo de vida da aprendizagem automática com folhas de dados

Documentar a informação certa no processo de aprendizagem automática é fundamental para tomar decisões responsáveis em cada fase. As folhas de dados são uma forma de documentar os ativos de aprendizagem automática que são usados e criados como parte do ciclo de vida da aprendizagem automática.

Os modelos tendem a ser considerados como "caixas negras" e muitas vezes há pouca informação sobre eles. Uma vez que os sistemas de aprendizagem automática estão a tornar-se mais abrangentes e são utilizados para a tomada de decisões, a utilização de folhas de dados é um passo no sentido de desenvolver sistemas de aprendizagem automática mais responsáveis.

Algumas informações do modelo que pode querer documentar como parte de uma folha de dados:

- Utilização prevista
- Arquitetura modelo
- Dados de formação utilizados
- Dados de avaliação utilizados
- Métricas de desempenho do modelo de treino
- Informação justa.

Consulte a seguinte amostra para aprender a utilizar o Azure Machine Learning SDK para implementar folhas de [dados para modelos](https://github.com/microsoft/MLOps/blob/master/pytorch_with_datasheet/model_with_datasheet.ipynb).

## <a name="additional-resources"></a>Recursos adicionais

- Saiba mais sobre o conjunto de diretrizes [sobre](https://www.partnershiponai.org/about-ml/) ml para documentação do sistema de aprendizagem automática.