---
title: Criar ou modificar um peering do Exchange com o portal
titleSuffix: Azure
description: Criar ou modificar um peering do Exchange com o portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 2c186decf68d167ab2c5ab7696c2dfb51d77a071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774578"
---
# <a name="create-or-modify-an-exchange-peering-using-the-portal"></a>Criar ou modificar um peering do Exchange com o portal

Este artigo descreve como criar um microsoft Exchange peering usando o portal. Este artigo também mostra como verificar o estado do recurso, atualizá-lo ou eliminá-lo e desfortá-lo.

Se preferir, pode completar este guia utilizando o [PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) e percorra o [persado](walkthrough-exchange-all.md) do Exchange antes de iniciar a configuração.
* Caso já tenha perspares exchange com a Microsoft, que não são convertidos para recursos Azure, consulte a [Converta um legado Exchange peering para](howto-legacy-exchange-portal.md) o recurso Azure usando o portal

## <a name="create-and-provision-an-exchange-peering"></a>Criar e fornecer um persparo de troca

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Inscreva-se no portal e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Criar um persparo de troca

Pode criar um novo pedido de peering utilizando recurso **Peering.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Lançar recurso e configurar definições básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurar ligações e submeter
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Verifique um peering de troca
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modificar um peering de troca
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Deprovisionamento de um persparo de troca
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um peering Direto com o portal](howto-direct-portal.md)
* [Converter um peering Direto legado para o recurso do Azure com o portal](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para mais informações, visite [faQs de observação de Internet](faqs.md)
