---
title: Converter um peering Direto legado para o recurso do Azure com o portal
titleSuffix: Azure
description: Converter um peering Direto legado para o recurso do Azure com o portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a40c7bbc9f37135814b7bba3396d368faf97a166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775202"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-the-portal"></a>Converter um peering do Exchange legado para o recurso do Azure com o portal

Este artigo descreve como converter um legado existente Exchange peering para recurso Azure usando o portal.

Se preferir, pode completar este guia utilizando o [PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) e percorra o [persado](walkthrough-exchange-all.md) do Exchange antes de iniciar a configuração.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Converter um peering do Exchange legado para o recurso do Azure

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Inscreva-se no portal e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Converter o legado Exchange peering

Pode converter ligações de observação de legados utilizando recurso **Peering.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Lançar recurso e configurar definições básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurar ligações e submeter
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Verificar o peering de troca
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Recursos adicionais

Para mais informações, visite [faQs de observação de Internet](faqs.md)

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um peering do Exchange com o portal](howto-exchange-portal.md)
