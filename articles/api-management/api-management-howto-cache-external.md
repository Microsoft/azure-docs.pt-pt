---
title: Utilize uma cache externa na Azure API Management | Microsoft Docs
description: Aprenda a configurar e use uma cache externa na Azure API Management. A utilização de uma cache externa permite-lhe ultrapassar algumas limitações da cache incorporada.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: 6288a10e111e42629abf5e09b84a6a7791dcfe95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018227"
---
# <a name="use-an-external-redis-compatible-cache-in-azure-api-management"></a>Utilize uma cache externa compatível com Redis na Gestão API da Azure

Além de utilizar a cache incorporada, a Azure API Management permite obter respostas de cache numa cache externa compatível com Redis, por exemplo, Cache Azure para Redis.

A utilização de uma cache externa permite-lhe ultrapassar algumas limitações da cache incorporada:

* Evite ter o seu cache periodicamente limpo durante as atualizações de Gestão da API
* Tenha mais controlo sobre a sua configuração de cache
* Cache mais dados do que o seu nível de Gestão de API permite
* Utilizar o caching com o nível de consumo da Gestão de API
* Permitir o caching nos [gateways auto-hospedados](self-hosted-gateway-overview.md) da API Management

Para obter informações mais detalhadas sobre a colocação em cache, veja [Colocação em cache das políticas de Gestão de API](api-management-caching-policies.md) e [Colocação em cache personalizada na Gestão de API do Azure](api-management-sample-cache-by-key.md).

![Traga o seu próprio cache para a APIM](media/api-management-howto-cache-external/overview.png)

O que irá aprender:

> [!div class="checklist"]
> * Adicione uma cache externa na Gestão da API

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

+ [crie uma instância da Gestão de API do Azure](get-started-create-service-instance.md)
+ Compreender [o caching na Azure API Management](api-management-howto-cache.md)

## <a name="create-azure-cache-for-redis"></a><a name="create-cache"></a> Criar cache Azure para Redis

Esta secção explica como criar uma Cache Azure para Redis em Azure. Se já tem um Cache Azure para Redis, dentro ou fora de Azure, pode <a href="#add-external-cache">saltar</a> para a secção seguinte.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="deploy-redis-cache-to-kubernetes"></a><a name="create-cache"></a> Implementar cache Redis para Kubernetes

Para caches, os gateways auto-hospedados dependem exclusivamente de caches externos. Para que os caches sejam portais auto-hospedados eficazes e a cache em que dependem devem ser localizados perto uns dos outros para minimizar a procura e armazenar latências. Implantar uma cache Redis no mesmo cluster Kubernetes ou num aglomerado separado nas proximidades são as melhores opções. Siga este [link](https://github.com/kubernetes/examples/tree/master/guestbook) para aprender a implantar a cache redis para um cluster Kubernetes.

## <a name="add-an-external-cache"></a><a name="add-external-cache"> </a>Adicione uma cache externa

Siga os passos abaixo para adicionar uma Cache Azure externa para Redis na Gestão API Azure.

![Screenshot que mostra como adicionar um Cache Azure externo para Redis na Azure API Management.](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> A **utilização da** definição especifica uma região Azure ou um local de gateway auto-hospedado que utilizará a cache configurada. Os caches configurados como **Padrão** serão ultrapassados por caches com uma região ou valor de localização específico.
>
> Por exemplo, se a API Management estiver hospedada nas regiões leste dos EUA, Sudeste Asiático e Europa Ocidental e existirem dois caches configurados, um para **o Default** e outro para o **Sudeste Asiático**, a API Management no **Sudeste Asiático** utilizará a sua própria cache, enquanto as outras duas regiões utilizarão a entrada de cache **padrão.**

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Adicione um Cache Azure para Redis da mesma subscrição

1. Navegue pela sua instância de Gestão de API no portal Azure.
2. Selecione o **separador cache externo** do menu à esquerda.
3. Clique no botão **+ Adicionar**.
4. Selecione a sua cache no campo de dropdown **de instância cache.**
5. Selecione **Predefinição** ou especifique a região desejada no campo **Utilização a partir do** campo de dropdown.
6. Clique em **Guardar**.

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Adicione uma Cache Azure para Redis hospedado fora da subscrição atual do Azure ou Azure em geral

1. Navegue pela sua instância de Gestão de API no portal Azure.
2. Selecione o **separador cache externo** do menu à esquerda.
3. Clique no botão **+ Adicionar**.
4. Selecione **Custom** no campo de dropdown **de instância cache.**
5. Selecione **Predefinição** ou especifique a região desejada no campo **Utilização a partir do** campo de dropdown.
6. Forneça a sua cache Azure para a cadeia de ligação Redis no campo **de cordas Connection.**
7. Clique em **Guardar**.

### <a name="add-a-redis-cache-to-a-self-hosted-gateway"></a>Adicione uma cache Redis a um portal auto-hospedado

1. Navegue pela sua instância de Gestão de API no portal Azure.
2. Selecione o **separador cache externo** do menu à esquerda.
3. Clique no botão **+ Adicionar**.
4. Selecione **Custom** no campo de dropdown **de instância cache.**
5. Especifique a localização de gateway auto-hospedada desejada ou o **Padrão** no **campo de utilização a partir do** campo dropdown.
6. Forneça a sua cadeia de ligação de cache Redis no campo **de cordas Connection.**
7. Clique em **Guardar**.

## <a name="use-the-external-cache"></a>Use a cache externa

Uma vez configurada a cache externa na Azure API Management, pode ser utilizada através de políticas de cache. Consulte [o Caching para melhorar o desempenho na Azure API Management](api-management-howto-cache.md) para obter etapas detalhadas.

## <a name="next-steps"></a><a name="next-steps"> </a>Passos seguintes

* Para obter mais informações sobre as políticas de colocação em cache, consulte [Políticas de colocação em cache][Caching policies] na [Referência de política da API Management][API Management policy reference].
* Para obter informações sobre a colocação em cache de itens por chave utilizando expressões de política, consulte [Colocação em cache personalizada na API Management do Azure](api-management-sample-cache-by-key.md).

[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-caching-policies.md
