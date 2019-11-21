---
title: 'Tutorial: Create an internal load balancer - Azure portal'
titleSuffix: Azure Load Balancer
description: This tutorial shows you how to create an internal Basic load balancer by using the Azure portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internal traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 1b9d943f540a0132abc6a70eba888aa5f8f46093
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225210"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-basic-load-balancer-in-the-azure-portal"></a>Tutorial: Balance internal traffic load with a Basic load balancer in the Azure portal

Load balancing provides a higher level of availability and scale by spreading incoming requests across virtual machines (VMs). You can use the Azure portal to create a Basic load balancer and balance internal traffic among VMs. This tutorial shows you how to create and configure an internal load balancer, back-end servers, and network resources at the Basic pricing tier.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

If you prefer, you can do these steps using the [Azure CLI](load-balancer-get-started-ilb-arm-cli.md) or [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) instead of the portal.

To do the steps using this tutorial, sign in to the Azure portal at [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>Create a VNet, back-end servers, and a test VM

First, create a virtual network (VNet). In the VNet, create two VMs to use for the back-end pool of your Basic load balancer, and a third VM to use for testing the load balancer. 

### <a name="create-a-virtual-network"></a>Criar rede virtual

1. On the upper-left side of the portal, select **Create a resource** > **Networking** > **Virtual network**.
   
1. In the **Create virtual network** pane, type or select these values:
   
   - **Name**: Type *MyVNet*.
   - **ResourceGroup**: Select **Create new**, then enter *MyResourceGroupLB*, and select **OK**. 
   - **Subnet** > **Name**: Type *MyBackendSubnet*.
   
1. Selecione **Criar**.

   ![Criar rede virtual](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

1. On the upper-left side of the portal, select **Create a resource** > **Compute** > **Windows Server 2016 Datacenter**. 
   
1. In **Create a virtual machine**, type or select the following values in the **Basics** tab:
   - **Subscription** > **Resource Group**: Drop down and select **MyResourceGroupLB**.
   - **Instance Details** > **Virtual machine name**: Type *MyVM1*.
   - **Instance Details** > **Availability Options**: 
     1. Drop down and select **Availability set**. 
     2. Select **Create new**, type *MyAvailabilitySet*, and select **OK**.
   
1. Select the **Networking** tab, or select **Next: Disks**, then **Next: Networking**. 
   
   Make sure the following are selected:
   - **Virtual network**: **MyVNet**
   - **Subnet**: **MyBackendSubnet**
   
   Under **Network Security Group**:
   1. Selecione **Avançadas**. 
   1. Drop down **Configure network security group** and select **None**. 
   
1. Select the **Management** tab, or select **Next** > **Management**. Under **Monitoring**, set **Boot diagnostics** to **Off**.
   
1. Selecione **Rever + criar**.
   
1. Review the settings, and then select **Create**. 

1. Follow the steps to create a second VM named *MyVM2*, with all the other settings the same as MyVM1. 

1. Follow the steps again to create a third VM named *MyTestVM*. 

## <a name="create-a-basic-load-balancer"></a>Criar um balanceador de carga Básico

Create a Basic internal load balancer by using the portal. The name and IP address you create are automatically configured as the load balancer's front end.

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Rede** > **Balanceador de Carga**.
   
2. In the **Basics** tab of the **Create load balancer** page, enter or select the following information, accept the defaults for the remaining settings, and then select **Review + create**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | Select **Create new** and type *MyResourceGroupLB* in the text box.|
    | Nome                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Tipo          | Select **Internal**.                                        |
    | SKU           | Selecione **Básico**.                          |
    | Rede virtual           | Select *MyVNet*.                          |    
    | IP address assignment              | Select **Static**.   |
    | Private IP address|Type an address that is in the address space of your virtual network and subnet, for example *10.3.0.7*.  |

3. In the **Review + create** tab, click **Create**. 
   

## <a name="create-basic-load-balancer-resources"></a>Create Basic load balancer resources

In this section, you configure load balancer settings for a back-end address pool and a health probe, and specify load balancer rules.

### <a name="create-a-back-end-address-pool"></a>Criar um conjunto de endereços de back-end

To distribute traffic to the VMs, the load balancer uses a back-end address pool. The back-end address pool contains the IP addresses of the virtual network interfaces (NICs) that are connected to the load balancer. 

**To create a back-end address pool that includes VM1 and VM2:**

1. Select **All resources** on the left menu, and then select **MyLoadBalancer** from the resource list.
   
1. Em **Definições**, selecione **Conjuntos de back-end** e, em seguida, selecione **Adicionar**.
   
1. On the **Add a backend pool** page, type or select the following values:
   
   - **Name**: Type *MyBackendPool*.
   - **Associated to**: Drop down and select **Availability set**.
   - **Availability set**: Select **MyAvailabilitySet**.
   
1. Select **Add a target network IP configuration**. 
   1. Add **MyVM1** and **MyVM2** to the back-end pool.
   2. After you add each machine, drop down and select its **Network IP configuration**. 
   
   >[!NOTE]
   >Do not add **MyTestVM** to the pool. 
   
1. Selecione **OK**.
   
   ![Add the back-end address pool](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)
   
1. On the **Backend pools** page, expand **MyBackendPool** and make sure both **VM1** and **VM2** are listed.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

To allow the load balancer to monitor VM status, you use a health probe. A sonda de estado de funcionamento adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na respetiva resposta às verificações de estado de funcionamento. 

**To create a health probe to monitor the health of the VMs:**

1. Select **All resources** on the left menu, and then select **MyLoadBalancer** from the resource list.
   
1. Em **Definições**, selecione **Sondas de estado de funcionamento** e, em seguida, selecione **Adicionar**.
   
1. On the **Add a health probe** page, type or select the following values:
   
   - **Name**: Type *MyHealthProbe*.
   - **Protocol**: Drop down and select **HTTP**. 
   - **Port**: Type *80*. 
   - **Path**: Accept */* for the default URI. You can replace this value with any other URI. 
   - **Interval**: Type *15*. Interval is the number of seconds between probe attempts.
   - **Unhealthy threshold**: Type *2*. This value is the number of consecutive probe failures that occur before a VM is considered unhealthy.
   
1. Selecione **OK**.
   
   ![Add a probe](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

As regras de balanceador de carga definem a forma como o tráfego é distribuído pelas VMs. The rule defines the front-end IP configuration for incoming traffic, the back-end IP pool to receive the traffic, and the required source and destination ports. 

The load balancer rule named **MyLoadBalancerRule** listens to port 80 in the front-end **LoadBalancerFrontEnd**. The rule sends network traffic to the back-end address pool **MyBackendPool**, also on port 80. 

**To create the load balancer rule:**

1. Select **All resources** on the left menu, and then select **MyLoadBalancer** from the resource list.
   
1. Em **Definições**, selecione **Regras de balanceamento de carga** e, em seguida, selecione **Adicionar**.
   
1. On the **Add load balancing rule** page, type or select the following values, if not already present:
   
   - **Name**: Type *MyLoadBalancerRule*.
   - **Frontend IP address:** Type *LoadBalancerFrontEnd* if not present.
   - **Protocol**: Select **TCP**.
   - **Port**: Type *80*.
   - **Backend port**: Type *80*.
   - **Backend pool**: Select **MyBackendPool**.
   - **Health probe**: Select **MyHealthProbe**. 
   
1. Selecione **OK**.
   
   ![Add a load balancer rule](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Install Internet Information Services (IIS) on the back-end servers, then use MyTestVM to test the load balancer using its private IP address. Each back-end VM serves a different version of the default IIS web page, so you can see the load balancer distribute requests between the two VMs.

In the portal, on the **Overview** page for **MyLoadBalancer**, find its IP address under **Private IP Address**. Hover over the address and select the **Copy** icon to copy it. In this example, it is **10.3.0.7**. 

### <a name="connect-to-the-vms-with-rdp"></a>Connect to the VMs with RDP

First, connect to all three VMs with Remote Desktop (RDP). 

>[!NOTE]
>By default, the VMs already have the **RDP** (Remote Desktop) port open to allow remote desktop access. 

**To remote desktop (RDP) into the VMs:**

1. In the portal, select **All resources** on the left menu. From the resource list, select each VM in the **MyResourceGroupLB** resource group.
   
1. On the **Overview** page, select **Connect**, and then select **Download RDP file**. 
   
1. Open the RDP file you downloaded, and select **Connect**.
   
1. On the Windows Security screen, select **More choices** and then **Use a different account**. 
   
   Enter username and password and then select **OK**.
   
1. Respond **Yes** to any certificate prompt. 
   
   The VM desktop opens in a new window. 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>Install IIS and replace the default IIS page on the back-end VMs

On each back-end server, use PowerShell to install IIS and replace the default IIS web page with a customized page.

>[!NOTE]
>You can also use the **Add Roles and Features Wizard** in **Server Manager** to install IIS. 

**To install IIS and update the default web page with PowerShell:**

1. On MyVM1 and on MyVM2, launch **Windows PowerShell** from the **Start** menu. 

2. Run the following commands to install IIS and replace the default IIS web page:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. Close the RDP connections with MyVM1 and MyVM2 by selecting **Disconnect**. Do not shut down the VMs.

### <a name="test-the-load-balancer"></a>Testar o balanceador de carga

1. On MyTestVM, open **Internet Explorer**, and respond **OK** to any configuration prompts. 
   
1. Paste or type the load balancer's private IP address (*10.3.0.7*) into the address bar of the browser. 
   
   The customized IIS web server default page appears in the browser. The message reads either **Hello World from MyVM1**, or **Hello World from MyVM2**.
   
1. Refresh the browser to see the load balancer distribute traffic across VMs. You may also need to clear your browser cache between attempts.

   Sometimes the **MyVM1** page appears, and other times the **MyVM2** page appears, as the load balancer distributes the requests to each back-end VM. 

   ![New IIS default page](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>Limpar recursos

To delete the load balancer and all related resources when you no longer need them, open the **MyResourceGroupLB** resource group and select **Delete resource group**.

## <a name="next-steps"></a>Passos seguintes

In this tutorial, you created a Basic-tier internal load balancer. You created and configured network resources, back-end servers, a health probe, and rules for the load balancer. You installed IIS on the back-end VMs and used a test VM to test the load balancer in the browser. 

Next, learn how to load balance VMs across availability zones.

> [!div class="nextstepaction"]
> [Balancear carga de VMs por zonas de disponibilidade](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
