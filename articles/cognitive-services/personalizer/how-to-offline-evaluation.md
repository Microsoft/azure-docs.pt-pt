---
title: Avaliação offline – personalizador
titleSuffix: Azure Cognitive Services
description: Saiba como analisar seu loop de aprendizagem com uma avaliação offline
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a8a75601daf36ca21ea56a5930219d7d467f0c85
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69557815"
---
# <a name="how-to-analyze-your-learning-loop-with-an-offline-evaluation"></a>Como analisar seu loop de aprendizagem com uma avaliação offline


Saiba como concluir uma avaliação offline e entender os resultados.

As avaliações offline permitem que você meça a eficiência do personalizador em comparação com o comportamento padrão do aplicativo, saiba quais recursos estão contribuindo com a personalização e descubra novas configurações de Machine Learning automaticamente.

Leia sobre [avaliações offline](concepts-offline-evaluation.md) para saber mais.


## <a name="prerequisites"></a>Pré-requisitos

1. Você deve ter um loop personalizado configurado
1. O loop personalizador deve ter uma quantidade representativa de dados – como um aproximada, recommmend pelo menos 50.000 eventos em seus logs para obter resultados significativos de avaliação.

Opcionalmente, você também pode ter exportado anteriormente arquivos de _política de aprendizado_ que você pode comparar e testar na mesma avaliação.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Etapas para iniciar uma nova avaliação offline

1. Localize o recurso de loop de personalização no portal do Azure.
1. Navegue até a seção "avaliação".
1. Clique em nova avaliação
1. Selecione uma data de início e de término para a avaliação offline. Essas são as datas no passado, que especificam o intervalo de dados a ser usado na avaliação. Esses dados devem estar presentes nos logs, conforme especificado na configuração de [retenção de dados](how-to-settings.md) .
1. Opcionalmente, você pode carregar sua própria política de aprendizado. 
1. Especifique se o personalizador deve criar uma política de aprendizado otimizada com base no comportamento do usuário observado neste período de tempo.
1. Iniciar a avaliação

## <a name="results"></a>Resultados

As avaliações podem levar muito tempo para serem executadas, dependendo da quantidade de dados a serem processados, do número de políticas de aprendizado a serem comparadas e se uma otimização foi solicitada.

Depois de concluído, você poderá ver os seguintes resultados:

1. Comparações de políticas de aprendizado, incluindo:
    * **Política online**: A política de aprendizado atual usada no Personalizador
    * **Linha de base**: O padrão do aplicativo (conforme determinado pela primeira ação enviada em chamadas de classificação),
    * **Política aleatória**: Um comportamento de classificação imaginário que sempre retorna a opção aleatória de ações dos fornecidos.
    * **Políticas personalizadas**: Políticas de aprendizado adicionais carregadas ao iniciar a avaliação.
    * **Política otimizada**: Se a avaliação foi iniciada com a opção de descobrir uma política otimizada, ela também será comparada e você poderá baixá-la ou torná-la a política de aprendizado online, substituindo a atual.

1. Eficácia dos [recursos](concepts-features.md) para ações e contexto.


## <a name="more-information"></a>Mais Informações

* Saiba [como funcionam as avaliações offline](concepts-offline-evaluation.md).
