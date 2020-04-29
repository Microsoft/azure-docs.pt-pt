---
title: Como realizar avaliação offline - Personalizer
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como usar a avaliação offline para medir a eficácia da sua app e analisar o seu ciclo de aprendizagem.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: diberry
ms.openlocfilehash: ce85c2d264b2b4849a4a36ed757150292fdf39f0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77622784"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analise o seu ciclo de aprendizagem com uma avaliação offline

Aprenda a completar uma avaliação offline e compreenda os resultados.

As Avaliações Offline permitem-lhe medir a eficácia do Personalizer em comparação com o comportamento padrão da sua aplicação, aprender quais as funcionalidades que mais contribuem para a personalização e descobrir automaticamente novos valores de aprendizagem automática.

Leia sobre [avaliações offline](concepts-offline-evaluation.md) para saber mais.

## <a name="prerequisites"></a>Pré-requisitos

* Um loop personalizado configurado
* O loop Personalizer deve ter uma quantidade representativa de dados - como um estádio recomendamos pelo menos 50.000 eventos nos seus registos para resultados significativos de avaliação. Opcionalmente, também pode ter exportado ficheiros de política de _aprendizagem_ que pode comparar e testar na mesma avaliação.

## <a name="run-an-offline-evaluation"></a>Executar uma avaliação offline

1. No [portal Azure,](https://azure.microsoft.com/free/)localize o seu recurso Personalizer.
1. No portal Azure, vá à secção **de Avaliações** e selecione **Criar Avaliação.**
    ![No portal Azure, vá à secção **Avaliações** e selecione **Create Evaluation**.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Configure os seguintes valores:

    * Um nome de avaliação.
    * Data de início e fim - estas são datas que especificam o leque de dados a utilizar na avaliação. Estes dados devem estar presentes nos registos, conforme especificado no valor de Retenção de [Dados.](how-to-settings.md)
    * Otimização Discovery definido para **sim**.

    > [!div class="mx-imgBorder"]
    > ![Escolha as definições de avaliação offline](./media/offline-evaluation/create-an-evaluation-form.png)

1. Inicie a Avaliação selecionando **Ok**.

## <a name="review-the-evaluation-results"></a>Rever os resultados da avaliação

As avaliações podem demorar muito tempo a ser executadas, dependendo da quantidade de dados para processar, número de políticas de aprendizagem para comparar, e se foi solicitada uma otimização.

Uma vez concluída, pode selecionar a avaliação da lista de avaliações e, em seguida, selecionar **Compare a pontuação da sua aplicação com outras definições de aprendizagem potenciais**. Selecione esta funcionalidade quando quiser ver como funciona a sua política de aprendizagem atual em comparação com uma nova política.

1. Reveja o desempenho das políticas de [aprendizagem.](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy)

    > [!div class="mx-imgBorder"]
    > [![Resultados da avaliação de revisão](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. Selecione **Aplicar** para aplicar a política que melhora o modelo melhor para os seus dados.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [como funcionam as avaliações offline.](concepts-offline-evaluation.md)
