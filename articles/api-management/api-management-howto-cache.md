---
title: Adicionar a colocação em cache para melhorar o desempenho na Gestão de API do Azure | Microsoft Docs
description: Saiba como melhorar a latência, o consumo de largura de banda e a carga do serviço Web para chamadas de serviço da API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: apimpm
ms.openlocfilehash: 732abed830afdb759ed52fd933673edd8e5cade6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94638738"
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Adicionar a colocação em cache para melhorar o desempenho na API Management do Azure

As APIs e as operações na Gestão da API podem ser configuradas com o caching de resposta. O caching de resposta pode reduzir significativamente a latência para os chamadores de API e a carga de backend para os fornecedores de API.

> [!IMPORTANT]
> A cache incorporada é volátil e é partilhada por todas as unidades da mesma região no mesmo serviço de Gestão API.

Para obter informações mais detalhadas sobre a colocação em cache, veja [Colocação em cache das políticas de Gestão de API](api-management-caching-policies.md) e [Colocação em cache personalizada na Gestão de API do Azure](api-management-sample-cache-by-key.md).

![políticas de cache](media/api-management-howto-cache/cache-policies.png)

O que irá aprender:

> [!div class="checklist"]
> * adicionar a colocação em cache de resposta para a API
> * verificar a colocação em cache em ação

## <a name="availability"></a>Disponibilidade

> [!NOTE]
> A cache interna não está disponível no nível de **Consumo** da Azure API Management. Em vez disso, pode [utilizar um Cache Azure externo para redis.](api-management-howto-cache-external.md)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

+ [crie uma instância da Gestão de API do Azure](get-started-create-service-instance.md)
+ [Importar e publicar uma API](import-and-publish.md)

## <a name="add-the-caching-policies"></a><a name="caching-policies"> </a>adicione as políticas de colocação em cache

Com as políticas de colocação em cache deste exemplo, o primeiro pedido efetuado à operação **GetSpeakers** devolve uma resposta do serviço de back-end. Esta resposta é colocada em cache, codificada pelos cabeçalhos e pelos parâmetros de cadeia de consulta especificados. Para as chamadas subsequentes à operação que tenham parâmetros correspondentes, será devolvida a resposta em cache, até que o intervalo de duração da cache expire.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até à sua instância APIM.
3. Selecione o separador **API**.
4. Clique em **API da Conferência de Demonstração** na lista de API.
5. Selecione **GetSpeakers**.
6. Na parte superior do ecrã, selecione o separador **Design**.
7. Na secção **Processamento de entrada**, clique no ícone **</>**.

    ![editor de código](media/api-management-howto-cache/code-editor.png)

8. No elemento **entrada**, adicione a seguinte política:

   ```
   <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
       <vary-by-header>Accept</vary-by-header>
       <vary-by-header>Accept-Charset</vary-by-header>
       <vary-by-header>Authorization</vary-by-header>
   </cache-lookup>
   ```

9. No elemento **saída**, adicione a seguinte política:

   ```
   <cache-store duration="20" />
   ```

    **Duração** especifica o intervalo de expiração das respostas em cache. Neste exemplo, o intervalo restante é de **20** segundos.

> [!TIP]
> Se estiver a utilizar uma cache externa, conforme descrito na [Utilização de uma Cache Azure externa para Redis na Gestão API Azure,](api-management-howto-cache-external.md)pode querer especificar o `caching-type` atributo das políticas de cache. Consulte [as políticas de caching da API Management](api-management-caching-policies.md) para obter mais detalhes.

## <a name="call-an-operation-and-test-the-caching"></a><a name="test-operation"> </a>Chamar uma operação e testar a colocação em cache
Para ver a colocação em cache em ação, chame a operação a partir do portal do programador.

1. No portal do Azure, navegue para a instância APIM.
2. Selecione o **separador APIs.**
3. Selecione a API à qual adicionou as políticas de colocação em cache.
4. Selecione a operação **GetSpeakers**.
5. Clique no separador **Teste** no menu superior direito.
6. Prima **Enviar**.

## <a name="next-steps"></a><a name="next-steps"> </a>Passos seguintes
* Para obter mais informações sobre as políticas de colocação em cache, consulte [Políticas de colocação em cache][Caching policies] na [Referência de política da API Management][API Management policy reference].
* Para obter informações sobre a colocação em cache de itens por chave utilizando expressões de política, consulte [Colocação em cache personalizada na API Management do Azure](api-management-sample-cache-by-key.md).
* Para obter mais informações sobre a utilização de Cache Azure externo para redis, consulte [utilize uma cache Azure externa para Redis na Azure API Management](api-management-howto-cache-external.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md

[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-caching-policies.md

[Create an API Management service instance]: get-started-create-service-instance.md

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
