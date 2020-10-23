---
title: Traga a sua própria chave (chaves geridas pelo cliente) com serviços de mídia
description: Pode utilizar uma chave gerida pelo cliente (isto é, traga a sua própria chave) com os Media Services.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: a89ff56eb9e0f0a29b5b1fed7543c5f718425e51
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326038"
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

## <a name="tutorial"></a>Tutorial
Experimente as [teclas geridas pelo cliente tutorial ou traga a sua própria chave (BYOK) com os Serviços](tutorial-byok.md) de Comunicação Social para aprender a configurar e utilizar as chaves geridas pelo cliente com a API do Postman e da Azure REST.

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Use chaves geridas pelo cliente ou traga a sua própria chave (BYOK) com serviços de mídia](tutorial-byok.md)
