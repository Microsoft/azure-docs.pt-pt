---
title: Ligar a outros serviços ao moderadores conteúdo - Content Moderator
titlesuffix: Azure Cognitive Services
description: Saiba como aceder a outros APIs para os fluxos de trabalho do Content Moderator utilizando conectores.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 3ee582e2541e4eb55e5ea1424782df132ecf3575
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58116559"
---
# <a name="connect-to-other-cognitive-services"></a>Ligar a outros serviços cognitivos

Azure Content Moderator os fluxos de trabalho podem utilizar outras APIs, para além das APIs de moderador do conteúdo. Aceder a outros APIs utilizando um conector no Content Moderator. O conector fornece um link para as outras APIs.

O Content Moderator inclui estes conectores padrão:

* API de Emoções
* API Face
* Serviço em nuvem de PhotoDNA

![Conectores disponíveis do moderador de conteúdo](images/connectors-1.png)

## <a name="verify-your-credentials"></a>Verifique as suas credenciais 

Antes de definir um fluxo de trabalho, certifique-se de que tem as credenciais válidas para o conector de API que pretende utilizar:

1. A ferramenta de revisão de Dashboard, selecione **configurações** > **conectores**.

   ![Conteúdo conectores selecionadas do moderador](images/connectors-2.png)

2. Selecione o **editar** símbolo junto o conector que pretende verificar as credenciais para.

   ![O Content Moderator, selecione o símbolo de edição](images/connectors-3.png)

3. É apresentada a chave de subscrição. Se efetue as edições, selecione **guardar** quando tiver terminado.

   ![Página de conectores de editar moderador de conteúdo](images/connectors-4-1.png)
 
## <a name="add-a-connector"></a>Adicionar um conector

1. Antes de adicionar um conector, precisa de uma chave de subscrição. A ferramenta de revisão de Dashboard, selecione **configurações** > **credenciais**. Selecione e copie o valor que está a **Ocp-Admin-Subscription-Key** caixa.

2. Selecione **conectores**. Selecione um dos conectores disponíveis que são apresentados na ferramenta de revisão Dashboard. Em seguida, selecione **Connect**. 

   ![Página de conector de adicionar moderador de conteúdo](images/connectors-5.png)

3. Na **Ocp-Admin-Subscription-Key** caixa, cole a chave que copiou. Em seguida, selecione **Guardar**.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como utilizar conectores para [definir fluxos de trabalho personalizados](workflows.md).
