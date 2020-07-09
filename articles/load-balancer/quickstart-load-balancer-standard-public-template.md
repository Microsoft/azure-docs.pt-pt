---
title: 'Quickstart: Criar um balanceador de carga - modelo Azure'
titleSuffix: Azure Load Balancer
description: Este quickstart mostra como criar um equilibrador de carga utilizando o modelo Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: allensu
ms.custom: mvc,subject-armqs
ms.openlocfilehash: ebf2f926f5be86ffee5f3a3e30277962a6060762
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85479765"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>Quickstart: Criar um balanceador de carga para carregar VMs de equilíbrio utilizando um modelo ARM

O balanceamento de carga oferece um nível mais elevado de disponibilidade e dimensionamento ao propagar os pedidos recebidos por múltiplas máquinas virtuais (VMs). Este quickstart mostra-lhe como implementar um modelo de Gestor de Recursos Azure (modelo ARM) que cria um balanceador de carga Standard para carregar VMs de equilíbrio. A utilização de um modelo ARM dá menos passos em comparação com outros métodos de implantação.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente satisfaça os pré-requisitos e estiver familiarizado com a utilização de modelos ARM, selecione o botão **Implementar para Azul.** O modelo será aberto no portal Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-load-balancer-standard-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste arranque rápido é de [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/101-load-balancer-standard-create/).

O Balancer de Carga e os SKUs IP Públicos devem coincidir. Quando criar um Balancer de Carga Padrão, também deve criar um novo endereço IP público standard que esteja configurado como frontend para o balançador de carga Standard. Se pretender criar um Balanceador de Carga Básico, utilize [este modelo](https://azure.microsoft.com/resources/templates/201-2-vms-loadbalancer-natrules/). A Microsoft recomenda a utilização do Standard SKU para cargas de trabalho de produção.

:::code language="json" source="~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json" range="1-324" highlight="57-122":::

Vários recursos Azure foram definidos no modelo:

- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses): para o equilibrador de carga e para cada uma das três máquinas virtuais.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines) (3 deles).
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) (3 deles).
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) (3 delas): use para configurar o IIS e as páginas web.

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

Azure PowerShell é usado para implementar o modelo. Além do Azure PowerShell, também pode utilizar o portal Azure CLI e a REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../azure-resource-manager/templates/deploy-portal.md).

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

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar deles, elimine o grupo de recursos, o equilibrador de carga e todos os recursos relacionados. Para tal, vá ao portal Azure, selecione o grupo de recursos que contém o equilibrador de carga e, em seguida, **selecione Delete resource group**.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um equilibrador de carga Standard, anexou VMs a ele, configuraram a regra de tráfego do balanceador de carga, fez uma sonda de saúde e, em seguida, testou o equilibrador de carga.

Para saber mais, continue para os tutoriais para Balancer de Carga.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
