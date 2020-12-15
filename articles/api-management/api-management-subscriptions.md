---
title: Assinaturas na Azure API Management / Microsoft Docs
description: Conheça o conceito de subscrições na Azure API Management. Os consumidores têm acesso a APIs utilizando subscrições na Azure API Management.
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
ms.openlocfilehash: cdc7668b06308bd5532f4885d0e1365fc34ae989
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511251"
---
# <a name="subscriptions-in-azure-api-management"></a>Subscrições na Gestão de API do Azure

As assinaturas são um conceito importante na Azure API Management. São a forma mais comum de os consumidores de API terem acesso a APIs publicados através de um caso de Gestão de API. Este artigo fornece uma visão geral do conceito.

## <a name="what-are-subscriptions"></a>O que são assinaturas?

Quando publica APIs através da API Management, é fácil e comum garantir o acesso a essas APIs utilizando chaves de subscrição. Os desenvolvedores que precisam de consumir as APIs publicadas devem incluir uma chave de subscrição válida em pedidos HTTP quando fazem chamadas para essas APIs. Caso contrário, as chamadas são rejeitadas imediatamente pela porta de entrada da API Management. Não são encaminhados para os serviços de back-end.

Para obter uma chave de subscrição para aceder a APIs, é necessária uma subscrição. Uma subscrição é essencialmente um recipiente nomeado para um par de chaves de subscrição. Os desenvolvedores que precisam de consumir as APIs publicadas podem obter subscrições. E não precisam da aprovação dos editores da API. Os editores da API também podem criar subscrições diretamente para os consumidores de API.

> [!TIP]
> A API Management também apoia outros mecanismos para garantir o acesso às APIs, incluindo os seguintes exemplos:
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [Certificados de cliente](api-management-howto-mutual-certificates-for-clients.md)
> - [Restringir os IPs de chamadas](./api-management-access-restriction-policies.md#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Âmbito de subscrições

As subscrições podem ser associadas a vários âmbitos: produto, todas as APIs ou uma API individual.

### <a name="subscriptions-for-a-product"></a>Assinaturas para um produto

Tradicionalmente, as assinaturas na API Management estavam sempre associadas a um único âmbito [de produto API.](api-management-terminology.md) Os desenvolvedores encontraram a lista de produtos no Portal do Desenvolvedor. Depois, enviavam pedidos de subscrição para os produtos que queriam usar. Após a aprovação de um pedido de subscrição, quer automaticamente quer por editores da API, o desenvolvedor pode utilizar as chaves nele para aceder a todas as APIs do produto. Atualmente, o portal do desenvolvedor apenas mostra as subscrições de âmbito do produto na secção de perfil do utilizador. 

![Subscrições de produtos](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> Em certos cenários, os editores da API podem querer publicar um produto API ao público sem a exigência de subscrições. Podem desmarcar a opção **de subscrição 'Exigir'** na página **Definições** do produto no portal Azure. Como resultado, todas as APIs sob o produto podem ser acedidas sem uma chave API.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Assinaturas para todas as APIs ou uma API individual

Quando introduzimos o nível de [consumo](https://aka.ms/apimconsumptionblog) da Gestão de API, fizemos algumas alterações para simplificar a gestão de chaves:
- Em primeiro lugar, adicionámos mais dois âmbitos de subscrição: todas as APIs e uma única API. O âmbito das subscrições já não se limita a um produto API. Agora é possível criar chaves que concedam acesso a uma API, ou a todas as APIs dentro de uma instância de Gestão da API, sem necessidade de criar um produto e adicionar as APIs a ele primeiro. Além disso, cada instância de Gestão da API vem agora com uma subscrição imutável, all-APIs. Esta subscrição torna mais fácil e simples testar e depurar APIs dentro da consola de teste.

- Em segundo lugar, a API Management permite agora subscrições **autónomas.** As subscrições deixaram de ser necessárias para serem associadas a uma conta de programador. Esta funcionalidade é útil em cenários como quando vários desenvolvedores ou equipas partilham uma subscrição.

- Finalmente, os editores da API podem agora [criar subscrições](api-management-howto-create-subscriptions.md) diretamente no portal Azure:

    ![Assinaturas flexíveis](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Passos seguintes
Obtenha mais informações sobre a Gestão da API:

+ Aprenda [outros conceitos](api-management-terminology.md) na API Management.
+ Siga os [nossos tutoriais](import-and-publish.md) para saber mais sobre a API Management.
+ Consulte a nossa [página de PERGUNTAS Frequentes](api-management-faq.md) para obter questões comuns.
