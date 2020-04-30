---
title: Importar uma Aplicação Lógica como uma API com o portal do Azure | Microsoft Docs
description: Este tutorial mostra como utilizar a Gestão de API (APIM) para importar uma Aplicação Lógica como uma API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: b6cb0fa3611da44726dabd879c37fec357fc3031
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82202779"
---
# <a name="import-a-logic-app-as-an-api"></a>Importar uma Aplicação Lógica como uma API

Este artigo mostra como importar uma App Lógica como API e testar a API importada.

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> -   Importar uma Aplicação Lógica como uma API
> -   Testar a API no portal do Azure
> -   Testar a API no portal do Programador

## <a name="prerequisites"></a>Pré-requisitos

-   Complete o seguinte quickstart: Criar uma instância de [Gestão API Azure](get-started-create-service-instance.md)
-   Certifique-se de que existe uma Aplicação Lógica na sua subscrição que expõe um ponto final http. Para mais informações, [Trigger fluxos de trabalho com pontos finais HTTP](../logic-apps/logic-apps-http-endpoint.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importar e publicar uma API de back-end

1. Navegue para o seu serviço de Gestão API no portal Azure e selecione **APIs** do menu.
2. Selecione **Aplicação Lógica** na lista **Adicionar uma nova API**.

    ![Aplicação lógica](./media/import-logic-app-as-api/logic-app-api.png)

3. Pressione **O Venopara** ver a lista de Aplicações Lógicas com o gatilho HTTP na sua subscrição. (Note que as Aplicações Lógicas sem gatilho HTTP não aparecerão na lista.)
4. Selecione a aplicação. A API Management encontra a arrogância associada à app selecionada, vai buscá-la e importa-a.
5. Adicione um sufixo de URL de API. O sufixo é um nome que identifica esta API específica nesta instância de Gestão de API. Tem de ser único neste caso de Gestão API.
6. Publique a API ao associá-la a um produto. Neste caso, é utilizado o produto "_Unlimited_". Se pretender que a API seja publicada e esteja disponível para programadores, adicione-a a um produto. Pode fazê-lo durante a criação da API ou defini-lo mais tarde.

    Os produtos são associações de uma ou mais APIs. Pode incluir um número de APIs e disponibilizá-las para os programadores através do portal do programador. Os programadores têm de subscrever primeiro um produto para obter acesso à API. Quando subscrevem, recebem uma chave de subscrição que é válida para qualquer API nesse produto. Se criou a instância de Gestão API, já é administrador, pelo que é subscrito a cada produto por defeito.

    Por predefinição, cada instância daAPI Management é fornecida com dois produtos de exemplo:

    - **Inicial**
    - **Ilimitado**

7. Introduza outras definições de API. Pode definir os valores durante a criação ou configurá-los mais tarde, indo ao separador **Definições.** As definições são explicadas no Importar e publicar o seu primeiro tutorial [API.](import-and-publish.md#-import-and-publish-a-backend-api)
8. Selecione **Criar**.

## <a name="test-the-api-in-the-azure-portal"></a>Testar a API no portal do Azure

As operações podem ser chamadas diretamente a partir do Portal do Azure, que fornece um meio cómodo para ver e testar as operações de uma API.

1. Selecione a API que criou no passo anterior.
2. Prima o separador **Teste**.
3. Selecione uma operação.

    A página apresenta campos para os parâmetros de consulta e campos para os cabeçalhos. Um dos cabeçalhos é “Ocp-Apim-Subscription-Key”, para a chave de subscrição do produto que está associado a esta API. Se tiver criado a instância de Gestão de API, já é um administrador, pelo que a chave é preenchida automaticamente.

4. Prima **Enviar**.

    O back-end responde com **200 OK** e alguns dados.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

>[!NOTE]
>Cada Aplicação Lógica tem a operação **manual-invoke**. Se pretender que a API seja composta por várias aplicações lógicas, para não ter colisão, terá de mudar o nome da função.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>
> [Transforme e proteja um api publicado](transform-api.md)
