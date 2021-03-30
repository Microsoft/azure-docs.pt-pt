---
title: Editar uma API com o portal do Azure | Microsoft Docs
description: Saiba como utilizar a API Management (APIM) para editar uma API. Adicione, elimine ou rebatize as operações no caso APIM, ou edite a arrogância da API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/08/2017
ms.author: apimpm
ms.openlocfilehash: 1c4e64251390936e8a63ee904ec69f173cac6114
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146710"
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

![Screenshot que destaca o processo de edição de uma API em APIM.](./media/edit-api/edit-api001.png)

1. Clique no separador **APIs**.
2. Selecione uma das APIs que importou anteriormente.
3. Selecione o separador **Design**.
4. Selecione a operação que pretende editar.
5. Para mudar o nome da operação, selecione um **lápis** na janela **Front-end**.

## <a name="update-the-swagger"></a>Atualizar o swagger

Pode atualizar a API do seu back-end no portal do Azure, seguindo estes passos:

1. Selecione **Todas as operações**.
2. Clique no lápis na janela **Front-end**.

    ![Screenshot que destaca o ícone do lápis no ecrã Frontend.](./media/edit-api/edit-api002.png)

    É apresentado o swagger da sua API.

    ![Editar uma API](./media/edit-api/edit-api003.png)

3. Atualize o swagger.
4. Prima **Guardar**.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> Amostras de [política da APIM](./policy-reference.md) 
>  [Transformar e proteger uma API publicada](transform-api.md)