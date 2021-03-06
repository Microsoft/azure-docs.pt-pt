---
title: 'Gateway Azure VPN: Verifique uma ligação de gateway'
description: Este artigo mostra-lhe como verificar uma ligação virtual VPN Gateway de rede virtual.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: devx-track-azurecli
ms.openlocfilehash: b59294d07ef64875cb6fbd3e3a49dec61d8b8135
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659408"
---
# <a name="verify-a-vpn-gateway-connection"></a>Verifique uma ligação VPN Gateway

Este artigo mostra-lhe como verificar uma ligação de gateway VPN para os modelos clássicos e gestores de recursos.

## <a name="azure-portal"></a>Portal do Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Para verificar uma ligação de gateway VPN para o modelo de implementação do Gestor de Recursos utilizando o PowerShell, instale a versão mais recente dos [cmdlets PowerShell do Gestor de Recursos Azure](/powershell/azure/).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>CLI do Azure

Para verificar uma ligação de gateway VPN para o modelo de implementação do Gestor de Recursos utilizando o Azure CLI, instale a versão mais recente dos [comandos CLI](/cli/azure/install-azure-cli) (2.0 ou mais tarde).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="azure-portal-classic"></a>Portal do Azure (clássico)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (clássico)

Para verificar a sua ligação de gateway VPN para o modelo de implementação clássico utilizando o PowerShell, instale as versões mais recentes dos cmdlets Azure PowerShell. Certifique-se de que descarrega e instala o módulo [de Gestão de Serviços.](/powershell/azure/servicemanagement/install-azure-ps?#azure-service-management-cmdlets) Utilize o 'Add-AzureAccount' para iniciar sessão no modelo clássico de implementação.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Passos seguintes

* Pode adicionar máquinas virtuais às suas redes virtuais. Veja [Criar uma Máquina Virtual](../virtual-machines/windows/quick-create-portal.md) para obter os passos.