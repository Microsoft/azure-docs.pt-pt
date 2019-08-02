---
title: Exportar ou excluir seus dados-Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Saiba como exportar ou excluir seus dados no Serviço de Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: b885f359d9416fbc5f778b094610260342a75f65
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564219"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Exportar ou excluir dados do usuário no Visão Personalizada

O Visão Personalizada coleta dados do usuário para operar o serviço, mas os clientes têm controle total sobre a exibição, a exportação e a exclusão de seus dados usando as [APIs de treinamento](https://go.microsoft.com/fwlink/?linkid=865446)de visão personalizada.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Para saber como exportar e excluir dados do usuário no Visão Personalizada, consulte a tabela a seguir.

| Data | Operação de exportação | Operação de Eliminação |
| ---- | ---------------- | ---------------- |
| Informações da conta (chaves de assinatura) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Excluir usando portal do Azure (assinaturas do Azure). Ou usando o botão "excluir sua conta" na página de configurações do CustomVision.ai (assinaturas de conta da Microsoft) | 
| Detalhes da iteração | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Detalhes de desempenho da iteração | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Lista de iterações | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projetos e detalhes do projeto | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) e [](https://go.microsoft.com/fwlink/?linkid=865446) getprojects | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Marcas de imagem | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) e [gettags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Imagens | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fornece URI para download de imagem) e [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fornece URI para download de imagem) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Modelos exportados | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Excluído após a exclusão da conta |
