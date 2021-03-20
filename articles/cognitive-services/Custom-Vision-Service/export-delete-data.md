---
title: Ver ou apagar os seus dados - Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Mantém o controlo total sobre os seus dados. Este artigo explica como pode ver, exportar ou apagar os seus dados no Serviço de Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: fe17fa4349085be47201974b418493fe8d912ece
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90527399"
---
# <a name="view-or-delete-user-data-in-custom-vision"></a>Ver ou apagar dados do utilizador em Visão Personalizada

A Custom Vision recolhe dados do utilizador para operar o serviço, mas os clientes têm total controlo sobre a visualização e eliminação dos seus dados utilizando as APIs de [Formação](https://go.microsoft.com/fwlink/?linkid=865446)de Visão Personalizada .

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Para saber como visualizar e eliminar dados do utilizador em Visão Personalizada, consulte a tabela seguinte.

| Dados | Ver operação | Eliminar operação |
| ---- | ---------------- | ---------------- |
| Informações de conta (Chaves de Subscrição) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Eliminar utilizando o portal Azure (Azure Subscriptions). Ou utilizando o botão "Eliminar a Sua Conta" na página CustomVision.ai definições (Subscrições de conta do Microsoft) | 
| Detalhes da iteração | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Detalhes do desempenho da iteração | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Lista de iterações | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projetos e detalhes do projeto | [Obter Projetos](https://go.microsoft.com/fwlink/?linkid=865446) e [Obter](https://go.microsoft.com/fwlink/?linkid=865446) | [ExcluirProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Tags de imagens | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) e [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Imagens | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fornece uri para download de imagem) e [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fornece uri para download de imagem) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Iterações exportadas | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Suprimido após a eliminação da conta |
