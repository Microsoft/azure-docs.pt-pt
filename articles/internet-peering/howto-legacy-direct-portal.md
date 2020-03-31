---
title: Converter um peering Direto legado para o recurso do Azure com o portal
titleSuffix: Azure
description: Converter um peering Direto legado para o recurso do Azure com o portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9900414d38bd597d08a80d15e908228c06ce06ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775059"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-the-portal"></a>Converter um peering Direto legado para o recurso do Azure com o portal

Este artigo descreve como converter um legado existente Espreitar diretamente para o recurso Azure usando o portal.

Se preferir, pode completar este guia utilizando o [PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) e [o direct peering walkthrough](walkthrough-direct-all.md) antes de iniciar a configuração.


## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Converter o legado Direto espreitando para o recurso Azure

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Inscreva-se no portal e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-direct-peering"></a><a name=create></a>Converter o legado De espreitar direto

Pode converter ligações de observação de legados utilizando recurso **Peering.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Lançar recurso e configurar definições básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurar ligações e submeter
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificar o peering direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Recursos adicionais

Para mais informações, visite [faQs de observação de Internet](faqs.md)

## <a name="next-steps"></a>Passos seguintes

* [Crie ou modifique um epeering direto utilizando o portal](howto-direct-portal.md).
