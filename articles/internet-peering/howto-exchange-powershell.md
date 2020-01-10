---
title: Criar ou modificar um emparelhamento do Exchange usando o PowerShell
titleSuffix: Azure
description: Criar ou modificar um emparelhamento do Exchange usando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 4fd7d345e5efbe6e4e86e5bb410e2df4dd917047
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774409"
---
# <a name="create-or-modify-an-exchange-peering-using-powershell"></a>Criar ou modificar um emparelhamento do Exchange usando o PowerShell

Este artigo descreve como criar um emparelhamento do Microsoft Exchange usando cmdlets do PowerShell e o modelo de implantação do Gerenciador de recursos. Este artigo também mostra como verificar o status do recurso, atualizá-lo ou excluí-lo e desprovisioná-lo.

Se preferir, você pode concluir este guia usando o [portal](howto-exchange-portal.md).

## <a name="before-you-begin"></a>Antes de começar
* Examine os [pré-requisitos](prerequisites.md) e o [emparelhamento do Exchange](walkthrough-exchange-all.md) com o Walkthrough antes de começar a configuração.
* Caso você já tenha emparelhamentos do Exchange com a Microsoft, que não são convertidos em recursos do Azure, consulte [converter um emparelhamento do Exchange herdado para recurso do Azure usando o PowerShell](howto-legacy-exchange-powershell.md)

### <a name="working-with-azure-powershell"></a>Trabalhar com o Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Criar e provisionar um emparelhamento do Exchange

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inicie sessão na sua conta do Azure e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name=exchange-location></a>Obter a lista de locais de emparelhamento com suporte para o emparelhamento do Exchange
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name=create></a>Criar um emparelhamento do Exchange
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name=get></a>Obter emparelhamento do Exchange
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify"></a>Modificar um emparelhamento do Exchange
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name=delete></a>Desprovisionar um emparelhamento do Exchange

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um emparelhamento direto usando o PowerShell](howto-direct-powershell.md)
* [Converter um emparelhamento direto herdado para o recurso do Azure usando o PowerShell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais
Para obter descrições detalhadas dos parâmetros de executar o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para obter mais informações, visite [perguntas frequentes sobre emparelhamento da Internet](faqs.md)
