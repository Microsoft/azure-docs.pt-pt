---
title: Converter um emparelhamento direto herdado para o recurso do Azure usando o portal
titleSuffix: Azure
description: Converter um emparelhamento direto herdado para o recurso do Azure usando o portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a40c7bbc9f37135814b7bba3396d368faf97a166
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775202"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-the-portal"></a>Converter um emparelhamento do Exchange herdado para o recurso do Azure usando o portal

Este artigo descreve como converter um emparelhamento do Exchange herdado existente para o recurso do Azure usando o Portal.

Se preferir, você pode concluir este guia usando o [PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Examine os [pré-requisitos](prerequisites.md) e o [emparelhamento do Exchange](walkthrough-exchange-all.md) com o Walkthrough antes de começar a configuração.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Converter um emparelhamento do Exchange herdado para o recurso do Azure

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Entre no portal e selecione sua assinatura
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Converter o emparelhamento do Exchange herdado

Você pode converter conexões de emparelhamento herdadas usando o recurso de **emparelhamento** .

#### <a name="launch-resource-and-configure-basic-settings"></a>Iniciar recurso e definir configurações básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurar conexões e enviar
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name=get></a>Verificar o emparelhamento do Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, visite [perguntas frequentes sobre emparelhamento da Internet](faqs.md)

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um emparelhamento do Exchange usando o portal](howto-exchange-portal.md)
