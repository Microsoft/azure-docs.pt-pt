---
title: Criar ou modificar um peering direto usando powerShell
titleSuffix: Azure
description: Criar ou modificar um peering direto usando powerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 69031550bdab1535213c78f81426fa76e8ea62ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774240"
---
# <a name="create-or-modify-a-direct-peering-using-powershell"></a>Criar ou modificar um peering direto usando powerShell

Este artigo descreve como criar um microsoft direct peering usando cmdlets PowerShell e o modelo de implementação do Gestor de Recursos. Este artigo também mostra como verificar o estado do recurso, atualizá-lo ou eliminá-lo e desfortá-lo.

Se preferir, pode completar este guia utilizando o [portal](howto-direct-portal.md).

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) e [o direct peering walkthrough](walkthrough-direct-all.md) antes de iniciar a configuração.
* Caso já tenha o directing peering com a Microsoft, que não são convertidos para recursos Azure, consulte a [Converta um legado Direct peering para o recurso Azure usando o PowerShell](howto-legacy-direct-powershell.md)

### <a name="working-with-azure-powershell"></a>Trabalhar com a Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Criar e fornecer um peering direto

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inscreva-se na sua conta Azure e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>Obtenha a lista de locais de observação suportados para o peering direto
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Criar um olhar direto
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificar o peering direto
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modificar um olhar direto
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Desprovisionamento de um peering direto
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar o peering de troca utilizando powerShell](howto-exchange-powershell.md).
* [Converta um legado Exchange peering para o recurso Azure usando powerShell](howto-legacy-exchange-powershell.md).

## <a name="additional-resources"></a>Recursos adicionais
Pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para mais informações, visite [faQs de observação de Internet](faqs.md)
