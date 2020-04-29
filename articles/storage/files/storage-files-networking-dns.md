---
title: Configurar o encaminhamento de DNS para Ficheiros Azure / Microsoft Docs
description: Uma visão geral das opções de networking para Ficheiros Azure.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 35dfbcb274721049f2160719222ca89038c93356
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80082501"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>Configurar o reencaminhamento de DNS para Ficheiros do Azure
O Azure Files permite-lhe criar pontos finais privados para as contas de armazenamento que contêm as suas ações de ficheiro. Embora úteis para muitas aplicações diferentes, os pontos finais privados são especialmente úteis para conectar as suas partilhas de ficheiros Azure da sua rede no local usando uma ligação VPN ou ExpressRoute usando o peering privado. 

Para que as ligações à sua conta de armazenamento ultrapassem o túnel de rede, o nome de domínio totalmente qualificado (FQDN) da sua conta de armazenamento deve ser resolvido para o endereço IP privado do seu ponto final privado. Para isso, deve encaminhar o sufixo`core.windows.net` final de armazenamento (para as regiões públicas de nuvem) para o serviço DNS privado Azure acessível a partir da sua rede virtual. Este guia mostrará como configurar e configurar o encaminhamento de DNS para resolver corretamente o endereço IP do ponto final da sua conta de armazenamento.

Recomendamos vivamente que leia planeamento para uma implementação de [Ficheiros Azure](storage-files-planning.md) e considerações de [rede Azure Files](storage-files-networking-overview.md) antes de completar os passos descritos neste artigo.

## <a name="overview"></a>Descrição geral
O Azure Files fornece dois tipos principais de pontos finais para aceder a ações de ficheiros Azure: 
- Pontos finais públicos, que têm um endereço IP público e podem ser acedidos a partir de qualquer parte do mundo.
- Pontos finais privados, que existem dentro de uma rede virtual e têm um endereço IP privado a partir do espaço de endereço dessa rede virtual.

Existem pontos finais públicos e privados na conta de armazenamento Azure. Uma conta de armazenamento é uma construção de gestão que representa um conjunto partilhado de armazenamento no qual você pode implementar várias ações de arquivo, bem como outros recursos de armazenamento, tais como recipientes de bolhas ou filas.

Cada conta de armazenamento tem um nome de domínio totalmente qualificado (FQDN). Para as regiões públicas de nuvem, este `storageaccount.file.core.windows.net` `storageaccount` FQDN segue o padrão onde está o nome da conta de armazenamento. Ao fazer pedidos contra este nome, tais como a montagem da parte na sua estação de trabalho utilizando SMB, o seu sistema operativo realiza uma procura de DNS para resolver o nome de domínio totalmente qualificado para um endereço IP que pode usar para enviar os pedidos de SMB.

Por predefinição, `storageaccount.file.core.windows.net` resolve-se no endereço IP do ponto final do público. O ponto final público para uma conta de armazenamento está hospedado num cluster de armazenamento Azure que acolhe muitos outros pontos finais públicos de contas de armazenamento. Ao criar um ponto final privado, uma zona privada de DNS está ligada à `storageaccount.file.core.windows.net` rede virtual a que foi adicionada, com um mapeamento de registo CNAME a uma entrada de registo a um endereço IP privado do ponto final privado da sua conta de armazenamento. Isto permite-lhe `storageaccount.file.core.windows.net` utilizar o FQDN dentro da rede virtual e resolvê-lo para o endereço IP do ponto final privado.

Uma vez que o nosso objetivo final é aceder às ações de ficheiros Azure alojadas dentro da conta de armazenamento a partir de um túnel de rede como uma ligação VPN ou ExpressRoute, deve configurar os seus servidores DNS no local para encaminhar pedidos feitos ao serviço Azure Files para o serviço DNS privado Azure. Para isso, é necessário configurar o `*.core.windows.net` *reencaminhamento condicional* de (ou o sufixo final de armazenamento adequado para o Governo dos EUA, Alemanha ou Nuvens nacionais da China) para um servidor DNS alojado dentro da sua rede virtual Azure. Este servidor DNS irá então recursivamente encaminhar o pedido para o serviço Privado DNS da Azure que resolverá o nome de domínio totalmente qualificado da conta de armazenamento para o endereço IP privado apropriado.

