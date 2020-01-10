---
title: Criar ou modificar um emparelhamento do Exchange usando o portal
titleSuffix: Azure
description: Criar ou modificar um emparelhamento do Exchange usando o portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 2c186decf68d167ab2c5ab7696c2dfb51d77a071
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774578"
---
# <a name="create-or-modify-an-exchange-peering-using-the-portal"></a>Criar ou modificar um emparelhamento do Exchange usando o portal

Este artigo descreve como criar um emparelhamento do Microsoft Exchange usando o Portal. Este artigo também mostra como verificar o status do recurso, atualizá-lo ou excluí-lo e desprovisioná-lo.

Se preferir, você pode concluir este guia usando o [PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Examine os [pré-requisitos](prerequisites.md) e o [emparelhamento do Exchange](walkthrough-exchange-all.md) com o Walkthrough antes de começar a configuração.
* Caso você já tenha emparelhamentos do Exchange com a Microsoft, que não são convertidos em recursos do Azure, consulte [converter um emparelhamento do Exchange herdado para recurso do Azure usando o portal](howto-legacy-exchange-portal.md)

## <a name="create-and-provision-an-exchange-peering"></a>Criar e provisionar um emparelhamento do Exchange

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Entre no portal e selecione sua assinatura
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Criar um emparelhamento do Exchange

Você pode criar uma nova solicitação de emparelhamento usando o recurso de **emparelhamento** .

#### <a name="launch-resource-and-configure-basic-settings"></a>Iniciar recurso e definir configurações básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurar conexões e enviar
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name=get></a>Verificar um emparelhamento do Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify"></a>Modificar um emparelhamento do Exchange
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="delete"></a>Desprovisionar um emparelhamento do Exchange
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um emparelhamento direto usando o portal](howto-direct-portal.md)
* [Converter um emparelhamento direto herdado para o recurso do Azure usando o portal](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, visite [perguntas frequentes sobre emparelhamento da Internet](faqs.md)
