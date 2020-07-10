---
title: Pré-visualização responsável de machine learning (ML)
titleSuffix: Azure Machine Learning
description: Saiba o que é ml responsável e como usá-lo em Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 07/09/2020
ms.openlocfilehash: 4f14d4a9207b3bd0ba242973443b8e756527fd70
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86201933"
---
# <a name="responsible-machine-learning-ml-preview"></a>Pré-visualização responsável de machine learning (ML)

Neste artigo, você vai aprender o que é ML Responsável e maneiras de colocá-lo em prática com Azure Machine Learning.

Ao longo do desenvolvimento e utilização dos sistemas de IA, a confiança deve estar no centro. Confiança na plataforma, processo e modelos. Na Microsoft, o ML Responsável engloba os seguintes valores e princípios:

- Compreender modelos de aprendizagem automática
  - Interpretar e explicar o comportamento do modelo
  - Avaliar e mitigar a injustiça do modelo
- Proteger as pessoas e os seus dados
  - Prevenir a exposição de dados com privacidade diferencial  
- Controlar o processo de aprendizagem automática de ponta a ponta
  - Documente o ciclo de vida de aprendizagem automática com folhas de dados

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="Pilares ML responsáveis":::

À medida que a inteligência artificial e os sistemas autónomos se integram mais no tecido da sociedade, é importante fazer um esforço proactivo para antecipar e mitigar as consequências não intencionais destas tecnologias.

## <a name="interpret-and-explain-model-behavior"></a>Interpretar e explicar o comportamento do modelo

É difícil explicar ou os sistemas de caixas opacas podem ser problemáticos porque dificulta que as partes interessadas, como desenvolvedores de sistemas, reguladores, utilizadores e decisores empresariais, compreendam porque é que os sistemas tomam determinadas decisões. Alguns sistemas de IA são mais explicáveis do que outros e às vezes há uma troca entre um sistema com maior precisão e um que é mais explicável.

Para construir sistemas de IA interpretáveis, utilize [o InterpretML,](https://github.com/interpretml/interpret)um pacote de código aberto construído pela Microsoft. [A InterpretML pode ser utilizada dentro da Azure Machine Learning](how-to-machine-learning-interpretability.md) para [interpretar e explicar os seus modelos de aprendizagem automática,](how-to-machine-learning-interpretability-aml.md)incluindo [modelos automatizados de aprendizagem automática.](how-to-machine-learning-interpretability-automl.md)

## <a name="assess-and-mitigate-model-unfairness"></a>Avaliar e mitigar a injustiça do modelo

À medida que os sistemas de IA se envolvem mais na tomada de decisões diárias da sociedade, é de extrema importância que estes sistemas funcionem bem na prestação de resultados justos para todos.

A injustiça nos sistemas de IA pode resultar nas seguintes consequências não intencionais:

- Ocultar oportunidades, recursos ou informações de indivíduos.
- Reforçando os preconceitos e os estereótipos.

Muitos aspetos da equidade não podem ser capturados ou representados por métricas. Existem ferramentas e práticas que podem melhorar a equidade na conceção e desenvolvimento de sistemas de IA.

Dois passos fundamentais para reduzir a injustiça nos sistemas de IA são a avaliação e a mitigação. Recomendamos [o FairLearn,](https://github.com/fairlearn/fairlearn)um pacote de código aberto que pode avaliar e mitigar a potencial injustiça dos sistemas de IA. Para saber mais sobre a equidade e o pacote FairLearn, consulte o [artigo da Justiça no artigo da ML](./concept-fairness-ml.md).

## <a name="prevent-data-exposure-with-differential-privacy"></a>Prevenir a exposição de dados com privacidade diferencial

Quando os dados são usados para análise, é importante que os dados permanecem privados e confidenciais durante todo o seu uso. A privacidade diferencial é um conjunto de sistemas e práticas que ajudam a manter os dados dos indivíduos seguros e privados.

Nos cenários tradicionais, os dados brutos são armazenados em ficheiros e bases de dados. Quando os utilizadores analisam os dados, normalmente utilizam os dados brutos. Isto é uma preocupação porque pode violar a privacidade de um indivíduo. A privacidade diferencial tenta lidar com este problema adicionando "ruído" ou aleatoriedade aos dados para que os utilizadores não possam identificar quaisquer pontos de dados individuais.

É difícil implementar sistemas privados diferencialmente. [WhiteNoise](https://github.com/opendifferentialprivacy/whitenoise-core) é um projeto de código aberto que contém diferentes componentes para a construção de sistemas privados diferenciais globais. Para saber mais sobre privacidade diferencial e o projeto WhiteNoise, consulte a privacidade dos [dados preservando utilizando privacidade diferencial e artigo whiteNoise.](./concept-differential-privacy.md)

> [!NOTE]
> Por favor, note que estamos renomeando o kit de ferramentas e apresentaremos o novo nome nas próximas semanas. 

## <a name="document-the-machine-learning-lifecycle-with-datasheets"></a>Documente o ciclo de vida de aprendizagem automática com folhas de dados

Documentar a informação certa no processo de aprendizagem automática é fundamental para tomar decisões responsáveis em cada fase. As folhas de dados são uma forma de documentar os ativos de aprendizagem automática que são usados e criados como parte do ciclo de vida de aprendizagem automática.

Os modelos tendem a ser considerados como "caixas opacas" e muitas vezes há pouca informação sobre elas. Como os sistemas de aprendizagem automática estão a tornar-se mais abrangentes e são utilizados para a tomada de decisões, a utilização de folhas de dados é um passo para o desenvolvimento de sistemas de aprendizagem automática mais responsáveis.

Algumas informações do modelo que pode querer documentar como parte de uma folha de dados:

- Utilização pretendida
- Arquitetura modelo
- Dados de formação utilizados
- Dados de avaliação utilizados
- Métricas de desempenho do modelo de treino
- Informação de equidade.

Consulte a amostra seguinte para aprender a usar o Azure Machine Learning SDK para implementar [folhas de dados para modelos](https://github.com/microsoft/MLOps/blob/master/pytorch_with_datasheet/model_with_datasheet.ipynb).

## <a name="additional-resources"></a>Recursos adicionais

- Utilize encriptação homomórfica para [implementar um serviço web de inferenculação encriptado](how-to-homomorphic-encryption-seal.md).
- Saiba mais sobre o conjunto de diretrizes [do ML](https://www.partnershiponai.org/about-ml/) PARA a documentação do sistema de aprendizagem automática.
