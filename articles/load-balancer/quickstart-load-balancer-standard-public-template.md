---
title: 'Início rápido: Criar um Azure Resource Manager modelo de balanceador de carga padrão'
titlesuffix: Azure Load Balancer
description: Este guia de início rápido mostra como criar um balanceador de carga padrão usando o modelo de Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Standard load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: e1a04cad7fe5c9c95397ffad2faa80c7d657d0f8
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273803"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-an-azure-resource-manager-template"></a>Início rápido: Criar um balanceador de carga padrão para balancear a carga de VMs usando um modelo de Azure Resource Manager

O balanceamento de carga oferece um nível mais elevado de disponibilidade e dimensionamento ao propagar os pedidos recebidos por múltiplas máquinas virtuais (VMs). Este guia de início rápido mostra como implantar um modelo de Azure Resource Manager que cria um balanceador de carga padrão para balancear a carga de VMs.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-standard-load-balancer"></a>Criar um balanceador de carga padrão

Standard Load Balancer dá suporte apenas a um endereço IP público padrão. Ao criar um balanceador de carga padrão, você também deve criar um novo endereço IP público padrão configurado como o front-end para o balanceador de carga padrão.

Você pode usar muitos métodos para criar um balanceador de carga padrão. Neste guia de início rápido, você usa Azure PowerShell para implantar um [modelo do Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json). Os modelos do Resource Manager são ficheiros JSON que definem os recursos que precisa de implementar para a sua solução.

Para entender os conceitos associados à implantação e ao gerenciamento de suas soluções do Azure, consulte a [documentação do Azure Resource Manager](/azure/azure-resource-manager/). Para encontrar mais modelos relacionados ao Azure Load Balancer, consulte modelos de [início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

1. Selecione **Experimente** no seguinte bloco de código para abrir Azure cloud Shell e, em seguida, siga as instruções para entrar no Azure.

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

   Aguarde até que você veja o prompt no console.

1. Selecione **copiar** no bloco de código anterior para copiar o script do PowerShell.

1. Clique com o botão direito do mouse no painel do console do Shell e selecione **colar**.

1. Insira os valores.

   A implantação de modelo cria três zonas de disponibilidade. As zonas de disponibilidade têm suporte apenas em [determinadas regiões](../availability-zones/az-overview.md). Use uma das regiões com suporte. Se você não tiver certeza, digite centralus.

   O nome do grupo de recursos é o nome do projeto com **RG** acrescentado. Você precisa do nome do grupo de recursos na próxima seção.

Leva cerca de 10 minutos para implantar o modelo.

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **grupos de recursos** no painel esquerdo.

1. Selecione o grupo de recursos que você criou na seção anterior. O nome do grupo de recursos padrão é o nome do projeto com **RG** acrescentado.

1. Selecione o balanceador de carga. Seu nome padrão é o nome do projeto com **-lb** acrescentado.

1. Copie apenas a parte do endereço IP do endereço IP público e cole-a na barra de endereços do seu navegador. O navegador exibe a página padrão do servidor Web Serviços de Informações da Internet (IIS).

   ![Servidor Web do IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Para ver o balanceador de carga distribuir o tráfego entre todas as três VMs, você pode forçar uma atualização do seu navegador da Web do computador cliente.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais deles, exclua o grupo de recursos, o balanceador de carga e todos os recursos relacionados. Para fazer isso, vá para o portal do Azure, selecione o grupo de recursos que contém o balanceador de carga e, em seguida, selecione **excluir grupo de recursos**.

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você criou um balanceador de carga padrão, anexou VMs a ele, configurou a regra de tráfego do balanceador de carga, fez uma investigação de integridade e, em seguida, testou o balanceador de carga.

Para saber mais, continue nos tutoriais para Load Balancer.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
 