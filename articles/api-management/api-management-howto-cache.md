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
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 76a87d539e19acc30944a6a896cb0e01f431fa37
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073570"
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Adicionar a colocação em cache para melhorar o desempenho na API Management do Azure

É possível configurar as operações da API Management para colocar as respostas em cache. A colocação de respostas em cache pode reduzir significativamente a latência da API, o consumo de largura de banda e a carga do serviço Web para os dados que não são alterados com frequência.

Para obter informações mais detalhadas sobre a colocação em cache, veja [Colocação em cache das políticas de Gestão de API](api-management-caching-policies.md) e [Colocação em cache personalizada na Gestão de API do Azure](api-management-sample-cache-by-key.md).

![políticas de cache](media/api-management-howto-cache/cache-policies.png)

O que irá aprender:

> [!div class="checklist"]
> * adicionar a colocação em cache de resposta para a API
> * verificar a colocação em cache em ação

## <a name="availability"></a>Disponibilidade

> [!NOTE]
> O cache interno não está disponível na camada de **consumo** do gerenciamento de API do Azure. Você pode [usar um cache do Azure externo para Redis](api-management-howto-cache-external.md) em vez disso.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

+ [crie uma instância da Gestão de API do Azure](get-started-create-service-instance.md)
+ [Importe e publique uma API](import-and-publish.md)

## <a name="caching-policies"> </a>adicione as políticas de colocação em cache

Com as políticas de colocação em cache deste exemplo, o primeiro pedido efetuado à operação **GetSpeakers** devolve uma resposta do serviço de back-end. Esta resposta é colocada em cache, codificada pelos cabeçalhos e pelos parâmetros de cadeia de consulta especificados. Para as chamadas subsequentes à operação que tenham parâmetros correspondentes, será devolvida a resposta em cache, até que o intervalo de duração da cache expire.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até à sua instância APIM.
3. Selecione o separador **API**.
4. Clique em **API da Conferência de Demonstração** na lista de API.
5. Selecione **GetSpeakers**.
6. Na parte superior do ecrã, selecione o separador **Design**.
7. Na seção **processamento de entrada** , clique no **</>** ícone.

    ![editor de código](media/api-management-howto-cache/code-editor.png)

8. No elemento **entrada**, adicione a seguinte política:

        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
            <vary-by-header>Accept</vary-by-header>
            <vary-by-header>Accept-Charset</vary-by-header>
            <vary-by-header>Authorization</vary-by-header>
        </cache-lookup>

9. No elemento **saída**, adicione a seguinte política:

        <cache-store caching-mode="cache-on" duration="20" />

    **Duração** especifica o intervalo de expiração das respostas em cache. Neste exemplo, o intervalo restante é de **20** segundos.

> [!TIP]
> Se você estiver usando um cache externo, conforme descrito em [usar um cache externo do Azure para Redis no gerenciamento de API do Azure](api-management-howto-cache-external.md), talvez você queira `caching-type` especificar o atributo das políticas de cache. Consulte [políticas de cache do gerenciamento de API](api-management-caching-policies.md) para obter mais detalhes.

## <a name="test-operation"> </a>Chamar uma operação e testar a colocação em cache
Para ver a colocação em cache em ação, chame a operação a partir do portal do programador.

1. No portal do Azure, navegue para a instância APIM.
2. Selecione o separador **APIs**.
3. Selecione a API à qual adicionou as políticas de colocação em cache.
4. Selecione a operação **GetSpeakers**.
5. Clique no separador **Teste** no menu superior direito.
6. Prima **Enviar**.

## <a name="next-steps"> </a>Passos seguintes
* Para obter mais informações sobre as políticas de colocação em cache, consulte [Políticas de colocação em cache][Caching policies] na [Referência de política da API Management][API Management policy reference].
* Para obter informações sobre a colocação em cache de itens por chave utilizando expressões de política, consulte [Colocação em cache personalizada na API Management do Azure](api-management-sample-cache-by-key.md).
* Para obter mais informações sobre como usar o cache externo do Azure para Redis, consulte [usar um cache do Azure externo para Redis no gerenciamento de API do Azure](api-management-howto-cache-external.md).

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


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Create an API Management service instance]: get-started-create-service-instance.md

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
