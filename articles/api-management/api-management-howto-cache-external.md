---
title: Utilize uma cache externa na Azure API Management [ Microsoft Docs
description: Aprenda a configurar e utilizar uma cache externa na Gestão da API Azure.
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "70072492"
---
# <a name="use-an-external-azure-cache-for-redis-in-azure-api-management"></a>Utilizar uma Cache do Azure externa para Redis na Gestão de API do Azure

Além de utilizar a cache incorporada, a Azure API Management também permite respostas de cache em um Azure Cache externo para Redis.

A utilização de uma cache externa permite ultrapassar algumas limitações da cache incorporada. É especialmente benéfico se quiser:

* Evite ter a sua cache periodicamente limpa durante as atualizações de Gestão da API
* Tenha mais controlo sobre a configuração da sua cache
* Cache mais dados do que o seu nível de Gestão API permite
* Use o cache com o nível de consumo da Gestão de API

Para obter informações mais detalhadas sobre a colocação em cache, veja [Colocação em cache das políticas de Gestão de API](api-management-caching-policies.md) e [Colocação em cache personalizada na Gestão de API do Azure](api-management-sample-cache-by-key.md).

![Traga a sua própria cache para a APIM](media/api-management-howto-cache-external/overview.png)

O que irá aprender:

> [!div class="checklist"]
> * Adicione uma cache externa na Gestão de API

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

+ [crie uma instância da Gestão de API do Azure](get-started-create-service-instance.md)
+ Compreender [o cache na Gestão da API Azure](api-management-howto-cache.md)

## <a name="create-azure-cache-for-redis"></a><a name="create-cache"> </a> Criar cache azure para redis

Esta secção explica como criar um Azure Cache para Redis em Azure. Se já tiver um Azure Cache para Redis, dentro ou fora de Azure, pode <a href="#add-external-cache">saltar</a> para a secção seguinte.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="add-an-external-cache"></a><a name="add-external-cache"> </a>Adicione uma cache externa

Siga os passos abaixo para adicionar um Azure Cache externo para Redis na Gestão aPI Azure.

![Traga a sua própria cache para a APIM](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> A **utilização da** definição especifica quais a implantação regional da API Management comunicará com a cache configurada em caso de configuração multi-regional da Gestão da API. Os caches especificados como **Padrão** serão sobremontados por caches com um valor regional.
>
> Por exemplo, se a API Management estiver alojada nas regiões leste dos EUA, Sudeste Asiático e Europa Ocidental e houver dois caches configurados, um para **o Default** e outro para o **Sudeste Asiático,** a API Management no **Sudeste Asiático** utilizará a sua própria cache, enquanto as outras duas regiões utilizarão a entrada em cache **padrão.**

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Adicione um Cache Azure para Redis a partir da mesma subscrição

1. Navegue na sua instância de Gestão API no portal Azure.
2. Selecione o separador **de cache externo** do menu à esquerda.
3. Clique no botão **+ Adicionar**.
4. Selecione a sua cache no campo de dropdown da **instância Cache.**
5. Selecione **Predefinido** ou especifique a região desejada na **utilização a partir do** campo de dropdown.
6. Clique em **Guardar**.

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Adicione um Azure Cache para Redis hospedado fora da atual subscrição Azure ou Azure em geral

1. Navegue na sua instância de Gestão API no portal Azure.
2. Selecione o separador **de cache externo** do menu à esquerda.
3. Clique no botão **+ Adicionar**.
4. Selecione **Custom** no campo de dropdown da **instância Cache.**
5. Selecione **Predefinido** ou especifique a região desejada na **utilização a partir do** campo de dropdown.
6. Forneça o seu Cache Azure para a cadeia de ligação Redis no campo de **cordas Connection.**
7. Clique em **Guardar**.

## <a name="use-the-external-cache"></a>Use a cache externa

Uma vez configurada a cache externa na Gestão aPI Azure, pode ser usada através de políticas de cache. Consulte [Adicionar cachepara melhorar o desempenho na Gestão de API Azure](api-management-howto-cache.md) para passos detalhados.

## <a name="next-steps"></a><a name="next-steps"> </a>Passos seguintes

* Para obter mais informações sobre as políticas de colocação em cache, consulte [Políticas de colocação em cache][Caching policies] na [Referência de política da API Management][API Management policy reference].
* Para obter informações sobre a colocação em cache de itens por chave utilizando expressões de política, consulte [Colocação em cache personalizada na API Management do Azure](api-management-sample-cache-by-key.md).

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
