---
title: 'Início rápido: Criar um balanceador de carga Standard - modelo Azure Resource Manager'
titlesuffix: Azure Load Balancer
description: Este início rápido mostra como criar um balanceador de carga Standard com o modelo Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 00dbb29cac30f2a3bbecf71727c79617e24af3e0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441305"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-an-azure-resource-manager-template"></a>Início rápido: Criar um balanceador de carga Standard para balancear carga de VMs com um modelo Azure Resource Manager

O balanceamento de carga oferece um nível mais elevado de disponibilidade e dimensionamento ao propagar os pedidos recebidos por múltiplas máquinas virtuais (VMs). Este guia de introdução mostra-lhe como implementar um modelo do Azure Resource Manager que cria um balanceador de carga Standard para balancear carga de VMs.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-standard-load-balancer"></a>Criar um balanceador de carga Standard

Balanceador de carga Standard oferece suporte a apenas um endereço IP público Standard. Quando cria um balanceador de carga Standard, também tem de criar um endereço IP público Standard novo que está configurado como o front-end do Balanceador de carga Standard.

Pode utilizar vários métodos para criar um balanceador de carga Standard. Neste início rápido, vai utilizar do Azure PowerShell para implementar um [modelo do Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json). Os modelos do Resource Manager são ficheiros JSON que definem os recursos que precisa de implementar para a sua solução.

Para compreender os conceitos associados à implementação e gestão de suas soluções do Azure, veja [documentação do Azure Resource Manager](/azure/azure-resource-manager/). Para obter mais modelos relacionados com o Balanceador de carga do Azure, veja [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

1. Selecione **experimente** do bloco de código seguinte para abrir o Azure Cloud Shell e, em seguida, siga as instruções para iniciar sessão no Azure.

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

1. Selecione **cópia** do bloco de código anterior para copiar o script do PowerShell.

1. Com o botão direito do painel de consola de shell e, em seguida, selecione **colar**.

1. Introduza os valores.

   A implementação do modelo cria três zonas de disponibilidade. As zonas de disponibilidade são suportadas apenas num [determinadas regiões](../availability-zones/az-overview.md). Utilize uma das regiões suportadas. Se não tiver a certeza, introduza **centralus**.

   O nome de grupo de recursos é o nome do projeto com **rg** anexado. Tem o nome do grupo de recursos na secção seguinte.

Demora cerca de 10 minutos para implementar o modelo.

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **grupos de recursos** no painel à esquerda.

1. Selecione o grupo de recursos que criou na secção anterior. O nome de grupo de recursos do padrão é o nome do projeto com **rg** anexado.

1. Selecione o Balanceador de carga. O nome predefinido é o nome de projeto com **-lb** anexado.

1. Copie apenas a parte do endereço IP do endereço IP público e, em seguida, cole-o na barra de endereço do seu browser. O browser apresenta a página predefinida do servidor web dos serviços de informação Internet (IIS).

   ![Servidor Web do IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Para ver o Balanceador de carga a distribuir tráfego pelas três VMs, pode forçar uma atualização do seu navegador da web do computador cliente.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar, elimine o grupo de recursos, o Balanceador de carga e todos os recursos relacionados. Para tal, aceda ao portal do Azure, selecione o grupo de recursos que contém o Balanceador de carga e, em seguida, selecione **eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou um balanceador de carga Standard, anexou VMs ao mesmo, configurou a regra de tráfego do Balanceador de carga, fiz uma sonda de estado de funcionamento e, em seguida, testou o Balanceador de carga.

Para obter mais informações, avance para os tutoriais para o Balanceador de carga.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
 