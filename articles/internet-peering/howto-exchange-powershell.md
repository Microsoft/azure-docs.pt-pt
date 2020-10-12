---
title: Criar ou modificar um persco de troca utilizando o PowerShell
titleSuffix: Azure
description: Criar ou modificar um persco de troca utilizando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 59d098f1ff0f78e37601544619c603b776e0cf22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89071759"
---
# <a name="create-or-modify-an-exchange-peering-by-using-powershell"></a>Criar ou modificar um persco de troca utilizando o PowerShell

Este artigo descreve como criar um microsoft exchange espreitando usando cmdlets PowerShell e o modelo de implementação do Gestor de Recursos. Este artigo também mostra como verificar o estado do recurso, atualizá-lo ou eliminá-lo e desprovisioná-lo.

Se preferir, pode completar este guia utilizando o [portal](howto-exchange-portal.md)Azure .

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) e o [Persimento do Persimento cambial](walkthrough-exchange-all.md) antes de iniciar a configuração.
* Se já tem perscos de Troca com a Microsoft que não são convertidos para recursos Azure, consulte [Converter um legado Exchange olhando para um recurso Azure utilizando o PowerShell](howto-legacy-exchange-powershell.md).

### <a name="work-with-azure-powershell"></a>Trabalhar com a Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Criar e providenciar um persto cambial

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inscreva-se na sua conta Azure e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>Obtenha a lista de locais de observação suportados para persiagens de câmbio
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Criar um perscruta de intercâmbio
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>Obter Peering Exchange
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modificar um persco de troca
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>Deprovisionar um perscruta de trocas

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um espreitamento direto utilizando o PowerShell](howto-direct-powershell.md)
* [Converter um legado Espreitar diretamente para um recurso Azure utilizando o PowerShell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais
Pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para obter mais informações, consulte [as PERGUNTAS Frequentes de Observação da Internet.](faqs.md)
