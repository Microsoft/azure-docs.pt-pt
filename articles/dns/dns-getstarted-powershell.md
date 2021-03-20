---
title: 'Quickstart: Crie uma zona Azure DNS e grave - Azure PowerShell'
titleSuffix: Azure DNS
description: Saiba como criar uma zona DNS e o registar no DNS do Azure. Este é um guia passo a passo para criar e gerir a sua primeira zona DNS e registar com o Azure PowerShell.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f2563c33d02490732f73fcf9d1a78f548ec2d3e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92282225"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>Início rápido: Criar uma zona DNS do Azure e registar com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Neste início rápido, cria a sua primeira zona DNS e regista-a com o Azure PowerShell. Também pode executar estes passos com o [portal do Azure](dns-getstarted-portal.md) ou o [CLI do Azure](dns-getstarted-cli.md). 

Uma zona DNS é utilizada para alojar os registos DNS para um determinado domínio. Para começar a alojar o seu domínio no DNS do Azure, tem de criar uma zona DNS para esse nome de domínio. Cada registo DNS para o seu domínio é então criado no interior desta zona DNS. Por fim, para publicar a zona DNS na Internet, tem de configurar os servidores de nomes do domínio. Cada um destes passos está descrito abaixo.

O DNS do Azure também suporta a criação de domínios privados. Para obter instruções passo-a-passo sobre como criar a primeira zona DNS privada e o registo, veja [Introdução às zonas privadas do DNS do Azure com o PowerShell](private-dns-getstarted-powershell.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure PowerShell instalado localmente ou Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar a zona DNS, crie um grupo de recursos para conter a zona DNS:

```powershell
New-AzResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada ao utilizar o cmdlet `New-AzDnsZone`. O exemplo a seguir cria uma zona DENS chamada *contoso.xyz* no grupo de recursos chamado *MyResourceGroup*. Utilize o exemplo para criar uma zona DNS, substituindo os valores pelos seus.

```powershell
New-AzDnsZone -Name contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>Criar um registo DNS

Pode criar conjuntos de registos com o cmdlet `New-AzDnsRecordSet`. O exemplo a seguir cria um registo com o nome relativo "www" na Zona DNS "contoso.xyz", no grupo de recursos "MyResourceGroup". O nome totalmente qualificado do recorde é "www.contoso.xyz". O tipo de gravação é "A", com endereço IP "10.10.10.10", e o TTL é de 3600 segundos.

```powershell
New-AzDnsRecordSet -Name www -RecordType A -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.10.10.10")
```

## <a name="view-records"></a>Ver registos

Para listar os registos DNS na sua zona, utilize:

```powershell
Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="test-the-name-resolution"></a>Testar a resolução de nomes

Agora que tem uma zona de DNS de teste com um registo de teste 'A', pode testar a resolução do nome com uma ferramenta chamada *nslookup*. 

**Para testar a resolução do nome DNS:**

1. Executar o seguinte cmdlet para obter a lista de servidores de nome para a sua zona:

   ```azurepowershell
   Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -RecordType ns
   ```

1. Copie um dos nomes do servidor de nomes da saída do passo anterior.

1. Abra um pedido de comando e executar o seguinte comando:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Por exemplo:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Deve ver algo como o seguinte ecrã:

   ![A screenshot mostra uma janela de pedido de comando com um comando de procura n s e valores para Servidor, Endereço, Nome e Endereço.](media/dns-getstarted-portal/nslookup.PNG)

O nome de anfitrião **www \. contoso.xyz** resolve para **10.10.10.10**, tal como o configura. Este resultado verifica que a resolução de nomes está a funcionar corretamente.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não forem necessários, pode eliminar todos os recursos criados neste Início Rápido ao eliminar o grupo de recursos:

```powershell
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Agora que criou sua primeira zona DNS e o registo com o Azure PowerShell, pode criar registos para uma aplicação Web num domínio personalizado.

> [!div class="nextstepaction"]
> [Criar registos DNS para uma aplicação Web num domínio personalizado](./dns-web-sites-custom-domain.md)

