---
title: Não é possível se conectar a VMs do Azure porque a porta RDP não está habilitada em NSG | Microsoft Docs
description: Saiba como solucionar um problema no qual o RDP falha devido à configuração do NSG no portal do Azure | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 5f95b42fd17aec4e3ec6b7b8fac1965772fefa67
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162598"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Não é possível se conectar remotamente a uma VM porque a porta RDP não está habilitada em NSG

Este artigo explica como resolver um problema no qual você não pode se conectar a uma VM (máquina virtual) do Windows do Azure porque a porta protocolo RDP (RDP) não está habilitada no grupo de segurança de rede (NSG).


> [!NOTE] 
> O Azure tem dois modelos de implantação para criar e trabalhar com recursos: [Resource Manager e clássico](../../azure-resource-manager/resource-manager-deployment-model.md). Recomendamos que você use o modelo de implantação do Gerenciador de recursos em vez do modelo de implantação clássico para novas implantações. 

## <a name="symptom"></a>Sintoma

Você não pode fazer uma conexão RDP com uma VM no Azure porque a porta RDP não está aberta no grupo de segurança de rede.

## <a name="solution"></a>Solução 

Quando você cria uma nova VM, todo o tráfego da Internet é bloqueado por padrão. 

Para habilitar a porta RDP em um NSG, siga estas etapas:
1. Inicie sessão no [do portal do Azure](https://portal.azure.com).
2. Em **máquinas virtuais**, selecione a VM que tem o problema. 
3. Em **configurações**, selecione **rede**. 
4. Em **regras de porta de entrada**, verifique se a porta para RDP está definida corretamente. Veja a seguir um exemplo da configuração: 

    **Prioridade**: 300 </br>
    **Nome**: Port_3389 </br>
    **Porta (destino)** : 3389 </br>
    **Protocolo**: TCP </br>
    **Origem**: Any </br>
    **Destinos**: Any </br>
    **Ação**: Allow </br>

Se você especificar o endereço IP de origem, essa configuração permitirá o tráfego somente de um endereço IP ou intervalo de endereços IP específico para se conectar à VM. Verifique se o computador que você está usando para iniciar a sessão RDP está dentro do intervalo.

Para obter mais informações sobre NSGs, consulte [grupo de segurança de rede](../../virtual-network/security-overview.md).

> [!NOTE]
> A porta RDP 3389 é exposta à Internet. Portanto, é recomendável que você use essa porta somente para teste recomendado. Para ambientes de produção, recomendamos que você use uma VPN ou conexão privada.

## <a name="next-steps"></a>Passos seguintes

Se a porta RDP já estiver habilitada no NSG, consulte [solucionar problemas de um erro geral de RDP na VM do Azure](./troubleshoot-rdp-general-error.md).



