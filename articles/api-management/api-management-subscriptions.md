---
title: As subscrições na gestão de API do Azure | Documentos da Microsoft
description: Saiba mais sobre o conceito de subscrições na gestão de API do Azure.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: afd43bbf6f52f498ad8f56d5a48b960d45d84137
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243249"
---
# <a name="subscriptions-in-azure-api-management"></a>Subscrições na gestão de API do Azure

As assinaturas são um conceito importante na gestão de API do Azure. Eles são a forma mais comum para os consumidores de APIS obter acesso a APIs publicadas através de uma instância de gestão de API. Este artigo fornece uma descrição geral do conceito.

## <a name="what-are-subscriptions"></a>Quais são as subscrições?

Quando publicar APIs através da gestão de API, é fácil e comum para proteger o acesso a essas APIs com chaves de subscrição. Os desenvolvedores que precisam de consumir as APIs publicadas tem de incluir uma chave de subscrição válido em pedidos de HTTP quando eles fazem chamadas para essas APIs. Caso contrário, as chamadas são rejeitadas imediatamente pelo gateway de gestão de API. Eles não são reencaminhados para os serviços de back-end.

Para obter uma chave de subscrição para aceder a APIs, é necessária uma subscrição. Uma subscrição é essencialmente um contentor com o nome para um par de chaves de subscrição. Os desenvolvedores que precisam de consumir as APIs publicadas podem obter subscrições. E não precisam de aprovação de editores de APIS. Os publicadores de APIS também podem criar subscrições diretamente para os consumidores de APIS.

> [!TIP]
> Gestão de API também suporta outros mecanismos para proteger o acesso a APIs, incluindo os exemplos seguintes:
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [Certificados de cliente](api-management-howto-mutual-certificates-for-clients.md)
> - [Listas de permissões de IP](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Âmbito de subscrições

As assinaturas podem ser associadas a vários âmbitos: produto, todas as APIs ou uma API individual.

### <a name="subscriptions-for-a-product"></a>Subscrições para um produto

Tradicionalmente, as subscrições na gestão de API sempre foram associadas um único [produto da API](api-management-terminology.md) âmbito. Os desenvolvedores encontrar a lista de produtos no Portal do programador. Em seguida, eles poderia submeter pedidos de subscrição para os produtos que gostaria de usar. Depois de um pedido de subscrição é aprovado, automaticamente ou pelos publicadores de API, o programador pode utilizar as chaves na mesma para aceder a todas as APIs no produto. No momento, o portal do programador mostra apenas as subscrições de âmbito do produto na seção de perfil do usuário. 

![Assinaturas do produto](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> Em determinados cenários, os publicadores de APIS talvez queira publicar um produto da API para o público sem a necessidade de subscrições. Eles podem desmarcar a **requer subscrição** opção a **definições** página do produto no portal do Azure. Como resultado, todas as APIs sob o produto podem ser acedidas sem uma chave de API.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Subscrições para todas as APIs ou uma API individual

Quando Introduzimos o [consumo](https://aka.ms/apimconsumptionblog) camada de gestão de API, fizemos algumas alterações para simplificar a gestão de chaves:
- Em primeiro lugar, adicionámos mais dois âmbitos de subscrição: todas as APIs e uma única API. O âmbito das subscrições já não está limitado a um produto da API. Agora é possível criar chaves que concedem acesso a uma API ou todas as APIs numa instância de gestão de API, sem a necessidade de criar um produto e adicionar-lhe as APIs do primeiro. Além disso, cada instância de gestão de API agora vem com uma assinatura imutável, todas as APIs. Esta subscrição torna mais fácil e mais simples de testar e depurar as APIs dentro da consola de teste.

- Em segundo lugar, gestão de API agora permite **autónomo** subscrições. Subscrições já não são necessários para estar associado a uma conta de programador. Esta funcionalidade é útil em cenários, como quando vários desenvolvedores ou equipes de partilham uma subscrição.

- Por fim, os publicadores de APIS podem agora [criar subscrições](api-management-howto-create-subscriptions.md) diretamente no portal do Azure:

    ![Subscrições flexíveis](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Passos Seguintes
Obter mais informações sobre a gestão de API:

+ Saiba outro [conceitos](api-management-terminology.md) na gestão de API.
+ Siga nosso [tutoriais](import-and-publish.md) para saber mais sobre a gestão de API.
+ Verifique nossas [página de perguntas frequentes](api-management-faq.md) para perguntas comuns.
