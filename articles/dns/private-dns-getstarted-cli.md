---
title: Quickstart - Criar uma zona DNS privada Azure utilizando o Azure CLI
description: Neste arranque rápido, você cria e testa uma zona privada de DNS e regista em DNS Azure. Este é um guia passo a passo para criar e gerir a sua primeira zona DNS privada e o registo com a CLI do Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 34a9858ffbec2360a347b8c765d53551fd2d9fc0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76935433"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-cli"></a>Quickstart: Criar uma zona DNS privada Azure utilizando o Azure CLI

Este quickstart acompanha-o através dos degraus para criar a sua primeira zona privada de DNS e gravar usando o Azure CLI.

Uma zona DNS é utilizada para alojar os registos DNS para um determinado domínio. Para começar a alojar o seu domínio no DNS do Azure, tem de criar uma zona DNS para esse nome de domínio. Cada registo DNS para o seu domínio é então criado no interior desta zona DNS. Para publicar uma zona DNS privada na sua rede virtual, especifique a lista de redes virtuais autorizadas a resolver registos na zona.  Estas são chamadas redes virtuais *ligadas.* Quando o registo automático está ativado, o Azure DNS também atualiza os registos de zona sempre que uma máquina virtual é criada, altera o seu endereço IP ou é eliminado.

Neste início rápido, vai aprender a:

> [!div class="checklist"]
> * Criar uma zona privada de DNS
> * Criar máquinas virtuais de teste
> * Criar um registo DNS adicional
> * Testar a zona privada

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se preferir, pode completar este quickstart utilizando [o Azure PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Primeiro, crie um grupo de recursos para conter a zona DNS: 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-private-dns-zone"></a>Criar uma zona privada de DNS

O exemplo seguinte cria uma rede virtual chamada **myAzureVNet**. Em seguida, cria uma zona DNS chamada **private.contoso.com** no grupo de recursos **MyAzureResourceGroup,** liga a zona DNS à rede virtual **MyAzureVnet** e permite o registo automático.

```azurecli
az network vnet create \
  --name myAzureVNet \
  --resource-group MyAzureResourceGroup \
  --location eastus \
  --address-prefix 10.2.0.0/16 \
  --subnet-name backendSubnet \
  --subnet-prefixes 10.2.0.0/24

az network private-dns zone create -g MyAzureResourceGroup \
   -n private.contoso.com

az network private-dns link vnet create -g MyAzureResourceGroup -n MyDNSLink \
   -z private.contoso.com -v myAzureVNet -e true
```

Se quiser criar uma zona apenas para resolução de nomes `-e false` (sem registo automático do nome de anfitrião), pode utilizar o parâmetro.

### <a name="list-dns-private-zones"></a>Listar as zonas DNS privadas

Para enumerar as zonas DNS, utilize `az network private-dns zone list`. Para obter ajuda, consulte `az network dns zone list --help`.

Especificar o grupo de recursos lista apenas essas zonas no grupo de recursos:

```azurecli
az network private-dns zone list \
  -g MyAzureResourceGroup
```

A omissão do grupo de recursos lista todas as zonas na subscrição:

```azurecli
az network private-dns zone list 
```

## <a name="create-the-test-virtual-machines"></a>Criar as máquinas virtuais de teste

Agora, vai criar duas máquinas virtuais para testar a zona DNS privada:

```azurecli
az vm create \
 -n myVM01 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter

az vm create \
 -n myVM02 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter
```

Este processo irá demorar alguns minutos a concluir.

## <a name="create-an-additional-dns-record"></a>Criar um registo DNS adicional

Para criar um registo DNS, utilize o comando `az network private-dns record-set [record type] add-record`. Para obter ajuda ao adicionar registos A, por exemplo, veja `az network private-dns record-set A add-record --help`.

 O exemplo seguinte cria um registo com o nome relativo **db** na Zona DNS **private.contoso.com**, no grupo de recursos **MyAzureResourceGroup**. O nome totalmente qualificado do conjunto de discos é **db.private.contoso.com**. O tipo de registo é "A", com o endereço IP "10.2.0.4".

```azurecli
az network private-dns record-set a add-record \
  -g MyAzureResourceGroup \
  -z private.contoso.com \
  -n db \
  -a 10.2.0.4
```

### <a name="view-dns-records"></a>Ver os registos DNS

Para listar os registos DNS na sua zona, execute:

```azurecli
az network private-dns record-set list \
  -g MyAzureResourceGroup \
  -z private.contoso.com
```

## <a name="test-the-private-zone"></a>Testar a zona privada

Agora pode testar a resolução de nomes para a sua zona privada **private.contoso.com.**

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

Quando já não for necessário, elimine o grupo de recursos **myAzureResourceGroup** para eliminar os recursos criados neste arranque rápido.

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Cenários de Zonas Privadas do DNS do Azure](private-dns-scenarios.md)

