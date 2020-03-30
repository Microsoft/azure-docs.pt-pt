---
title: Criar ou modificar um peering Direto com o portal
titleSuffix: Azure
description: Criar ou modificar um peering Direto com o portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 67a4944082d3ebc14fd564eedee0310afe6e3ff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775332"
---
# <a name="create-or-modify-a-direct-peering-using-the-portal"></a>Criar ou modificar um peering Direto com o portal

Este artigo descreve como criar um microsoft direct peering usando o portal. Este artigo também mostra como verificar o estado do recurso, atualizá-lo ou eliminá-lo e desfortá-lo.

Se preferir, pode completar este guia utilizando o [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) e [o direct peering walkthrough](walkthrough-direct-all.md) antes de iniciar a configuração.
* Caso já tenha o directing peering com a Microsoft, que não são convertidos para recursos Azure, consulte a [Converta um legado Direct peering para o recurso Azure usando o portal](howto-legacy-direct-portal.md)

## <a name="create-and-provision-a-direct-peering"></a>Criar e fornecer um peering direto

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Inscreva-se no portal e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Criar um olhar direto

Pode criar um novo pedido de peering utilizando recurso **Peering.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Lançar recurso e configurar definições básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurar ligações e submeter
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificar o peering direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modificar um olhar direto
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Desprovisionamento de um peering direto
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar o peering de troca utilizando o portal](howto-exchange-portal.md).
* [Converter um legado Exchange peering para o recurso Azure usando o portal](howto-legacy-exchange-portal.md).

## <a name="additional-resources"></a>Recursos adicionais

Para mais informações, visite [faQs de observação de Internet](faqs.md)
