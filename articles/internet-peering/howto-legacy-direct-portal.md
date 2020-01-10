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
ms.openlocfilehash: 9900414d38bd597d08a80d15e908228c06ce06ea
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775059"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-the-portal"></a>Converter um emparelhamento direto herdado para o recurso do Azure usando o portal

Este artigo descreve como converter um emparelhamento direto herdado existente para recursos do Azure usando o Portal.

Se preferir, você pode concluir este guia usando o [PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Examine [pré-requisitos](prerequisites.md) e [direcionamento direto de emparelhamento](walkthrough-direct-all.md) antes de começar a configuração.


## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Converter o emparelhamento direto herdado para o recurso do Azure

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Entre no portal e selecione sua assinatura
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Converter emparelhamento direto herdado

Você pode converter conexões de emparelhamento herdadas usando o recurso de **emparelhamento** .

#### <a name="launch-resource-and-configure-basic-settings"></a>Iniciar recurso e definir configurações básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurar conexões e enviar
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name=get></a>Verificar emparelhamento direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, visite [perguntas frequentes sobre emparelhamento da Internet](faqs.md)

## <a name="next-steps"></a>Passos seguintes

* [Crie ou modifique um emparelhamento direto usando o portal](howto-direct-portal.md).
