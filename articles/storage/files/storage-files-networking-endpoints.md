---
title: Configurar pontos finais da rede Azure Files Microsoft Docs
description: Saiba como configurar os pontos finais da rede de ficheiros Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/17/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 880eeb87d8727d65b2aaecdad8b0ed9ccaacea7a
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629857"
---
# <a name="configuring-azure-files-network-endpoints"></a>Configurar pontos finais da rede Azure Files

A Azure Files fornece dois tipos principais de pontos finais para aceder a ações de ficheiros Azure: 
- Pontos finais públicos, que têm um endereço IP público e podem ser acedidos a partir de qualquer parte do mundo.
- Pontos finais privados, que existem dentro de uma rede virtual e têm um endereço IP privado a partir do espaço de endereço dessa rede virtual.

Os pontos finais públicos e privados existem na conta de armazenamento Azure. Uma conta de armazenamento é uma construção de gestão que representa um conjunto partilhado de armazenamento no qual você pode implementar várias partilhas de arquivos, bem como outros recursos de armazenamento, tais como recipientes blob ou filas.

Este artigo centra-se em como configurar os pontos finais de uma conta de armazenamento para aceder diretamente à partilha de ficheiros Azure. A maior parte dos detalhes fornecidos neste documento também se aplica à forma como o Azure File Sync interopera com pontos finais públicos e privados para a conta de armazenamento, no entanto para obter mais informações sobre considerações de networking para uma implementação de Azure File Sync, consulte [configurar configurações de proxy e firewall do Azure File Sync](storage-sync-files-firewall-and-proxy.md).

Recomendamos a leitura [de considerações de networking de ficheiros Azure](storage-files-networking-overview.md) files antes de ler este como orientar.

## <a name="prerequisites"></a>Pré-requisitos

- Este artigo pressupõe que já criou uma subscrição do Azure. Se ainda não tem uma subscrição, então crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Este artigo pressupõe que já criou uma partilha de ficheiros Azure numa conta de armazenamento à que gostaria de ligar a partir do local. Para aprender a criar uma partilha de ficheiros Azure, consulte [Criar uma partilha de ficheiros Azure](storage-how-to-create-file-share.md).
- Se pretender utilizar o Azure PowerShell, [instale a versão mais recente](/powershell/azure/install-az-ps).
- Se pretender utilizar o Azure CLI, [instale a versão mais recente](/cli/azure/install-azure-cli).

## <a name="endpoint-configurations"></a>Configurações de ponto final

Pode configurar os seus pontos finais para restringir o acesso à rede à sua conta de armazenamento. Existem duas abordagens para restringir o acesso a uma conta de armazenamento a uma rede virtual:

