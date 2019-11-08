---
title: Exportar ou excluir dados do usuário-Content Moderator
titleSuffix: Azure Cognitive Services
description: Você tem controle total sobre seus dados. Saiba como exibir, exportar ou excluir seus dados no Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 81713bf8d424b9f272f6b1bccf3657810160d4cf
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73744789"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Exportar ou excluir dados do usuário no Content Moderator

O Content Moderator coleta dados do usuário para operar o serviço, mas os clientes têm controle total para exibir, exportar e excluir seus dados usando a [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) e as APIs de [moderação e revisão](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Para obter mais informações sobre como exportar e excluir dados do usuário no Content Moderator, consulte a tabela a seguir.

| Dados | Operação de exportação | Operação de exclusão |
| ---- | ---------------- | ---------------- |
| Informações da conta (chaves de assinatura) | N/D | Exclua usando o portal do Azure (assinaturas do Azure). Ou use o botão **excluir equipe** na página [examinar as configurações da equipe da interface do usuário](https://contentmoderator.cognitive.microsoft.com/) . |
| Imagens para correspondência personalizada | Chame a [API obter IDs de imagem](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). As imagens são armazenadas em um formato de hash proprietário unidirecional e não há como extrair as imagens reais. | Chame a [API excluir todas as imagens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Ou exclua o recurso de Content Moderator usando o portal do Azure. |
| Termos para correspondência personalizada | Cal para a [API obter todos os termos](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | Chame a [API excluir todos os termos](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Ou exclua o recurso de Content Moderator usando o portal do Azure. |
| Etiquetas | N/D | Use o ícone de **exclusão** disponível para cada marca na página examinar configurações de marca de interface do usuário. Ou use o botão **excluir equipe** na página [examinar as configurações da equipe da interface do usuário](https://contentmoderator.cognitive.microsoft.com/) . |
| Revisões | Chamar a [API de obtenção de revisão](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Use o botão **excluir equipe** na página [examinar as configurações da equipe da interface do usuário](https://contentmoderator.cognitive.microsoft.com/) .
| Utilizadores | N/D | Use o ícone de **exclusão** disponível para cada usuário na página examinar as configurações [da equipe da interface do usuário](https://contentmoderator.cognitive.microsoft.com/) . Ou use o botão **excluir equipe** na página [examinar as configurações da equipe da interface do usuário](https://contentmoderator.cognitive.microsoft.com/) . |

