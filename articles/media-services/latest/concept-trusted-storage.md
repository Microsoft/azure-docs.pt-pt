---
title: Armazenamento fidedigno para Serviços de Mídia
description: A autenticação de identidades geridas permite que os Serviços de Comunicação Social acedam à conta de armazenamento que foi configurada com uma firewall ou uma restrição VNet através de acesso de armazenamento fidedigno.
keywords: armazenamento confiável, identidades geridas
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 59c1eb7936bc113f8935d6fa2ad378c6994c3ca9
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99408517"
---
# <a name="trusted-storage-for-media-services"></a>Armazenamento fidedigno para Serviços de Mídia

Ao criar uma conta de Serviços de Comunicação, deve associá-la a uma conta de armazenamento. Os Serviços de Mídia podem aceder a essa conta de armazenamento utilizando a autenticação do sistema. Os Serviços de Comunicação Social validam que a conta de Serviços de Mídia e a conta de armazenamento estão na mesma subscrição e valida que o utilizador que acrescenta a associação tem acesso à conta de armazenamento com o Azure Resource Manager RBAC.

No entanto, se pretender utilizar uma firewall para proteger a sua conta de armazenamento e ativar o armazenamento fidedigno, deve utilizar a autenticação [Managed Identities.](concept-managed-identities.md) Permite que os Serviços de Comunicação Social acedam à conta de armazenamento que foi configurada com uma firewall ou uma restrição VNet através de acesso de armazenamento fidedigno.

Para compreender os métodos de criação de armazenamento fidedignos com identidades geridas, leia [Identidades Geridas e Serviços de Media.](concept-managed-identities.md)

Para obter mais informações sobre as chaves geridas pelo cliente e o Key Vault, consulte [Bring your own key (chaves geridas pelo cliente) com serviços de mídia](concept-use-customer-managed-keys-byok.md)

Para obter mais informações sobre serviços fidedignos da Microsoft, consulte [firewalls de armazenamento Configure Azure e redes virtuais.](../../storage/common/storage-network-security.md#trusted-microsoft-services)

## <a name="tutorials"></a>Tutoriais

Estes tutoriais incluem ambos os cenários acima mencionados.

- [Utilize o portal Azure para utilizar chaves geridas pelo cliente ou BYOK com Serviços de Mídia](tutorial-byok-portal.md)
- [Utilize chaves geridas pelo cliente ou BYOK com serviços de mídia REST API](tutorial-byok-postman.md).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o que as identidades geridas podem fazer por si e pelas suas aplicações Azure, consulte [as identidades geridas aZure AD](../../active-directory/managed-identities-azure-resources/overview.md).