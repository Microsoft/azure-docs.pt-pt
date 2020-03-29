---
title: Exportar ou eliminar os seus dados - Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Mantém o controlo total sobre os seus dados. Este artigo explica como pode ver, exportar ou eliminar os seus dados no Serviço de Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 82d9f4508db376ebbe69ef772c15fb732391a31d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718963"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Exportar ou excluir dados do utilizador na Visão Personalizada

A Custom Vision recolhe dados do utilizador para operar o serviço, mas os clientes têm total controlo sobre visualização, exportação e aparas os seus dados através das [APIs](https://go.microsoft.com/fwlink/?linkid=865446)de Treino de Visão Personalizada .

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Para aprender a exportar e eliminar os dados dos utilizadores na Visão Personalizada, consulte a tabela seguinte.

| Dados | Operação de Exportação | Eliminar operação |
| ---- | ---------------- | ---------------- |
| Informação da Conta (Chaves de Subscrição) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Eliminar utilizando o portal Azure (Assinaturas Azure). Ou utilizar o botão "Eliminar a sua conta" na página de definições CustomVision.ai (Subscrições da Conta Microsoft) | 
| Detalhes da iteração | [GetItitit](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteItação](https://go.microsoft.com/fwlink/?linkid=865446) |
| Detalhes do desempenho da iteração | [GetItEraPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteItação](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Lista de iterações | [GetItas](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteItação](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projetos e detalhes do projeto | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) e [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Etiquetas de imagem | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) e [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Imagens | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fornece uri para download de imagem) e [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fornece uri para download de imagem) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Modelos exportados | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Eliminado após a eliminação da conta |
