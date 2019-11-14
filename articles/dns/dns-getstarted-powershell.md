---
title: 'Início rápido: criar uma zona DNS do Azure e Azure PowerShell de registro'
titleSuffix: Azure DNS
description: Saiba como criar uma zona DNS e o registar no DNS do Azure. Este é um guia passo a passo para criar e gerir a sua primeira zona DNS e registar com o Azure PowerShell.
services: dns
author: asudbring
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: allensu
ms.openlocfilehash: 0d3852824702bbc88925981c3c665b1b3fda7404
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072175"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>Início rápido: Criar uma zona DNS do Azure e registar com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Neste início rápido, cria a sua primeira zona DNS e regista-a com o Azure PowerShell. Também pode executar estes passos com o [portal do Azure](dns-getstarted-portal.md) ou o [CLI do Azure](dns-getstarted-cli.md). 

Uma zona DNS serve para alojar os registos DNS para um determinado domínio. Para começar a alojar o seu domínio no DNS do Azure, tem de criar uma zona DNS para esse nome de domínio. Cada registo DNS para o seu domínio é então criado no interior desta zona DNS. Por fim, para publicar a zona DNS na Internet, tem de configurar os servidores de nomes do domínio. Cada um destes passos está descrito abaixo.

O DNS do Azure também suporta a criação de domínios privados. Para obter instruções passo-a-passo sobre como criar a primeira zona DNS privada e o registo, veja [Introdução às zonas privadas do DNS do Azure com o PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar a zona DNS, crie um grupo de recursos para conter a zona DNS:

```powershell
New-AzResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada ao utilizar o cmdlet `New-AzDnsZone`. O exemplo a seguir cria uma zona DNS chamada *contoso. xyz* no grupo de recursos chamado *MyResource*Group. Utilize o exemplo para criar uma zona DNS, substituindo os valores pelos seus.

```powershell
New-AzDnsZone -Name contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>Criar um registo DNS

Pode criar conjuntos de registos com o cmdlet `New-AzDnsRecordSet`. O exemplo a seguir cria um registro com o nome relativo "www" na zona DNS "contoso. xyz", no grupo de recursos "MyResource Group". O nome totalmente qualificado do conjunto de registros é "www. contoso. xyz". O tipo de registro é "A", com o endereço IP "10.10.10.10", e o TTL é de 3600 segundos.

```powershell
New-AzDnsRecordSet -Name www -RecordType A -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.10.10.10")
```

## <a name="view-records"></a>Ver registos

Para listar os registos DNS na sua zona, utilize:

```powershell
Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="test-the-name-resolution"></a>Testar a resolução de nomes

Agora que você tem uma zona DNS de teste com um registro ' A ' de teste, você pode testar a resolução de nome com uma ferramenta chamada *nslookup*. 

**Para testar a resolução de nomes DNS:**

1. Execute o seguinte cmdlet para obter a lista de servidores de nomes para sua zona:

   ```azurepowershell
   Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -RecordType ns
   ```

1. Copie um dos nomes de servidor de nomes da saída da etapa anterior.

1. Abra um prompt de comando e execute o seguinte comando:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Por exemplo:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Você deverá ver algo parecido com a tela a seguir:

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

O nome de host **www\.contoso. xyz** é resolvido para **10.10.10.10**, assim como você o configurou. Esse resultado verifica se a resolução de nomes está funcionando corretamente.

## <a name="delete-all-resources"></a>Eliminar todos os recursos

Quando já não forem necessários, pode eliminar todos os recursos criados neste Início Rápido ao eliminar o grupo de recursos:

```powershell
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Agora que criou sua primeira zona DNS e o registo com o Azure PowerShell, pode criar registos para uma aplicação Web num domínio personalizado.

> [!div class="nextstepaction"]
> [Criar registos DNS para uma aplicação Web num domínio personalizado](./dns-web-sites-custom-domain.md)

