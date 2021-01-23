---
title: Suporte FTP ativo Azure Firewall
description: Por predefinição, o FTP ativo é desativado no Azure Firewall. Pode activar-o utilizando o modelo PowerShell, CLI e ARM.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: victorh
ms.openlocfilehash: 2c89eb19025212490d29c97a061da52e779ecf42
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704402"
---
# <a name="azure-firewall-active-ftp-support"></a>Suporte FTP ativo Azure Firewall

Com o FTP ativo, o servidor FTP inicia a ligação de dados à porta de dados do cliente FTP designada. As firewalls na rede do lado do cliente normalmente bloqueiam um pedido de ligação externa a uma porta de cliente interno. Para obter mais informações, consulte [FtP Ativo vs. FtP passivo, uma explicação definitiva](https://slacksite.com/other/ftp.html).

Por predefinição, o suporte FTP ativo é desativado no Azure Firewall para proteger contra ataques de ressalto FTP utilizando o `PORT` comando FTP. No entanto, pode ativar o FTP ativo quando implementar utilizando o Azure PowerShell, o Azure CLI ou um modelo Azure ARM.

> [!NOTE]
> Atualmente, o Ative FTP é suportado apenas para firewalls implantados numa rede virtual. O suporte virtual WAN será adicionado mais tarde.

## <a name="azure-powershell"></a>Azure PowerShell

Para implementar utilizando a Azure PowerShell, utilize o `AllowActiveFTP` parâmetro. Para obter mais informações, consulte [Criar uma firewall com Permitir FTP ativo](/powershell/module/az.network/new-azfirewall?view=azps-5.4.0#16---create-a-firewall-with-allow-active-ftp-).

## <a name="azure-cli"></a>CLI do Azure

Para implementar utilizando o CLI Azure, utilize o `--allow-active-ftp` parâmetro. Para obter mais informações, consulte [a az network firewall create](/cli/azure/ext/azure-firewall/network/firewall?view=azure-cli-latest#ext_azure_firewall_az_network_firewall_create-optional-parameters). 

## <a name="azure-resource-manager-arm-template"></a>Modelo do Azure Resource Manager (ARM)

Para implementar usando um modelo ARM, utilize o `AdditionalProperties` campo:

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
Para obter mais informações, consulte [microsoft.Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="next-steps"></a>Próximos passos

Para aprender a implantar uma Firewall Azure, consulte [implementar e configurar a Azure Firewall utilizando a Azure PowerShell](deploy-ps.md).