---
title: Criar ou modificar um espreitamento direto utilizando o PowerShell
titleSuffix: Azure
description: Criar ou modificar um espreitamento direto utilizando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: be4bab10d369fcefe626bbe890acfff7fc7004b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079069"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>Criar ou modificar um espreitamento direto utilizando o PowerShell

Este artigo descreve como criar um microsoft direct peering usando cmdlets PowerShell e o modelo de implementação do Azure Resource Manager. Este artigo também mostra como verificar o estado do recurso, atualizá-lo ou eliminá-lo e desprovisioná-lo.

Se preferir, pode completar este guia utilizando o [portal](howto-direct-portal.md)Azure .

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) e o [passo de observação direto](walkthrough-direct-all.md) antes de iniciar a configuração.
* Se já tem ligações diretas de observação com a Microsoft que não são convertidas em recursos Azure, consulte [Converter um legado Direta a espreitar para um recurso Azure utilizando o PowerShell](howto-legacy-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Trabalhar com a Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Criar e providenciar um persimento direto

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inscreva-se na sua conta Azure e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>Obtenha a lista de locais de observação suportados para olhando direto
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Criar um espreitamento direto
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificar o espreitamento direto
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modifique um espreitamento direto
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Desprovisionar um olhar direto
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar o persto cambial utilizando o PowerShell](howto-exchange-powershell.md)
* [Converter um legado Exchange olhando para um recurso Azure usando PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais
Pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para obter mais informações, consulte [as PERGUNTAS Frequentes de Observação da Internet.](faqs.md)
