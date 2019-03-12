---
title: Pilha plano, oferta, quota e subscrição descrição geral do Azure | Documentos da Microsoft
description: Como um operador de nuvem, quero compreender os planos, ofertas, quotas e subscrições do Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 10/12/2018
ms.openlocfilehash: 0de021f4666da805eab8faba527f7c5322c39e9d
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763457"
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Plano, oferta, quota e descrição geral da subscrição

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

[O Azure Stack](azure-stack-poc.md) permite que forneça uma grande variedade de serviços, como as máquinas virtuais, bancos de dados SQL, SharePoint, Exchange e até mesmo [itens de Azure Marketplace](azure-stack-marketplace-azure-items.md). Como um operador do Azure Stack, configurar e fornecem serviços no Azure Stack através de planos, ofertas e quotas.

Ofertas de contenham um ou mais planos e cada plano inclui um ou mais serviços. Ao criar planos e combiná-los num ofertas diferentes, pode gerir:

- Quais recursos e serviços, os utilizadores podem aceder.
- A quantidade de recursos que os utilizadores podem consumir.
- Que regiões têm acesso aos recursos.

Quando publicar um serviço, siga estes passos de alto nível:

1. Adicione um serviço que deseja fornecer aos seus utilizadores.
2. Crie um plano que tem um ou mais serviços. Ao criar um plano, selecione ou crie as quotas que definem os limites de recursos de cada serviço no plano.
3. Crie uma oferta que contém um ou mais planos. A oferta pode incluir planos base e planos de suplementos opcionais.

Depois de criar a oferta, podem subscrever os seus utilizadores para aceder a serviços e recursos que a oferta fornece. Os utilizadores podem subscrever ofertas quantos desejar. A figura seguinte mostra um exemplo simples de um utilizador que subscreveu a duas ofertas. Cada oferta tem um plano ou dois, e cada plano lhes concede acesso a serviços.

![Subscrição com ofertas e planos de inquilino](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Planos

Planos consistem em agrupamentos de um ou mais serviços. Como um operador do Azure Stack, [criar planos de](azure-stack-create-plan.md) para oferecer aos seus utilizadores. Por sua vez, os seus utilizadores subscrevem suas ofertas para utilizar os planos e serviços incluídos. Durante a criação de planos, certifique-se definir suas quotas, definir os planos de bases e considere incluir planos de suplementos opcionais.

### <a name="quotas"></a>Quotas

Para ajudar a gerir a sua capacidade de nuvem, pode usar pré-configurados *quotas*, ou criar uma quota de novo para cada serviço num plano. Quotas definem os limites de recursos superior que uma subscrição de utilizador pode aprovisionar ou consumir. Por exemplo, uma quota pode permitir que um usuário criar até cinco máquinas virtuais (VMs).

É possível configurar quotas por região. Por exemplo, um plano que fornece serviços de computação para a região A poderia ter uma quota de duas VMs.

>[!NOTE]
>No Azure Stack Development Kit, apenas numa região (com o nome *local*) está disponível.

Saiba mais sobre [os tipos de ficheiro no Azure Stack](azure-stack-quota-types.md).

### <a name="base-plan"></a>Plano base

Ao criar uma oferta, o administrador de serviços pode incluir um plano base. Estes planos bases estão incluídos por predefinição, quando um utilizador subscrever essa oferta. Quando um utilizador subscrever, eles têm acesso a todos os fornecedores de recursos especificado nesses planos base (com as quotas de correspondentes).

### <a name="add-on-plans"></a>Planos de suplementos

Planos de suplementos são opcionais planos de que adicionar a uma oferta. Planos de suplementos não estão incluídos por predefinição na subscrição. Planos de suplementos são planos adicionais (com cotas) disponíveis numa oferta que um subscritor pode adicionar a suas assinaturas. Por exemplo, pode oferecer um plano base com recursos limitados para uma versão de avaliação e um plano de suplemento com os recursos mais substanciais para os clientes que decidirem adotar o serviço.

## <a name="offers"></a>Ofertas

As ofertas são grupos de um ou mais planos que criar para que os utilizadores podem subscrevê-los. Por exemplo, oferecem Alpha pode conter A de plano, que fornece um conjunto de serviços de computação e de plano B, que fornece um conjunto de serviços de armazenamento e rede.

Quando [criar uma oferta](azure-stack-create-offer.md), tem de incluir, pelo menos, um plano base, mas também pode criar planos de suplementos que os usuários podem adicionar a sua subscrição.

## <a name="subscriptions"></a>Subscrições

Uma subscrição é como os utilizadores aceder às suas ofertas. Se for um operador do Azure Stack para um fornecedor de serviços, os utilizadores (inquilinos) compram seus serviços através da subscrição de suas ofertas. Se for um operador do Azure Stack numa organização, os utilizadores (funcionários) podem subscrever os serviços que oferecem sem ter de adquirir.

Cada combinação de um utilizador com uma oferta é uma assinatura exclusiva. Um utilizador pode ter subscrições para várias ofertas, mas cada subscrição aplica-se apenas a uma oferta. Planos, ofertas e quotas só se aplicam a um único de subscrição – eles não podem ser compartilhados entre subscrições. Cada recurso que cria um utilizador está associado uma subscrição.

### <a name="default-provider-subscription"></a>Subscrição do fornecedor predefinido

A subscrição do fornecedor padrão é criada automaticamente ao implementar o Development Kit do Azure Stack. Esta subscrição pode ser utilizada para gerir o Azure Stack, implementar fornecedores de recursos adicionais e criar planos e ofertas para os utilizadores. Por motivos de licenciamento e de segurança, ele não deve ser usado para executar aplicações e cargas de trabalho do cliente.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre os planos e ofertas, consulte [criar um plano](azure-stack-create-plan.md).
