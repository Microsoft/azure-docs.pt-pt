---
title: Exportar ou apagar dados do utilizador - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Tem controlo total sobre os seus dados. Saiba como ver, exportar ou eliminar os seus dados no Moderador de Conteúdo.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 81713bf8d424b9f272f6b1bccf3657810160d4cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "73744789"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Exportar ou eliminar dados dos utilizadores no Moderador de Conteúdo

O Content Moderador recolhe dados dos utilizadores para operar o serviço, mas os clientes têm total controlo para visualizar, exportar e eliminar os seus dados utilizando a [ferramenta Review](https://contentmoderator.cognitive.microsoft.com/) e as [APIs de Moderação e Revisão.](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference)

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Para obter mais informações sobre como exportar e eliminar dados do utilizador no Moderador de Conteúdo, consulte a tabela seguinte.

| Dados | Operação de Exportação | Eliminar operação |
| ---- | ---------------- | ---------------- |
| Informações de conta (Chaves de Subscrição) | N/D | Eliminar utilizando o portal Azure (Azure Subscriptions). Ou utilize o botão **Eliminar Team** na página de definições de [Equipa UI de revisão.](https://contentmoderator.cognitive.microsoft.com/) |
| Imagens para correspondência personalizada | Ligue para a [API de IDs de imagem Get](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). As imagens são armazenadas num formato de haxixe unidireccionário, e não há como extrair as imagens reais. | Ligue para [eliminar todas as imagens API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Ou elimine o recurso De Moderador de Conteúdo utilizando o portal Azure. |
| Termos para correspondência personalizada | Cal the [Get todos os termos API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | Ligue [para eliminar todos os termos API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Ou elimine o recurso De Moderador de Conteúdo utilizando o portal Azure. |
| Etiquetas | N/D | Utilize o ícone **Eliminar** disponível para cada tag na página 'Ver definições', 'Ver's'. Ou utilize o botão **Eliminar Team** na página de definições de [Equipa UI de revisão.](https://contentmoderator.cognitive.microsoft.com/) |
| Revisões | Ligue para a [API de revisão de revisão](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Utilize o botão **Eliminar Team** na página 'Rever as definições da Equipa [UI'.](https://contentmoderator.cognitive.microsoft.com/)
| Utilizadores | N/D | Utilize o ícone **Eliminar** disponível para cada utilizador na página de definições [do ''SY's'](https://contentmoderator.cognitive.microsoft.com/) 's'. Ou utilize o botão **Eliminar Team** na página de definições de [Equipa UI de revisão.](https://contentmoderator.cognitive.microsoft.com/) |

