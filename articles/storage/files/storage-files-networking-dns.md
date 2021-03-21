---
title: Configurar o reencaminhamento de DNS para a Azure Files | Microsoft Docs
description: Saiba como configurar o reencaminhamento de DNS para ficheiros Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9abe306668a4b20e42e45c498bf85b540dfaaee5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94630197"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>Configurar o reencaminhamento de DNS para Ficheiros do Azure
O Azure Files permite-lhe criar pontos finais privados para as contas de armazenamento que contêm as suas ações de ficheiro. Embora úteis para muitas aplicações diferentes, os pontos finais privados são especialmente úteis para ligar às suas ações de ficheiros Azure da sua rede de acesso ao local utilizando uma ligação VPN ou ExpressRoute utilizando o peering privado. 

Para que as ligações à sua conta de armazenamento se debruçassem sobre o seu túnel de rede, o nome de domínio totalmente qualificado (FQDN) da sua conta de armazenamento deve resolver-se para o endereço IP privado do seu ponto final privado. Para isso, deve encaminhar o sufixo de ponto final de armazenamento `core.windows.net` (para regiões de nuvem pública) para o serviço de DNS privado Azure acessível a partir da sua rede virtual. Este guia mostrará como configurar e configurar o reencaminhamento de DNS para resolver corretamente o endereço IP do seu ponto final privado da sua conta de armazenamento.

Recomendamos vivamente que leia Planeamento para uma implementação de [Ficheiros Azure](storage-files-planning.md) e [considerações de networking de Ficheiros Azure](storage-files-networking-overview.md) antes de completar os passos descritos neste artigo.

## <a name="overview"></a>Descrição geral
A Azure Files fornece dois tipos principais de pontos finais para aceder a ações de ficheiros Azure: 
- Pontos finais públicos, que têm um endereço IP público e podem ser acedidos a partir de qualquer parte do mundo.
- Pontos finais privados, que existem dentro de uma rede virtual e têm um endereço IP privado a partir do espaço de endereço dessa rede virtual.

Os pontos finais públicos e privados existem na conta de armazenamento Azure. Uma conta de armazenamento é uma construção de gestão que representa um conjunto partilhado de armazenamento no qual você pode implementar várias partilhas de arquivos, bem como outros recursos de armazenamento, tais como recipientes blob ou filas.

Cada conta de armazenamento tem um nome de domínio totalmente qualificado (FQDN). Para as regiões de nuvem pública, este FQDN segue o padrão `storageaccount.file.core.windows.net` onde está o nome da conta de `storageaccount` armazenamento. Quando faz pedidos contra este nome, como a montagem da parte na sua estação de trabalho utilizando o SMB, o seu sistema operativo realiza uma procura de DNS para resolver o nome de domínio totalmente qualificado para um endereço IP ao qual pode usar para enviar os pedidos de SMB.

Por predefinição, `storageaccount.file.core.windows.net` resolve-se o endereço IP do ponto final público. O ponto final público de uma conta de armazenamento está hospedado num cluster de armazenamento Azure que acolhe os pontos finais públicos de muitas outras contas de armazenamento. Quando cria um ponto final privado, uma zona privada de DNS está ligada à rede virtual a que foi adicionada, com um mapeamento de registo CNAME `storageaccount.file.core.windows.net` para uma entrada de registo para o endereço IP privado do ponto final privado da sua conta de armazenamento. Isto permite-lhe utilizar `storageaccount.file.core.windows.net` o FQDN dentro da rede virtual e resolvê-lo para o endereço IP do ponto final privado.

Uma vez que o nosso objetivo final é aceder às ações de ficheiros Azure a partir das instalações através de um túnel de rede, como uma ligação VPN ou ExpressRoute, deve configurar os seus servidores DNS no local para encaminhar pedidos feitos ao serviço Azure Files para o serviço DNS privado Azure. Para isso, você precisa *configurar* o encaminhamento condicional `*.core.windows.net` de (ou o sufixo de ponto final de armazenamento apropriado para o Governo dos EUA, Alemanha ou nuvens nacionais da China) para um servidor DNS hospedado dentro da sua rede virtual Azure. Este servidor DNS irá então recursivamente encaminhar o pedido para o serviço privado de DNS da Azure que irá resolver o nome de domínio totalmente qualificado da conta de armazenamento para o endereço IP privado apropriado.

