---
title: Criar ou modificar um emparelhamento direto usando o PowerShell
titleSuffix: Azure
description: Criar ou modificar um emparelhamento direto usando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 69031550bdab1535213c78f81426fa76e8ea62ad
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774240"
---
# <a name="create-or-modify-a-direct-peering-using-powershell"></a>Criar ou modificar um emparelhamento direto usando o PowerShell

Este artigo descreve como criar um emparelhamento direto da Microsoft usando cmdlets do PowerShell e o modelo de implantação do Gerenciador de recursos. Este artigo também mostra como verificar o status do recurso, atualizá-lo ou excluí-lo e desprovisioná-lo.

Se preferir, você pode concluir este guia usando o [portal](howto-direct-portal.md).

## <a name="before-you-begin"></a>Antes de começar
* Examine [pré-requisitos](prerequisites.md) e [direcionamento direto de emparelhamento](walkthrough-direct-all.md) antes de começar a configuração.
* Caso você já tenha o emparelhamento direto com a Microsoft, que não é convertido em recursos do Azure, consulte [converter um emparelhamento direto herdado para recurso do Azure usando o PowerShell](howto-legacy-direct-powershell.md)

### <a name="working-with-azure-powershell"></a>Trabalhar com o Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Criar e provisionar um emparelhamento direto

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inicie sessão na sua conta do Azure e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name=direct-location></a>Obter a lista de locais de emparelhamento com suporte para emparelhamento direto
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name=create></a>Criar um emparelhamento direto
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name=get></a>Verificar emparelhamento direto
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify"></a>Modificar um emparelhamento direto
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="delete"></a>Desprovisionar um emparelhamento direto
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar o emparelhamento do Exchange usando o PowerShell](howto-exchange-powershell.md).
* [Converta um emparelhamento do Exchange herdado para o recurso do Azure usando o PowerShell](howto-legacy-exchange-powershell.md).

## <a name="additional-resources"></a>Recursos adicionais
Para obter descrições detalhadas dos parâmetros de executar o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para obter mais informações, visite [perguntas frequentes sobre emparelhamento da Internet](faqs.md)
