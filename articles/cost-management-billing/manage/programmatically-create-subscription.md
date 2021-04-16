---
title: Criar subscrições Azure programáticamente
description: Este artigo ajuda-o a compreender as opções disponíveis para criar subscrições do Azure através de programação.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/11/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ce08ebf473b11eecae327c7de050c791f5bc1b1a
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379014"
---
# <a name="create-azure-subscriptions-programmatically"></a>Criar subscrições Azure programáticamente

Este artigo ajuda-o a compreender as opções disponíveis para criar subscrições do Azure através de programação.

Com as várias APIs REST, pode criar uma subscrição para os seguintes tipos de contrato do Azure:

- Contrato Enterprise (EA)
- Contrato de Cliente da Microsoft (MCA)
- Contrato de Parceiro da Microsoft (MPA)

Não é possível criar subscrições adicionais para outros tipos de acordo com APIs REST.

Os requisitos e detalhes para criar subscrições diferem consoante os contratos e as versões de API. Veja os seguintes artigos que se aplicam à sua situação:

APIs mais recentes:

- [Criar subscrições EA](programmatically-create-subscription-enterprise-agreement.md)
- [Criar subscrições MCA](programmatically-create-subscription-microsoft-customer-agreement.md)
- [Criar subscrições MPA](programmatically-create-subscription-microsoft-partner-agreement.md)

Estes artigos também mostram como criar subscrições com um modelo de Gestor de Recursos Azure (modelo ARM). Um modelo ARM ajuda a automatizar o processo de criação de subscrição.

Se ainda estiver a utilizar [APIs de pré-visualização,](programmatically-create-subscription-preview.md)pode continuar a criar subscrições com elas. 

## <a name="next-steps"></a>Passos seguintes

* Depois de criar uma subscrição, pode conceder essa capacidade a outros utilizadores e principais de serviço. Para obter mais informações, veja [Conceder acesso para criar subscrições Enterprise do Azure (pré-visualização)](grant-access-to-create-subscription.md).
* Para obter mais informações sobre como gerir grandes números de subscrições com grupos de gestão, veja [Organizar recursos com os grupos de gestão do Azure](../../governance/management-groups/overview.md).
