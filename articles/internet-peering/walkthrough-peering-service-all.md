---
title: Instruções para parceiros do Peering Service
titleSuffix: Azure
description: Instruções para parceiros do Peering Service
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d626d98613bd5d988b599d0980c885d7f73bb958
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720253"
---
# <a name="peering-service-partner-walkthrough"></a>Instruções para parceiros do Peering Service

Esta seção explica as etapas que um provedor precisa seguir para habilitar um emparelhamento direto para o serviço de emparelhamento.

## <a name="create-direct-peering-connection-for-peering-service"></a>Criar conexão de emparelhamento direto para o serviço de emparelhamento
Os provedores de serviços podem expandir seu alcance geográfico criando um novo emparelhamento direto que dá suporte ao serviço de emparelhamento. Para fazer isso,
1. Se tornar um parceiro de serviço de emparelhamento, se ainda não estiver
1. Siga as instruções para [criar ou modificar um emparelhamento direto usando o portal](howto-direct-portal.md). Verifique se ele atende aos requisitos de alta disponibilidade.
1. Em seguida, siga as etapas para [habilitar o serviço de emparelhamento em um emparelhamento direto usando o portal](howto-peering-service-portal.md).

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>Utilize a conexão de peering direto legado para o Serviço de Peering
Se você tiver um emparelhamento direto herdado que deseja usar para dar suporte ao serviço de emparelhamento,
1. Torne-se um parceiro de serviço de emparelhamento, se ainda não estiver.
1. Siga as instruções para [converter um emparelhamento direto herdado para o recurso do Azure usando o portal](howto-legacy-direct-portal.md). Se necessário, solicite circuitos adicionais para atender aos requisitos de alta disponibilidade.
1. Em seguida, siga as etapas para [habilitar o serviço de emparelhamento em um emparelhamento direto usando o portal](howto-peering-service-portal.md).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a [política de emparelhamento](https://peering.azurewebsites.net/peering).
* Para saber mais sobre as etapas para configurar o emparelhamento direto com a Microsoft, siga [a explicação do emparelhamento direto](walkthrough-direct-all.md).
* Para saber mais sobre as etapas para configurar o emparelhamento do Exchange com a Microsoft, siga [a explicação do emparelhamento do Exchange](walkthrough-exchange-all.md).