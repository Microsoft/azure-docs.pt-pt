---
title: Implementar um modelo de voz personalizada - serviços de voz
titlesuffix: Azure Cognitive Services
description: Neste documento, aprenderá como criar e implementar um ponto de extremidade com o portal de voz personalizada.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: fc51c1d9d47340da85d42f7c398c8ee21c601beb
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025873"
---
# <a name="deploy-a-custom-model"></a>Implementar um modelo personalizado

Depois de ter carregado e inspecioná-lo de dados, avaliada a precisão e preparado um modelo personalizado, pode implementar um ponto final personalizado para utilizar com as suas aplicações, ferramentas e produtos. Neste documento, aprenderá como criar e implementar um ponto de extremidade com o portal de voz personalizada.

## <a name="create-a-custom-endpoint"></a>Criar um ponto final personalizado

Para criar um novo ponto final personalizado, selecione **implementação** no menu de voz personalizada na parte superior da página. Se esta for a primeira execução, observará que existem não existem pontos finais listados na tabela. Depois de criar um ponto de extremidade, usará esta página para controlar cada ponto de extremidade implementado.

Em seguida, selecione **adicionar ponto final** e introduza um **nome** e **Descrição** para seu ponto final personalizado. Em seguida, selecione o modelo personalizado que pretende associar a este ponto final. Nesta página, também pode ativar o registo. Registo permite-lhe monitorizar o tráfego de ponto final. Se desativado, o tráfego não será armazenado.

![Como implementar um modelo](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Não se esqueça de aceitar os termos de utilização e os detalhes dos preços.

Em seguida, selecione **criar**. Esta ação devolve-lhe a **implementação** página. A tabela inclui agora uma entrada que corresponde ao seu ponto final personalizado. Estado do ponto de extremidade mostra o estado atual. Pode demorar até 30 minutos para instanciar um novo ponto final com seus modelos personalizados. Quando o estado da implementação é alterado para **Complete**, o ponto final está pronto para ser utilizada.

Depois do ponto final é implementado, o nome do ponto final é apresentado como um link. Clique na ligação para apresentar informações específicas para o ponto final, como a chave de ponto final, o URL de ponto final e o código de exemplo.

## <a name="view-logging-data"></a>Ver dados de registo

Dados de registo estão disponíveis para download em **Endpoint > detalhes**.

## <a name="next-steps"></a>Passos Seguintes

* Utilizar o seu ponto final personalizado com o [SDK de voz](speech-sdk.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Preparar e testar seus dados](how-to-custom-speech-test-data.md)
* [Inspecione os seus dados](how-to-custom-speech-inspect-data.md)
* [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)
* [Preparar o seu modelo](how-to-custom-speech-train-model.md)
* [Implementar o seu modelo](how-to-custom-speech-deploy-model.md)
