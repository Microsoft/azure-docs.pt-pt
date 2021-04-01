---
title: 'Quickstart: Crie uma zona de DNS Azure e grave - Azure CLI'
titleSuffix: Azure DNS
description: Início Rápido - Saiba como criar uma zona DNS e o registar no DNS do Azure. Este é um guia passo a passo para criar e gerir a sua primeira zona DNS e registar com a CLI do Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1929cd512d18d7fd234aff1f55814c423455e63b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94561374"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>Início Rápido: criar uma zona DNS do Azure e registar com a CLI do Azure

Este artigo explica-lhe os passos para criar a primeira zona DNS e registar com a CLI 1.0 do Azure, que está disponível para Windows, Mac e Linux. Também pode executar estes passos com o [portal do Azure](dns-getstarted-portal.md) ou com o [Azure PowerShell](dns-getstarted-powershell.md).

Uma zona DNS é utilizada para alojar os registos DNS para um determinado domínio. Para começar a alojar o seu domínio no DNS do Azure, tem de criar uma zona DNS para esse nome de domínio. Cada registo DNS para o seu domínio é então criado no interior desta zona DNS. Por fim, para publicar a zona DNS na Internet, tem de configurar os servidores de nomes do domínio. Cada um destes passos está descrito abaixo.

O Azure DNS também suporta zonas privadas de DNS. Para saber mais sobre zonas DNS privadas, veja [Utilizar o DNS do Azure para domínios privados](private-dns-overview.md). Para obter um exemplo de como criar uma zona DNS privada, veja [Começar a utilizar zonas privadas do DNS do Azure com a CLI](./private-dns-getstarted-cli.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.4 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar a zona DNS, crie um grupo de recursos para conter a zona DNS:

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada ao utilizar o comando `az network dns zone create`. Para ver a ajuda deste comando, escreva `az network dns zone create -h`.

O exemplo a seguir cria uma zona DENS chamada *contoso.xyz* no grupo de recursos *MyResourceGroup*. Utilize o exemplo para criar uma zona DNS, substituindo os valores pelos seus.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.xyz
```

## <a name="create-a-dns-record"></a>Criar um registo DNS

Para criar um registo DNS, utilize o comando `az network dns record-set [record type] add-record`. Para obter ajuda com os registos A, veja `azure network dns record-set A add-record -h`.

O exemplo a seguir cria um registo com o nome relativo "www" na Zona DNS "contoso.xyz" no grupo de recursos "MyResourceGroup". O nome totalmente qualificado do recorde é "www.contoso.xyz". O tipo de gravação é "A", com endereço IP "10.10.10.10", e um TTL padrão de 3600 segundos (1 hora).

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.xyz -n www -a 10.10.10.10
```

## <a name="view-records"></a>Ver registos

Para listar os registos DNS na sua zona, execute:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.xyz
```

## <a name="test-the-name-resolution"></a>Testar a resolução de nomes

Agora que tem uma zona de DNS de teste com um registo de teste 'A', pode testar a resolução do nome com uma ferramenta chamada *nslookup*. 

**Para testar a resolução do nome DNS:**

1. Executar o seguinte cmdlet para obter a lista de servidores de nome para a sua zona:

   ```azurecli
   az network dns record-set ns show --resource-group MyResourceGroup --zone-name contoso.xyz --name @
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

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Agora que criou sua primeira zona DNS e o registo com a CLI do Azure, pode criar registos para uma aplicação Web num domínio personalizado.

> [!div class="nextstepaction"]
> [Criar registos DNS para uma aplicação Web num domínio personalizado](./dns-web-sites-custom-domain.md)
