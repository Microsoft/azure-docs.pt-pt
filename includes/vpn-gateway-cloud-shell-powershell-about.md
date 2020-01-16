---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 684b212ca771af6c336cf6239e18ea367f2da5ce
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045114"
---
Este artigo usa cmdlets do PowerShell. Para executar os cmdlets, você pode usar Azure Cloud Shell, um ambiente de shell interativo hospedado no Azure e usado por meio do navegador. Azure Cloud Shell vem com os cmdlets Azure PowerShell pré-instalados.

Para executar qualquer código contido neste artigo em Azure Cloud Shell, abra uma sessão Cloud Shell, use o botão **copiar** em um bloco de código para copiar o código e cole-o na sessão do Cloud shell com __Ctrl + Shift + v__ no Windows e no Linux ou __cmd + shift + v__ no MacOS. O texto colado não é executado automaticamente, portanto pressione **Enter** para executar o código.

Você pode iniciar o Azure Cloud Shell com:

|  |   |
|-----------------------------------------------|---|
| Selecione **Experimentar** no canto superior direito de um bloco de código. Isso __não__ copia automaticamente o texto para Cloud Shell. | ![Exemplo de teste para Azure Cloud Shell](./media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Abra o [shell.Azure.com](https://shell.azure.com) no navegador. | [botão de Azure Cloud Shell iniciar ![](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Selecione o botão **Cloud Shell** no menu, no canto superior direito do [portal do Azure](https://portal.azure.com). | ![Botão Cloud Shell no portal do Azure](./media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

**Executando o PowerShell localmente**

Você também pode instalar e executar os cmdlets Azure PowerShell localmente no seu computador. Os cmdlets do PowerShell são atualizados com frequência. Se você não estiver executando a versão mais recente, os valores especificados nas instruções poderão falhar. Para localizar as versões do Azure PowerShell instaladas no seu computador, use o cmdlet `Get-Module -ListAvailable Az`. Para instalar ou atualizar o, consulte [instalar o Azure PowerShell Module](/powershell/azure/install-az-ps).

Se você estiver executando o PowerShell localmente, certifique-se de executar ' Connect-AzAccount ' para criar sua conexão com o Azure.