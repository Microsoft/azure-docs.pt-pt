---
title: Assinaturas no gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre o conceito de assinaturas no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 9144af131e1427d0b3226655c871921ac1d91665
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073409"
---
# <a name="subscriptions-in-azure-api-management"></a>Assinaturas no gerenciamento de API do Azure

As assinaturas são um conceito importante no gerenciamento de API do Azure. Eles são a maneira mais comum para os consumidores de API obterem acesso às APIs publicadas por meio de uma instância de gerenciamento de API. Este artigo fornece uma visão geral do conceito.

## <a name="what-are-subscriptions"></a>O que são assinaturas?

Quando você publica APIs por meio do gerenciamento de API, é fácil e comum proteger o acesso a essas APIs usando chaves de assinatura. Os desenvolvedores que precisam consumir as APIs publicadas devem incluir uma chave de assinatura válida em solicitações HTTP quando fizerem chamadas para essas APIs. Caso contrário, as chamadas serão rejeitadas imediatamente pelo gateway de gerenciamento de API. Eles não são encaminhados para os serviços de back-end.

Para obter uma chave de assinatura para acessar APIs, é necessária uma assinatura. Uma assinatura é essencialmente um contêiner nomeado para um par de chaves de assinatura. Os desenvolvedores que precisam consumir as APIs publicadas podem obter assinaturas. E eles não precisam de aprovação de editores de API. Os editores de API também podem criar assinaturas diretamente para consumidores de API.

> [!TIP]
> O gerenciamento de API também dá suporte a outros mecanismos para proteger o acesso às APIs, incluindo os seguintes exemplos:
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [Certificados de cliente](api-management-howto-mutual-certificates-for-clients.md)
> - [Lista de permissões de IP](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Escopo de assinaturas

As assinaturas podem ser associadas a vários escopos: produto, todas as APIs ou uma API individual.

### <a name="subscriptions-for-a-product"></a>Assinaturas de um produto

Tradicionalmente, as assinaturas no gerenciamento de API sempre estavam associadas a um único escopo de [produto de API](api-management-terminology.md) . Os desenvolvedores encontraram a lista de produtos no portal do desenvolvedor. Em seguida, eles enviariam solicitações de assinatura para os produtos que desejavam usar. Depois que uma solicitação de assinatura é aprovada, seja automaticamente ou por editores de API, o desenvolvedor pode usar as chaves nela para acessar todas as APIs do produto. No momento, o portal do desenvolvedor mostra apenas as assinaturas de escopo do produto na seção perfil do usuário. 

![Assinaturas de produto](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> Em determinados cenários, os editores de API podem querer publicar um produto de API para o público sem o requisito de assinaturas. Eles podem desmarcar a opção **exigir assinatura** na página **configurações** do produto na portal do Azure. Como resultado, todas as APIs sob o produto podem ser acessadas sem uma chave de API.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Assinaturas para todas as APIs ou uma API individual

Quando apresentamos a camada de [consumo](https://aka.ms/apimconsumptionblog) do gerenciamento de API, fizemos algumas alterações para simplificar o gerenciamento de chaves:
- Primeiro, adicionamos mais dois escopos de assinatura: todas as APIs e uma única API. O escopo de assinaturas não é mais limitado a um produto de API. Agora é possível criar chaves que concedem acesso a uma API ou a todas as APIs em uma instância de gerenciamento de API, sem a necessidade de criar um produto e adicionar as APIs a ele primeiro. Além disso, cada instância de gerenciamento de API agora vem com uma assinatura imutável, com todas as APIs. Essa assinatura torna mais fácil e mais simples testar e depurar APIs no console de teste.

- Em segundo lugar, o gerenciamento de API agora permite assinaturas autônomas. Não é mais necessário que as assinaturas sejam associadas a uma conta de desenvolvedor. Esse recurso é útil em cenários como, por exemplo, quando vários desenvolvedores ou equipes compartilham uma assinatura.

- Por fim, os editores de API agora podem [criar assinaturas](api-management-howto-create-subscriptions.md) diretamente no portal do Azure:

    ![Assinaturas flexíveis](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Passos Seguintes
Obtenha mais informações sobre o gerenciamento de API:

+ Aprenda outros [conceitos](api-management-terminology.md) no gerenciamento de API.
+ Siga nossos [tutoriais](import-and-publish.md) para saber mais sobre o gerenciamento de API.
+ Confira nossa [página de perguntas frequentes](api-management-faq.md) para perguntas comuns.
