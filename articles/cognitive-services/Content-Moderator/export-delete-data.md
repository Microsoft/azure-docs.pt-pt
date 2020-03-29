---
title: Exportar ou eliminar dados do utilizador - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Tem total controlo sobre os seus dados. Saiba como visualizar, exportar ou eliminar os seus dados no Moderador de Conteúdo.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 81713bf8d424b9f272f6b1bccf3657810160d4cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73744789"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Exportar ou eliminar dados dos utilizadores em Moderador de Conteúdo

O Moderador de Conteúdo recolhe os dados dos utilizadores para operar o serviço, mas os clientes têm total controlo para visualizar, exportar e eliminar os seus dados utilizando a [ferramenta Review](https://contentmoderator.cognitive.microsoft.com/) e as APIs de [moderação e revisão](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Para obter mais informações sobre como exportar e eliminar os dados dos utilizadores no Moderador de Conteúdo, consulte a tabela seguinte.

| Dados | Operação de Exportação | Eliminar operação |
| ---- | ---------------- | ---------------- |
| Informação da Conta (Chaves de Subscrição) | N/D | Elimine utilizando o portal Azure (Assinaturas Azure). Ou utilize o botão **Eliminar Team** na página de definições da Equipa De [Revisão UI.](https://contentmoderator.cognitive.microsoft.com/) |
| Imagens para correspondência personalizada | Ligue para a API de [IDs de imagem Get](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). As imagens são armazenadas num formato de hash proprietário de ida e não há forma de extrair as imagens reais. | Ligue para apagar [todas as imagens API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Ou eliminar o recurso Moderador de Conteúdo utilizando o portal Azure. |
| Termos para correspondência personalizada | Cal the [Get todos os termos API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | Ligue para apagar [todos os termos API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Ou eliminar o recurso Moderador de Conteúdo utilizando o portal Azure. |
| Etiquetas | N/D | Utilize o ícone **Eliminar** disponível para cada etiqueta na página de definições de etiquetas De Revisão UI. Ou utilize o botão **Eliminar Team** na página de definições da Equipa De [Revisão UI.](https://contentmoderator.cognitive.microsoft.com/) |
| Revisões | Ligue para a [API de revisão Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Utilize o botão **Eliminar Team** na página de definições da Equipa De [Revisão UI.](https://contentmoderator.cognitive.microsoft.com/)
| Utilizadores | N/D | Utilize o ícone **Eliminar** disponível para cada utilizador na página de definições da Equipa [De Revisão UI.](https://contentmoderator.cognitive.microsoft.com/) Ou utilize o botão **Eliminar Team** na página de definições da Equipa De [Revisão UI.](https://contentmoderator.cognitive.microsoft.com/) |

