---
title: Importar uma Aplicação API como uma API com o portal do Azure | Microsoft Docs
description: Este tutorial mostra como utilizar a Gestão de API (APIM) para importar uma Aplicação API como uma API.
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
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: 120fe358e5c8afe63e98038ad353d5ebc45a1937
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60255908"
---
# <a name="import-an-api-app-as-an-api"></a>Importar uma Aplicação API como uma API

Este artigo mostra como importar uma Aplicação API como uma API. O artigo também mostra como testar a API APIM.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Importar uma Aplicação API como uma API
> * Testar a API no portal do Azure
> * Testar a API no portal do Programador

## <a name="prerequisites"></a>Pré-requisitos

+ Conclua o início rápido seguinte: [crie uma instância da Gestão de API do Azure](get-started-create-service-instance.md)
+ Certifique-se de que existe uma Aplicação API na sua subscrição. Para obter mais informações, consulte [documentação do serviço de aplicações](https://docs.microsoft.com/azure/app-service/)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importar e publicar uma API de back-end

1. Selecione **APIs** em **GESTÃO DE API**.
2. Selecione **Aplicação API** na lista **Adicionar uma nova API**.

    ![Aplicação de API](./media/import-api-app-as-api/api-app.png)
3. Prima **Procurar** para ver a lista de Aplicações API na sua subscrição.
4. Selecione a aplicação. O APIM localiza o swagger associado à aplicação selecionada, obtém-no e importa-o. 

    Caso o APIM não localize o swagger, expõe a API como uma API de "pass-through". 
5. Adicione um sufixo de URL de API. O sufixo é um nome que identifica esta API específica nesta instância de APIM. Tem de ser exclusivo nesta instância de APIM.
6. Associe a API a um produto e publique-a. Neste caso, é utilizado o produto "*Unlimited*".  Se pretender que a API seja publicada e esteja disponível para programadores, adicione-a a um produto. Pode fazê-lo durante a criação da API ou defini-lo mais tarde.

    Os produtos são associações de uma ou mais APIs. Pode incluir um número de APIs e disponibilizá-las para os programadores através do portal do programador. Os programadores têm de subscrever primeiro um produto para obter acesso à API. Quando subscrevem, recebem uma chave de subscrição que é válida para qualquer API nesse produto. Se tiver criado a instância APIM, já é um administrador, pelo que tem todos os produtos subscritos por predefinição.

    Por predefinição, cada instância daAPI Management é fornecida com dois produtos de exemplo:

    * **Inicial**
    * **Ilimitado**   
7. Selecione **Criar**.

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Testar a nova API de APIM no Portal do Azure

As operações podem ser chamadas diretamente a partir do Portal do Azure, que fornece um meio cómodo para ver e testar as operações de uma API.  

1. Selecione a API que criou no passo anterior.
2. Prima o separador **Teste**.
3. Selecione uma operação.

    A página apresenta campos para os parâmetros de consulta e campos para os cabeçalhos. Um dos cabeçalhos é “Ocp-Apim-Subscription-Key”, para a chave de subscrição do produto que está associado a esta API. Se tiver criado a instância de APIM, já é um administrador, pelo que a chave é preenchida automaticamente. 
1. Prima **Enviar**.

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

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Transformar e proteger a sua API](transform-api.md)
