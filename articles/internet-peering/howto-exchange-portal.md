---
title: Criar ou modificar um persparo de troca utilizando o portal Azure
titleSuffix: Azure
description: Criar ou modificar um persparo de troca utilizando o portal Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e4f2ee72cbe17c094567aab5c7cc4720b02cde68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680952"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Criar ou modificar um persparo de troca utilizando o portal Azure

Este artigo descreve como criar um microsoft exchange peering usando o portal Azure. Este artigo também mostra como verificar o estado do recurso, atualizá-lo ou eliminá-lo e desfortá-lo.

Se preferir, pode completar este guia utilizando o [PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) e o ["Exchange peering walkthrough"](walkthrough-exchange-all.md) antes de iniciar a configuração.
* Se já tem percevejos de Exchange com a Microsoft que não são convertidos em recursos Azure, consulte [Converter um legado Exchange espreitando um recurso Azure utilizando o portal](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Criar e fornecer um persparo de troca

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Inscreva-se no portal e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Criar um persparo de troca

Pode criar um novo pedido de peering utilizando o recurso **Peering.**

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Lançar o recurso e configurar as definições básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurar ligações e submeter
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Verifique um peering de troca
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modificar um peering de troca
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Deprovisionamento de um persparo de troca
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um epeering direto utilizando o portal](howto-direct-portal.md)
* [Converter um legado De espreitar diretamente para um recurso Azure usando o portal](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para mais informações, consulte [as FAQs de observação da Internet](faqs.md).
