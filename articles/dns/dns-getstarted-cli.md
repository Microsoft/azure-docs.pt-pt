---
title: Início Rápido - Criar uma zona DNS do Azure e registar com a CLI do Azure
description: Início Rápido - Saiba como criar uma zona DNS e o registar no DNS do Azure. Este é um guia passo a passo para criar e gerir a sua primeira zona DNS e registar com a CLI do Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: b24b06ab121b1484e458a9e725fd90590af05c08
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761502"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>Início rápido: Criar uma zona DNS do Azure e o registo com a CLI do Azure

Este artigo explica-lhe os passos para criar a primeira zona DNS e registar com a CLI 1.0 do Azure, que está disponível para Windows, Mac e Linux. Também pode executar estes passos com o [portal do Azure](dns-getstarted-portal.md) ou com o [Azure PowerShell](dns-getstarted-powershell.md).

Uma zona DNS é utilizada para alojar os registos DNS para um determinado domínio. Para começar a alojar o seu domínio no DNS do Azure, tem de criar uma zona DNS para esse nome de domínio. Cada registo DNS para o seu domínio é então criado no interior desta zona DNS. Por fim, para publicar a zona DNS na Internet, tem de configurar os servidores de nomes do domínio. Cada um destes passos está descrito abaixo.

O DNS do Azure também suporta agora zonas DNS privadas (atualmente em pré-visualização pública). Para saber mais sobre zonas DNS privadas, veja [Utilizar o DNS do Azure para domínios privados](private-dns-overview.md). Para obter um exemplo de como criar uma zona DNS privada, veja [Começar a utilizar zonas privadas do DNS do Azure com a CLI](./private-dns-getstarted-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar a zona DNS, crie um grupo de recursos para conter a zona DNS:

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada ao utilizar o comando `az network dns zone create`. Para ver a ajuda deste comando, escreva `az network dns zone create -h`.

O exemplo seguinte cria uma zona DNS denominada *contoso.xyz* no grupo de recursos *MyResourceGroup*. Utilize o exemplo para criar uma zona DNS, substituindo os valores pelos seus.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.xyz
```

## <a name="create-a-dns-record"></a>Criar um registo DNS

Para criar um registo DNS, utilize o comando `az network dns record-set [record type] add-record`. Para obter ajuda com os registos A, veja `azure network dns record-set A add-record -h`.

O exemplo seguinte cria um registo com o nome relativo "www" na zona DNS "contoso.xyz" no grupo de recursos "MyResourceGroup". O nome completamente qualificado do conjunto de registos é "www.contoso.xyz". O tipo de registo é "A", com o endereço IP "10.10.10.10" e um TTL predefinido de 3600 segundos (1 hora).

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.xyz -n www -a 10.10.10.10
```

## <a name="view-records"></a>Ver registos

Para listar os registos DNS na sua zona, execute:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.xyz
```

## <a name="test-the-name-resolution"></a>Testar a resolução de nomes

Agora que tem uma zona DNS de teste com um registo de teste "A", pode testar a resolução de nomes com uma ferramenta chamada *nslookup*. 

**Para testar a resolução de nome DNS:**

1. Execute o seguinte cmdlet para obter a lista de servidores de nomes para a sua zona:

   ```azurecli
   az network dns record-set ns show --resource-group MyResourceGroup --zone-name contoso.xyz --name @
   ```

1. Copie um dos nomes de servidor a partir da saída do passo anterior.

1. Abra uma linha de comandos e execute o seguinte comando:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Por exemplo:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Deverá ver algo semelhante ao seguinte ecrã:

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

O nome do anfitrião **www.contoso.xyz** é resolvida para **10.10.10.10**, tal como tiver configurado. Este resultado verifica se a resolução de nomes está a funcionar corretamente.

## <a name="delete-all-resources"></a>Eliminar todos os recursos

Quando já não forem necessários, pode eliminar todos os recursos criados neste Início Rápido ao eliminar o grupo de recursos:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Agora que criou sua primeira zona DNS e o registo com a CLI do Azure, pode criar registos para uma aplicação Web num domínio personalizado.

> [!div class="nextstepaction"]
> [Criar registos DNS para uma aplicação Web num domínio personalizado](./dns-web-sites-custom-domain.md)
