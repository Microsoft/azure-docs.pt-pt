---
title: Identidades geridas
description: Os Serviços de Comunicação Social podem ser utilizados com identidades geridas azure.
keywords: ''
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 0bbfb54d6ba7483e96633bdf05bb580e5517d216
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277747"
---
# <a name="managed-identities"></a>Identidades geridas

Um desafio comum para os desenvolvedores é a gestão de segredos e credenciais para garantir a comunicação entre diferentes serviços. No Azure, identidades geridas eliminam a necessidade de os desenvolvedores terem de gerir credenciais fornecendo uma identidade para o recurso Azure em Azure AD e usando-o para obter fichas do Azure Ative Directory (Azure AD).

Existem atualmente dois cenários em que as identidades geridas podem ser utilizadas com os Serviços de Comunicação Social:

- Utilize a identidade gerida da conta dos Serviços de Comunicação social para aceder às contas de armazenamento.

- Utilize a identidade gerida da conta Media Services para aceder ao Key Vault para aceder às chaves do cliente.

As duas secções seguintes descrevem os passos dos dois cenários.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>Utilize a identidade gerida da conta dos Serviços de Comunicação social para aceder a contas de armazenamento

1. Criar uma conta de Serviços de Comunicação com uma identidade gerida.
1. Conceda o acesso principal de identidade gerida a uma conta de armazenamento que possui.
1. Os Serviços de Mídia podem então aceder à conta de armazenamento em seu nome utilizando a identidade gerida.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>Utilize a identidade gerida da conta Dos Serviços de Comunicação social para aceder ao Cofre chave para aceder às chaves dos clientes

1. Criar uma conta de Serviços de Comunicação com uma identidade gerida.
1. Concede o acesso principal de identidade gerido a um Cofre-Chave que possui.
1. Configure a conta dos Serviços de Comunicação para utilizar a encriptação da conta baseada na chave do cliente.
1. Os Serviços de Comunicação Social acedem ao Key Vault em seu nome usando a identidade gerida.

Para obter mais informações sobre as chaves geridas pelo cliente e o Key Vault, consulte [Bring your own key (chaves geridas pelo cliente) com serviços de mídia](concept-use-customer-managed-keys-byok.md)

## <a name="tutorials"></a>Tutoriais

Estes tutoriais incluem ambos os cenários acima mencionados.

- [Utilize o portal Azure para utilizar chaves geridas pelo cliente ou BYOK com Serviços de Mídia](security-customer-managed-keys-portal-tutorial.md)
- [Utilize chaves geridas pelo cliente ou BYOK com serviços de mídia REST API](security-customer-managed-keys-rest-postman-tutorial.md).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o que as identidades geridas podem fazer por si e pelas suas aplicações Azure, consulte [as identidades geridas aZure AD](../../active-directory/managed-identities-azure-resources/overview.md).
