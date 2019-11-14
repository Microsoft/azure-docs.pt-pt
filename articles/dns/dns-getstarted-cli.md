---
title: 'Início rápido: criar uma zona DNS do Azure e CLI do Azure de registro'
titleSuffix: Azure DNS
description: Início Rápido - Saiba como criar uma zona DNS e o registar no DNS do Azure. Este é um guia passo a passo para criar e gerir a sua primeira zona DNS e registar com a CLI do Azure.
services: dns
author: asudbring
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: allensu
ms.openlocfilehash: 14d47a82ec6b5ec0ede626748216889a6943bfa6
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072166"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>Início Rápido: criar uma zona DNS do Azure e registar com a CLI do Azure

Este artigo explica-lhe os passos para criar a primeira zona DNS e registar com a CLI 1.0 do Azure, que está disponível para Windows, Mac e Linux. Também pode executar estes passos com o [portal do Azure](dns-getstarted-portal.md) ou com o [Azure PowerShell](dns-getstarted-powershell.md).

Uma zona DNS serve para alojar os registos DNS para um determinado domínio. Para começar a alojar o seu domínio no DNS do Azure, tem de criar uma zona DNS para esse nome de domínio. Cada registo DNS para o seu domínio é então criado no interior desta zona DNS. Por fim, para publicar a zona DNS na Internet, tem de configurar os servidores de nomes do domínio. Cada um destes passos está descrito abaixo.

O DNS do Azure também dá suporte a zonas DNS privadas. Para saber mais sobre zonas DNS privadas, veja [Utilizar o DNS do Azure para domínios privados](private-dns-overview.md). Para obter um exemplo de como criar uma zona DNS privada, veja [Começar a utilizar zonas privadas do DNS do Azure com a CLI](./private-dns-getstarted-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar a zona DNS, crie um grupo de recursos para conter a zona DNS:

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada ao utilizar o comando `az network dns zone create`. Para ver a ajuda deste comando, escreva `az network dns zone create -h`.

O exemplo a seguir cria uma zona DNS chamada *contoso. xyz* no grupo de recursos *MyResource*Group. Utilize o exemplo para criar uma zona DNS, substituindo os valores pelos seus.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.xyz
```

## <a name="create-a-dns-record"></a>Criar um registo DNS

Para criar um registo DNS, utilize o comando `az network dns record-set [record type] add-record`. Para obter ajuda com os registos A, veja `azure network dns record-set A add-record -h`.

O exemplo a seguir cria um registro com o nome relativo "www" na zona DNS "contoso. xyz" no grupo de recursos "MyResource Group". O nome totalmente qualificado do conjunto de registros é "www. contoso. xyz". O tipo de registro é "A", com o endereço IP "10.10.10.10", e um TTL padrão de 3600 segundos (1 hora).

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.xyz -n www -a 10.10.10.10
```

## <a name="view-records"></a>Ver registos

Para listar os registos DNS na sua zona, execute:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.xyz
```

## <a name="test-the-name-resolution"></a>Testar a resolução de nomes

Agora que você tem uma zona DNS de teste com um registro ' A ' de teste, você pode testar a resolução de nome com uma ferramenta chamada *nslookup*. 

**Para testar a resolução de nomes DNS:**

1. Execute o seguinte cmdlet para obter a lista de servidores de nomes para sua zona:

   ```azurecli
   az network dns record-set ns show --resource-group MyResourceGroup --zone-name contoso.xyz --name @
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

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Agora que criou sua primeira zona DNS e o registo com a CLI do Azure, pode criar registos para uma aplicação Web num domínio personalizado.

> [!div class="nextstepaction"]
> [Criar registos DNS para uma aplicação Web num domínio personalizado](./dns-web-sites-custom-domain.md)
