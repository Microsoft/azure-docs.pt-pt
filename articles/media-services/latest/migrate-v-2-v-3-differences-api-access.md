---
title: Acesso à API dos Serviços de Comunicação Social V2 vs v3
description: Este artigo descreve as diferenças de acesso da API entre a Azure Media Services V2 a V3.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 831bc737fbd54685cdc8358642bc37e7b2df2c42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98953721"
---
# <a name="api-access-differences-between-azure-media-services-v2-to-v3-api"></a>Diferenças de acesso da API entre Azure Media Services V2 a V3 API

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-2.svg)

Este artigo descreve as diferenças de acesso da API entre a Azure Media Services V2 a V3.

## <a name="api-access"></a>Acesso API

Todas as contas dos Serviços de Comunicação Social terão acesso à API V3. No entanto, recomendamos vivamente o desenvolvimento da migração numa nova conta antes de aplicar o código atualizado a uma conta V2 existente. Isto porque as entidades V3 não são compatíveis com v2. Algumas entidades V2 como ativos são compatíveis com v3.
Pode continuar a usar as contas existentes se não misturar as APIs V2 e V3 e tentar voltar a V2, mas isso é desencorajado.

O acesso à API V2 estará disponível até que seja reformado em 2024.

Enquanto está a migrar, pode criar uma conta V3 que ainda tem acesso ao V2.  A criação da conta pode ser feita com:

- A API REST e a versão mais antiga
- Selecionando a caixa de verificação no portal.

> [!div class="mx-imgBorder"]
> [![criação de conta no portal ](./media/migration-guide/v-3-v-2-access-account-creation-small.png)](./media/migration-guide/v-3-v-2-access-account-creation.png#lightbox)

Todos os .NET, CLI e outros SDKs terão como alvo as mais recentes API 2020-05-01, pelo que encontre ou configuure as versões API mais antigas.

> [!NOTE]
> As novas contas criadas com a API 2020-05-01 não podem utilizar APIs V2.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
