---
title: Traga a sua própria chave (chaves geridas pelo cliente)
description: Pode utilizar uma chave gerida pelo cliente (isto é, traga a sua própria chave) com os Media Services.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 4564e28f76aebe7f708c2b6f68903fe67bcefe26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101698863"
---
# <a name="bring-your-own-key-customer-managed-keys-with-media-services"></a>Traga a sua própria chave (chaves geridas pelo cliente) com serviços de mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bring Your Own Key (BYOK) é uma iniciativa da Azure wide para ajudar os clientes a mover as suas cargas de trabalho para a nuvem. As chaves geridas pelo cliente permitem que os clientes cumpram as normas de conformidade da indústria e melhorem o isolamento do arrendatário de um serviço. Dar aos clientes o controlo das chaves de encriptação é uma forma de minimizar o acesso e o controlo desnecessários e criar confiança nos serviços da Microsoft.

## <a name="keys-and-key-management"></a>Chaves e gestão de chaves

Pode utilizar a sua própria chave com serviços de mídia quando utilizar a API dos Media Services 2020-05-01. Uma chave de conta padrão é criada para todas as contas que é encriptada por uma chave de sistema detida pelos Media Services. Quando usa a sua própria chave, a chave da conta é encriptada com a sua chave. As chaves de conteúdo são encriptadas pela chave da conta. Os urls de símis e as teclas de validação de fichas de JobInputHttp e as teclas de validação de fichas simétricas também estão encriptadas.

:::image type="content" source="./media/customer-managed-key/customer-managed-key.svg" alt-text="Uma chave gerida pelo cliente substitui uma chave gerida pelo sistema":::

Os Serviços de Comunicação Social utilizam a identidade gerida da conta Media Services para ler a sua chave a partir de um Cofre-Chave que lhe pertence. Os Serviços de Comunicação Social exigem que o Cofre-Chave esteja na mesma região que a conta, e que tenha uma proteção de eliminação e purga ativada.

A sua chave pode ser uma chave 2048, 3072 ou uma chave RSA 4096, e ambas as teclas de HSM e software são suportadas.

> [!NOTE]
> As chaves CE não são suportadas.

Pode especificar um nome chave e uma versão chave, ou apenas um nome chave. Quando utilizar apenas um nome chave, os Serviços de Mídia utilizarão a versão chave mais recente. Novas versões das chaves do cliente são detetadas automaticamente e a chave da conta é reencrimada.

> [!WARNING]
> O Media Services monitoriza o acesso à chave do cliente. Se a chave do cliente se tornar inacessível (por exemplo, a chave foi eliminada ou o Cofre-Chave foi eliminado ou a concessão de acesso foi removida), os Serviços de Comunicação social transitarão a conta para o Estado Inacessível da Chave cliente (desativando efetivamente a conta). No entanto, a conta pode ser eliminada neste estado. As únicas operações suportadas são conta GET, LIST e DELETE; todos os outros pedidos (codificação, streaming, e assim por diante) falharão até que o acesso à chave de conta seja restaurado.

## <a name="double-encryption"></a>Encriptação dupla

Os Serviços de Mídia suportam automaticamente a dupla encriptação. Para os dados em repouso, a primeira camada de encriptação utiliza uma chave gerida pelo cliente ou uma chave gerida pela Microsoft dependendo da `AccountEncryption` definição da conta.  A segunda camada de encriptação para dados em repouso é fornecida automaticamente usando uma chave gerida pela Microsoft separada. Para saber mais sobre a dupla encriptação, consulte [a encriptação dupla Azure.](../../security/fundamentals/double-encryption.md)

> [!NOTE]
> A dupla encriptação é ativada automaticamente na conta dos Serviços de Mídia. No entanto, é necessário configurar a chave gerida pelo cliente e duplicar a encriptação na sua conta de armazenamento separadamente. Ver, [encriptação storege.](../../storage/common/storage-service-encryption.md)

## <a name="tutorials"></a>Tutoriais

- [Utilize o portal Azure para utilizar chaves geridas pelo cliente ou BYOK com Serviços de Mídia](tutorial-byok-portal.md)
- [Utilize chaves geridas pelo cliente ou BYOK com serviços de mídia REST API](tutorial-byok-postman.md).

## <a name="next-steps"></a>Passos seguintes

[Proteja o seu conteúdo com encriptação dinâmica dos Media Services](content-protection-overview.md)