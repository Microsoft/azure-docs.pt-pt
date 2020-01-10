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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774227"
---
Antes de iniciar a configuração, instale e importe os módulos necessários. Você precisará de privilégios de administrador para instalar os módulos no PowerShell.

1. Instalar e importar o módulo AZ
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Instalar e importar o módulo AZ. emparelhamento
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Verifique se os módulos são importados corretamente usando o comando a seguir.
    ```powershell
    Get-Module
    ```
1. Entre em sua conta do Azure usando o comando a seguir.
    ```powershell
    Connect-AzAccount
    ```
1. Verifique as assinaturas da conta e selecione a assinatura na qual você deseja criar um emparelhamento.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Se você ainda não tiver um grupo de recursos, deverá criar um antes de criar um emparelhamento. Pode fazê-lo ao executar o seguinte comando:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Se você ainda não tiver associado seu ASN e sua assinatura, siga as etapas para [associar o ASN de mesmo nível](../howto-subscription-association-powershell.md). Isso é necessário para solicitar um emparelhamento.

> [!NOTE]
> O local do grupo de recursos é independente do local onde você opta por configurar um emparelhamento.
&nbsp;
