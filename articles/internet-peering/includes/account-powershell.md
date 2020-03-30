---
title: incluir ficheiro
titleSuffix: Azure
description: incluir ficheiro
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3a5f7157ef8f3645dd03ec93684238dd8bbc067e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774227"
---
Antes de iniciar a configuração, instale e importe os módulos necessários. Necessitará de privilégios do Administrador para instalar módulos no PowerShell.

1. Instalar e importar módulo Az
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Instale e importe módulo Az.Peering
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Verifique se os módulos são importados com recurso a um comando abaixo.
    ```powershell
    Get-Module
    ```
1. Inscreva-se na sua conta Azure utilizando o seguinte comando.
    ```powershell
    Connect-AzAccount
    ```
1. Verifique as subscrições da conta e selecione a subscrição na qual pretende criar um epeering.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Se ainda não tem um grupo de recursos, tem de criar um antes de criar um olhar. Pode fazê-lo executando o seguinte comando:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Se ainda não associou o seu ASN e a sua subscrição, siga os passos para [associate peer ASN](../howto-subscription-association-powershell.md). Isto é necessário para solicitar um olhar.

> [!NOTE]
> A localização do grupo de recursos é independente do local onde escolhe configurar um peering.
&nbsp;
