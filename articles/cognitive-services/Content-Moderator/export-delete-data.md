---
title: Exportar ou eliminar dados de utilizador - Content Moderator
titlesuffix: Azure Cognitive Services
description: Saiba como exportar ou eliminar os seus dados no Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 1cd70bee22e56e2580b322b93e0f121261d97a94
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60699194"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Exportar ou eliminar dados de utilizador no Content Moderator

Conteúdo moderador recolhe dados de utilizador para operar o serviço, mas os clientes têm controlo total para exibir, exportar e eliminar os seus dados com o [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) e o [moderação e APIs de revisão](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Para obter mais informações sobre como exportar e eliminar dados de utilizador no Content Moderator, consulte a tabela seguinte.

| Dados | Operação de exportação | Operação de eliminação |
| ---- | ---------------- | ---------------- |
| Informações da conta (chaves de subscrição) | N/A | Eliminação com o portal do Azure (subscrições do Azure). Ou utilize o **eliminar equipe** botão no [interface do Usuário de revisão](https://contentmoderator.cognitive.microsoft.com/) página de configurações de equipe. |
| Imagens para correspondência | Chamar o [API de IDs de imagem de Get](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). As imagens são armazenadas num formato de hash unidirecional de proprietário e não é possível extrair as imagens reais. | Chamar o [eliminar todas as APIS de imagens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Ou eliminar o recurso do Content Moderator no portal do Azure. |
| Termos de correspondência | CAL de [obter todos os termos de API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | Chamar o [eliminar todos os termos de API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Ou eliminar o recurso do Content Moderator no portal do Azure. |
| Tags | N/A | Utilize o **eliminar** disponível para cada marca na página de definições de revisão da interface do Usuário marca do ícone. Ou utilize o **eliminar equipe** botão no [interface do Usuário de revisão](https://contentmoderator.cognitive.microsoft.com/) página de configurações de equipe. |
| Revisões | Chamar o [Get revisão de API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Utilize o **eliminar equipe** botão no [interface do Usuário de revisão](https://contentmoderator.cognitive.microsoft.com/) página de configurações de equipe.
| Utilizadores | N/A | Utilize o **eliminar** ícone disponível para todos os utilizadores a [interface do Usuário de revisão](https://contentmoderator.cognitive.microsoft.com/) página de configurações de equipe. Ou utilize o **eliminar equipe** botão no [interface do Usuário de revisão](https://contentmoderator.cognitive.microsoft.com/) página de configurações de equipe. |

