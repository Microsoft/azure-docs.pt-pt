---
title: Implementar um modelo para Discurso Personalizado - Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Neste documento, você vai aprender a criar e implementar um ponto final usando o portal Discurso Personalizado.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: c7f03027abf7f3c5e330e5cd95075cce1152a7d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85130420"
---
# <a name="deploy-a-custom-model"></a>Implementar um modelo personalizado

Depois de ter carregado e inspecionado dados, avaliado a precisão e treinado um modelo personalizado, pode implementar um ponto final personalizado para usar com as suas apps, ferramentas e produtos. Neste documento, você vai aprender a criar e implementar um ponto final usando o [portal Discurso Personalizado](https://speech.microsoft.com/customspeech).

## <a name="create-a-custom-endpoint"></a>Criar um ponto final personalizado

Para criar um novo ponto final personalizado, inscreva-se no [portal Discurso Personalizado](https://speech.microsoft.com/customspeech) e selecione **Implementação** a partir do menu Desemesto de Discurso Personalizado no topo da página. Se esta for a sua primeira corrida, vai notar que não há pontos finais listados na tabela. Depois de criar um ponto final, usará esta página para rastrear cada ponto final implantado.

Em seguida, **selecione Adicionar ponto final** e introduza um **Nome** e **Descrição** para o seu ponto de final personalizado. Em seguida, selecione o modelo personalizado que gostaria de associar a este ponto final. A partir desta página, também pode ativar o registo. A sessão de registo permite-lhe monitorizar o tráfego do ponto final. Se for desativado, o tráfego não será armazenado.

![Como implementar um modelo](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Não se esqueça de aceitar os termos de uso e detalhes de preços.

Em seguida, **selecione Criar**. Esta ação devolve-o à página **de Implementação.** A tabela agora inclui uma entrada que corresponde ao seu ponto final personalizado. O estado do ponto final mostra o seu estado atual. Pode levar até 30 minutos para instantaneaizar um novo ponto final usando os seus modelos personalizados. Quando o estado da implantação muda para **Concluir,** o ponto final está pronto a ser utilizado.

Depois de o seu ponto final ser implantado, o nome do ponto final aparece como um link. Clique no link para apresentar informações específicas do seu ponto final, como a chave de ponto final, URL de ponto final e código de amostra.

## <a name="view-logging-data"></a>Ver dados de registo

Os dados de registo estão disponíveis para download em **Detalhes > Endpoint**.
> [!NOTE]
>Os dados de registo estão disponíveis durante 30 dias no armazenamento da Microsoft e serão removidos posteriormente. Caso uma conta de armazenamento detida por um cliente esteja ligada à subscrição de serviços cognitivos, os dados de registo não serão automaticamente eliminados.

## <a name="next-steps"></a>Passos seguintes

* Saiba como usar o seu modelo personalizado [aqui.](how-to-specify-source-language.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Prepare e teste os seus dados](how-to-custom-speech-test-data.md)
* [Fiscalizar os seus dados](how-to-custom-speech-inspect-data.md)
* [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)
* [Preparar o seu modelo](how-to-custom-speech-train-model.md)
* [Implemente o seu modelo](how-to-custom-speech-deploy-model.md)
