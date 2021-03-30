---
title: 'Quickstart: Criar um equilibrador de carga pública - modelo Azure'
titleSuffix: Azure Load Balancer
description: Este quickstart mostra como criar um equilibrador de carga utilizando um modelo de Gestor de Recursos Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/09/2020
ms.author: allensu
ms.custom: mvc,subject-armqs
ms.openlocfilehash: 378ab88f4dee0c725e89f77cc6b2ffe049ff877a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97008440"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>Quickstart: Criar um equilibrador de carga público para carregar VMs de equilíbrio utilizando um modelo ARM

O balanceamento de carga oferece um nível mais elevado de disponibilidade e dimensionamento ao propagar os pedidos recebidos por múltiplas máquinas virtuais (VMs). 

Este arranque rápido mostra-lhe como implantar um balanceador de carga padrão para carregar máquinas virtuais de equilíbrio.

A utilização de um modelo ARM dá menos passos em comparação com outros métodos de implantação.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-load-balancer-standard-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-load-balancer-standard-create/).

O equilibrador de carga e os SKUs IP públicos devem coincidir. Quando cria um balanceador de carga padrão, também deve criar um novo endereço IP público padrão que é configurado como o frontend para o balançador de carga padrão. Se pretender criar um equilibrador de carga básico, utilize [este modelo](https://azure.microsoft.com/resources/templates/201-2-vms-loadbalancer-natrules/). A Microsoft recomenda a utilização de SKU padrão para cargas de trabalho de produção.

:::code language="json" source="~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json":::

Vários recursos Azure foram definidos no modelo:

- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses): para o equilibrador de carga, hospedeiro de bastião e para cada uma das três máquinas virtuais.
- [**Microsoft.Network/bastionHosts**](/azure/templates/microsoft.network/bastionhosts)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines) (3).
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) (3).
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) (3): use para configurar o Servidor de Informações da Internet (IIS) e as páginas web.

Para encontrar mais modelos relacionados com o Azure Load Balancer, consulte [os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione **Experimentá-lo** a partir do bloco de código que se segue para abrir a Azure Cloud Shell e, em seguida, siga as instruções para iniciar súm no Azure.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
   $adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword

   Write-Host "Press [ENTER] to continue."
   ```

   Espere até ver o pedido da consola.

1. Selecione **Copiar** do bloco de código anterior para copiar o script PowerShell.

1. Clique com o botão direito da placa de consola da concha e, em seguida, **selecione Pasta**.

1. Insira os valores.

   A implementação do modelo cria três zonas de disponibilidade. As zonas de disponibilidade só são suportadas em [determinadas regiões.](../availability-zones/az-overview.md) Use uma das regiões apoiadas. Se não tem certeza, entre **no central.**

   O nome do grupo de recursos é o nome do projeto com **rg** anexado. Precisa do nome do grupo de recursos na secção seguinte.

Leva cerca de 10 minutos para implantar o modelo. Quando concluída, a saída é semelhante a:

![Modelo de implementação do gestor de recursos do balanceador de carga padrão Azure](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

Azure PowerShell é usado para implementar o modelo. Também pode utilizar o portal Azure, Azure CLI e REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../azure-resource-manager/templates/deploy-portal.md).

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **grupos** de recursos do painel esquerdo.

1. Selecione o grupo de recursos que criou na secção anterior. O nome do grupo de recursos predefinidos é o nome do projeto com **rg** anexado.

1. Selecione o equilibrador de carga. O seu nome padrão é o nome do projeto com **-lb** anexado.

1. Copie apenas a parte do endereço IP do endereço IP público e, em seguida, cole-o na barra de endereço do seu navegador.

   ![Modelo público do gestor de recursos de carga padrão Azure](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    O navegador exibe a página padrão do servidor web dos Serviços de Informação da Internet (IIS).

   ![Servidor Web do IIS](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

Para ver o equilibrador de carga distribuir tráfego através dos três VMs, você pode forçar uma atualização do seu navegador web a partir da máquina do cliente.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar, elimine o: 

* Grupo de recursos
* Balanceador de carga
* Recursos relacionados

Vá ao portal Azure, selecione o grupo de recursos que contém o equilibrador de carga e, em seguida, **selecione Eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, irá:

* Criou uma rede virtual para o equilibrador de carga e máquinas virtuais.
* Criei um anfitrião Azure Bastion para a gestão.
* Criei um balanceador de carga padrão e ligou-lhe VMs.
* Configurado a regra de tráfego do balanceador de carga, e a sonda de saúde.
* Testei o equilibrador de carga.

Para saber mais, continue os tutoriais para O Balancer de Carga Azure.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