Configurar o encaminhamento de DNS para Ficheiros Azure exigirá executar uma máquina virtual para alojar um servidor DNS para encaminhar os pedidos, no entanto este é um passo único para todas as ações de ficheiro sinuosas do Azure alojadas dentro da sua rede virtual. Além disso, este não é um requisito exclusivo para o Azure Files - qualquer serviço Azure que suporte pontos finais privados a que deseja aceder a partir de instalações pode fazer uso do encaminhamento DNS que você irá configurar neste guia: armazenamento Azure Blob, SQL Azure, Cosmos DB, etc. 

Este guia mostra os passos para configurar o encaminhamento de DNS para o ponto final de armazenamento Azure, pelo que, para além dos Ficheiros Azure, os pedidos de resolução de nomes DNS para todos os outros serviços de armazenamento Azure (armazenamento Azure Blob, armazenamento de mesa Azure, armazenamento de fila Azure, etc.) serão encaminhados para o serviço privado dNS da Azure. Os pontos finais adicionais para outros serviços Azure também podem ser adicionados se desejar. O DNS reencaminhado para os seus servidores DNS no local também será configurado, permitindo que os recursos da nuvem dentro da sua rede virtual (como um servidor DFS-N) resolvam os nomes das máquinas no local. 

## <a name="prerequisites"></a>Pré-requisitos
Antes de configurar o DNS reencaminhamento para Ficheiros Azure, tem de ter concluído os seguintes passos:

