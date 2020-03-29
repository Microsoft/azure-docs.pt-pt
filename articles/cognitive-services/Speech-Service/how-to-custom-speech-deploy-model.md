---
title: Implementar um modelo para discurso personalizado - serviço de discurso
titleSuffix: Azure Cognitive Services
description: Neste documento, você vai aprender a criar e implementar um ponto final usando o portal de Discurso Personalizado.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: e41653e4644b09cc357510223e5ee86c3806ae2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806118"
---
# <a name="deploy-a-custom-model"></a>Implementar um modelo personalizado

Depois de ter carregado e inspecionado dados, avaliado a precisão e treinado um modelo personalizado, pode implementar um ponto final personalizado para usar com as suas apps, ferramentas e produtos. Neste documento, você vai aprender a criar e implementar um ponto final usando o [portal de Discurso Personalizado](https://speech.microsoft.com/customspeech).

## <a name="create-a-custom-endpoint"></a>Criar um ponto final personalizado

Para criar um novo ponto final personalizado, inscreva-se no [portal de Discurso Personalizado](https://speech.microsoft.com/customspeech) e selecione **Deployment** do menu de Discurso Personalizado no topo da página. Se esta for a sua primeira corrida, vai notar que não há pontos finais listados na tabela. Depois de criar um ponto final, usará esta página para rastrear cada ponto final implantado.

Em seguida, selecione **Adicionar ponto final** e introduza um **nome** e **descrição** para o seu ponto final personalizado. Em seguida, selecione o modelo personalizado que gostaria de associar a este ponto final. A partir desta página, também pode ativar o registo. O registo permite-lhe monitorizar o tráfego de pontos finais. Se estiver incapacitado, o tráfego não será armazenado.

![Como implementar um modelo](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Não se esqueça de aceitar os termos de uso e detalhes de preços.

Em seguida, selecione **Criar**. Esta ação devolve-o à página **de Implantação.** A tabela agora inclui uma entrada que corresponde ao seu ponto final personalizado. O estado do ponto final mostra o seu estado atual. Pode levar até 30 minutos para instantaneamente um novo ponto final usando os seus modelos personalizados. Quando o estado da implantação muda para **Completar,** o ponto final está pronto a ser utilizado.

Depois de o seu ponto final ser implantado, o nome final aparece como um link. Clique no link para visualizar informações específicas do seu ponto final, tais como a chave de ponto final, URL de ponto final e código de amostra.

## <a name="view-logging-data"></a>Ver dados de registo

Os dados de registo estão disponíveis para download em **Dados > Endpoint**.

## <a name="next-steps"></a>Passos seguintes

* Use o seu ponto final personalizado com o [SDK](speech-sdk.md) do Discurso

## <a name="additional-resources"></a>Recursos adicionais

* [Prepare e teste os seus dados](how-to-custom-speech-test-data.md)
* [Inspecione os seus dados](how-to-custom-speech-inspect-data.md)
* [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)
* [Preparar o seu modelo](how-to-custom-speech-train-model.md)
* [Implante o seu modelo](how-to-custom-speech-deploy-model.md)
