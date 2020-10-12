---
title: ficheiro de inclusão
titleSuffix: Azure
description: ficheiro de inclusão
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: beffb2babefd86c2807e21e9337cba66f42fcfc2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81678507"
---
Antes de iniciar a configuração, instale e importe os módulos necessários. Precisa de privilégios de administrador para instalar módulos no PowerShell.

1. Instale e importe o módulo Az.
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Instale e importe o módulo Az.Peering.
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Verifique se os módulos importados corretamente utilizando este comando:
    ```powershell
    Get-Module
    ```
1. Inscreva-se na sua conta Azure utilizando este comando:
    ```powershell
    Connect-AzAccount
    ```
1. Verifique as subscrições da conta e selecione a subscrição na qual pretende criar um espreitamento.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Se ainda não tem um grupo de recursos, tem de criar um antes de criar um espreitamento. Pode fazê-lo executando o seguinte comando:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Se ainda não associou a sua ASN e a subscrição, siga os passos no [Associate Peer ASN](../howto-subscription-association-powershell.md). Esta ação é necessária para solicitar um espreitamento.

> [!NOTE]
> A localização de um grupo de recursos é independente do local onde escolhe configurar um espreitamento.
&nbsp;
