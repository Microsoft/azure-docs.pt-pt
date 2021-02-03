---
title: Criar subscrições do Azure através de programação
description: Este artigo ajuda-o a compreender as opções disponíveis para criar subscrições do Azure através de programação.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/13/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 546ed24b5f9e7892f40c9d425b668f60ad705f8f
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493893"
---
# <a name="create-azure-subscriptions-programmatically"></a>Criar subscrições Azure programáticamente

Este artigo ajuda-o a compreender as opções disponíveis para criar subscrições do Azure através de programação.

Com as várias APIs REST, pode criar uma subscrição para os seguintes tipos de contrato do Azure:

- Contrato Enterprise (EA)
- Contrato de Cliente da Microsoft (MCA)
- Contrato de Parceiro da Microsoft (MPA)

Não pode criar subscrições adicionais através de programação para outros tipos de contrato com as APIs REST.

Os requisitos e detalhes para criar subscrições diferem consoante os contratos e as versões de API. Veja os seguintes artigos que se aplicam à sua situação:

APIs mais recentes:

- [Criar subscrições EA](programmatically-create-subscription-enterprise-agreement.md)
- [Criar subscrições MCA](programmatically-create-subscription-microsoft-customer-agreement.md)
- [Criar subscrições MPA](programmatically-create-subscription-microsoft-partner-agreement.md)

Se ainda estiver a utilizar [APIs de pré-visualização](programmatically-create-subscription-preview.md), pode continuar a criar subscrições com as mesmas. 

Além disso, pode [criar subscrições com um modelo do ARM](create-subscription-template.md). Os modelos do ARM ajudam a automatizar o processo de criação de subscrições com APIs REST. 

## <a name="next-steps"></a>Passos seguintes

* Depois de criar uma subscrição, pode conceder essa capacidade a outros utilizadores e principais de serviço. Para obter mais informações, veja [Conceder acesso para criar subscrições Enterprise do Azure (pré-visualização)](grant-access-to-create-subscription.md).
* Para obter mais informações sobre como gerir grandes números de subscrições com grupos de gestão, veja [Organizar recursos com os grupos de gestão do Azure](../../governance/management-groups/overview.md).