- [Crie um ou mais pontos finais privados para a conta de armazenamento](#create-a-private-endpoint)  e restrinja todo o acesso ao ponto final público. Isto garante que apenas o tráfego originário das redes virtuais pretendidas pode aceder às ações de ficheiros Azure dentro da conta de armazenamento.
- [Restringir o ponto final público a uma ou mais redes virtuais](#restrict-public-endpoint-access). Isto funciona utilizando uma capacidade da rede virtual chamada *pontos finais de serviço*. Quando restringe o tráfego a uma conta de armazenamento através de um ponto final de serviço, ainda está a aceder à conta de armazenamento através do endereço IP público, mas o acesso só é possível a partir dos locais especificados na sua configuração.

### <a name="create-a-private-endpoint"></a>Criar um ponto final privado

A criação de um ponto final privado para a sua conta de armazenamento resultará na implementação dos seguintes recursos Azure:

- **Um ponto final privado** : Um recurso Azure que representa o ponto final privado da conta de armazenamento. Pode pensar nisto como um recurso que liga uma conta de armazenamento e uma interface de rede.
- **Uma interface de rede (NIC)** : A interface de rede que mantém um endereço IP privado dentro da rede/sub-rede virtual especificada. Este é exatamente o mesmo recurso que é implantado quando se implanta uma máquina virtual, no entanto, em vez de ser atribuído a um VM, é propriedade do ponto final privado.
- **Uma zona privada de DNS** : Se nunca implementou um ponto final privado para esta rede virtual antes, uma nova zona privada de DNS será implantada para a sua rede virtual. Será também criado um registo de DNS para a conta de armazenamento nesta zona de DNS. Se já implementou um ponto final privado nesta rede virtual, será adicionado um novo recorde A para a conta de armazenamento na zona de DNS existente. A implantação de uma zona DE DNS é opcional, por muito recomendada que seja, e necessária se estiver a montar as suas partilhas de ficheiros Azure com um responsável de serviço AD ou a utilizar a API filerest.

> [!Note]  
> Este artigo utiliza o sufixo DNS da conta de armazenamento para as regiões públicas do Azure, `core.windows.net` . Este comentário também se aplica às nuvens soberanas de Azure, como a nuvem do Governo dos EUA Azure e a nuvem Azure China - basta substituir os sufixos apropriados para o seu ambiente. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

Se tiver uma máquina virtual dentro da sua rede virtual, ou tiver configurado o reencaminhamento de DNS como descrito no [reencaminhamento de DNS para Ficheiros Azure,](storage-files-networking-dns.md)pode testar que o seu ponto final privado foi configurado corretamente executando os seguintes comandos da PowerShell, da linha de comando ou do terminal (funciona para Windows, Linux ou macOS). Deve substituir `<storage-account-name>` pelo nome da conta de armazenamento adequada:

```
nslookup <storage-account-name>.file.core.windows.net
```

Se tudo tiver funcionado com sucesso, deverá ver a seguinte saída, onde `192.168.0.5` está o endereço IP privado do ponto final privado na sua rede virtual (saída mostrada para Windows):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

Se tiver uma máquina virtual dentro da sua rede virtual, ou tiver configurado o reencaminhamento de DNS como descrito na [configuração do encaminhamento de DNS para ficheiros Azure,](storage-files-networking-dns.md)pode testar que o seu ponto final privado foi configurado corretamente com os seguintes comandos:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Se tudo funcionou com sucesso, deverá ver a seguinte saída, onde `192.168.0.5` está o endereço IP privado do ponto final privado na sua rede virtual:

```Output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-private-cli](../../../includes/storage-files-networking-endpoints-private-cli.md)]

Se tiver uma máquina virtual dentro da sua rede virtual, ou tiver configurado o reencaminhamento de DNS como descrito na [configuração do encaminhamento de DNS para ficheiros Azure,](storage-files-networking-dns.md)pode testar que o seu ponto final privado foi configurado corretamente com os seguintes comandos:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Se tudo funcionou com sucesso, deverá ver a seguinte saída, onde `192.168.0.5` está o endereço IP privado do ponto final privado na sua rede virtual. Deve ainda utilizar storageaccount.file.core.windows.net para montar a sua partilha de ficheiros em vez do `privatelink` caminho.

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

### <a name="restrict-public-endpoint-access"></a>Restringir o acesso ao ponto final público

Limitar o acesso ao ponto final público requer primeiro que desative o acesso geral ao ponto final público. O acesso incapacitante ao ponto final público não afeta os pontos finais privados. Depois de desativado o ponto final público, pode selecionar redes específicas ou endereços IP que possam continuar a aceder ao mesmo. Em geral, a maioria das políticas de firewall para uma conta de armazenamento restringem o acesso em rede a uma ou mais redes virtuais.

#### <a name="disable-access-to-the-public-endpoint"></a>Desativar o acesso ao ponto final público

Quando o acesso ao ponto final público é desativado, a conta de armazenamento ainda pode ser acedida através dos seus pontos finais privados. Caso contrário, os pedidos válidos para o ponto final da conta de armazenamento serão rejeitados, a menos que sejam de [uma fonte especificamente permitida.](#restrict-access-to-the-public-endpoint-to-specific-virtual-networks) 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>Restringir o acesso ao ponto final público a redes virtuais específicas

Quando restringe a conta de armazenamento a redes virtuais específicas, está a permitir pedidos para o ponto final público a partir das redes virtuais especificadas. Isto funciona utilizando uma capacidade da rede virtual chamada *pontos finais de serviço*. Isto pode ser usado com ou sem pontos finais privados.

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

## <a name="see-also"></a>Ver também

- [Considerações de networking de ficheiros Azure](storage-files-networking-overview.md)
- [Configurar o reencaminhamento de DNS para Ficheiros do Azure](storage-files-networking-dns.md)
- [Configurar S2S VPN para ficheiros Azure](storage-files-configure-s2s-vpn.md)