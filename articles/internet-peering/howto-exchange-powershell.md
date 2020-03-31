---
title: Criar ou modificar um persparo de troca utilizando powerShell
titleSuffix: Azure
description: Criar ou modificar um persparo de troca utilizando powerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 4fd7d345e5efbe6e4e86e5bb410e2df4dd917047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774409"
---
# <a name="create-or-modify-an-exchange-peering-using-powershell"></a>Criar ou modificar um persparo de troca utilizando powerShell

Este artigo descreve como criar um microsoft exchange peering usando cmdlets PowerShell e o modelo de implementação do Gestor de Recursos. Este artigo também mostra como verificar o estado do recurso, atualizá-lo ou eliminá-lo e desfortá-lo.

Se preferir, pode completar este guia utilizando o [portal](howto-exchange-portal.md).

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) e percorra o [persado](walkthrough-exchange-all.md) do Exchange antes de iniciar a configuração.
* Caso já tenha perceções de Troca com a Microsoft, que não são convertidas em recursos Azure, consulte a [Converta um legado Exchange peering para o recurso Azure usando o PowerShell](howto-legacy-exchange-powershell.md)

### <a name="working-with-azure-powershell"></a>Trabalhar com a Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Criar e fornecer um persparo de troca

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inscreva-se na sua conta Azure e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>Obtenha a lista de locais de observação suportados para perspinagem de exchange
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Criar um persparo de troca
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>Obter persping de troca
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modificar um peering de troca
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>Deprovisionamento de um persparo de troca

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um peering direto usando powerShell](howto-direct-powershell.md)
* [Converter um legado De espreitar diretamente para o recurso Azure usando powerShell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais
Pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para mais informações, visite [faQs de observação de Internet](faqs.md)
