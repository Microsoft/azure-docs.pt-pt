---
title: Editar uma API com o portal do Azure | Microsoft Docs
description: Este tutorial mostra como utilizar a Gestão de API (APIM) para editar uma API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/08/2017
ms.author: apimpm
ms.openlocfilehash: 6f1a0cf6025cb3a398ab93320c6fcb69b1e62429
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60558094"
---
# <a name="edit-an-api"></a>Editar uma API

Os passos deste tutorial mostram como utilizar a Gestão de API (APIM) para editar uma API. 

+ Pode fazê-lo ao adicionar, eliminar e mudar o nome de operações na instância de APIM. 
+ Pode editar o swagger da sua API.

## <a name="prerequisites"></a>Pré-requisitos

+ [crie uma instância da Gestão de API do Azure](get-started-create-service-instance.md)
+ [Importar e publicar a sua primeira API](import-and-publish.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="edit-an-api-in-apim"></a>Editar uma API no APIM

![Editar uma API](./media/edit-api/edit-api001.png)

1. Clique no separador **APIs**.
2. Selecione uma das APIs que importou anteriormente.
3. Selecione o separador **Design**.
4. Selecione a operação que pretende editar.
5. Para mudar o nome da operação, selecione um **lápis** na janela **Front-end**.

## <a name="update-the-swagger"></a>Atualizar o swagger

Pode atualizar a API do seu back-end no portal do Azure, seguindo estes passos:

1. Selecione **Todas as operações**.
2. Clique no lápis na janela **Front-end**.

    ![Editar uma API](./media/edit-api/edit-api002.png)

    É apresentado o swagger da sua API.

    ![Editar uma API](./media/edit-api/edit-api003.png)

3. Atualize o swagger.
4. Prima **Guardar**.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Exemplos de política de APIM](policy-samples.md)
> [Transformar e proteger uma API publicada](transform-api.md)