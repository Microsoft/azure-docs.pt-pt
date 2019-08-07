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
ms.devlang: na
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: apimpm
ms.openlocfilehash: db341a2075238ccef214ea151d2b2d2860eb6f3a
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68837945"
---
# <a name="import-a-logic-app-as-an-api"></a>Importar uma Aplicação Lógica como uma API

Este artigo mostra como importar um aplicativo lógico como uma API e testar a API importada.

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> -   Importar uma Aplicação Lógica como uma API
> -   Testar a API no portal do Azure
> -   Testar a API no portal do Programador

## <a name="prerequisites"></a>Pré-requisitos

-   Conclua o início rápido a seguir: [crie uma instância da Gestão de API do Azure](get-started-create-service-instance.md)
-   Verifique se há um aplicativo lógico em sua assinatura que expõe um ponto de extremidade HTTP. Para obter mais informações, [dispare fluxos de trabalho com pontos de extremidade http](../logic-apps/logic-apps-http-endpoint.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importar e publicar uma API de back-end

1. Selecione **APIs** em **GESTÃO DE API**.
2. Selecione **Aplicação Lógica** na lista **Adicionar uma nova API**.

    ![Aplicação lógica](./media/import-logic-app-as-api/logic-app-api.png)

3. Pressione **procurar** para ver a lista de aplicativos lógicos com gatilho http em sua assinatura. (Observe que os aplicativos lógicos sem gatilho HTTP não serão exibidos na lista.)
4. Selecione a aplicação. O gerenciamento de API localiza o Swagger associado ao aplicativo selecionado, o busca e o importa.
5. Adicione um sufixo de URL de API. O sufixo é um nome que identifica esta API específica nesta instância de Gestão de API. Ele deve ser exclusivo nesta instância de gerenciamento de API.
6. Associe a API a um produto e publique-a. Neste caso, é utilizado o produto "_Unlimited_". Se pretender que a API seja publicada e esteja disponível para programadores, adicione-a a um produto. Pode fazê-lo durante a criação da API ou defini-lo mais tarde.

    Os produtos são associações de uma ou mais APIs. Pode incluir um número de APIs e disponibilizá-las para os programadores através do portal do programador. Os programadores têm de subscrever primeiro um produto para obter acesso à API. Quando subscrevem, recebem uma chave de subscrição que é válida para qualquer API nesse produto. Se você criou a instância de gerenciamento de API, você já é um administrador, portanto, você está inscrito em cada produto por padrão.

    Por predefinição, cada instância daAPI Management é fornecida com dois produtos de exemplo:

    - **Inicial**
    - **Ilimitado**

7. Selecione **Criar**.

## <a name="test-the-imported-api-in-the-azure-portal"></a>Testar a API importada no portal do Azure

As operações podem ser chamadas diretamente a partir do Portal do Azure, que fornece um meio cómodo para ver e testar as operações de uma API.

1. Selecione a API que criou no passo anterior.
2. Prima o separador **Teste**.
3. Selecione uma operação.

    A página apresenta campos para os parâmetros de consulta e campos para os cabeçalhos. Um dos cabeçalhos é “Ocp-Apim-Subscription-Key”, para a chave de subscrição do produto que está associado a esta API. Se tiver criado a instância de Gestão de API, já é um administrador, pelo que a chave é preenchida automaticamente.

4. Prima **Enviar**.

    O back-end responde com **200 OK** e alguns dados.

## <a name="call-operation"> </a>Chamar uma operação a partir do portal do programador

Também é possível chamar operações a partir do **portal do Programador** para testar as APIs.

1. Selecione a API que criou no passo “Importar e publicar uma API de back-end”.
2. Prima **Portal do Programador**.

    O site "portal do Programador" abre-se.

3. Selecione a **API** que criou.
4. Clique na operação que pretende testar.
5. Prima **Experimente**.
6. Prima **Enviar**.

    Depois de uma operação ser invocada, o portal do programador apresenta o **Estado da resposta**, os **Cabeçalhos da resposta** e qualquer **Conteúdo da resposta**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

>[!NOTE]
>Cada Aplicação Lógica tem a operação **manual-invoke**. Se pretender que a API seja composta por várias aplicações lógicas, para não ter colisão, terá de mudar o nome da função.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
>
> [Transformar e proteger uma API publicada](transform-api.md)
