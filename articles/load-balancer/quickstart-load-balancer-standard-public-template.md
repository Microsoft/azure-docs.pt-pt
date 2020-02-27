---
title: 'Quickstart: Criar um Balancer de Carga - Modelo Azure'
titleSuffix: Azure Load Balancer
description: Este quickstart mostra como criar um equilibrador de carga utilizando o modelo do Gestor de Recursos Azure.
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
ms.custom: mvc
ms.openlocfilehash: e5b9b989ed8111e2bf36194ae2c8a333db7e66b4
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650809"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Quickstart: Criar um Balancer de carga para carregar VMs de equilíbrio utilizando o modelo de Gestor de Recursos Azure

O balanceamento de carga oferece um nível mais elevado de disponibilidade e dimensionamento ao propagar os pedidos recebidos por múltiplas máquinas virtuais (VMs). Este quickstart mostra-lhe como implementar um modelo de Gestor de Recursos Azure que cria um equilíbrio de carga Standard para carregar VMs de equilíbrio. A utilização do modelo de Gestor de Recursos dá menos passos em comparação com outros métodos de implementação.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-load-balancer"></a>Criar um Balancer de Carga

Balancer de carga e IP Público SKUs devem corresponder. Quando criar um Balancer de Carga Padrão, também deve criar um novo endereço IP público padrão que esteja configurado como o frontend para o equilíbrio de carga Standard. Se quiser criar um Balancer de Carga Básico, utilize [este modelo](https://azure.microsoft.com/resources/templates/201-2-vms-loadbalancer-natrules/). A Microsoft recomenda a utilização do Standard SKU para cargas de trabalho de produção.

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [Modelos De Arranque Rápido Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json).

:::code language="json" source="~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json" range="1-150" highlight="58-122":::

Vários recursos Azure foram definidos no modelo:

- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Network/publicIPAddresss**](/azure/templates/microsoft.network/publicipaddresses): para o equilibrador de carga e para cada uma das três máquinas virtuais.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines) (3 delas)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) (3 deles)
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) (3 delas): utilizar para configurar o IIS e as páginas web

Para encontrar mais modelos relacionados com o Equilíbrio de Carga Azure, consulte [os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione **Experimente** a partir do seguinte bloco de código para abrir a Azure Cloud Shell e, em seguida, siga as instruções para iniciar sessão no Azure.

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

   Aguarde até ver o pedido da consola.

1. Selecione **Copy** do bloco de código anterior para copiar o script PowerShell.

1. Clique na vidraça da consola da concha e, em seguida, selecione **Paste**.

1. Insira os valores.

   A implementação do modelo cria três zonas de disponibilidade. As zonas de disponibilidade são suportadas apenas em [determinadas regiões.](../availability-zones/az-overview.md) Utilize uma das regiões apoiadas. Se não tem certeza, entre no **centro.**

   O nome do grupo de recursos é o nome do projeto com **rg** anexado. Precisa do nome do grupo de recursos na próxima secção.

Leva cerca de 10 minutos para implementar o modelo. Quando concluída, a saída é semelhante a:

![Modelo de implementação de powerShell de gestor de recursos de balanço padrão de carga azure](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

O Azure PowerShell é usado para implantar o modelo. Além do Azure PowerShell, também pode utilizar o portal Azure, O ClI Azur e a Rest API. Para aprender outros métodos de implementação, consulte [os modelos de implantação](../azure-resource-manager/templates/deploy-portal.md).

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **Grupos de Recursos** a partir do painel esquerdo.

1. Selecione o grupo de recursos que criou na secção anterior. O nome do grupo de recursos padrão é o nome do projeto com **rg** anexado.

1. Selecione o equilibrador de carga. O seu nome padrão é o nome do projeto com **-lb** anexado.

1. Copie apenas a parte do endereço IP do endereço IP público e, em seguida, cole-o na barra de endereços do seu navegador.

   ![Modelo de gestor de recursos padrão de carga Azure](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    O navegador exibe a página padrão do servidor web dos Serviços de Informação da Internet (IIS).

   ![Servidor Web do IIS](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

Para ver o equilibrador de carga distribuir tráfego pelos três VMs, pode forçar uma atualização do seu navegador web a partir da máquina cliente.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar deles, elimine o grupo de recursos, o equilibrador de carga e todos os recursos relacionados. Para tal, vá ao portal Azure, selecione o grupo de recursos que contém o equilibrador de carga e, em seguida, **selecione Eliminar o grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um equilibrador de carga Standard, anexou-lhe VMs, configurou a regra de tráfego de equilíbrio de carga, fez uma sonda de saúde e depois testou o equilibrador de carga.

Para saber mais, continue aos tutoriais para Load Balancer.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