Configurar o encaminhamento de DNS para Azure Files exigirá executar uma máquina virtual para hospedar um servidor DNS para encaminhar os pedidos, no entanto este é um passo único para todas as ações de ficheiros Azure hospedadas na sua rede virtual. Além disso, este não é um requisito exclusivo para a Azure Files - qualquer serviço Azure que suporte pontos finais privados que você deseja aceder a partir de instalações pode fazer uso do reencaminhamento DNS que você configurará neste guia: Azure Blob armazenamento, SQL Azure, Cosmos DB, etc. 

Este guia mostra os passos para configurar o encaminhamento de DNS para o ponto final de armazenamento Azure, por isso, para além dos Ficheiros Azure, os pedidos de resolução de nomes DNS para todos os outros serviços de armazenamento Azure Blob (armazenamento Azure Blob, armazenamento de mesa Azure, armazenamento da fila Azure, etc.) serão encaminhados para o serviço privado dns da Azure. Os pontos finais adicionais para outros serviços Azure também podem ser adicionados se desejar. O encaminhamento de DNS para os seus servidores DNS no local também será configurado, permitindo que os recursos em nuvem dentro da sua rede virtual (como um servidor DFS-N) resolvam nomes de máquinas no local. 

## <a name="prerequisites"></a>Pré-requisitos
Antes de configurar o encaminhamento de DNS para os Ficheiros Azure, tem de ter concluído os seguintes passos:

