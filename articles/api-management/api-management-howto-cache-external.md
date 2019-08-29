---
title: Usar um cache externo no gerenciamento de API do Azure | Microsoft Docs
description: Saiba como configurar e usar um cache externo no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: apimpm
ms.openlocfilehash: 2e8863eed774884a99de8643c9e497378368d166
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072492"
---
# <a name="use-an-external-azure-cache-for-redis-in-azure-api-management"></a>Usar um cache do Azure externo para Redis no gerenciamento de API do Azure

Além de utilizar o cache interno, o gerenciamento de API do Azure também permite armazenar em cache as respostas em um cache externo do Azure para Redis.

O uso de um cache externo permite superar algumas limitações do cache interno. É especialmente benéfico se você quiser:

* Evitar que o cache seja limpo periodicamente durante atualizações de gerenciamento de API
* Ter mais controle sobre sua configuração de cache
* Armazenar em cache mais dados do que a camada de gerenciamento de API permite
* Usar o Caching com a camada de consumo do gerenciamento de API

Para obter informações mais detalhadas sobre a colocação em cache, veja [Colocação em cache das políticas de Gestão de API](api-management-caching-policies.md) e [Colocação em cache personalizada na Gestão de API do Azure](api-management-sample-cache-by-key.md).

![Traga seu próprio cache para APIM](media/api-management-howto-cache-external/overview.png)

O que irá aprender:

> [!div class="checklist"]
> * Adicionar um cache externo no gerenciamento de API

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

+ [crie uma instância da Gestão de API do Azure](get-started-create-service-instance.md)
+ Entender o [cache no gerenciamento de API do Azure](api-management-howto-cache.md)

## <a name="create-cache"></a> Criar cache do Azure para Redis

Esta seção explica como criar um cache do Azure para Redis no Azure. Se você já tiver um cache do Azure para Redis, dentro ou fora do Azure, poderá <a href="#add-external-cache">pular</a> para a próxima seção.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="add-external-cache"> </a>Adicionar um cache externo

Siga as etapas abaixo para adicionar um cache do Azure externo para Redis no gerenciamento de API do Azure.

![Traga seu próprio cache para APIM](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> A configuração **use from** especifica qual implantação regional de gerenciamento de API se comunicará com o cache configurado no caso de uma configuração de várias regiões do gerenciamento de API. Os caches especificados como **padrão** serão substituídos por caches por um valor regional.
>
> Por exemplo, se o gerenciamento de API estiver hospedado nas regiões leste dos EUA, Sudeste Asiático e Europa Ocidental e houver dois caches configurados, um para o **padrão** e outro para o **sudeste asiático**, o gerenciamento de API no **sudeste asiático** usará seu próprio cache, enquanto as outras duas regiões usarão a entrada de cache **padrão** .

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Adicionar um cache do Azure para Redis da mesma assinatura

1. Navegue até sua instância de gerenciamento de API no portal do Azure.
2. Selecione a guia **cache externo** no menu à esquerda.
3. Clique no botão **+ Adicionar** .
4. Selecione o cache no campo suspenso **instância de cache** .
5. Selecione **padrão** ou especifique a região desejada no campo suspenso **usar de** .
6. Clique em **Guardar**.

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Adicionar um cache do Azure para Redis hospedado fora da assinatura atual do Azure ou do Azure em geral

1. Navegue até sua instância de gerenciamento de API no portal do Azure.
2. Selecione a guia **cache externo** no menu à esquerda.
3. Clique no botão **+ Adicionar** .
4. Selecione **personalizado** no campo suspenso de **instância de cache** .
5. Selecione **padrão** ou especifique a região desejada no campo suspenso **usar de** .
6. Forneça o cache do Azure para a cadeia de conexão Redis no campo **cadeia de conexão** .
7. Clique em **Guardar**.

## <a name="use-the-external-cache"></a>Usar o cache externo

Depois que o cache externo é configurado no gerenciamento de API do Azure, ele pode ser usado por meio de políticas de cache. Consulte [Adicionar cache para melhorar o desempenho no gerenciamento de API do Azure](api-management-howto-cache.md) para obter etapas detalhadas.

## <a name="next-steps"> </a>Passos seguintes

* Para obter mais informações sobre as políticas de colocação em cache, consulte [Políticas de colocação em cache][Caching policies] na [Referência de política da API Management][API Management policy reference].
* Para obter informações sobre a colocação em cache de itens por chave utilizando expressões de política, consulte [Colocação em cache personalizada na API Management do Azure](api-management-sample-cache-by-key.md).

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
