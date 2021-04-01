---
title: Configure o reposição do balançador de carga TCP e o tempo limite de marcha lenta
titleSuffix: Azure Load Balancer
description: Neste artigo, aprenda a configurar o tempo de tempo de marcha lenta E reset do Balancer de Carga Azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2020
ms.author: allensu
ms.openlocfilehash: 8a6be588544883b77c3ff115c9dba5e6ecd5fbd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92747215"
---
# <a name="configure-tcp-reset-and-idle-timeout-for-azure-load-balancer"></a>Configurar o reset do TCP e o tempo limite de marcha lenta para o balançador de carga Azure

O Azure Load Balancer tem a seguinte gama de tempo limite de marcha lenta:

* 4 minutos a 100 minutos para regras de saída
* 4 minutos a 30 minutos para as regras do Balanceador de Carga e regras NAT de entrada

Por padrão, está programado para 4 minutos. Se um período de inatividade for maior do que o valor de tempo limite, não há garantia de que a sessão TCP ou HTTP seja mantida entre o cliente e o seu serviço. 

As secções seguintes descrevem como alterar o tempo limite de marcha lenta e as definições de reset tcp para os recursos do balançador de carga.

## <a name="set-tcp-reset-and-idle-timeout"></a>Definir reset tcp e tempo limite de marcha lenta
---
# <a name="portal"></a>[**Portal**](#tab/tcp-reset-idle-portal)

Para definir o tempo limite de marcha lenta e o reset tcp para um equilibrador de carga, edite a regra equilibrada de carga. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No menu da esquerda, selecione **Grupos de Recursos.**

3. Selecione o grupo de recursos para o seu equilibrador de carga. Neste exemplo, o grupo de recursos é nomeado **myResourceGroup**.

4. Selecione o seu balanceador de carga. Neste exemplo, o balanceador de carga chama-se **myLoadBalancer**.

5. Em **Definições**, selecione **regras de equilíbrio de carga**.

     :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules.png" alt-text="Editar as regras do balançador de carga." border="true":::

6. Selecione a sua regra de equilíbrio de carga. Neste exemplo, a regra de equilíbrio de carga chama-se **myLBrule**.

7. Na regra de equilíbrio de carga, mova o slider no **intervalo de tempo de marcha lenta (minutos)** para o seu valor de tempo limite.  

8. No **reset TCP**, selecione **Ativado**.

   :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules-tcp-reset.png" alt-text="Desative o tempo limite e reset tcp." border="true":::

9. Selecione **Guardar**.

# <a name="powershell"></a>[**PowerShell**](#tab/tcp-reset-idle-powershell)

Para definir o tempo limite de marcha lenta e o reset tcp, desajuste os valores nos seguintes parâmetros de regra de equilíbrio de carga com [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

* **IdleTimeoutInMinutes**
* **EnableTcpReset**

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar o PowerShell localmente, também precisa de correr `Connect-AzAccount` para criar uma ligação com o Azure.

Substitua os seguintes exemplos pelos valores dos seus recursos:

* **myResourceGroup**
* **myLoadBalancer**

```azurepowershell
$lb = Get-AzLoadBalancer -Name "myLoadBalancer" -ResourceGroup "myResourceGroup"
$lb.LoadBalancingRules[0].IdleTimeoutInMinutes = '15'
$lb.LoadBalancingRules[0].EnableTcpReset = 'true'
Set-AzLoadBalancer -LoadBalancer $lb
```

# <a name="azure-cli"></a>[**CLI do Azure**](#tab/tcp-reset-idle-cli)

Para definir o tempo de inatividade e o reset do TCP, utilize os seguintes parâmetros para [a atualização da regra lb da rede Az](/cli/azure/network/lb/rule?az_network_lb_rule_update):

* **--tempo inativo**
* **--activar-tcp-reset**

Valide o seu ambiente antes de começar:

* Inscreva-se no portal Azure e verifique se a sua subscrição está ativa executando `az login` .
* Verifique a sua versão do Azure CLI numa janela de terminal ou comando em funcionamento `az --version` . Para a versão mais recente, consulte as [últimas notas de lançamento](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Se não tiver a versão mais recente, atualize a sua instalação seguindo o [guia de instalação do seu sistema operativo ou plataforma.](/cli/azure/install-azure-cli)

Substitua os seguintes exemplos pelos valores dos seus recursos:

* **myResourceGroup**
* **myLoadBalancer**
* **myLBrule**


```azurecli
az network lb rule update \
    --resource-group myResourceGroup \
    --name myLBrule \
    --lb-name myLoadBalancer \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
---
## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o tempo limite de marcha lenta e reinicialização do tcp, consulte [o Reposição do Balanceador](load-balancer-tcp-reset.md) de Carga e o Tempo de Saída de Marcha lenta

Para obter mais informações sobre a configuração do modo de distribuição do balançador de carga, consulte [configurar um modo de distribuição do balançador de carga](load-balancer-distribution-mode.md).
