---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 11d2172d085fe9b47587f4084908f99d7b54437e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92103242"
---
Quando cria vNets clássicos no portal Azure, o nome que vê não é o nome completo que usa para PowerShell. Por exemplo, um VNet que parece ser nomeado **TestVNet1** no portal, pode ter um nome muito mais longo no ficheiro de configuração da rede. Para um VNet no grupo de recursos o nome "ClassicRG" pode parecer algo como: **Group ClassicRG TestVNet1**. Quando cria as suas ligações, é importante utilizar os valores que vê no ficheiro de configuração da rede.

Nos seguintes passos, irá ligar-se à sua conta Azure e descarregar e ver o ficheiro de configuração da rede para obter os valores necessários para as suas ligações.

1. Descarregue e instale a versão mais recente dos cmdlets PowerShell powerShell da Azure Service Management (SM). A maioria das pessoas tem os módulos de Gestor de Recursos instalados localmente, mas não têm módulos de Gestão de Serviços. Os módulos de Gestão de Serviços são legados e devem ser instalados separadamente. Para obter mais informações, consulte [cmdlets de Gestão de Serviços de Instalação](/powershell/azure/servicemanagement/install-azure-ps).

1. Abra a consola do PowerShell com direitos elevados e ligue-se à sua conta. Utilize os seguintes exemplos para o ajudar a ligar. Tem de executar estes comandos localmente utilizando o módulo de Gestão de Serviços PowerShell. Ligar à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

   ```powershell
   Add-AzureAccount
   ```
1. Verifique as subscrições da conta.

   ```powershell
   Get-AzureSubscription
   ```
1. Se tiver mais do que uma subscrição, selecione a subscrição que pretende utilizar.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```
1. Crie um diretório no seu computador. Por exemplo, C:\AzureVNet
1. Exporte o ficheiro de configuração da rede para o diretório. Neste exemplo, o ficheiro de configuração da rede é exportado para **C:\AzureNet**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
1. Abra o ficheiro com um editor de texto e veja os nomes dos seus VNets e sites. Estes nomes serão os nomes que usa quando criar as suas ligações.<br>Os nomes **VNet** estão listados como **Nome VirtualNetworkSite =**<br>**Os** nomes do site estão listados como **LocalNetworkSiteRef nome =**