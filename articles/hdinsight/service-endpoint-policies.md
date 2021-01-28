---
title: Configure políticas de ponto final de serviço - Azure HDInsight
description: Saiba como configurar as políticas de ponto final de serviço para a sua rede virtual com o Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/15/2020
ms.openlocfilehash: 992cd994f96b5637d5afd91bccfecde8704d2886
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940625"
---
# <a name="configure-virtual-network-service-endpoint-policies-for-azure-hdinsight"></a>Configure políticas de ponto final de serviço de rede virtual para Azure HDInsight

Este artigo fornece informações sobre como implementar políticas de ponto final de serviço em redes virtuais com a Azure HDInsight.

## <a name="background"></a>Fundo

O Azure HDInsight permite-lhe criar clusters na sua própria rede virtual. Se precisar de permitir o tráfego de saída da sua rede virtual para outros serviços Azure, como contas de armazenamento, pode criar [políticas de ponto final de serviço](../virtual-network/virtual-network-service-endpoint-policies-overview.md). No entanto, as políticas de ponto final de serviço que são criadas através do portal Azure apenas permitem criar uma política para uma única conta, todas as contas numa subscrição ou todas as contas de um grupo de recursos.

No entanto, como um serviço gerido, o Azure HDInsight recolhe dados e regista ficheiros de cada cluster em contas de armazenamento específicas em cada região. Para que estes dados cheguem a HDInsight a partir da sua rede virtual, é necessário criar políticas de ponto final de serviço que permitam o tráfego de saída para pontos específicos de recolha de dados geridos pelo Azure HDInsight.

## <a name="service-endpoint-policies-for-hdinsight"></a>Políticas de ponto final de serviço para HDInsight

Estas políticas de ponto final de serviço suportam a seguinte funcionalidade:

- Recolha de registos e telemetria sobre criação de clusters, execução de empregos e operações de plataforma como escala.
- Anexar discos rígidos virtuais (VHDs) a novos nós de cluster para o fornecimento de software e bibliotecas no seu cluster.

Se as políticas de ponto final de serviço não forem criadas para permitir este fluxo de dados, a criação de clusters pode falhar e o Azure HDInsight não poderá fornecer suporte para os seus clusters.

## <a name="create-service-endpoint-policies-for-hdinsight"></a>Criar políticas de ponto final de serviço para HDInsight

Certifique-se de que as políticas corretas de ponto final de serviço estão ligadas à sua rede virtual antes de criar novos clusters. Caso contrário, a criação do cluster pode falhar ou resultar em erro.

Utilize o seguinte processo para criar as políticas de ponto final de serviço necessários:

1. Decida a região onde vai criar o seu cluster HDInsight.
1. Procure esta região na [lista de recursos de política de ponto final de serviço](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json), que dá a todos os grupos de recursos para contas de armazenamento de gestão HDInsight.
1. Selecione a lista de grupos de recursos para a sua região. Um exemplo dos recursos para os recursos `Canada Central` é mostrado abaixo:

    ```json
    "Canada Central":[
        "/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
        "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG"
    ],
    ```

1. Insira esta lista de grupos de recursos no script de configuração escrito em Azure CLI ou Azure PowerShell.

    ```azurecli
    $subscriptionId = "<subscription id>"
    $rgName="<resource group name> "
    $location="<location name>"
    $vnetName="<vnet name>"
    $subnetName="<subnet name>"
    $sepName="<service endpoint policy name>"
    $sepDefName="<service endpoint policy definition name>"
    
    # Set to the right subscription ID
    az account set --subscription $subscriptionId
    
    # setup service endpoint on the virtual network subnet
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoints Microsoft.Storage
    
    # Create Service Endpoint Policy
    az network service-endpoint policy create -g $rgName  -n $sepName -l $location
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",`
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Assign service resources to the SEP policy.
    az network service-endpoint policy-definition create -g $rgName --policy-name $sepName -n $sepDefName --service "Microsoft.Storage" --service-resources $resources
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoint-policy $sepName
    ```

    Se preferir configurar a sua política de ponto final de serviço utilizando o PowerShell, utilizando o seguinte corte de código.
    
    ```powershell
    #Script to assign SEP 
    $subscriptionId = "<subscription id>"
    $rgName = "<resource group name>"
    $vnetName = "<vnet name>"
    $subnetName = "<subnet Name"
    $location = "Canada Central"
    
    # Connect to your Azure Account
    Connect-AzAccount
    
    # Select the Subscription that you want to use
    Select-AzSubscription -SubscriptionId $subscriptionId
    
    # Retrieve VNet Config
    $vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
    
    # Retrieve Subnet Config
    $subnet = Get-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Declare service endpoint policy definition
    $sepDef = New-AzServiceEndpointPolicyDefinition -Name "SEPHDICanadaCentral" -Description "Service Endpoint Policy Definition" -Service "Microsoft.Storage" -ServiceResource $resources
    
    # Service Endpoint Policy
    $sep= New-AzServiceEndpointPolicy -ResourceGroupName $rgName -Name "SEPHDICanadaCentral" -Location $location -ServiceEndpointPolicyDefinition $sepDef
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    Set-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet -AddressPrefix $subnet.AddressPrefix -ServiceEndpointPolicy $sep
    ```

## <a name="next-steps"></a>Próximos passos

* [Arquitetura de rede virtual Azure HDInsight](hdinsight-virtual-network-architecture.md)
* [Configurar aplicação virtual de rede](./network-virtual-appliance.md)
