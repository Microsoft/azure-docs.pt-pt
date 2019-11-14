---
title: 'Início rápido: criar um balanceador de carga padrão-modelo do Azure'
titleSuffix: Azure Load Balancer
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
ms.date: 09/20/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 884ba1106e7b68fefaf91ee4e25cacd05bf96fc0
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048753"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Início rápido: criar um balanceador de carga padrão para balancear a carga de VMs usando Azure Resource Manager modelo

O balanceamento de carga oferece um nível mais elevado de disponibilidade e dimensionamento ao propagar os pedidos recebidos por múltiplas máquinas virtuais (VMs). Este guia de início rápido mostra como implantar um modelo de Azure Resource Manager que cria um balanceador de carga padrão para balancear a carga de VMs. O uso do modelo do Resource Manager leva menos etapas em comparação com outros métodos de implantação.

O [modelo do Resource Manager](../azure-resource-manager/template-deployment-overview.md) é um arquivo JavaScript Object Notation (JSON) que define a infraestrutura e a configuração do seu projeto. O modelo usa a sintaxe declarativa, que permite que você declare o que pretende implantar sem precisar escrever a sequência de comandos de programação para criá-lo. Se você quiser saber mais sobre como desenvolver modelos do Resource Manager, consulte a [documentação do Resource Manager](/azure/azure-resource-manager/) e a [referência do modelo](/azure/templates/microsoft.network/loadbalancers).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-standard-load-balancer"></a>Criar um balanceador de carga padrão

Standard Load Balancer dá suporte apenas a um endereço IP público padrão. Ao criar um balanceador de carga padrão, você também deve criar um novo endereço IP público padrão configurado como o front-end para o balanceador de carga padrão.

O modelo usado neste guia de início rápido é um [modelo de início rápido](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json).

[!code-json[<Azure Resource Manager template create standard load balancer>](~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json)]

Vários recursos do Azure foram definidos no modelo:

- **Microsoft.Network/loadBalancers**
- **Microsoft. Network/publicIPAddresses**: para o balanceador de carga.
- **Microsoft.Network/networkSecurityGroups**
- **Microsoft.Network/virtualNetworks**
- **Microsoft. Compute/virutalMachines** (três deles)
- **Microsoft. Network/publicIPAddresses** (três deles): para cada uma das três máquinas virtuais.
- **Microsoft. Network/NetworkInterfaces** (três deles)
- **Microsoft. Compute/VirtualMachine/Extensions** (três deles): Use para configurar o IIS e as páginas da Web

Para encontrar mais modelos relacionados ao Azure Load Balancer, consulte modelos de [início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

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

   A implantação de modelo cria três zonas de disponibilidade. As zonas de disponibilidade têm suporte apenas em [determinadas regiões](../availability-zones/az-overview.md). Use uma das regiões com suporte. Se você não tiver certeza, digite **centralus**.

   O nome do grupo de recursos é o nome do projeto com **RG** acrescentado. Você precisa do nome do grupo de recursos na próxima seção.

Leva cerca de 10 minutos para implantar o modelo. Quando concluído, a saída é semelhante a:

![Saída de implantação do PowerShell do modelo do Azure Standard Load Balancer Resource Manager](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

Azure PowerShell é usado para implantar o modelo. Além de Azure PowerShell, você também pode usar o portal do Azure, CLI do Azure e a API REST. Para saber mais sobre outros métodos de implantação, consulte [implantar modelos](../azure-resource-manager/resource-group-template-deploy-portal.md).

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **grupos de recursos** no painel esquerdo.

1. Selecione o grupo de recursos que você criou na seção anterior. O nome do grupo de recursos padrão é o nome do projeto com **RG** acrescentado.

1. Selecione o balanceador de carga. Seu nome padrão é o nome do projeto com **-lb** acrescentado.

1. Copie apenas a parte do endereço IP do endereço IP público e cole-a na barra de endereços do seu navegador.

   ![IP público do modelo do Gerenciador de carga do Azure Load Balancer Standard](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    O navegador exibe a página padrão do servidor Web Serviços de Informações da Internet (IIS).

   ![Servidor Web do IIS](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

Para ver o balanceador de carga distribuir o tráfego entre todas as três VMs, você pode forçar uma atualização do seu navegador da Web do computador cliente.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais deles, exclua o grupo de recursos, o balanceador de carga e todos os recursos relacionados. Para fazer isso, vá para o portal do Azure, selecione o grupo de recursos que contém o balanceador de carga e, em seguida, selecione **excluir grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um balanceador de carga padrão, anexou VMs a ele, configurou a regra de tráfego do balanceador de carga, fez uma investigação de integridade e, em seguida, testou o balanceador de carga.

Para saber mais, continue nos tutoriais para Load Balancer.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
