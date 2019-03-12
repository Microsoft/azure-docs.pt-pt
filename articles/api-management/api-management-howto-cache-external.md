---
title: Utilizar um cache externo na gestão de API do Azure | Documentos da Microsoft
description: Saiba como configurar e utilizar um cache externo na gestão de API do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 7d44f7f5d97e558121c5611bc34282989b12f535
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761247"
---
# <a name="use-an-external-azure-cache-for-redis-in-azure-api-management"></a>Utilizar uma Cache do Azure externo para Redis na gestão de API do Azure

Além de utilizar a cache interna, API Management do Azure também permite a colocação em cache as respostas num Cache externo do Azure para Redis.

Utilizar uma cache externa permite superar algumas limitações da cache interna. É especialmente útil se quiser:

* Evite ter a sua cache periodicamente limpa durante atualizações de gestão de API
* Ter mais controle sobre a sua configuração de cache
* Colocar em cache mais dados do que permite que sua camada de gestão de API
* Utilizar a cache com o escalão de consumo da gestão de API

Para obter informações mais detalhadas sobre a colocação em cache, veja [Colocação em cache das políticas de Gestão de API](api-management-caching-policies.md) e [Colocação em cache personalizada na Gestão de API do Azure](api-management-sample-cache-by-key.md).

![Traga a sua própria cache para APIM](media/api-management-howto-cache-external/overview.png)

O que irá aprender:

> [!div class="checklist"]
> * Adicionar um cache externo na gestão de API

## <a name="availability"></a>Disponibilidade

> [!NOTE]
> Esta funcionalidade está atualmente disponível apenas nos **consumo** camada de API Management do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

+ [crie uma instância da Gestão de API do Azure](get-started-create-service-instance.md)
+ Compreender [colocação em cache na gestão de API do Azure](api-management-howto-cache.md)

## <a name="create-cache"> </a> Criar a Cache do Azure para Redis

Esta secção explica como criar uma Cache do Azure para Redis no Azure. Se já tiver uma Cache do Azure para Redis, dentro ou fora do Azure, pode <a href="#add-external-cache">ignorar</a> para a secção seguinte.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="add-external-cache"> </a>Adicionar a cache de externo

Siga os passos abaixo para adicionar uma Cache do Azure externo para Redis na gestão de API do Azure.

![Traga a sua própria cache para APIM](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> O **utilizada a partir do** definição especifica que a gestão de API regional implementação comunicará com a cache configurada em caso de uma configuração com múltiplos regional da gestão de API. Os caches especificados como **predefinido** serão substituídos pelas caches com um valor regional.
>
> Por exemplo, se a gestão de API está alojada nas regiões E.U.A. leste, Sudeste asiático e Europa Ocidental e existem dois caches configurado, um para **predefinido** e outra para **Sudeste asiático**, gestão de API no  **Sudeste asiático** utilizará o seu próprio cache, enquanto as outras duas regiões utilizarão o **predefinido** entrada da cache.

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Adicionar uma Cache do Azure para Redis da mesma subscrição

1. Navegue para a sua instância de gestão de API no portal do Azure.
2. Selecione o **cache externo** separador no menu à esquerda.
3. Clique nas **+ adicionar** botão.
4. Selecione a sua cache no **instância da Cache** campo de lista pendente.
5. Selecione **predefinido** ou especificar a região desejada na **utilizada a partir do** campo de lista pendente.
6. Clique em **Guardar**.

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Adicionar uma Cache do Azure para Redis alojados fora da subscrição do Azure atual ou o Azure, em geral

1. Navegue para a sua instância de gestão de API no portal do Azure.
2. Selecione o **cache externo** separador no menu à esquerda.
3. Clique nas **+ adicionar** botão.
4. Selecione **personalizada** no **instância da Cache** campo de lista pendente.
5. Selecione **predefinido** ou especificar a região desejada na **utilizada a partir do** campo de lista pendente.
6. Fornecer a sua Cache do Azure para a cadeia de ligação de Redis no **cadeia de ligação** campo.
7. Clique em **Guardar**.

## <a name="use-the-external-cache"></a>Utilizar a cache de externa

Depois da cache externa é configurada na gestão de API do Azure, pode ser utilizado por meio de políticas de colocação em cache. Ver [adicionar colocação em cache para melhorar o desempenho na gestão de API do Azure](api-management-howto-cache.md) para obter passos detalhados.

## <a name="next-steps"> </a>Passos seguintes
* Para obter mais informações sobre as políticas de colocação em cache, consulte [Políticas de colocação em cache][Caching policies] na [Referência de política da Gestão de API][API Management policy reference].
* Para obter informações sobre a colocação em cache de itens por chave utilizando expressões de política, consulte [Colocação em cache personalizada na API Management do Azure](api-management-sample-cache-by-key.md).

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx