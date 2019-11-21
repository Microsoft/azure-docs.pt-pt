---
title: Quickstart - Create an Azure private DNS zone using the Azure portal
description: In this quickstart, you create and test a private DNS zone and record in Azure DNS. This is a step-by-step guide to create and manage your first private DNS zone and record using the Azure portal.
services: dns
author: asudbring
ms.service: dns
ms.topic: quickstart
ms.date: 10/11/2019
ms.author: allensu
ms.openlocfilehash: 48c5684dd491a91b6da212f78c535433ac2aecf7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74210757"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Quickstart: Create an Azure private DNS zone using the Azure portal

This quickstart walks you through the steps to create your first private DNS zone and record using the Azure portal.

Uma zona DNS é utilizada para alojar os registos de DNS de um domínio específico. Para começar a alojar o seu domínio no DNS do Azure, tem de criar uma zona DNS para esse nome de domínio. Cada registo DNS para o seu domínio é então criado no interior desta zona DNS. Para publicar uma zona DNS privada na sua rede virtual, especifique a lista de redes virtuais autorizadas a resolver registos na zona.  These are called *linked* virtual networks. When autoregistration is enabled, Azure DNS also updates the zone records whenever a virtual machine is created, changes its' IP address, or is deleted.

Neste início rápido, vai aprender a:

> [!div class="checklist"]
> * Create a private DNS zone
> * Criar rede virtual
> * Link the virtual network
> * Criar máquinas virtuais de teste
> * Criar um registo DNS adicional
> * Testar a zona privada

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

If you prefer, you can complete this quickstart using [Azure PowerShell](private-dns-getstarted-powershell.md) or [Azure CLI](private-dns-getstarted-cli.md).

## <a name="create-a-private-dns-zone"></a>Create a private DNS zone

The following example creates a DNS zone called **private.contoso.com** in a resource group called **MyAzureResourceGroup**.

A DNS zone contains the DNS entries for a domain. To start hosting your domain in Azure DNS, you create a DNS zone for that domain name.

![Private DNS zones search](media/private-dns-portal/search-private-dns.png)

1. On the portal search bar, type **private dns zones** in the search text box and press **Enter**.
1. Select **Private DNS zone**.
2. Select **Create private dns zone**.

1. On the **Create Private DNS zone** page, type or select the following values:

   - **Resource group**: Select **Create new**, enter *MyAzureResourceGroup*, and select **OK**. The resource group name must be unique within the Azure subscription. 
   -  **Name**: Type *private.contoso.com* for this example.
1. For **Resource group location**, select **West Central US**.

1. Select **Review + Create**.

1. Selecione **Criar**.

A criação da zona pode demorar alguns minutos.

## <a name="create-a-virtual-network"></a>Criar rede virtual

1. On the portal page upper left, select **Create a resource**, then **Networking**, then select **Virtual network**.
2. For **Name**, type **myAzureVNet**.
3. For **Resource group**, select **MyAzureResourceGroup**.
4. For **Location**, select **West Central US**.
5. Accept the other default values and select **Create**.

## <a name="link-the-virtual-network"></a>Link the virtual network

To link the private DNS zone to a virtual network, you create a virtual network link.

![Add virtual network link](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Open the **MyAzureResourceGroup** resource group and select the **private.contoso.com** private zone.
2. On the left pane, select **Virtual network links**.
3. Selecione **Adicionar**.
4. Type **myLink** for the **Link name**.
5. For **Virtual network**, select **myAzureVNet**.
6. Select the **Enable auto registration** check box.
7. Selecione **OK**.

## <a name="create-the-test-virtual-machines"></a>Criar as máquinas virtuais de teste

Agora, vai criar duas máquinas virtuais para testar a zona DNS privada:

1. On the portal page upper left, select **Create a resource**, and then select **Windows Server 2016 Datacenter**.
1. Select **MyAzureResourceGroup** for the resource group.
1. Type **myVM01** - for the name of the virtual machine.
1. Select **West Central US** for the **Region**.
1. Type **azureadmin** for the administrator user name.
2. Type **Azure12345678** for the password and confirm the password.

5. For **Public inbound ports**, select **Allow selected ports**, and then select **RDP (3389)** for **Select inbound ports**.
10. Accept the other defaults for the page and then click **Next: Disks >** .
11. Accept the defaults on the **Disks** page, then click **Next: Networking >** .
1. Make sure that **myAzureVNet** is selected for the virtual network.
1. Accept the other defaults for the page, and then click **Next: Management >** .
2. For **Boot diagnostics**, select **Off**, accept the other defaults, and then select **Review + create**.
1. Review the settings and then click **Create**.

Repeat these steps and create another virtual machine named **myVM02**.

It will take a few minutes for both virtual machines to complete.

## <a name="create-an-additional-dns-record"></a>Criar um registo DNS adicional

 The following example creates a record with the relative name **db** in the DNS Zone **private.contoso.com**, in resource group **MyAzureResourceGroup**. The fully qualified name of the record set is **db.private.contoso.com**. The record type is "A", with the IP address of **myVM01**.

1. Open the **MyAzureResourceGroup** resource group and select the **private.contoso.com** private zone.
2. Selecione **+ Conjunto de registos**.
3. For **Name**, type **db**.
4. For **IP Address**, type the IP address you see for **myVM01**. This should be auto registered when the virtual machine started.
5. Selecione **OK**.

## <a name="test-the-private-zone"></a>Testar a zona privada

Now you can test the name resolution for your **private.contoso.com** private zone.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Configurar as VMs para permitir ICMP de entrada

Pode utilizar o comando ping para testar a resolução de nomes. Assim, configure a firewall em ambas as máquinas virtuais para permitir pacotes ICMP de entrada.

1. Ligue a myVM01 e abra uma janela do Windows PowerShell com privilégios de administrador.
2. Execute o seguinte comando:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Repita para myVM02.

### <a name="ping-the-vms-by-name"></a>Enviar pings para as VMs por nome

1. Na linha de comandos do Windows PowerShell para myVM02, envie um ping myVM01 com o nome de anfitrião registado automaticamente:
   ```
   ping myVM01.private.contoso.com
   ```
   Deverá ver um resultado semelhante ao seguinte:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Agora, envie um ping para o nome **db** criado anteriormente:
   ```
   ping db.private.contoso.com
   ```
   Deverá ver um resultado semelhante ao seguinte:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Eliminar todos os recursos

When no longer needed, delete the **MyAzureResourceGroup** resource group to delete the resources created in this quickstart.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Azure DNS Private Zones scenarios](private-dns-scenarios.md)

