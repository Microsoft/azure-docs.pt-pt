---
title: Tutorial - Criar uma zona privada do DNS do Azure com o Azure PowerShell
description: Neste tutorial, vai criar e testar uma zona DNS privada e um registo no DNS do Azure. Este é um guia passo a passo para criar e gerir a sua primeira zona DNS privada e o registo com o Azure PowerShell.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 7/24/2018
ms.author: victorh
ms.openlocfilehash: 872227e0521bd54e6bf7fdbe3626dfca34170863
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257730"
---
# <a name="tutorial-create-an-azure-dns-private-zone-using-azure-powershell"></a>Tutorial: Criar uma zona privada do DNS do Azure com o Azure PowerShell

Este tutorial explica-lhe os passos para criar a sua primeira zona DNS privada e o registo com o Azure PowerShell.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Uma zona DNS é utilizada para alojar os registos DNS para um determinado domínio. Para começar a alojar o seu domínio no DNS do Azure, tem de criar uma zona DNS para esse nome de domínio. Cada registo DNS para o seu domínio é então criado no interior desta zona DNS. Para publicar uma zona DNS privada na sua rede virtual, especifique a lista de redes virtuais autorizadas a resolver registos na zona.  São denominadas *redes virtuais de resolução*. Também pode especificar redes virtuais para as quais o DNS do Azure mantém registos de nomes de anfitrião sempre que uma VM for criada, mudar de IP ou for eliminada.  Chamamos a isto uma *rede virtual de registo*.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma zona DNS privada
> * Criar máquinas virtuais de teste
> * Criar um registo DNS adicional
> * Testar a zona privada

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se preferir, pode concluir este tutorial com a [CLI do Azure](private-dns-getstarted-cli.md).

<!--- ## Get the Preview PowerShell modules
These instructions assume you have already installed and signed in to Azure PowerShell, including ensuring you have the required modules for the Private Zone feature. -->

<!---[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)] -->

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Primeiro, crie um grupo de recursos para conter a zona DNS: 

```azurepowershell
New-AzureRMResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-dns-private-zone"></a>Criar uma zona DNS privada

Uma zona DNS é criada com o cmdlet `New-AzureRmDnsZone`, com um valor de *Privado* para o parâmetro **ZoneType**. O exemplo seguinte cria uma zona DNS denominada **contoso.local** no grupo de recursos **MyAzureResourceGroup** e disponibiliza a zona DNS na rede virtual **MyAzureVnet** .

Se o parâmetro **ZoneType** for omitido, a zona é criada como uma zona pública, pelo que é necessário para criar uma zona privada. 

```azurepowershell
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyAzureResourceGroup `
   -ZoneType Private `
   -RegistrationVirtualNetworkId @($vnet.Id)
```

Se quiser criar uma zona apenas para resolução de nomes (criação de nomes de anfitrião não automática), pode utilizar o parâmetro *ResolutionVirtualNetworkId* em vez do parâmetro *RegistrationVirtualNetworkId*.

> [!NOTE]
> Não poderá ver os registos de nomes de anfitrião criados automaticamente. No entanto, mais tarde, vai testar para garantir que existem.

### <a name="list-dns-private-zones"></a>Listar as zonas DNS privadas

Ao omitir o nome da zona de `Get-AzureRmDnsZone`, pode enumerar todas as zonas de um grupo de recursos. Esta operação devolve uma matriz de objetos de zona.

```azurepowershell
Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Ao omitir o nome da zona e o nome do grupo de recursos de `Get-AzureRmDnsZone`, pode enumerar todas as zonas na subscrição do Azure.

```azurepowershell
Get-AzureRmDnsZone
```

## <a name="create-the-test-virtual-machines"></a>Criar as máquinas virtuais de teste

Agora, vai criar duas máquinas virtuais para testar a zona DNS privada:

```azurepowershell
New-AzureRmVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzureRmVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM02" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389
```

Este processo irá demorar alguns minutos a concluir.

## <a name="create-an-additional-dns-record"></a>Criar um registo DNS adicional

Pode criar conjuntos de registos com o cmdlet `New-AzureRmDnsRecordSet`. O exemplo seguinte cria um registo com o nome relativo **db** na Zona DNS **contoso.com**, no grupo de recursos **MyAzureResourceGroup**. O nome totalmente qualificado do conjunto de registos é **db.contoso.local**. O tipo de registo é "A", com o endereço IP "10.2.0.4" e o TTL é de 3600 segundos.

```azurepowershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>Ver os registos DNS

Para listar os registos DNS na sua zona, execute:

```azurepowershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyAzureResourceGroup
```
Lembre-se de que não verá os registos A criados automaticamente para as duas máquinas virtuais de teste.

## <a name="test-the-private-zone"></a>Testar a zona privada

Agora, pode testar a resolução de nomes para a zona privada **contoso.local**.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Configurar as VMs para permitir ICMP de entrada

Pode utilizar o comando ping para testar a resolução de nomes. Assim, configure a firewall em ambas as máquinas virtuais para permitir pacotes ICMP de entrada.

1. Ligue a myVM01 e abra uma janela do Windows PowerShell com privilégios de administrador.
2. Execute o seguinte comando:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Repita para myVM02.

### <a name="ping-the-vms-by-name"></a>Enviar pings para as VMs por nome

1. Na linha de comandos do Windows PowerShell para myVM02, envie um ping myVM01 com o nome de anfitrião registado automaticamente:
   ```
   ping myVM01.contoso.local
   ```
   Deverá ver um resultado semelhante ao seguinte:
   ```
   PS C:\> ping myvm01.contoso.local

   Pinging myvm01.contoso.local [10.2.0.4] with 32 bytes of data:
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
   ping db.contoso.local
   ```
   Deverá ver um resultado semelhante ao seguinte:
   ```
   PS C:\> ping db.contoso.local

   Pinging db.contoso.local [10.2.0.4] with 32 bytes of data:
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

Quando já não for necessário, elimine o grupo de recursos **MyAzureResourceGroup** para eliminar os recursos criados neste tutorial.

```azurepowershell
Remove-AzureRMResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou uma zona DNS privada, criou um registo DNS e testou a zona.
Em seguida, pode saber mais sobre zonas DNS privadas.

> [!div class="nextstepaction"]
> [Utilizar o DNS do Azure para domínios privados](private-dns-overview.md)




