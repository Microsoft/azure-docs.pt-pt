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
ms.openlocfilehash: 87f52bef07644370c268168c984a452d5ae3f9bd
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279464"
---
# <a name="trusted-storage-for-media-services"></a>Armazenamento fidedigno para Serviços de Mídia

Ao criar uma conta de Serviços de Comunicação, deve associá-la a uma conta de armazenamento. Os Serviços de Mídia podem aceder a essa conta de armazenamento utilizando a autenticação do sistema ou a autenticação de Identidade Gerida. Os Serviços de Comunicação Social validam que a conta de Serviços de Mídia e a conta de armazenamento estão na mesma subscrição e valida que o utilizador que acrescenta a associação tem acesso à conta de armazenamento com o Azure Resource Manager RBAC.

>[!NOTE]
>O armazenamento fidedigno só está disponível na API, e não está atualmente ativado no portal Azure.

## <a name="trusted-storage-with-a-firewall"></a>Armazenamento confiável com uma firewall

No entanto, se pretender utilizar uma firewall para proteger a sua conta de armazenamento e ativar o armazenamento fidedigno, a autenticação [Managed Identities](concept-managed-identities.md) é a opção preferida. Permite que os Serviços de Comunicação Social acedam à conta de armazenamento que foi configurada com uma firewall ou uma restrição VNet através de acesso de armazenamento fidedigno.

## <a name="tutorial"></a>Tutorial

Você pode saber mais sobre como ativar o armazenamento fidedigno com o tutorial [de armazenamento fidedigno dos Media Services.](security-trusted-storage-rest-tutorial.md)

> [!NOTE]
> É necessário conceder o acesso do Contribuinte de Dados do Blob de Armazenamento de Identidade Gerido da AMS para que os Serviços de Mídia possam ler e escrever na conta de armazenamento.  Conceder o papel genérico de Contribuinte não funcionará, uma vez que não permite as permissões corretas no plano de dados.

## <a name="further-reading"></a>Leitura adicional

Para compreender os métodos de criação de armazenamento fidedignos com identidades geridas, leia [Identidades Geridas e Serviços de Media.](concept-managed-identities.md)

Para obter mais informações sobre serviços fidedignos da Microsoft, consulte [firewalls de armazenamento Configure Azure e redes virtuais.](../../storage/common/storage-network-security.md#trusted-microsoft-services)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o que as identidades geridas podem fazer por si e pelas suas aplicações Azure, consulte [as identidades geridas aZure AD](../../active-directory/managed-identities-azure-resources/overview.md).
