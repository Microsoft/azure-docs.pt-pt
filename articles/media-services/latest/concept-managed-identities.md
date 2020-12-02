---
title: Identidades geridas e armazenamento fidedigno com serviços de mídia
description: Os Serviços de Mídia podem ser utilizados com identidades geridas para permitir o armazenamento fidedigno.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: inhenkel
ms.openlocfilehash: d0811e8f9183ee334d413bcad69f2c7b32023be3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499361"
---
# <a name="managed-identities-and-trusted-storage-with-media-services"></a>Identidades geridas e armazenamento fidedigno com serviços de mídia

Os Serviços de Mídia podem ser utilizados com [identidades geridas](../../active-directory/managed-identities-azure-resources/overview.md) para permitir o armazenamento fidedigno. Ao criar uma conta de Serviços de Comunicação, deve associá-la a uma conta de armazenamento. Os Serviços de Mídia podem aceder a essa conta de armazenamento utilizando a autenticação do sistema. Os Serviços de Comunicação Social validam que a conta de Serviços de Mídia e a conta de armazenamento estão na mesma subscrição e valida que o utilizador que acrescenta a associação tem acesso à conta de armazenamento com o Azure Resource Manager RBAC.

## <a name="trusted-storage"></a>Armazenamento fidedigno

No entanto, se pretender utilizar uma firewall para proteger a sua conta de armazenamento, tem de utilizar a autenticação de identidade gerida. Permite que os Serviços de Comunicação Social acedam à conta de armazenamento que foi configurada com uma firewall ou uma restrição VNet através de acesso de armazenamento fidedigno.  Para obter mais informações sobre serviços fidedignos da Microsoft, consulte [firewalls de armazenamento Configure Azure e redes virtuais.](../../storage/common/storage-network-security.md#trusted-microsoft-services)

## <a name="media-services-managed-identity-scenarios"></a>Os serviços de comunicação social geriram cenários de identidade

Existem atualmente dois cenários em que a identidade gerida pode ser utilizada com os Serviços de Comunicação Social:

- Utilize a identidade gerida da conta dos Serviços de Comunicação social para aceder às contas de armazenamento.

- Utilize a identidade gerida da conta Media Services para aceder ao Key Vault para aceder às chaves do cliente.

As duas secções seguintes descrevem as diferenças nos dois cenários.

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>Utilize a identidade gerida da conta dos Serviços de Comunicação social para aceder a contas de armazenamento

1. Criar uma conta de Serviços de Comunicação com uma identidade gerida.
1. Conceda o acesso principal de identidade gerida a uma conta de armazenamento que possui.
1. Os Serviços de Mídia podem então aceder à conta de Armazenamento em seu nome utilizando a identidade gerida.

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>Utilize a identidade gerida da conta Dos Serviços de Comunicação social para aceder ao Cofre chave para aceder às chaves dos clientes

1. Criar uma conta de Serviços de Comunicação com uma identidade gerida.
1. Concede o acesso principal de identidade gerido a um Cofre-Chave que possui.
1. Configure a conta dos Serviços de Comunicação para utilizar a encriptação da conta baseada na chave do cliente.
1. Os Serviços de Comunicação Social acedem ao Key Vault em seu nome usando a identidade gerida.

Para obter mais informações sobre as chaves geridas pelo cliente e o Key Vault, consulte [Bring your own key (chaves geridas pelo cliente) com serviços de mídia](concept-use-customer-managed-keys-byok.md)

## <a name="tutorials"></a>Tutoriais

Estes tutoriais incluem ambos os cenários acima mencionados.

- [Utilize o portal Azure para utilizar chaves geridas pelo cliente ou BYOK com Serviços de Mídia](tutorial-byok-portal.md)
- [Utilize chaves geridas pelo cliente ou BYOK com serviços de mídia REST API](tutorial-byok-postman.md).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o que as identidades geridas podem fazer por si e pelas suas aplicações Azure, consulte [as identidades geridas aZure AD](../../active-directory/managed-identities-azure-resources/overview.md).