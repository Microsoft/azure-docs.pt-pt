---
title: Converta um legado De espreitar diretamente para um recurso Azure utilizando o portal Azure
titleSuffix: Azure
description: Converta um legado De espreitar diretamente para um recurso Azure utilizando o portal Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e49e4d5debe63b99039bbafbc14f7788367314f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678838"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Converta um legado De espreitar diretamente para um recurso Azure utilizando o portal Azure

Este artigo descreve como converter um legado existente Espreitar diretamente para um recurso Azure utilizando o portal Azure.

Se preferir, pode completar este guia utilizando o [PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) e o [passe direto](walkthrough-direct-all.md) antes de iniciar a configuração.


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Converter um legado De espreitar diretamente para um recurso Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Inscreva-se no portal e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Converter um legado de espreitar direto

Pode converter ligações de observação de legados utilizando o recurso **Peering.**

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Lançar o recurso e configurar as definições básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurar ligações e submeter
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificar o peering direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Recursos adicionais

Para mais informações, consulte [as FAQs de observação da Internet](faqs.md).

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um epeering direto utilizando o portal](howto-direct-portal.md)
