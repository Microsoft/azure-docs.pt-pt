---
title: Avaliação offline
titleSuffix: Personalizer - Azure Cognitive Services
description: Saiba como analisar seu ciclo de aprendizagem com uma edição de avaliação offline
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: e99a8242e438ef5a8ab7fd917724450f8080bb41
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027062"
---
# <a name="how-to-analyze-your-learning-loop-with-an-offline-evaluation"></a>Como analisar seu ciclo de aprendizagem com uma edição de avaliação offline


Saiba como realizar uma avaliação offline e compreender os resultados.

Avaliações offline permitem-lhe medir como Personalizer eficaz é comparado com o comportamento de padrão de seu aplicativo, saiba que funcionalidades são contribui mais para personalização e descubra novas definições de aprendizado de máquina automaticamente.

Leia sobre [Offline avaliações](concepts-offline-evaluation.md) para saber mais.


## <a name="prerequisites"></a>Pré-requisitos

1. Tem de ter um loop de Personalizer configurado
1. O loop Personalizer tem de ter, pelo menos, 50 000 eventos nos seus registos para os resultados da avaliação significativo.

Opcionalmente, pode também exportou anteriormente _política de aprendizagem_ arquivos, pode comparar e teste na avaliação do mesmo.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Passos para começar uma nova avaliação Offline

1. Localize o recurso de Loop de personalização no portal do Azure.
1. Navegue para a secção de "Avaliação".
1. Clique em nova avaliação
1. Selecione uma data de início e de fim para a avaliação offline. Estas são as datas no passado, especifique o intervalo de dados para utilizar na avaliação. Estes dados tem de estar presentes nos registos, conforme especificado na [retenção de dados](how-to-settings.md) definição.
1. Opcionalmente, pode carregar sua própria política de aprendizado. 
1. Especifique se Personalizer deve criar uma política de Learning otimizado, com base no comportamento do utilizador foi observado durante este período de tempo.
1. Iniciar a avaliação

## <a name="results"></a>Resultados

Avaliações podem demorar muito tempo a executar, dependendo da quantidade de dados para processar, o número de políticas para comparar, de aprendizagem e se foi pedida uma otimização.

Depois de concluído, pode ver os seguintes resultados:

1. Comparações de políticas de Aprendizado, incluindo:
    * **Política online**: A diretiva de Aprendizado atual usada no Personalizer
    * **Linha de base**: Padrão da aplicação (conforme determinado pela primeira ação enviada em chamadas de classificação),
    * **Política aleatória**: Um comportamento classificação imaginário que sempre retorna escolha aleatória de ações entre as fornecido.
    * **As políticas personalizadas**: Políticas de aprendizagem adicionais carregados ao iniciar a avaliação.
    * **Com otimização de política**: Se a avaliação foi iniciada com a opção para detetar uma política de otimizada, também irá ser comparada e será possível baixá-lo ou torná-lo a política de aprendizagem online, substituindo atual.

1. Eficácia dos [funcionalidades](concepts-features.md) para ações e contexto.


## <a name="more-information"></a>Mais Informações

* Saiba mais [funcionam como offline de avaliações](concepts-offline-evaluation.md).