- Uma conta de armazenamento contendo uma partilha de ficheiros Azure que gostaria de montar. Para aprender a criar uma conta de armazenamento e uma partilha de ficheiros Azure, consulte [Create a Azure file share](storage-how-to-create-file-share.md).
- Um ponto final privado para a conta de armazenamento. Para aprender a criar um ponto final privado para ficheiros Azure, consulte [Criar um ponto final privado](storage-files-networking-endpoints.md#create-a-private-endpoint).
- A [versão mais recente](https://docs.microsoft.com/powershell/azure/install-az-ps) do módulo Azure PowerShell.

> [!Important]  
> Este guia pressupõe que está a utilizar o servidor DNS dentro do Windows Server no seu ambiente no local. Todos os passos descritos neste guia são possíveis com qualquer servidor DNS, e não apenas com o Servidor DNS do Windows.

## <a name="manually-configuring-dns-forwarding"></a>Configuração manual do encaminhamento de DNS
Se já tem servidores DNS no lugar dentro da sua rede virtual Azure, ou se simplesmente prefere implementar as suas próprias máquinas virtuais para serem servidores DNS por qualquer metodologia que a sua organização utilize, pode configurar o DNS manualmente com os cmdlets de servidor DNS incorporados PowerShell.

Nos seus servidores DNS no local, crie `Add-DnsServerConditionalForwarderZone`um avançado condicionado utilizando . Este avançado condicional deve ser implantado em todos os seus servidores DNS no local para ser eficaz no encaminhamento adequado do tráfego para O Azure. Lembre-se `<azure-dns-server-ip>` de substituir pelos endereços IP apropriados para o seu ambiente.

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

Nos servidores DNS dentro da sua rede virtual Azure, também terá de colocar um avançado no lugar, de modo a que os pedidos para a conta `168.63.129.16`de armazenamento DNS sejam direcionados para o serviço DNS privado Azure, que é encabeçado pelo endereço IP reservado. (Lembre-se `$storageAccountEndpoint` de povoar se estiver a comandar os comandos numa sessão diferente da PowerShell.)

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>Utilização do módulo Azure Files Hybrid para configurar o encaminhamento de DNS
De forma a tornar o encaminhamento de DNS o mais fácil possível, fornecemos automação no módulo Azure Files Hybrid. Os cmdlets fornecidos para manipular dNS neste módulo irão ajudá-lo a implementar servidores DNS na sua rede virtual Azure e atualizar os seus servidores DNS no local para os encaminhar. 

Se nunca utilizou o módulo Azure Files Hybrid, tem primeiro de o instalar na sua estação de trabalho. Descarregue a [versão mais recente](https://github.com/Azure-Samples/azure-files-samples/releases) do módulo PowerShell Híbrido Azure Files:

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

A implementação da solução de encaminhamento dNS tem duas etapas, criando um conjunto de regras de encaminhamento dNS, que define quais os serviços Azure a que pretende encaminhar pedidos e a implantação real dos avançados do DNS. 

O exemplo seguinte reme os pedidos para a conta de armazenamento, pedidos inclusivos a Ficheiros Azure, armazenamento Azure Blob, armazenamento de mesa Azure e armazenamento de fila Azure. Se desejar, pode adicionar encaminhamento para um serviço `-AzureEndpoints` Azure adicional `New-AzDnsForwardingRuleSet` à regra através do parâmetro do cmdlet. Lembre-se `<virtual-network-resource-group>` `<virtual-network-name>`de `<subnet-name>` substituir, e com os valores apropriados para o seu ambiente.

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

| Nome do parâmetro | Tipo | Descrição |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | Por padrão, os servidores DNS serão implantados no mesmo grupo de recursos que a rede virtual. Se isto não for desejado, este parâmetro permite-lhe escolher um grupo de recursos alternativos para que sejam implantados. |
| `DnsForwarderRootName` | `string` | Por padrão, os servidores DNS que são implantados em Azure têm os nomes `DnsFwder-*`, onde o asterisco é povoado por um iterdor. Este parâmetro altera a raiz desse nome `DnsFwder`(isto é, ). |
| `VmTemporaryPassword` | `SecureString` | Por predefinição, é escolhida uma palavra-passe aleatória para a conta de incumprimento temporária que um VM tem antes de ser unida pelo domínio. Depois de o domínio ser unido, a conta predefinida é desativada. |
| `DomainToJoin` | `string` | O domínio para aderir aos DNS VM(s) para aderir. Por predefinição, este domínio é escolhido com base no domínio do computador onde está a executar os cmdlets. |
| `DnsForwarderRedundancyCount` | `int` | O número de VMs DNS a implantar para a sua rede virtual. Por padrão, `New-AzDnsForwarder` implementa dois servidores DNS na sua rede virtual Azure, num Conjunto de Disponibilidade, para garantir a redundância. Este número pode ser modificado conforme desejado. |
| `OnPremDnsHostNames` | `HashSet<string>` | Uma lista manualmente especificada dos nomes de anfitriões do DNS no local para criar os avançados. Este parâmetro é útil quando não pretende aplicar os avançados em todos os servidores DNS no local, como quando tem um leque de clientes com nomes DNS especificados manualmente. |
| `Credential` | `PSCredential` | Uma credencial a utilizar ao atualizar os servidores DNS. Isto é útil quando a conta de utilizador com a sua conta de utilizador não tem permissões para modificar as definições de DNS. |
| `SkipParentDomain` | `SwitchParameter` | Por padrão, os avançados DNS são aplicados ao domínio de nível mais alto que existe no seu ambiente. Por exemplo, `northamerica.corp.contoso.com` se for `corp.contoso.com`um domínio infantil de , o avançado será `corp.contoso.com`criado para os servidores DNS associados a . Este parâmetro fará com que os `northamerica.corp.contoso.com`avançados sejam criados em . |

## <a name="confirm-dns-forwarders"></a>Confirme os avançados do DNS
Antes de testar para ver se os avançados do DNS foram aplicados com sucesso, recomendamos que limpe a cache DNS na sua estação de trabalho local utilizando `Clear-DnsClientCache`. Para testar para ver se consegue resolver com sucesso o nome `Resolve-DnsName` `nslookup`de domínio totalmente qualificado da sua conta de armazenamento, use ou .

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

Se a resolução de nome for bem sucedida, deve ver que o endereço IP resolvido corresponde ao endereço IP da sua conta de armazenamento.

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

Se já criou uma ligação VPN ou ExpressRoute, também pode utilizar `Test-NetConnection` para ver se uma ligação TCP pode ser feita com sucesso na sua conta de armazenamento.

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>Consulte também
- [Planear uma implementação dos Ficheiros do Azure](storage-files-planning.md)
- [Considerações de networking de Ficheiros Azure](storage-files-networking-overview.md)
- [Configurar pontos finais da rede Azure Files](storage-files-networking-endpoints.md)