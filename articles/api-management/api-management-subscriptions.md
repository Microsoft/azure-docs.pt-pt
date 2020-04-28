---
title: Assinaturas na Gestão da API azure [ Microsoft Docs
description: Conheça o conceito de subscrições na Azure API Management.
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "70073409"
---
# <a name="subscriptions-in-azure-api-management"></a>Subscrições na Gestão de API do Azure

As subscrições são um conceito importante na Gestão da API Azure. São a forma mais comum de os consumidores de API terem acesso a APIs publicados através de uma instância de Gestão API. Este artigo fornece uma visão geral do conceito.

## <a name="what-are-subscriptions"></a>O que são subscrições?

Quando publica APIs através da API Management, é fácil e comum garantir o acesso a essas APIs utilizando chaves de subscrição. Os desenvolvedores que necessitem de consumir as APIs publicadas devem incluir uma chave de subscrição válida nos pedidos http quando fazem chamadas para essas APIs. Caso contrário, as chamadas são imediatamente rejeitadas pela porta de entrada da API Management. Não são encaminhados para os serviços de back-end.

Para obter uma chave de subscrição para aceder a APIs, é necessária uma subscrição. Uma subscrição é essencialmente um recipiente nomeado para um par de chaves de subscrição. Os desenvolvedores que precisam de consumir as APIs publicadas podem obter subscrições. E não precisam da aprovação dos editores da API. Os editores da API também podem criar subscrições diretamente para consumidores de API.

> [!TIP]
> A API Management também apoia outros mecanismos para garantir o acesso às APIs, incluindo os seguintes exemplos:
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [Certificados de cliente](api-management-howto-mutual-certificates-for-clients.md)
> - [Lista de whitelisting IP](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Âmbito das assinaturas

As subscrições podem ser associadas a vários âmbitos: produto, todas ASAP ou uma API individual.

### <a name="subscriptions-for-a-product"></a>Assinaturas de um produto

Tradicionalmente, as subscrições na API Management foram sempre associadas a um único âmbito de [produtos API.](api-management-terminology.md) Os desenvolvedores encontraram a lista de produtos no Portal do Desenvolvimento. Depois, submeteriam pedidos de subscrição para os produtos que queriam usar. Após a aprovação de um pedido de subscrição, quer automaticamente quer pelos editores da API, o desenvolvedor pode utilizar as chaves no mesmo para aceder a todas as APIs do produto. Atualmente, o portal de desenvolvimento apenas mostra as subscrições de âmbito do produto na secção de perfil do utilizador. 

![Assinaturas de produtos](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> Em certos cenários, os editores da API podem querer publicar um produto API ao público sem a exigência de subscrições. Podem desseleccionar a opção de **subscrição Require** na página **Definições** do produto no portal Azure. Como resultado, todas as APIs sob o produto podem ser acedidas sem uma chave API.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Assinaturas para todas as APIs ou uma API individual

Quando introduzimos o nível de [consumo](https://aka.ms/apimconsumptionblog) da Gestão de API, fizemos algumas alterações para simplificar a gestão chave:
- Em primeiro lugar, adicionámos mais dois âmbitos de subscrição: todas as APIs e uma Única API. O âmbito das subscrições já não se limita a um produto API. Agora é possível criar chaves que concedam acesso a uma API, ou a todas as APIs dentro de uma instância de Gestão API, sem necessidade de criar um produto e adicionar as APIs primeiro. Além disso, cada instância de Gestão API vem agora com uma subscrição imutável, all-APIs. Esta subscrição torna mais fácil e simples testar e depurar APIs dentro da consola de teste.

- Em segundo lugar, a API Management permite agora subscrições **autónomas.** As subscrições já não são necessárias para serem associadas a uma conta de desenvolvimento. Esta funcionalidade é útil em cenários como quando vários desenvolvedores ou equipas partilham uma subscrição.

- Finalmente, os editores da API podem agora [criar subscrições](api-management-howto-create-subscriptions.md) diretamente no portal Azure:

    ![Assinaturas flexíveis](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Passos seguintes
Obtenha mais informações sobre a Gestão da API:

+ Conheça outros [conceitos](api-management-terminology.md) na Gestão De API.
+ Siga os [nossos tutoriais](import-and-publish.md) para saber mais sobre a Gestão da API.
+ Consulte a nossa [página de PERGUNTAS FAQ](api-management-faq.md) para obter perguntas comuns.
