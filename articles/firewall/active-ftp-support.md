---
title: Suporte FTP ativo Azure Firewall
description: Por predefinição, o FTP ativo é desativado no Azure Firewall. Pode activar-o utilizando o modelo PowerShell, CLI e ARM.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: victorh
ms.openlocfilehash: 18b3680e47fe808413998144259e033a4cbcaa27
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864474"
---
# <a name="azure-firewall-active-ftp-support"></a>Suporte FTP ativo Azure Firewall

Com o FTP ativo, o servidor FTP inicia a ligação de dados à porta de dados do cliente FTP designada. As firewalls na rede do lado do cliente normalmente bloqueiam um pedido de ligação externa a uma porta de cliente interno. Para obter mais informações, consulte [FtP Ativo vs. FtP passivo, uma explicação definitiva](https://slacksite.com/other/ftp.html).

Por predefinição, o suporte FTP ativo é desativado no Azure Firewall para proteger contra ataques de ressalto FTP utilizando o `PORT` comando FTP. No entanto, pode ativar o FTP ativo quando implementar utilizando o Azure PowerShell, o Azure CLI ou um modelo Azure ARM.

Para suportar o modo ativo FTP, devem ser abertas as seguintes portas TCP:

- Porta do servidor FTP 21 de qualquer lugar (ligação do cliente inicia)
- A porta 21 do servidor FTP para as portas > 1023 (o servidor responde à porta de controlo do cliente)
- A porta 20 do servidor FTP para as portas > 1023 nos clientes (o servidor inicia a ligação de dados à porta de dados do cliente)
- A porta 20 do servidor FTP a partir de portas > 1023 em clientes (o cliente envia ACKs para a porta de dados do servidor)

## <a name="azure-powershell"></a>Azure PowerShell

Para implementar utilizando a Azure PowerShell, utilize o `AllowActiveFTP` parâmetro. Para obter mais informações, consulte [Criar uma firewall com Permitir FTP ativo](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-).

## <a name="azure-cli"></a>CLI do Azure

Para implementar utilizando o CLI Azure, utilize o `--allow-active-ftp` parâmetro. Para obter mais informações, consulte [a az network firewall create](/cli/azure/network/firewall#az_network_firewall_create-optional-parameters). 

## <a name="azure-resource-manager-arm-template"></a>Modelo do Azure Resource Manager (ARM)

Para implementar usando um modelo ARM, utilize o `AdditionalProperties` campo:

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
Para obter mais informações, consulte [microsoft.Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="next-steps"></a>Passos seguintes

Para aprender a implantar uma Firewall Azure, consulte [implementar e configurar a Azure Firewall utilizando a Azure PowerShell](deploy-ps.md).