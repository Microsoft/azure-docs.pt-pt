---
title: Importar uma especificação de OpenAPI com o portal do Azure | Microsoft Docs
description: Saiba como importar uma especificação de OpenAPI com a Gestão de API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 2e4dee74eb0c50e8e12d3f9ff0dccdd83271ea65
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82202927"
---
# <a name="import-an-openapi-specification"></a>Importar uma especificação de OpenAPI

Este artigo mostra como importar uma API de back-end de “especificação de OpenAPI” que reside em https://conferenceapi.azurewebsites.net?format=json. Esta API de back-end é fornecida pela Microsoft e alojada no Azure. O artigo também mostra como testar a API APIM.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Importar uma API de back-end de “especificação de OpenAPI”
> * Testar a API no portal do Azure
> * Testar a API no portal do Programador

## <a name="prerequisites"></a>Pré-requisitos

Complete o seguinte quickstart: Criar uma instância de [Gestão API Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importar e publicar uma API de back-end

1. Navegue para o seu serviço de Gestão API no portal Azure e selecione **APIs** do menu.
2. Selecione **Especificação de OpenAPI** na lista **Adicionar uma API nova**.

    ![Especificação OpenAPI](./media/import-api-from-oas/oas-api.png)
3. Introduza as definições da API. Pode definir os valores durante a criação ou configurá-los mais tarde, indo ao separador **Definições.** As definições são explicadas no Importar e publicar o seu primeiro tutorial [API.](import-and-publish.md#-import-and-publish-a-backend-api)
4. Selecione **Criar**.

> [!NOTE]
> As limitações de importação da API estão documentadas num [outro artigo.](api-management-api-import-restrictions.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Transformar e proteger a sua API](transform-api.md)
