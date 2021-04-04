---
title: Ativar o Serviço de Estojo Azure num esprevamento direto utilizando o PowerShell
titleSuffix: Azure
description: Ativar o Serviço de Estojo Azure num esprevamento direto utilizando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 98341fbbbcafb6aee938870c22050c6edec352ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079052"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>Ativar o Serviço de Estojo Azure num esprevamento direto utilizando o PowerShell

Este artigo descreve como ativar o [Serviço de Peering](overview-peering-service.md) Azure num esprevamento direto utilizando cmdlets PowerShell e o modelo de implementação do Gestor de Recursos Azure.

Se preferir, pode completar este guia utilizando o [portal](howto-peering-service-portal.md)Azure .

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) antes de iniciar a configuração.
* Escolha um espreitamento direto na sua subscrição para a qual pretende ativar o Serviço de Peering. Se não tiver um, converta um legado espreitando diretamente ou crie um novo olhar direto:
    * Para converter um legado Espreitar diretamente, siga as instruções em [Converter um legado Espreitar diretamente para um recurso Azure utilizando o PowerShell](howto-legacy-direct-powershell.md).
    * Para criar um novo espreitamento direto, siga as instruções em [Criar ou modifique um espreitamento direto utilizando o PowerShell](howto-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Trabalhar com a Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Ativar o Peering Service num peering Direto

### <a name="view-direct-peering"></a><a name= get></a>Ver persmo direto
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Ativar o espreitamento direto para o serviço de peering

Depois de ter um olhar direto no passo anterior, ative-o para o Serviço de Observação.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Modificar uma ligação de persimento direto

Se precisar de modificar as definições de ligação, consulte a secção "Modificar um esprevamento direto" em [Criar ou modificar um esprevamento direto utilizando o PowerShell](howto-direct-powershell.md).

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar o persto cambial utilizando o PowerShell](howto-exchange-powershell.md)
* [Converter um legado Exchange olhando para um recurso Azure usando PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais
Pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para perguntas frequentes, consulte o [Serviço de Observação DE OBSERVAÇÃO .](service-faqs.md)