- Uma conta de armazenamento contendo uma partilha de ficheiroS Azure que gostaria de montar. Para aprender a criar uma conta de armazenamento e uma partilha de ficheiros Azure, consulte [Criar uma partilha de ficheiros Azure](storage-how-to-create-file-share.md).
- Um ponto final privado para a conta de armazenamento. Para aprender a criar um ponto final privado para ficheiros Azure, consulte [Criar um ponto final privado](storage-files-networking-endpoints.md#create-a-private-endpoint).
- A [versão mais recente](/powershell/azure/install-az-ps) do módulo Azure PowerShell.

> [!Important]  
> Este guia pressupõe que está a utilizar o servidor DNS dentro do Windows Server no seu ambiente no local. Todos os passos descritos neste guia são possíveis com qualquer servidor DNS, e não apenas com o Servidor DNS do Windows.

## <a name="manually-configuring-dns-forwarding"></a>Reencaminhamento manual de DNS
Se já tiver servidores DNS no lugar dentro da sua rede virtual Azure, ou se simplesmente preferir implementar as suas próprias máquinas virtuais para serem servidores DNS, seja qual for a metodologia que a sua organização utiliza, pode configurar o DNS manualmente com os cmdlets do servidor DNS embutida.

Nos servidores DNS no local, crie um reencaminhador condicional utilizando `Add-DnsServerConditionalForwarderZone` . Este reencaminhador condicional deve ser implantado em todos os seus servidores DNS no local para ser eficaz no encaminhamento adequado do tráfego para Azure. Lembre-se de substituir `<azure-dns-server-ip>` pelos endereços IP apropriados para o seu ambiente.

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

Nos servidores DNS dentro da sua rede virtual Azure, também terá de colocar um reencaminhador para que os pedidos para a conta de armazenamento DNS sejam direcionados para o serviço DNS privado Azure, que é frontalizado pelo endereço IP `168.63.129.16` reservado. (Lembre-se de povoar `$storageAccountEndpoint` se estiver a executar os comandos numa sessão diferente do PowerShell.)

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>Utilizar o módulo Azure Files Hybrid para configurar o encaminhamento de DNS
De forma a tornar o encaminhamento de DNS o mais fácil possível, fornecemos automatização no módulo Azure Files Hybrid. Os cmdlets previstos para manipular o DNS neste módulo irão ajudá-lo a implementar servidores DNS na sua rede virtual Azure e atualizar os servidores DNS no local para os encaminhar. 

Se nunca utilizou o módulo Azure Files Hybrid, tem primeiro de o instalar na sua estação de trabalho. Descarregue a [versão mais recente](https://github.com/Azure-Samples/azure-files-samples/releases) do módulo Azure Files Hybrid PowerShell:

```PowerShell
# Unzip the downloaded file
Expand-Archive -Path AzFilesHybrid.zip

# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\AzFilesHybrid\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid
```

A implementação da solução de encaminhamento de DNS tem dois passos, criando um conjunto de regras de encaminhamento de DNS, que define quais os serviços Azure a que pretende encaminhar os pedidos, e a implantação real dos reencaminhadores dns. 

O exemplo seguinte remete pedidos para a conta de armazenamento, pedidos inclusivos para Azure Files, armazenamento Azure Blob, armazenamento de mesa Azure e armazenamento da fila Azure. Se desejar, pode adicionar o encaminhamento para um serviço adicional de Azure à regra através `-AzureEndpoints` do parâmetro do `New-AzDnsForwardingRuleSet` cmdlet. Lembre-se de substituir `<virtual-network-resource-group>` , e com os `<virtual-network-name>` `<subnet-name>` valores apropriados para o seu ambiente.

```PowerShell
# Create a rule set, which defines the forwarding rules
$ruleSet = New-AzDnsForwardingRuleSet -AzureEndpoints StorageAccountEndpoint

# Deploy and configure DNS forwarders
New-AzDnsForwarder `
        -DnsForwardingRuleSet $ruleSet `
        -VirtualNetworkResourceGroupName "<virtual-network-resource-group>" `
        -VirtualNetworkName "<virtual-network-name>" `
        -VirtualNetworkSubnetName "<subnet-name>"
```

Pode ainda achar útil/necessário fornecer vários parâmetros adicionais:

| Nome do parâmetro | Tipo | Description |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | Por predefinição, os servidores DNS serão implantados no mesmo grupo de recursos que a rede virtual. Se isso não for desejado, este parâmetro permite-lhe escolher um grupo de recursos alternativos para que sejam implantados. |
| `DnsForwarderRootName` | `string` | Por predefinição, os servidores DNS que são implantados no Azure têm os nomes `DnsFwder-*` , onde o asterisco é povoado por um iterador. Este parâmetro altera a raiz desse nome (isto é, `DnsFwder` ). |
| `VmTemporaryPassword` | `SecureString` | Por predefinição, uma palavra-passe aleatória é escolhida para a conta por defeito temporária que um VM tem antes de ser unida ao domínio. Depois de ser desativado o domínio, a conta predefinida é desativada. |
| `DomainToJoin` | `string` | O domínio para se juntar ao DNS VM(s) para se juntar. Por predefinição, este domínio é escolhido com base no domínio do computador onde está a executar os cmdlets. |
| `DnsForwarderRedundancyCount` | `int` | O número de DNS VMs para implantar para a sua rede virtual. Por predefinição, `New-AzDnsForwarder` implementa dois servidores DNS na sua rede virtual Azure, num Conjunto de Disponibilidade, para garantir a redundância. Este número pode ser modificado conforme desejado. |
| `OnPremDnsHostNames` | `HashSet<string>` | Uma lista manualmente especificada de nomes de anfitriões DNS no local para criar reencaminhadores ligados. Este parâmetro é útil quando não pretende aplicar reencaminhadores em todos os servidores DNS no local, como quando tem um leque de clientes com nomes DNS especificados manualmente. |
| `Credential` | `PSCredential` | Uma credencial a utilizar ao atualizar os servidores DNS. Isto é útil quando a conta de utilizador com que fez login não tem permissões para modificar as definições de DNS. |
| `SkipParentDomain` | `SwitchParameter` | Por padrão, os reencaminhadores DNS são aplicados ao domínio de nível mais alto que existe no seu ambiente. Por exemplo, se `northamerica.corp.contoso.com` for um domínio infantil de , o `corp.contoso.com` reencaminhador será criado para os servidores DNS associados a `corp.contoso.com` . Este parâmetro fará com que os reencaminhadores sejam criados em `northamerica.corp.contoso.com` . |

## <a name="confirm-dns-forwarders"></a>Confirmar os reencaminhadores dns
Antes de testar para ver se os reencaminhadores de DNS foram aplicados com sucesso, recomendamos a limpeza da cache DNS na sua estação de trabalho local utilizando `Clear-DnsClientCache` . Para testar para ver se consegue resolver com sucesso o nome de domínio totalmente qualificado da sua conta de armazenamento, utilize `Resolve-DnsName` ou `nslookup` .

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

Se a resolução do nome for bem sucedida, deverá ver o endereço IP resolvido corresponder ao endereço IP da sua conta de armazenamento.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4
```

Se já criou uma ligação VPN ou ExpressRoute, também pode usar `Test-NetConnection` para ver se uma ligação TCP pode ser feita com sucesso na sua conta de armazenamento.

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>Ver também
- [Planear uma implementação de Ficheiros do Azure](storage-files-planning.md)
- [Considerações de networking de ficheiros Azure](storage-files-networking-overview.md)
- [Configurar pontos finais da rede Azure Files](storage-files-networking-endpoints.md)