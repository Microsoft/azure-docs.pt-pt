---
title: Exportar ou excluir seus dados-Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Você mantém controle total sobre seus dados. Este artigo explica como você pode exibir, exportar ou excluir seus dados no Serviço de Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 82d9f4508db376ebbe69ef772c15fb732391a31d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718963"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Exportar ou excluir dados do usuário no Visão Personalizada

O Visão Personalizada coleta dados do usuário para operar o serviço, mas os clientes têm controle total sobre a exibição, a exportação e a exclusão de seus dados usando as [APIs de treinamento](https://go.microsoft.com/fwlink/?linkid=865446)de visão personalizada.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Para saber como exportar e excluir dados do usuário no Visão Personalizada, consulte a tabela a seguir.

| Dados | Operação de exportação | Operação de exclusão |
| ---- | ---------------- | ---------------- |
| Informações da conta (chaves de assinatura) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Excluir usando portal do Azure (assinaturas do Azure). Ou usando o botão "excluir sua conta" na página de configurações do CustomVision.ai (assinaturas de conta da Microsoft) | 
| Detalhes da iteração | [Getiteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Detalhes de desempenho da iteração | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Lista de iterações | [Getiterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projetos e detalhes do projeto | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) e [getprojects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Marcas de imagem | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) e [gettags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Imagens | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fornece o URI para download de imagem) e [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fornece o URI para download de imagem) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Modelos exportados | [Exports](https://go.microsoft.com/fwlink/?linkid=865446) | Excluído após a exclusão da conta |
