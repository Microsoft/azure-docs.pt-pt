---
title: Criar ou modificar um persparo de troca utilizando o PowerShell
titleSuffix: Azure
description: Criar ou modificar um persparo de troca utilizando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 23c905f148da614c7785b61b76abed191206cd90
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678602"
---
# <a name="create-or-modify-an-exchange-peering-by-using-powershell"></a>Criar ou modificar um persparo de troca utilizando o PowerShell

Este artigo descreve como criar um microsoft exchange peering usando cmdlets PowerShell e o modelo de implementação do Gestor de Recursos. Este artigo também mostra como verificar o estado do recurso, atualizá-lo ou eliminá-lo e desfortá-lo.

Se preferir, pode completar este guia utilizando o [portal](howto-exchange-portal.md)Azure .

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) e o ["Exchange peering walkthrough"](walkthrough-exchange-all.md) antes de iniciar a configuração.
* Se já tem percevejos de Exchange com a Microsoft que não são convertidos em recursos Azure, consulte [Converter um legado Exchange espreitando um recurso Azure utilizando powerShell](howto-legacy-exchange-powershell.md).

### <a name="work-with-azure-powershell"></a>Trabalhar com a Azure PowerShell
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

* [Criar ou modificar um peering direto utilizando o PowerShell](howto-direct-powershell.md)
* [Converta um legado De espreitar diretamente para um recurso Azure usando o PowerShell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais
Pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para mais informações, consulte [as FAQs de observação da Internet](faqs.md).
