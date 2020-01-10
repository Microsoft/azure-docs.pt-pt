---
title: Criar ou modificar um emparelhamento direto usando o portal
titleSuffix: Azure
description: Criar ou modificar um emparelhamento direto usando o portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 67a4944082d3ebc14fd564eedee0310afe6e3ff5
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775332"
---
# <a name="create-or-modify-a-direct-peering-using-the-portal"></a>Criar ou modificar um emparelhamento direto usando o portal

Este artigo descreve como criar um emparelhamento direto da Microsoft usando o Portal. Este artigo também mostra como verificar o status do recurso, atualizá-lo ou excluí-lo e desprovisioná-lo.

Se preferir, você pode concluir este guia usando o [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Examine [pré-requisitos](prerequisites.md) e [direcionamento direto de emparelhamento](walkthrough-direct-all.md) antes de começar a configuração.
* Caso você já tenha o emparelhamento direto com a Microsoft, que não é convertido em recursos do Azure, consulte [converter um emparelhamento direto herdado para o recurso do Azure usando o portal](howto-legacy-direct-portal.md)

## <a name="create-and-provision-a-direct-peering"></a>Criar e provisionar um emparelhamento direto

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Entre no portal e selecione sua assinatura
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Criar um emparelhamento direto

Você pode criar uma nova solicitação de emparelhamento usando o recurso de **emparelhamento** .

#### <a name="launch-resource-and-configure-basic-settings"></a>Iniciar recurso e definir configurações básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurar conexões e enviar
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name=get></a>Verificar emparelhamento direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify"></a>Modificar um emparelhamento direto
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="delete"></a>Desprovisionar um emparelhamento direto
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Passos seguintes

* [Crie ou modifique o emparelhamento do Exchange usando o portal](howto-exchange-portal.md).
* [Converta um emparelhamento do Exchange herdado para o recurso do Azure usando o portal](howto-legacy-exchange-portal.md).

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, visite [perguntas frequentes sobre emparelhamento da Internet](faqs.md)
