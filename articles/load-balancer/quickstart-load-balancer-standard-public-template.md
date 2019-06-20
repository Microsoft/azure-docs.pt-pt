---
title: 'Início rápido: Criar um balanceador de carga Standard - modelo Azure Resource Manager'
titlesuffix: Azure Load Balancer
description: Este início rápido mostra como criar um balanceador de carga Standard com o modelo Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load Balancer by using Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: a95bedc8b2b395f856512ec49bae630666fe36da
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272878"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Início rápido: Criar um Standard Balanceador de carga para balancear carga de VMs com o modelo Azure Resource Manager

O balanceamento de carga oferece um nível mais elevado de disponibilidade e dimensionamento ao propagar os pedidos recebidos por várias máquinas virtuais. Este guia de introdução mostra-lhe como implementar um modelo do Azure Resource Manager para criar um balanceador de carga standard para máquinas de virtuais (VMs) de balanceamento de carga.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-standard-load-balancer"></a>Criar um balanceador de carga standard

O Balanceador de Carga Standard só suporta endereços IP Públicos Standard. Quando cria um balanceador de carga Standard, também tem de criar um endereço IP público Standard novo que está configurado como front-end para o Balanceador de carga Standard. Existem vários métodos que podem ser utilizados para criar um balanceador de carga standard. Neste início rápido, vai utilizar do Azure PowerShell para implementar um [modelo do Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json). Os modelos do Resource Manager são ficheiros JSON que definem os recursos que precisa de implementar para a sua solução. Para compreender os conceitos associados à implementação e gestão de suas soluções do Azure, veja [documentação do Azure Resource Manager](/azure/azure-resource-manager/). Para obter mais modelo relacionado do Balanceador de carga do Azure, veja [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

1. Selecione **experimente** do bloco de código seguinte para abrir o Azure Cloud shell e, em seguida, siga as instruções para iniciar sessão no Azure.

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

   Aguarde até ver a linha a partir da consola.
2. Selecione **cópia** do bloco de código anterior para copiar o script do PowerShell.
3. Com o botão direito do painel de consola de shell e, em seguida, selecione **colar**.
4. Introduza os valores.

   A implementação do modelo cria três zonas de disponibilidade.  As zonas de disponibilidade só são suportadas no [determinadas regiões](../availability-zones/az-overview.md). Utilize uma das regiões suportadas. Se não tiver a certeza, introduza **centralus**.

   O nome de grupo de recursos é o nome do projeto com **rg** anexado. Tem o nome do grupo de recursos na secção seguinte.

Demora cerca de 10 minutos para implementar o modelo.

## <a name="test-the-load-balancer"></a>Testar o Balanceador de carga

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **grupos de recursos** no painel à esquerda.
1. Selecione o grupo de recursos que criou na última secção.  O nome de grupo de recursos do padrão é o nome do projeto com **rg** anexado.
1. Selecione o Balanceador de carga.  Há apenas um balanceador de carga. O nome predefinido é o nome do projeto com **-lb** anexado.
1. Copie o endereço IP público (apenas a parte de endereço IP) e, em seguida, cole-o na barra de endereço do seu browser. A página predefinida do servidor Web do IIS é apresentada no browser.

   ![Servidor Web do IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Para ver o Balanceador de carga a distribuir tráfego pelas três VMs, pode forçar a atualização seu navegador da web do computador cliente.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, Balanceador de carga e todos os recursos relacionados. Para tal, selecione o grupo de recursos que contém o Balanceador de carga do portal do Azure e, em seguida, selecione **eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou um Standard Balanceador de carga, anexou VMs ao mesmo, configurou a regra de tráfego do Balanceador de carga, sonda de estado de funcionamento e, em seguida, testou o Balanceador de carga. Para saber mais sobre o Balanceador de Carga do Azure, avance para os tutoriais do Balanceador de Carga do Azure.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
