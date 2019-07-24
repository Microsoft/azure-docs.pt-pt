---
title: Criar uma zona privada de DNS do Azure usando Azure PowerShell
description: Neste artigo, você cria e testa uma zona DNS privada e registro no DNS do Azure. Este é um guia passo a passo para criar e gerir a sua primeira zona DNS privada e o registo com o Azure PowerShell.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 06/14/2019
ms.author: victorh
ms.openlocfilehash: 6603929fa7b4c597a846fc299577a9682d8f54e0
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854125"
---
# <a name="create-an-azure-dns-private-zone-using-azure-powershell"></a>Criar uma zona privada de DNS do Azure usando Azure PowerShell

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Este artigo explica-lhe os passos para criar a sua primeira zona DNS privada e o registo com o Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Uma zona DNS é utilizada para alojar os registos DNS para um determinado domínio. Para começar a alojar o seu domínio no DNS do Azure, tem de criar uma zona DNS para esse nome de domínio. Cada registo DNS para o seu domínio é então criado no interior desta zona DNS. Para publicar uma zona DNS privada na sua rede virtual, especifique a lista de redes virtuais autorizadas a resolver registos na zona.  Elas são chamadas  de redes virtuais vinculadas. Quando o Autoregistro está habilitado, o DNS do Azure também atualiza os registros de zona sempre que uma máquina virtual é criada, altera seu ' endereço IP ' ou é excluído.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar uma zona DNS privada
> * Criar máquinas virtuais de teste
> * Criar um registo DNS adicional
> * Testar a zona privada

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se preferir, você pode concluir este procedimento usando [CLI do Azure](private-dns-getstarted-cli.md).

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Primeiro, crie um grupo de recursos para conter a zona DNS: 

```azurepowershell
New-AzResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-dns-private-zone"></a>Criar uma zona DNS privada

Uma zona DNS é criada ao utilizar o cmdlet `New-AzPrivateDnsZone`.

O exemplo a seguir cria uma rede virtual chamada **myAzureVNet**. Em seguida, ele cria uma zona DNS chamada **Private.contoso.com** no grupo de recursos **MyAzureResourceGroup** , vincula a zona DNS à rede virtual **MyAzureVnet** e habilita o registro automático.

```azurepowershell
Install-Module -Name Az.PrivateDns -force

$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

$zone = New-AzPrivateDnsZone -Name private.contoso.com -ResourceGroupName MyAzureResourceGroup

$link = New-AzPrivateDnsVirtualNetworkLink -ZoneName private.contoso.com `
  -ResourceGroupName MyAzureResourceGroup -Name "mylink" `
  -VirtualNetworkId $vnet.id -EnableRegistration
```

Se você quiser criar uma zona apenas para a resolução de nomes (sem registro de nome de host automático), `-EnableRegistration` poderá omitir o parâmetro.

### <a name="list-dns-private-zones"></a>Listar as zonas DNS privadas

Ao omitir o nome da zona de `Get-AzPrivateDnsZone`, pode enumerar todas as zonas de um grupo de recursos. Esta operação devolve uma matriz de objetos de zona.

```azurepowershell
$zones = Get-AzPrivateDnsZone -ResourceGroupName MyAzureResourceGroup
$zones
```

Ao omitir o nome da zona e o nome do grupo de recursos de `Get-AzPrivateDnsZone`, pode enumerar todas as zonas na subscrição do Azure.

```azurepowershell
$zones = Get-AzPrivateDnsZone
$zones
```

## <a name="create-the-test-virtual-machines"></a>Criar as máquinas virtuais de teste

Agora, vai criar duas máquinas virtuais para testar a zona DNS privada:

```azurepowershell
New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzVm `
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

Pode criar conjuntos de registos com o cmdlet `New-AzPrivateDnsRecordSet`. O exemplo a seguir cria um registro com o nome relativo **DB** na zona DNS **Private.contoso.com**, no grupo de recursos **MyAzureResourceGroup**. O nome totalmente qualificado do conjunto de registros é **DB.Private.contoso.com**. O tipo de registo é "A", com o endereço IP "10.2.0.4" e o TTL é de 3600 segundos.

```azurepowershell
New-AzPrivateDnsRecordSet -Name db -RecordType A -ZoneName private.contoso.com `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>Ver os registos DNS

Para listar os registos DNS na sua zona, execute:

```azurepowershell
Get-AzPrivateDnsRecordSet -ZoneName private.contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="test-the-private-zone"></a>Testar a zona privada

Agora você pode testar a resolução de nome para sua zona privada **Private.contoso.com** .

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
   Approximate round trip times in milliseconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Eliminar todos os recursos

Quando não for mais necessário, exclua o grupo de recursos **MyAzureResourceGroup** para excluir os recursos criados neste artigo.

```azurepowershell
Remove-AzResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, você implantou uma zona DNS privada, criou um registro DNS e testou a zona.
Em seguida, pode saber mais sobre zonas DNS privadas.

* [Utilizar o DNS do Azure para domínios privados](private-dns-overview.md)
