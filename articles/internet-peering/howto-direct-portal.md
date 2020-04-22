---
title: Criar ou modificar um epeering direto utilizando o portal Azure
titleSuffix: Azure
description: Criar ou modificar um epeering direto utilizando o portal Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dcd6aaf584691005dd071a7aba5958070f598978
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681075"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Criar ou modificar um epeering direto utilizando o portal Azure

Este artigo descreve como criar um microsoft direct peering usando o portal Azure. Este artigo também mostra como verificar o estado do recurso, atualizá-lo ou eliminá-lo e desfortá-lo.

Se preferir, pode completar este guia utilizando o Azure [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) e o [passe direto](walkthrough-direct-all.md) antes de iniciar a configuração.
* Se já tem ligações diretas com a Microsoft que não são convertidas em recursos Azure, consulte [Converter um legado Direct peering para um recurso Azure utilizando o portal](howto-legacy-direct-portal.md).

## <a name="create-and-provision-a-direct-peering"></a>Criar e fornecer um peering direto

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Inscreva-se no portal e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Criar um olhar direto

Pode criar um novo pedido de peering utilizando o recurso **Peering.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Lançar recurso e configurar definições básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurar ligações e submeter
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificar o peering direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modificar um olhar direto
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Desprovisionamento de um peering direto
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar o peering de troca utilizando o portal](howto-exchange-portal.md)
* [Converter um legado Exchange peering para um recurso Azure usando o portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para mais informações, consulte [as FAQs de observação da Internet](faqs.md).
