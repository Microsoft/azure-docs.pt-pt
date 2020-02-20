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
ms.date: 07/12/2019
ms.author: apimpm
ms.openlocfilehash: 57803ec9889cb6a19dae6d6d1070d8381577aff0
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77468393"
---
# <a name="import-an-openapi-specification"></a>Importar uma especificação de OpenAPI

Este artigo mostra como importar uma API de back-end de “especificação de OpenAPI” que reside em https://conferenceapi.azurewebsites.net?format=json. Esta API de back-end é fornecida pela Microsoft e alojada no Azure. O artigo também mostra como testar a API APIM.

> [!IMPORTANT]
> Veja este [documento](https://azure.microsoft.com/blog/announcing-the-preview-of-openapi-specification-v3-support-in-azure-api-management/) para obter informações importantes e sugestões relacionadas com a importação de OpenAPI.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Importar uma API de back-end de “especificação de OpenAPI”
> * Testar a API no Portal do Azure
> * Testar a API no portal do Programador

## <a name="prerequisites"></a>Pré-requisitos

Concluir o início rápido seguinte: [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importar e publicar uma API de back-end

1. Selecione **APIs** em **GESTÃO DE API**.
2. Selecione **Especificação de OpenAPI** na lista **Adicionar uma API nova**.

    ![Especificação OpenAPI](./media/import-api-from-oas/oas-api.png)
3. Introduza as definições apropriadas. Pode definir todos os valores da API durante a criação. Em alternativa, pode definir alguns mais tarde ao aceder ao separador **Definições**. <br/> Se premir **tab**, alguns ou todos os campos são preenchidos com as informações do serviço de back-end especificado.

    ![Criar uma API](./media/api-management-get-started/create-api.png)

    |Definição|Valor|Descrição|
    |---|---|---|
    |**Especificação de OpenAPI**|https://conferenceapi.azurewebsites.net?format=json|Referencia o serviço que implementa a API. A API de Gestão reencaminha os pedidos para este endereço.|
    |**Nome a apresentar**|*API da Conferência de Demonstração*|Se premir o separador depois de introduzir o URL do serviço, a APIM irá preencher este campo com base no que está no json. <br/>Este nome é apresentado no portal do Programador.|
    |**Nome**|*demo-conference-api*|Fornece um nome exclusivo para a API. <br/>Se premir o separador depois de introduzir o URL do serviço, a APIM irá preencher este campo com base no que está no json.|
    |**Descrição**|Forneça uma descrição opcional da API.|Se premir o separador depois de introduzir o URL do serviço, a APIM irá preencher este campo com base no que está no json.|
    |**Sufixo do URL da API**|*conferência*|O sufixo é anexado ao URL base do serviço Gestão de API. A Gestão de API distingue as APIs pelo respetivo sufixo, pelo que cada API tem de ter o seu sufixo exclusivo para um determinado editor.|
    |**Esquema do URL**|*HTTPS*|Determina que protocolos podem ser utilizados para aceder à API. |
    |**Produtos**|*Ilimitado*| Publique a API ao associá-la a um produto. Para adicionar opcionalmente esta nova API a um produto, escreva o nome do produto. Este passo pode ser repetido múltiplas vezes para adicionar a API a vários produtos.<br/>Os produtos são associações de uma ou mais APIs. Pode incluir um número de APIs e disponibilizá-las para os programadores através do portal do programador. Os programadores têm de subscrever primeiro um produto para obter acesso à API. Quando subscrevem, recebem uma chave de subscrição que é válida para qualquer API nesse produto. Se tiver criado a instância APIM, já é um administrador, pelo que tem todos os produtos subscritos por predefinição.<br/> Por predefinição, cada instância da Gestão de API é fornecida com dois produtos de exemplo: **Inicial** e **Ilimitado**. |

4. Selecione **Criar**.

> [!NOTE]
> As limitações de importação da API estão documentadas num [outro artigo.](api-management-api-import-restrictions.md)

## <a name="test-the-new-api-in-the-azure-portal"></a>Teste a nova API no portal Azure

![Testar o mapa da API](./media/api-management-get-started/01-import-first-api-01.png)

As operações podem ser chamadas diretamente a partir do Portal do Azure, que fornece um meio cómodo para ver e testar as operações de uma API.

1. Selecione a API que criou no passo anterior (do separador **APIs**).
2. Prima o separador **Teste**.
3. Clique em **GetSpeakers**. A página apresenta os campos para os parâmetros de consulta; contudo, neste caso, não temos nenhum, e cabeçalhos. Um dos cabeçalhos é “Ocp-Apim-Subscription-Key”, para a chave de subscrição do produto que está associado a esta API. A chave é preenchida automaticamente.
4. Prima **Enviar**.

    O back-end responde com **200 OK** e alguns dados.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Transformar e proteger a sua API](transform-api.md)
